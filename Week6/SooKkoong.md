```swift
//
//  Week6View.swift
//  SwiftUIStudy
//
//  Created by 전민수 on 1/16/24.
//

import SwiftUI

struct Week6View: View {
    var body: some View {
        // UserSettings를 environmentObject로 전달
        NewsListView()
            .environmentObject(UserSettings())
    }
}

// 뉴스 카테고리 열거형
enum NewsCategory: CaseIterable {
    case technology
    case entertainment
    case business
    case weather

    // 각 카테고리의 이름을 얻기 위한 계산 프로퍼티
    var displayName: String {
        switch self {
        case .technology:
            "Technology"
        case .entertainment:
            "Entertainment"
        case .business:
            "Business"
        case .weather:
            "Weather"
        }
    }
}

// 사용자 설정을 추적하기 위해 ObservableObject 클래스(UserSettings) 정의
class UserSettings: ObservableObject {
    // class에서의 상태 변화 감지를 위해 @Published
    // 프로퍼티 변경시 자동으로 뷰 업데이트
    // 사용자가 선호하는 뉴스 카테고리 저장
    @Published var preferredCategories: Set<NewsCategory> = []
}

// 뉴스 구성요소 모델
struct NewsItem: Identifiable {
    var id: String
    var title: String
    var summary: String
    var imageUrl: URL?
    var category: NewsCategory
    var isNew: Bool

    // 미리 설정한 더미 기사 반환 함수
    static func showDummy() -> [NewsItem] {
        [
            NewsItem(
                id: "1",
                title: "Tech News",
                summary: "Latest technology news...",
                imageUrl: URL(string: "https://example.com/image1.jpg"),
                category: .technology,
                isNew: false
            ),
            NewsItem(
                id: "2",
                title: "Business News",
                summary: "Today's business news...",
                imageUrl: URL(string: "https://example.com/image2.jpg"),
                category: .business,
                isNew: false
            ),
            NewsItem(
                id: "3",
                title: "Entertainment News",
                summary: "Shoking entertainment news...",
                imageUrl: URL(string: "https://example.com/image3.jpg"),
                category: .entertainment,
                isNew: false
            ),
            NewsItem(
                id: "4",
                title: "Weather News",
                summary: "Here's weather forecast...",
                imageUrl: URL(string: "https://example.com/image4.jpg"),
                category: .weather,
                isNew: false
            )
        ]
    }
}

// 뉴스에 대한 뷰 모델을 ObservableObject로 정의
class NewsViewModel: ObservableObject {
    @Published var filteredNewsItems: [NewsItem] = []
    var userSettings: UserSettings?

    // 전체 튜스 아이템을 더미 뉴스 아이템으로 초기화
    var allNewsItems = NewsItem.showDummy() {
        didSet {
            // allNewsItems가 업데이트되면 filteredNewsItems를 다시 계산
            loadNews()
        }
    }

    init() {
        loadNews()
    }

    // 뉴스 데이터 로드 메서드
    func loadNews() {
        // userSetting에 따른 필터링 제공
        // 선호 카테고리 없을시 모든 뉴스 아이템 표시
        filteredNewsItems = allNewsItems.filter { newsItem in
            guard let userSettings = userSettings else { return true }
            return userSettings.preferredCategories.isEmpty || userSettings.preferredCategories.contains(newsItem.category)
        }
    }
}

// 뉴스 목록 표시 뷰
struct NewsListView: View {
    @EnvironmentObject var userSettings: UserSettings
    @StateObject var viewModel = NewsViewModel()

    var body: some View {
        NavigationView {
            VStack {
                // 카테고리 선택 버튼을 위한 스크롤 뷰
                ScrollView(.horizontal, showsIndicators: false) {
                    HStack {
                        // 각 뉴스 카테고리에 대해 버튼을 생성
                        ForEach(NewsCategory.allCases, id: \.self) { category in
                            Button(category.displayName) {
                                // 탭할 때 사용자 설정에서 카테고리를 토글
                                if userSettings.preferredCategories.contains(category) {
                                    userSettings.preferredCategories.remove(category)
                                } else {
                                    userSettings.preferredCategories.insert(category)
                                }
                                // 새로운 선호도에 따라 뉴스를 다시 로드
                                viewModel.loadNews()
                            }
                            .buttonStyle(.bordered) // 버튼 스타일 적용
                            .tint(userSettings.preferredCategories.contains(category) ? .blue : .gray)
                        }
                    }
                }

                // 필터링된 뉴스 아이템 리스트
                List(viewModel.filteredNewsItems) { item in
                    NewsItemView(newsItem: item)
                        .swipeActions {
                            Button("저장") {
                                print("저장되었습니다..!")
                            }
                            .tint(.green)

                            Button("숨기기") {
                                print("숨겼습니다..!")
                            }
                            .tint(.red)
                        }
                        .badge(item.isNew ? "New" : nil) // list badge 적용
                }
            }
            .navigationTitle("News Feed")
            .toolbar {
                // + 버튼을 눌렀을 때 새로운 뉴스 아이템을 추가
                Button(action: {
                    // 랜덤 카테고리 선택
                    let randomCategory = NewsCategory.allCases.randomElement() ?? .technology
                    // 새로운 뉴스 아이템 생성
                    let newNewsItem = NewsItem(
                        id: UUID().uuidString,
                        title: "New News",
                        summary: "This is a new news item...",
                        imageUrl: URL(string: "https://example.com/new-image.jpg"),
                        category: randomCategory,
                        isNew: true // isNew를 true로 설정
                    )
                    // 생성된 뉴스 아이템을 추가
                    viewModel.allNewsItems.append(newNewsItem)
                }) {
                    Label("Add News", systemImage: "plus")
                }
            }
            .onAppear {
                // 뷰가 나타날 때 userSettings를 뷰 모델에 할당
                viewModel.userSettings = userSettings
            }
        }
    }
}

// 뉴스 아이템 표시 뷰
struct NewsItemView: View {
    var newsItem: NewsItem
    let impactMed = UIImpactFeedbackGenerator(style: .medium) // 진동 기능
    let notification = UINotificationFeedbackGenerator() // 진동 기능

    var body: some View {
        // 수이미지와 텍스트 컨텐츠를 수평으로 배치
        HStack(alignment: .center, spacing: 16) {
            // 뉴스 아이템의 이미지 비동기로 가져옴
            AsyncImage(url: newsItem.imageUrl) { phase in
                switch phase {
                case .empty:
                    ProgressView() // 이미지 로딩 중
                case .success(let image):
                    image.resizable().aspectRatio(contentMode: .fill) // 이미지 로드 성공
                case .failure(_):
                    Image(systemName: "photo").imageScale(.large) // 이미지 로드 실패
                @unknown default:
                    EmptyView() // 예상치 못한 상황 처리
                }
            }
            .frame(width: 100, height: 100)
            .cornerRadius(8)
            .shadow(radius: 4)

            // 뉴스 아이템 텍스트 및 버튼
            VStack(alignment: .leading, spacing: 8) {
                Text(newsItem.title)
                    .font(.headline)
                    .fontWeight(.bold)
                    .foregroundColor(.primary)
                    .lineLimit(2)
                    .textSelection(.enabled) // 텍스트 선택 활성화

                Text(newsItem.summary)
                    .font(.subheadline)
                    .foregroundColor(.secondary)
                    .lineLimit(3)
                    .textSelection(.enabled) // 텍스트 선택 활성화

                // 버튼 그룹
                HStack(spacing: 12) {
                    Button("Read") {
                        impactMed.impactOccurred() // 읽기 버튼 햅틱
                    }
                    .buttonStyle(.bordered) // 버튼 스타일
                    .controlSize(.regular)

                    Button("Share") {
                        notification.notificationOccurred(.success) // 공유 버튼 햅틱 피드백
                    }
                    .buttonStyle(.borderedProminent) // 버튼 스타일
                    .controlSize(.regular)
                }
            }
        }
        .padding()
        .frame(maxWidth: .infinity, alignment: .center) // 전체 너비 차지 및 중앙 정렬
        .background(.ultraThinMaterial, in: RoundedRectangle(cornerRadius: 10)) // background material 사용
        .shadow(color: .gray.opacity(0.4), radius: 5, x: 0, y: 2)
    }
}
```
