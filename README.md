# cv https://github.com/Yana-Sokolova2502/cv/blob/Yana-Sokolova2502-patch-1/README.md
# Cоколова Яна Сергеевна
## +375290000000
**Хочу заниматься программированием, так как мне это очень нравится**
*Прошла курсы от Modsen по JS, в университете изучаю С++*
# Пример кода
#include <iostream>
#include <vector>
#include <thread>
#include <mutex>
#include <random>
#include <future>
#include <algorithm>
#include <chrono>
#include <condition_variable>


struct Task {
    int id;
    std::vector<double> data;
    std::promise<double> result;
};

double processTask(const Task& task) {
    double sum = 0;
    for (double value : task.data) {
        sum += value * value; // Просто какой-то пример вычисления
        std::this_thread::sleep_for(std::chrono::milliseconds(10)); 
    }
    return sum;
}

class ThreadPool {
public:
    ThreadPool(int numThreads) : stop_(false) {
        for (int i = 0; i < numThreads; ++i) {
            threads_.emplace_back([this] {
                while (true) {
                    Task task;
                    {
                        std::unique_lock<std::mutex> lock(mutex_);
                        cv_.wait(lock, [this] { return stop_ || !tasks_.empty(); });
                        if (stop_) return;
                        task = std::move(tasks_.front());
                        tasks_.pop_front();
                    }
                    double result = processTask(task);
                    task.result.set_value(result);
                }
            });
        }
    }

    ~ThreadPool() {
        {
            std::unique_lock<std::mutex> lock(mutex_);
            stop_ = true;
        }
        cv_.notify_all();
        for (std::thread& thread : threads_) {
            thread.join();
        }
    }

    template<typename F, typename... Args>
    auto enqueue(F&& f, Args&&... args) -> std::future<decltype(f(args...))>
    {
        using return_type = decltype(f(args...));

        auto task = std::make_shared<std::packaged_task<return_type()>>(
            std::bind(std::forward<F>(f), std::forward<Args>(args)...));

        std::future<return_type> res = task->get_future();
        {
            std::unique_lock<std::mutex> lock(mutex_);

            if(stop_)
                throw std::runtime_error("enqueue on stopped ThreadPool");

            tasks_.emplace_back(Task{nextTaskId_++, std::vector<double>(), std::promise<double>()});
        }
        (*task)();

        cv_.notify_one();
        return res;
    }

private:

    int nextTaskId_ = 0;
    std::deque<Task> tasks_;
    std::vector<std::thread> threads_;
    std::mutex mutex_;
    std::condition_variable cv_;
    bool stop_;
};



int main() {
       ThreadPool pool(4);

    std::vector<std::future<double>> futures;
    for (int i = 0; i < 10; ++i) {
        std::vector<double> data;
        std::random_device rd;
        std::mt19937 gen(rd());
        std::uniform_real_distribution<> dis(0.0, 1.0);

        for (int j=0; j < 1000 ; ++j)
            data.push_back(dis(gen));


        futures.push_back(pool.enqueue(processTask, Task{i, data, std::promise<double>()}));
    }


    for (auto& future : futures) {
        std::cout << "Результат: " << future.get() << std::endl;
    }

    return 0;
}


    for (auto& future : futures) {
        std::cout << "Результат: " << future.get() << std::endl;
    }

    return 0;
}
