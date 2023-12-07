```swift
//
//  WeekOndayView.swift
//  SwiftUIStudy
//
//  Created by Groot on 2023/12/05.
//

import SwiftUI

struct WeekOneDayView: View {
    @State private var isRotating = false // 회전 상태를 나타내는 상태 변수
    
    var body: some View {
        VStack {
            Text("회전")
                .font(.title)
                .bold()
            
            GeometryReader { geometry in
                let centerX = geometry.size.width / 2
                let centerY = geometry.size.height / 2
                
                Circle()
                    .strokeBorder(Color.yellow, style: .init(lineWidth: 20, lineCap: .butt, dash: [10]))
                    .background(
                        Circle()
                            .foregroundColor(Color.cyan)
                    )
                    .overlay {
                        Path { path in
                            path.move(
                                to: .init(
                                    x: 20,
                                    y: centerY
                                )
                            )
                            path.addLine(to: .init(x: geometry.size.width - 20, y: centerY))
                        }
                        .stroke(.red, lineWidth: 10)
                        
                        Path { path in
                            path.move(
                                to: .init(
                                    x: centerX,
                                    y: geometry.size.width / 2 - 20
                                )
                            )
                            path.addLine(to: .init(x: centerX, y: geometry.size.width / (3/4)))
                        }
                        .stroke(.red, lineWidth: 10)
                    }
                    .rotationEffect(isRotating ? .degrees(360) : .degrees(0)) // 회전 효과 적용
                    .animation(.linear(duration: 1).repeatForever(autoreverses: false), value: isRotating) // 회전 애니메이션 적용
            }
            
            Spacer()
            
            Button (action: {
                withAnimation {
                    // 버튼을 누를 때마다 회전 상태 토글
                    isRotating.toggle()
                }
            }) {
                Text(isRotating ? "Stop" : "Rotate")
            }
            .frame(maxWidth: .infinity, maxHeight: 100)
            .background(.green)
            .padding()
        }
    }
}

struct WeekOndayView_Previews: PreviewProvider {
    static var previews: some View {
        WeekOneDayView()
    }
}

```
![Simulator Screen Recording - iPhone 14 Plus - 2023-12-07 at 22 02 17](https://github.com/KTRJB/SwiftUI_Study/assets/96932116/6fd68715-b474-48bf-b927-9bf0c5711381)

