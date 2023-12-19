## Code

```swift
//
//  Week2View.swift
//  SwiftUIStudy
//
//  Created by 전민수 on 12/12/23.
//

import SwiftUI

// 앱 설정(UI 테마 색상)을 관리하는 class
// ObservableObject: 변경 사항 감지 및 UI 업데이트 가능
class AppSettings: ObservableObject {
    // @Published - 변경 감지가 필요한 속성(주로 참조 객체에 <=> @State)
    @Published var themeColor: Color = .orange
}

// 앱의 현재 상태를 나타내는 enum
enum AppState {
    case active
    case inactive
}

// 아이템 목록을 관리하는 클래스
class ItemViewModel: ObservableObject {
    // 아이템 목록
    // @State로 설정시 내부(private)에서밖에 관리를 못해 기각
    @Published var items: [String] = []

    // 아이템 추가 기능(상태 업데이트)
    func addItem() {
        items.append("아이템 \(items.count + 1)")
    }
}

// 메인 뷰
struct Week2View: View {

    // MARK: - Properties

    // @StateObject: 뷰의 상태를 관리, 뷰의 수명 주기와 연결
    // Week2View가 처음 로드될 때 생성되고, 뷰가 존재하는 동안 유지됨을 의미
    @StateObject var viewModel = ItemViewModel()

    // @EnvironmentObject: 상위 뷰나 앱의 다른 부분에서 제공된 객체에 접근합
    // 따라서, AppSettings 객체는 Week2View의 상위 뷰나 SwiftUI 환경에서 제공 필요
    @EnvironmentObject var settings: AppSettings

    // @Environment: 시스템의 environment value를 가져오는 데 사용
    // 시스템의 라이트 모드, 다크 모드를 가져오기 위해 사용
    @Environment(\.colorScheme) var colorScheme

    // 앱의 현재 상태
    let appState: AppState

    // MARK: - Initializers

    // 이니셜라이저를 통한 상태 초기화
    init(state: AppState) {
        appState = state
    }

    // MARK: - Subviews

    var body: some View {
        ZStack {
            // 삼항연산자를 통한 배경색 설정
            // 라이트 모드일 때는 파란색, 다크 모드일 때는 빨간색
            colorScheme == .light ? Color.blue : Color.red

            // subview 추출을 통한 코드 간결화
            VStack {
                headerView
                // viewModel 및 EnvironmentObject 주입
                ItemsGridView(viewModel: viewModel)
                    .environmentObject(settings)
                addItemButton
            }
        }
    }

    // 헤더뷰
    var headerView: some View {
        Text("아이템을 추가해보자!")
            .padding()
            .font(.largeTitle)
            .fontWeight(.heavy)
    }

    // 아이템 추가 버튼
    var addItemButton: some View {
        Button("아이템 추가") {
            // state에 따른 분기문
            // 활성화 상태면, 아이템 추가
            if appState == .active {
                viewModel.addItem()
            }
        }
        .padding()
        .background(Color.green)
        .foregroundColor(.white)
        .fontWeight(.bold)
        .cornerRadius(15)
    }
}

// 아이템 그리드 뷰
// 헤더뷰, 아이템 추가 버튼 처럼 추출해도 되고
// 이렇게 구조체로 따로 빼도 괜찮아서 해봄
struct ItemsGridView: View {
    // @EnvironmentObject를 통해 AppSettings 객체에 접근
    @EnvironmentObject var settings: AppSettings
    // @ObservedObject를 통해 상위 뷰에서 전달받은 viewModel에 접근
    @ObservedObject var viewModel: ItemViewModel

    var body: some View {
        // 수직 스크롤뷰
        // 스크롤 인디케이터 삭제
        ScrollView(.vertical, showsIndicators: false) {
            gridContent
        }
        .frame(maxHeight: 500)
        .padding()
        .background(Color.yellow)
    }

    var gridContent: some View {
        // 그리드(LazyVGrid)
        // 2열 GridItem
        // .flexible()을 통한 유동적인 폭 사용
        LazyVGrid(
            columns: [GridItem(.flexible()), GridItem(.flexible())],
            spacing: 20
        ) {
            // 반복문(ForEach)
            // 반복가능한 객체(data): 뷰모델의 아이템 배열
            // 식별자(id): 각 아이템 문자열("아이템 \(items.count + 1)")
            // 내용물(content): 세팅의 테마 색상을 배경색으로 가진 아이템 텍스트
            ForEach(viewModel.items, id: \.self) { item in
                Text(item)
                    .frame(
                        minWidth: 0,
                        maxWidth: .infinity,
                        minHeight: 50
                    )
                    .background(settings.themeColor)
                    .foregroundColor(.white)
                    .cornerRadius(10)
            }
        }
    }
}


// MARK: - Previews

struct Week2View_Previews: PreviewProvider {
    static var previews: some View {
        Week2View(state: .active)
            .environmentObject(AppSettings())
    }
}

```

## GIF

![SwiftUI_Week2](https://github.com/KTRJB/SwiftUI_Study/assets/99063327/b6d2de02-c6b8-482f-aa58-21406664eb42)
