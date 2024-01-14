<img width="365" alt="스크린샷 2024-01-14 오후 9 21 42" src="https://github.com/KTRJB/SwiftUI_Study/assets/98514397/1a478a78-737d-4119-a0eb-8f1eba5d32af">

```swift
//✅ Date Picker
//✅ Slider
//✅ Stepper
//✅ If let guard let
//✅ OnAppear
//✅ OnTapGesture
//✅ TabView
//✅ DarkMode
//Markups & Documentation
//✅ @AppStorage

struct HomeView: View {
  @State var selectedDate : Date = Date()
  // Date() - 자동으로 현재 날짜를 불러와주는 함수
  @State var sliderValue: Double = 10
  @State var stepperValue : Int = 10
  @State var widthIncrement: CGFloat = 0
  @State var isLoading: Bool = false
  @State var count: Int = 0
  @State var roundRectangleSelect: Bool = false
  @State var rectangleColor: Color = .black
  @Environment(\.colorScheme) var colorScheme
  // SwiftUI에서 기본으로 제공하는 환경변수
  @State var name = ""
  @AppStorage("userName") var userName: String = ""

  var body: some View {
    NavigationView {
      ScrollView {
        VStack(alignment: .leading) {
          VStack(alignment: .leading) {
            HStack {
              TextField("이름을 입력하세요", text: $name)
                .padding()
                .background(Color(uiColor: .secondarySystemBackground))

              Button(action: {
                // ✅ @AppStorage
                userName = name
              }, label: {
                Text("저장")
              })
            }

            Text("내이름: \(userName)")
          }
          .padding()
          .border(.green, width: 2)

          Spacer()

          // ✅ Date Picker, DarkMode
          DatePicker("DatePicker 날짜를 선택해주세요", selection: $selectedDate)
            .tint(colorScheme == .light ? .green : .orange)
            .padding()
            .border(.pink, width: 2)

          Spacer()

          // ✅ Slider
          VStack {
            Text("Slider 비율: \(String(format: "%.1f", sliderValue))")
            Slider(value: $sliderValue, in: 0...100)
              .tint(.green)
          }
          .padding()
          .border(.green, width: 2)

          Spacer()

          //  ✅ Stepper, If
          VStack {
            if isLoading {
              RoundedRectangle(cornerRadius: 25.0)
                .foregroundStyle(.pink)
                .frame(width: 100 + widthIncrement, height: 100)
            }

            Stepper("Stepper \(String(format: "%.1f", widthIncrement))") {
              widthIncrement += 10

              if widthIncrement == 50 {
                isLoading = true
              } else {
                isLoading = false
              }
            } onDecrement: {
              widthIncrement -= 10

              if widthIncrement == 50 {
                isLoading = true
              } else {
                isLoading = false
              }
            }
          }
          .padding()
          .border(.pink, width: 2)

          Spacer()

          // ✅ if, onTapGesture
          if roundRectangleSelect {
            Text("\(rectangleColor == .pink ? "핑크색" : "초록색") 상자가 선택됐습니다!")
              .foregroundStyle(rectangleColor)
          }

          // ✅ onAppear
          LazyVStack {
            ForEach(0..<20) { num in
              RoundedRectangle(cornerRadius: 20)
                .frame(height: 50)
                .foregroundStyle(num % 2 == 0 ? .green : .pink)
                .padding()
                .onAppear {
                  count += 1
                }
                .onTapGesture {
                  roundRectangleSelect = true
                  rectangleColor = num % 2 == 0 ? .green : .pink
                }
            }
          }
        }
        .padding()
      }
      .navigationTitle("보이는 상자 개수 \(count)")
    }
  }
}

#Preview {
  HomeView()
}

```

```swift
import SwiftUI

// MARK: - MainTab

enum MainTab: Int, CaseIterable, Identifiable {
  var id: Self {
    return self
  }

  case home
  case cart
  case mypage

  var title: String {
    switch self {
    case .home: return "홈"
    case .cart: return "장바구니"
    case .mypage: return "내 정보"
    }
  }

  var imageName: String {
    switch self {
    case .home: return "house.fill"
    case .cart: return "cart"
    case .mypage: return "info.bubble"
    }
  }
}

extension MainTab {

  // MARK: - ViewBuilder

  @ViewBuilder
  var view: some View {
    switch self {
    case .home: HomeView()
    case .cart: placeholderView
    case .mypage: placeholderView
    }
  }

  private var placeholderView: some View {
    Text("비어있음")
  }
}


// @ViewBuilder는 무엇일까?
// 간단하게 "클로저에서 View를 구성할 수 있다", "해당 클로저가 여러 자식뷰를 제공할 수 있도록 한다."
// HStack, body 또한 @ViewBuilder로 선언됨
// https://zeddios.tistory.com/1324 설명 자세히 나와있듬!
// 제너릭한 뷰 만들기에 좋음.
// (예를들어 백그라운드가 항상 pink인 뷰를 제너릭하게 커스텀해서 사용하고 싶을 때 등)

```

```swift
import SwiftUI

// ✅ TabView
struct MainTabView: View {
  @State private var selectedTabIndex = MainTab.home.rawValue

  var body: some View {
    NavigationView {
      TabView(selection: $selectedTabIndex) {
        ForEach(MainTab.allCases, id: \.self) { tab in
          tab.view
            .tabItem {
              Image(systemName: tab.imageName)
              Text(tab.title)
            }
        }
      }
    }
  }
}
```
