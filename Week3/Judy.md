## 코드
```swift
struct PolygonListView: View {
    @State private var numbers = Array(3..<6)
    @State private var isShow = false
    @State private var inputText: String = ""
    
    var body: some View {
        NavigationView {
            VStack {
                AddButton(inputText: $inputText) {
                    add(inputText)
                }
                
                List {
                    Section {
                        ForEach(numbers, id: \.self) { num in
                            NavigationLink {
                                PolygonView(num)
                            } label: {
                                Text("\(num)")
                            }
                        }
                        .onDelete { indexSet in
                            delete(indexSet: indexSet)
                        }
                        .listRowBackground(Color.green)
                    } header: {
                        Text("다각형 리스트")
                            .font(.title3)
                    }
                }
                .listStyle(.sidebar)
            }
            .alert(isPresented: $isShow) {
                Alert(title: Text("숫자가 아닙니다.\n숫자를 입력해주세요."))
            }
        }
    }
    
    private func delete(indexSet: IndexSet) {
        numbers.remove(atOffsets: indexSet)
    }
    
    private func add(_ text: String) {
        if let number = Int(text) {
            numbers.append(number)
        } else {
            isShow.toggle()
        }
        inputText = ""
    }
}

struct AddButton: View {
    
    @Binding var inputText: String
    private let addAction: (() -> Void)
    
    init(inputText: Binding<String>, addAction: @escaping () -> Void) {
        self._inputText = inputText
        self.addAction = addAction
    }
    
    var body: some View {
        HStack {
            TextField("숫자를 입력하세요.", text: $inputText)
            Button {
                addAction()
            } label: {
                Image(systemName: "plus")
            }
        }
        .padding(16)
    }
}


struct Polygon: Shape {
    var sides: Int

    func path(in rect: CGRect) -> Path {
        var path = Path()

        let center = CGPoint(x: rect.width / 2, y: rect.height / 2)
        let radius = min(rect.width, rect.height) / 2

        for side in 0..<sides {
            let angle = CGFloat(Double(side) * 2 * .pi / Double(sides)) - (.pi / 2)
            let point = CGPoint(x: center.x + CGFloat(cos(angle) * radius),
                                y: center.y + CGFloat(sin(angle) * radius))
            if side == 0 {
                path.move(to: point)
            } else {
                path.addLine(to: point)
            }
        }

        path.closeSubpath()
        return path
    }
}

struct PolygonView: View {

    @State var showActionSheet: Bool = false
    @State var isBlack: Bool = true
    @State var isBold: Bool = false
    @State var showPopover: Bool = false

    private let num: Int
    
    init(_ num: Int) {
        self.num = num
    }
    
    var body: some View {
        VStack {
            ScrollView {
                LazyVStack {
                    ForEach(0..<num, id: \.self) { _ in
                        Polygon(sides: num)
                            .aspectRatio(contentMode: .fit)
                            .foregroundColor(isBlack ? .black : .red)
                    }
                }
            }
            
            Text("\(num)")
                .bold(isBold)
                .contextMenu {
                    Button(action: {
                        showPopover.toggle()
                    }) {
                        Label("단일보기", systemImage: "1.circle")
                    }
                    Button(action: {
                        isBold.toggle()
                    }) {
                        Label("Bold", systemImage: "bold")
                    }
                }
        }
        .toolbar {
            Button {
                showActionSheet.toggle()
            } label: {
                Image(systemName: "paintpalette")
            }
        }
        .sheet(isPresented: $showPopover) {
            Polygon(sides: num)
                .aspectRatio(contentMode: .fit)
                .foregroundColor(isBlack ? .black : .red)
        }
        .actionSheet(isPresented: $showActionSheet) {
            ActionSheet(
                title: Text("색상 변경"),
                buttons: [
                    .default(Text("Black")) {
                        isBlack = true
                    },
                    .default(Text("Red")) {
                        isBlack = false
                    },
                    .cancel()
                ]
            )
        }
    }
}
```

## 실행 예시
![Simulator Screen Recording - iPhone 14 Pro - 2023-12-21 at 21 35 58](https://github.com/KTRJB/SwiftUI_Study/assets/102353787/2d8d4790-9c64-43e9-acb7-789c1de086a2)


