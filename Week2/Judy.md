## 코드
```swift
class NumberModel: ObservableObject {
    
    @Published var numbers: [Int] = Array(1...100)
    @Published var selectedType: ColorType = .one
    
    func initNumbers() {
        numbers = Array(1...100)
    }
    
    func makeRandom() {
        var randomArray = [Int]()
        
        for _ in 1...100 {
            let randomNumber = Int.random(in: 1...100)
            randomArray.append(randomNumber)
        }
        
        numbers = Array(Set(randomArray))
    }
}

struct RainbowView: View {
    
    @StateObject var numberValue = NumberModel() // ✅ PropertyWrappers
    @State var showSheet: Bool = false // ✅ StatePropertyWrapper
    
    let colors: [ColorType] = ColorType.allCases
    
    var body: some View {
        VStack(spacing: 16) {
            ForEach(colors, id: \.self) { type in // ✅ ForEach
                RoundedRectangle(cornerRadius: 15)
                    .padding(.horizontal, 16)
                    .foregroundColor(type.color)
                    .overlay {
                        Text("\(type.rawValue)")
                            .font(.largeTitle)
                    }
                    .onTapGesture {
                        showSheet.toggle()
                        numberValue.selectedType = type
                        numberValue.initNumbers()
                    }
                    .fullScreenCover(isPresented: $showSheet) {
                        NumberView(value: numberValue)
                    }
            }
        }
    }
}

struct NumberView: View {
    @ObservedObject var value: NumberModel
    @Environment(\.dismiss) var presentationMode

    var body: some View {
        VStack(alignment: .trailing, spacing: 16) {
            HStack {
                Spacer()
                Button { // ✅ Button
                    value.makeRandom()
                } label: {
                    Text("Random")
                }
                Spacer()
                Button {
                    presentationMode()
                } label: {
                    Image(systemName: "xmark")
                }
            }
            .font(.title)
            .foregroundColor(.black)
            
            ScrollView { // ✅ ScrollView
                LazyVGrid(columns: Array(repeating: GridItem(.flexible()), count: 4)) { // ✅ Grid
                    ForEach(value.numbers, id: \.self) { number in
                        ZStack {
                            NumberButton(
                                value: value,
                                isMultiple: number % value.selectedType.rawValue == 0
                            )
                            Text("\(number)")
                                .foregroundColor(
                                    number % value.selectedType.rawValue != 0 ? .white : .black
                                )
                        }
                    }
                }
            }
        }
        .padding(.horizontal, 16)
        .ignoresSafeArea(edges: .bottom) // ✅ Ignore Safe Area
    }
}

struct NumberButton: View { // ✅ Extracting functions and subviews
    
    @ObservedObject var value: NumberModel
    
    let isMultiple: Bool
    
    var body: some View {
        ZStack {
            if isMultiple { // ✅ Conditional Statements
                Circle()
                    .foregroundColor(value.selectedType.color)
            } else {
                RoundedRectangle(cornerRadius: 15)
                    .foregroundColor(.gray)
            }
        }
        .aspectRatio(1, contentMode: .fill)
    }
}

enum ColorType: Int, CaseIterable { // ✅ Inits and Enums
    case one = 1
    case two
    case three
    case four
    case five
    case six
    case seven
    
    var color: Color {
        switch self {
        case .one:
            return .red
        case .two:
            return .orange
        case .three:
            return .yellow
        case .four:
            return .green
        case .five:
            return .cyan
        case .six:
            return .blue
        case .seven:
            return .purple
        }
    }
}

```

## 실행 예시

![Simulator Screen Recording - iPhone 14 Pro - 2023-12-18 at 22 18 54](https://github.com/KTRJB/SwiftUI_Study/assets/102353787/e91ce20c-4b36-4b22-8547-a59247d59e70)
