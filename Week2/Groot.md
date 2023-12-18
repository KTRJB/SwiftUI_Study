## Code

```swift
//
//  WeekTwoView.swift
//  SwiftUIStudy
//
//  Created by Groot on 2023/12/13.
//

import SwiftUI
import ComposableArchitecture

public extension Color {
    static func random(randomOpacity: Bool = false) -> Color {
        Color(
            red: .random(in: 0...1),
            green: .random(in: 0...1),
            blue: .random(in: 0...1),
            opacity: randomOpacity ? .random(in: 0...1) : 1
        )
    }
}

struct WeekTwoView: View {
    let store: StoreOf<WeekTwoViewReducer>
    // 둘의 차이는 없다
    let columns: [GridItem] = [
        GridItem(.flexible(), spacing: 5, alignment: nil),
        GridItem(.flexible(), spacing: 5, alignment: nil),
        GridItem(.flexible(), spacing: 5, alignment: nil)
    ]
    
    var body: some View {
        WithViewStore(self.store, observe: { $0 }) { viewStore in
            VStack {
                Rectangle()
                    .stroke()
                    .overlay {
                        Button {
                            viewStore.send(.chaingeText("전화번호를 입력하세요"))
                        } label: {
                            Text(viewStore.text)
                                .font(.largeTitle)
                                .foregroundColor(.black)
                        }
                        .background(Color.random())
                    }
                
                Spacer()
                
                RandomView()
                RandomView()
                RandomView()
                
                LazyVGrid(columns: columns) {
                    ForEach(1..<10) { index in
                        PhoneButton(
                            store: store.scope(
                                state: \.touchedNumber,
                                action: WeekTwoViewReducer.Action.touchedNumber
                            ),
                            number: index
                        )
                    }
                }
                .padding()
            }
        }
    }
}

struct WeekTwoView_Previews: PreviewProvider {
    static var previews: some View {
        WeekTwoView(store: .init(initialState: .init(
            touchedNumber: .init(touchedNumber: 0)),
                                 reducer: {
            WeekTwoViewReducer()
        }))
    }
}

struct PhoneButton: View {
    let store: StoreOf<NumberButtonReducer>
    let number: Int
    
    var body: some View {
        WithViewStore(self.store, observe: { $0 }) { viewStore in
            Button(action: {
                viewStore.send(.touchedNumber(number))
            }, label: {
                Text(number.description)
                    .font(.title)
                    .bold()
                    .foregroundColor(.black)
                    .padding()
                    .padding(.horizontal, 10)
                    .background(
                        Circle()
                            .stroke(lineWidth: 4)
                            .foregroundColor(.gray)
                    )
            })
            .background(Color.random())
            .padding()
        }
    }
}

struct NumberButtonReducer: Reducer {
    struct State: Equatable {
        var touchedNumber: Int = 0
    }
    
    @frozen
    enum Action: Equatable {
        case touchedNumber(Int)
    }
    
    func reduce(into state: inout State, action: Action) -> Effect<Action> {
        switch action {
        case .touchedNumber(let number):
            state.touchedNumber = number
            return .none
        }
    }
}

final class WeekTwoViewReducer: Reducer {
    static let placeholderText = "전화번호를 입력하세요"
    
    struct State: Equatable {
        var text: String = WeekTwoViewReducer.placeholderText
        var touchedNumber: NumberButtonReducer.State
    }
    
    @frozen
    enum Action: Equatable {
        case chaingeText(String)
        case touchedNumber(NumberButtonReducer.Action)
    }
    
    func reduce(into state: inout State, action: Action) -> Effect<Action> {
        switch action {
        case .chaingeText(let text):
            state.text = text
            return .none
        case .touchedNumber(let action):
            switch action {
            case .touchedNumber(let number):
                if state.text == WeekTwoViewReducer.placeholderText {
                    state.text = number.description
                } else if state.text.count < 12 {
                    state.text += number.description
                } else {
                    state.text = number.description
                }
            }
            return .none
        }
    }
}

struct RandomView: View {
    var body: some View {
        Text("진짜 아님")
            .background(Color.random())
    }
}

```

## GIF

![Simulator Screen Recording - iPhone 15 Pro - 2023-12-18 at 21 57 25](https://github.com/KTRJB/SwiftUI_Study/assets/96932116/44f62b46-7c52-4793-b23a-864d253d610a)
