BenchmarkMacro
Swift макрос для автоматического бенчмаркинга методов с помощью аннотации @Benchmark.

Возможности
🏷️ Простое использование: Просто добавьте @Benchmark над методом
⏱️ Автоматическое измерение: Поддержка синхронных и асинхронных методов
🔄 Множественные итерации: Настраиваемое количество итераций для точных измерений
📊 Красивый вывод: Автоматический вывод результатов в миллисекундах
🎯 Поддержка всех типов: Void и non-void функции, throwing и async throwing
💡 Compile-time генерация: Нулевые накладные расходы в runtime
Установка
Swift Package Manager
Добавьте в ваш Package.swift:

swift
dependencies: [
    .package(url: "https://github.com/your-username/BenchmarkMacro.git", from: "1.0.0")
]
Или в Xcode:

File → Add Package Dependencies
Введите URL репозитория
Добавьте BenchmarkMacro к вашему target
Использование
Основной синтаксис
swift
import BenchmarkMacro

class MyService {
    
    // Простейший случай - используется имя метода как label, 1 итерация
    @Benchmark
    func processData() -> String {
        // Ваша логика
        Thread.sleep(forTimeInterval: 0.1)
        return "processed"
    }
    
    // Кастомный label и количество итераций
    @Benchmark("Heavy Task", iterations: 10)
    func heavyComputation() -> Int {
        var result = 0
        for i in 1...10000 {
            result += i * i
        }
        return result
    }
    
    // Void функции
    @Benchmark("Cleanup", iterations: 5)
    func cleanup() {
        // логика очистки
        Thread.sleep(forTimeInterval: 0.01)
    }
    
    // Асинхронные функции
    @Benchmark("API Call", iterations: 3)
    func fetchData() async -> Data {
        try? await Task.sleep(nanoseconds: 100_000_000)
        return Data("response".utf8)
    }
    
    // Асинхронные void функции
    @Benchmark("Save Data")
    func saveData() async {
        try? await Task.sleep(nanoseconds: 50_000_000)
        print("Data saved")
    }
    
    // Функции с параметрами
    @Benchmark("Array Sort", iterations: 100)
    func sortArray(_ array: [Int]) -> [Int] {
        return array.sorted()
    }
    
    // Throwing функции
    @Benchmark("File Read")
    func readFile() throws -> String {
        return try String(contentsOfFile: "/path/to/file")
    }
    
    // Async throwing функции
    @Benchmark("Network Request", iterations: 5)
    func makeRequest() async throws -> Data {
        // сетевой запрос
        try? await Task.sleep(nanoseconds: 200_000_000)
        return Data()
    }
}
Пример использования
swift
let service = MyService()

// Вызываете метод как обычно - бенчмарк происходит автоматически
let result = service.processData()
// Output: ⏱️ [processData] sync ×1: ~100.123 ms

let computed = service.heavyComputation()
// Output: ⏱️ [Heavy Task] sync ×10: ~5.456 ms

service.cleanup()
// Output: ⏱️ [Cleanup] sync ×5: ~10.234 ms

let data = await service.fetchData()
// Output: ⏱️ [API Call] async ×3: ~100.567 ms
Как это работает
Макрос @Benchmark трансформирует ваш код во время компиляции:

Исходный код:

swift
@Benchmark("Test", iterations: 5)
func myFunction() -> String {
    return "result"
}
Сгенерированный код:

swift
func _benchmark_myFunction() -> String {
    return "result"
}

func myFunction() -> String {
    let timer = BenchmarkTimer()
    return timer.measure(label: "Test", iterations: 5) {
        return self._benchmark_myFunction()
    }
}
Параметры макроса
label (String, опционально): Имя для отображения в выводе. По умолчанию используется имя метода
iterations (Int, опционально): Количество итераций для выполнения. По умолч
