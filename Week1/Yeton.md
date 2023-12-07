```
//
//  ContentView.swift
//  SwiftUI-Playgrounds
//
//  Created by 이예은 on 12/7/23.
//

import SwiftUI

struct ContentView: View {
    var body: some View {
        VStack(spacing: 10) {
            ScrollView {
                Text("Hello, World!Hello, World!Hello, World!Hello, World!")
                    .multilineTextAlignment(.leading)
                    .lineSpacing(8) // 행간 조정
                    .kerning(8) // 자간 조정
                
                Circle()
//                                    .trim(from: 0.5, to: 1.0)
                    .stroke(.orange, style: StrokeStyle(lineWidth: 20, lineCap: .butt, dash: [10]))
                
                Capsule(style: .continuous)
                    .frame(width: 300, height: 200)
                
                RoundedRectangle(cornerRadius: 12)
                    .frame(width: 300, height: 200)
                    .shadow(color: .blue, radius: 16, x: 8, y: 8)
                
                Image(systemName: "heart.fill")
                    .resizable()
                    .frame(width: 200, height: 200)
                
                Text("Hello, World!")
                    .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .center) // 둘의 위치를 바꾸면 frame에 color가 적용됨
                    .background(Color.green)
                
                Text("Hello, World!")
                    .frame(width: 100, height: 100, alignment: .center)
                    .background(LinearGradient(colors: [.blue, .red], startPoint: .leading, endPoint: .trailing))
                
                ZStack (alignment: .bottom) {
                    // 처음 넣은 것부터 아래로 들어가면서 하나씩 쌓임
                    Circle()
                        .fill(Color.red)
                        .frame(width: 150, height: 150)
                    Circle()
                        .fill(Color.green)
                        .frame(width: 100, height: 100)
                    Circle()
                        .fill(Color.blue)
                        .frame(width: 50, height: 50)
                }
                
                Text("Hello World!")
//                    .background(Color.yellow)
                    .padding([.vertical, .leading], 20)
                    .background(Color.yellow) // color는 뒤에 있어야 앞에꺼가 전체적으로 다 ㅏ적용됨
            }
        }
        .frame(maxWidth: .infinity)
    }
}

#Preview {
    ContentView()
}


```
<img width="218" alt="스크린샷 2023-12-07 오후 10 03 04" src="https://github.com/KTRJB/SwiftUI_Study/assets/98514397/1e1d0e1b-1706-4319-88b1-a511c1b4c482">
