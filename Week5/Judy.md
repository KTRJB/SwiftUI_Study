## 코드

```swift
struct InfoView: View {
    
    // MARK: Properties
    
    @State private var selectedTab: Color = .red
    @State private var colors: [Color] = [.red, .yellow, .green, .blue, .purple]
    
    @State private var selectedDate: Date = Date()
    @AppStorage("count") var number: Int = 0
    @AppStorage("rate") var star: Double = 0
    
    @State private var isDarkMode: Bool = false
    
    @State private var isSheet: Bool = false
    @State private var backgroundColor: Color = .white
    
    // MARK: Body
    
    var body: some View {
        ZStack {
            VStack {
                TabView(selection: $selectedTab) {
                    ForEach(colors, id: \.self) { color in
                        color
                            .tag(color)
                    }
                }
                .tabViewStyle(.page(indexDisplayMode: .always))
                
                
                VStack(spacing: 8) {
                    Toggle("다크모드", isOn: $isDarkMode)
                        .onChange(of: isDarkMode) { newValue in
                            if let windowScene = UIApplication.shared.connectedScenes.first as? UIWindowScene {
                                windowScene.windows.first?.overrideUserInterfaceStyle = newValue ? .dark : .light
                                backgroundColor = newValue ? .black : .white
                            }
                        }
                    
                    DatePicker("날짜", selection: $selectedDate, displayedComponents: [.date])
                        .accentColor(selectedTab)
                    
                    Stepper("넘버: \(number)", value: $number)
                        .onChange(of: number) { newValue in
                            selectedTab = number < 5 ? colors[number] : .red
                        }
                    
                    HStack {
                        Slider(value: $star, in: 0...5)
                            .onChange(of: star) { newValue in
                                isSheet = Int(newValue) == 5
                            }
                        
                        Text("\(Int(star))점")
                    }
                }
                .padding()
                
            }
            
            if isSheet {
                Text("별점 5점~!")
                    .frame(width: 200, height: 100)
                    .font(.largeTitle)
                    .background(isDarkMode ? .black : .white)
                    .onAppear {
                        backgroundColor = .gray
                    }
                    .onDisappear {
                        backgroundColor = isDarkMode ? .black : .white
                    }
                    .onTapGesture {
                        isSheet = false
                    }
            }
        }
        .background(backgroundColor)
    }
}
```
<br>

## 실행 예시
![Simulator Screen Recording - iPhone 14 Pro - 2024-01-14 at 21 11 25](https://github.com/KTRJB/SwiftUI_Study/assets/102353787/bea2c0ca-445d-46ce-ac13-89068fd8586f)

