```import SwiftUI

struct Week3DayView: View {
    @State var isRatate: Bool = false
    @State var members: [String] = ["글우트", "예톤", "블애드", "주다이", "쑥웅"]
    @State var text: String = "남은 멤버 : 글우트, 예톤, 블애드, 주다이, 쑥웅"
    @State var input = ""
    
    var body: some View {
        NavigationView {
            VStack {
                PathView(isRatate: $isRatate, text: $text)
                
                Spacer()
                
                List {
                    ForEach(members, id: \.self) { member in
                        Text(member)
                    }
                    .onDelete { indexSet in
                        members.remove(atOffsets: indexSet)
                        text = "남은 멤버 : \(members.joined(separator: ","))"
                    }
                }
                
                TextField(.init(input), text: $input)
                .background(.brown)
                .padding()
                
                Spacer()
                
                Button(action: {
                    isRatate.toggle()
                }, label: {
                    Text("회전하기")
                        .contextMenu {
                            Text("이것은 회전한다.")
                        }
                })
            }
            .navigationBarTitle("Grocery List")
            .navigationBarItems(
                           leading: EditButton(),
                           trailing: addButton // 항목 추가 버튼 뷰
                       )
        }
        
    }
    
    var addButton: some View {
            Button("Add") {
                add()
            }
        }
    
    func add() {
        members.append(input)
        }
}

struct PathView: View {
    @Binding var isRatate: Bool
    @Binding var text: String
    
    private var foreverAnimation: Animation {
        Animation.linear(duration: 1.0)
          .repeatForever(autoreverses: false)
      }
    
    var body: some View {
        Path { path in
            // 1. 커서 이동
            path.move(to: CGPoint(x: 300, y: 100))
            // 2.
            path.addLine(to: CGPoint(x: 100, y: 300))
//            // 3.
            path.addLine(to: CGPoint(x: 300, y: 300))
//            // 4.
            path.addLine(to: CGPoint(x: 80, y: 180))
            
            path.addLine(to: CGPoint(x: 200, y: 330))
            
            path.addLine(to: CGPoint(x: 300, y: 100))
            // 5.
            
//            path.closeSubpath()
        }
        .stroke(Color.blue, style: StrokeStyle(lineWidth: 20, lineCap: .round, lineJoin: .round))
        .rotationEffect(
            Angle(
                degrees: self.isRatate ? 360 : 0
            )
        )
        .animation(self.isRatate ? foreverAnimation : .default)
        .overlay {
            Text(text)
                .background(.yellow)
        }
    }
}

#Preview {
    Week3DayView()
}
```

![Simulator Screenshot - iPhone 15 Pro - 2024-01-14 at 21 21 55](https://github.com/KTRJB/SwiftUI_Study/assets/96932116/a2e7fbd6-6bf0-4cea-8ba2-81d5e8aaff0e)
