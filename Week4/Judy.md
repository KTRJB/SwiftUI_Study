## 코드
```swift
struct InputView: View {
    @State private var text: String = ""
    @State private var showSheet: Bool = false
    @FocusState private var focus: Bool
   
    var body: some View {
        NavigationView {
            VStack {
                TextField("닉네임을 입력하세요.", text: $text)
                    .focused($focus)
                    .padding(.leading, 16)
                    .frame(height: 50)
                    .border(.gray)
                    .submitLabel(.next)
                    .onSubmit {
                        showSheet.toggle()
                        focus = false
                    }
                
                NavigationLink {
                    BubbleView()
                        .navigationTitle(text)
                } label: {
                    Text("다음 단계")
                        .bold()
                        .font(.title3)
                        .foregroundColor(.white)
                        .frame(maxWidth: .infinity, minHeight: 50)
                        .background(.blue)
                }
            }
            .padding(.horizontal, 16)
            .sheet(isPresented: $showSheet) {
                Text("\(text)(은)는 사용 \(text.count < 3 ? "불" : "")가능한 닉네임입니다.")
            }
        }
    }
}


struct BubbleView: View {
    
    @State private var isUp: Bool = true
    @State private var selectedColor: Color = .yellow
    @State private var opacity: CGFloat = 1.0
   
    private let numbers: [CGFloat] = [0.3, 0.7, 1.0]
    
    var body: some View {
        VStack {
            Rectangle()
                .frame(height: isUp ? 30 : nil)
                .foregroundColor(.gray)
                .animation(
                    .spring(response: 0.5, dampingFraction: 0.5),
                    value: isUp
                )
            Spacer()
            
            if isUp {
                Circle()
                    .foregroundColor(selectedColor)
                    .transition(
                        AnyTransition.scale.animation(.easeInOut)
                    )
                    .animation(.spring(dampingFraction: 0.3), value: isUp)
                    .opacity(opacity)
            }
            
            Spacer()
            VStack(spacing: 8) {
                Toggle("가림 유무", isOn: $isUp)
                ColorPicker("컬러 선택", selection: $selectedColor)
                Picker("대비 선택", selection: $opacity) {
                    ForEach(numbers, id: \.self) { num in
                        Text("\(String(format: "%.1f", num))")
                            .tag(num)
                    }
                }
                .pickerStyle(.segmented)
                .frame(height: 50)
            }
            .padding(16)
        }
    }
}
```

## 실행 예시

![Simulator Screen Recording - iPhone 14 Pro - 2024-01-05 at 23 03 54](https://github.com/KTRJB/SwiftUI_Study/assets/102353787/ba4d8fe5-3169-40e4-89d6-98880e33fefd)

