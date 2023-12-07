## 코드
```swift
struct ContentView: View {
    @State private var text: String = ""
    @State private var isSearching: Bool = false
    
    var body: some View {
        let gradient = Gradient(colors: [.green, .yellow])
        
        HStack {
            //            Text("검색어를 입력하세요.")
            TextField("검색어를 입력하세요.", text: $text)
                .padding(.leading, 10)
            Spacer()
            Image(systemName: "magnifyingglass")
                .foregroundStyle(.linearGradient(
                    gradient,
                    startPoint: .topLeading,
                    endPoint: .bottomTrailing)
                )
                .onTapGesture {
                    isSearching = true
                }
            
        }
        .padding(10.0)
        .overlay(
            RoundedRectangle(cornerRadius: 20.0)
                .stroke(style: StrokeStyle(lineWidth: 2))
                .foregroundStyle(.linearGradient(
                    gradient,
                    startPoint: .leading,
                    endPoint: .trailing)
                )
        )
        .padding(20)
        .alert("\(text) 검색!", isPresented: $isSearching) {
            Button("OK", role: .cancel) {
                isSearching = false
                text = ""
            }
        }
    }
}
```

## 사진

| 입력 | 돋보기 버튼 | 
| -------- | -------- |
|![Simulator Screenshot - iPhone 14 Pro - 2023-12-07 at 22 00 44](https://github.com/KTRJB/SwiftUI_Study/assets/102353787/405b5e62-cf4d-413b-98b0-eec95d04569e)|  ![Simulator Screenshot - iPhone 14 Pro - 2023-12-07 at 22 01 04](https://github.com/KTRJB/SwiftUI_Study/assets/102353787/84b81fe0-aa78-458d-bc58-159b5130845e)| 
