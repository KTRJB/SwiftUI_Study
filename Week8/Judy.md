## 코드
```swift
struct EightWeek: View {
    let timer = Timer.publish(every: 1.0, on: .main, in: .common).autoconnect()
    let data: [WaveModel] = [
        WaveModel("50"),
        WaveModel("33"),
        WaveModel("25"),
        WaveModel("10")
    ]
    
    @State private var animate: CGFloat = -50
    @State private var showSheet: Bool = false
    
    var body: some View {
        VStack {
            Group {
                WaveShape(point: animate)
                WaveShape(point: animate / 1.5)
            }
            .foregroundColor(.red)
           
            Group {
                WaveShape(point: animate / 2)
                WaveShape(point: animate / 5)
            }
            .foregroundColor(.blue)
            
            Button("종료") {
                timer.upstream.connect().cancel()
                showSheet.toggle()
            }
            .accentColor(.purple)
            .buttonStyle(.borderedProminent)
            .controlSize(.large)
            .buttonBorderShape(.capsule)
        }
        .onReceive(timer) { _ in
            withAnimation(.easeIn) {
                animate *= -1
            }
        }
        .sheet(isPresented: $showSheet) {
            ZStack {
                Color.purple.ignoresSafeArea()
                    .presentationDetents([.fraction(0.3)])
                VStack {
                    ForEach(data) { wave in
                        Spacer()
                        Text("범위: \(wave.height)")
                            .foregroundColor(.white)
                            .font(.title3)
                            .bold()
                    }
                }
            }
        }
    }
}

struct WaveModel: Identifiable {
    let id = UUID().uuidString
    let height: String
    
    init(_ height: String) {
        self.height = height
    }
}

struct WaveShape: Shape {
    var point: CGFloat
    var animatableData: CGFloat {
        get { point }
        set { point = newValue }
    }
    
    func path(in rect: CGRect) -> Path {
        Path { path in
            let start = CGPoint(x: .zero, y: 100)
            path.move(to: start)
            
            
            for i in 0..<3 {
                let start = CGPoint(x: i * 150, y: 100)
                path.addCurve(
                    to: CGPoint(x: start.x + 50 * 3, y: start.y),
                    control1: CGPoint(x: start.x + 50, y: start.y + 50 + point),
                    control2: CGPoint(x: start.x + 50 * 2 , y: start.y - 50 - point)
                )
            }
        }
    }
}
```

## 실행 예시
![Simulator Screen Recording - iPhone 14 Pro - 2024-01-31 at 21 44 15](https://github.com/KTRJB/SwiftUI_Study/assets/102353787/e372b484-b98b-45ea-baae-f39c7a144af7)
