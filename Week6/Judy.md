## 코드
```swift
struct Sixth: View {

    @StateObject var model = RandomImageModel()
    
    var body: some View {
        TabView {
            ListView(model: model)
                .tabItem {
                    Image(systemName: "list.bullet")
                    Text("리스트")
                }
                .badge(model.badgeCount)
                .onAppear { model.badgeCount = 0 }
                .tag(0)
            
            AddImageView(model: model)
                .tabItem {
                    Image(systemName: "plus.app")
                    Text("추가")
                }
                .tag(1)
        }
    }
}

struct ListView: View {
    @ObservedObject var model: RandomImageModel
    
    var body: some View {
        if model.imageList.isEmpty {
            AsyncImage(url: URL(string: "https://picsum.photos/400")) { image in
                image
                    .resizable()
                    .aspectRatio(contentMode: .fill)
                    .onAppear {
                        model.imageList.append(image)
                    }
            } placeholder: {
                ProgressView()
            }
        } else {
            List {
                ForEach(model.imageList, id: \.id) { image in
                    image
                        .swipeActions(edge: .trailing, allowsFullSwipe: false) {
                            Button("Delete") {
                                model.imageList.removeAll(where: { image == $0 })
                            }
                            .tint(.red)
                            Button("MoveToFirst") {
                                model.imageList.removeAll(where: { image == $0 })
                                model.imageList.insert(image, at: 0)
                            }
                            .tint(.yellow)
                        }
                }
            }
        }
    }
}

struct AddImageView: View {
    @ObservedObject var model: RandomImageModel
    @State private var url: URL? = URL(string: "https://picsum.photos/400")
    @State private var selectedImage: Image?
    
    var body: some View {
        VStack {
            Text("사진")
                .bold()
                .textSelection(.enabled)
            
            Spacer()
            
            AsyncImage(url: url) { image in
                image
                    .resizable()
                    .scaledToFit()
                    .onAppear {
                        selectedImage = image
                    }
            } placeholder: {
                Color.gray
                    .scaledToFit()
            }
            
            Spacer()
            
            Button("추가") {
                url = nil
                model.imageList.append(selectedImage!)
                model.badgeCount += 1
                HapticManager.shared.notification(type: .success)
                DispatchQueue.main.asyncAfter(deadline: .now() + 0.01) {
                    url = URL(string: "https://picsum.photos/400")
                }
            }
            .controlSize(.large)
            .buttonStyle(.bordered)
            .buttonBorderShape(.roundedRectangle(radius: 15))
            .foregroundColor(.green)
            
        }
        .padding(.vertical, 16)
        .background(.ultraThickMaterial)
    }
}

class RandomImageModel: ObservableObject { // 과일 데이터 업데이트 사항들은 이곳에 전부 몰아넣기
    @Published var imageList: [Image] = []
    @Published var badgeCount: Int = 0
}

extension Image: Identifiable {
    public var id: UUID {
        UUID()
    }
}


class HapticManager {
    
    static let shared: HapticManager = HapticManager() // 싱글톤

    func notification(type: UINotificationFeedbackGenerator.FeedbackType) {
        let generator = UINotificationFeedbackGenerator()
        generator.notificationOccurred(type) //진동
    }
    
    func impact(style: UIImpactFeedbackGenerator.FeedbackStyle) {
        let generator = UIImpactFeedbackGenerator(style: style)
        generator.impactOccurred() // 진동
    }
}
```

## 예시
![Simulator Screen Recording - iPhone 14 Pro - 2024-01-19 at 19 13 28](https://github.com/KTRJB/SwiftUI_Study/assets/102353787/8183a11e-7253-4ef4-b6d1-a8f51d72657e)
