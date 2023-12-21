
<img width="220" alt="스크린샷 2023-12-21 오후 9 40 28" src="https://github.com/KTRJB/SwiftUI_Study/assets/98514397/0c9459bd-2867-4a4d-bd02-389fdae9d780">

```swift
//
//  StarBucks.swift
//  SwiftUI-Playgrounds
//
//  Created by 이예은 on 12/15/23.
//

import SwiftUI

enum Beverage: CaseIterable {
    case refresher, coldBrew, blonde, espresso, decafCoffee, frappuccino, caffemocha, jejuMalcha, jejuCappuccino
}

extension Beverage {
    var title: String {
        switch self {
        case .blonde:
            return "blonde"
        case .coldBrew:
            return "coldBrew"
        case .refresher:
            return "refresher"
        case .espresso:
            return "espresso"
        case .decafCoffee:<img width="220" alt="스크린샷 2023-12-21 오후 9 40 28" src="https://github.com/KTRJB/SwiftUI_Study/assets/98514397/8032c08e-d285-4280-aa3c-0af5ed799561">

            return "decafCoffee"
        case .frappuccino:
            return "frappuccino"
        case .caffemocha:
            return "caffemocha"
        case .jejuMalcha:
            return "jejuMalcha"
        case .jejuCappuccino:
            return "jejuCappuccino"
        }
    }
    
    var imageName: String {
        switch self {
        case .blonde:
            return "blonde"
        case .coldBrew:
            return "coldBrew"
        case .refresher:
            return "refresher"
        case .espresso:
            return "espresso"
        case .decafCoffee:
            return "decafCoffee"
        case .frappuccino:
            return "frappuccino"
        case .caffemocha:
            return "caffeMocha"
        case .jejuMalcha:
            return "jejuMalcha"
        case .jejuCappuccino:
            return "jejuCappuccino"
        }
    }
    
    var price: String {
        switch self {
        case .blonde:
            return "6500"
        case .coldBrew:
            return "5000"
        case .refresher:
            return "6000"
        case .espresso:
            return "4000"
        case .decafCoffee:
            return "4300"
        case .frappuccino:
            return "6500"
        case .caffemocha:
            return "5000"
        case .jejuMalcha:
            return "6700"
        case .jejuCappuccino:
            return "7800"
        }
    }
}


class StarBucksViewModel: ObservableObject {
    @Published var selectedItem: Beverage = .blonde
    
    func itemSelected(item: Beverage) async {
        selectedItem = item
    }
}

struct StarBucksView: View {
    let columns: [GridItem] = Array(repeating: .init(.flexible()), count: 2)
    
    @Environment(\.captionBackgroundColor) var backgroundColor
    
    @ObservedObject private var viewModel: StarBucksViewModel
    // viewModel = StarBucksViewModel() 와 같이 만들어줄 땐
    // @StateObject을 사용
    // 실제 객체를 만들때는 뷰가 그려지는 것과 별개로 단일한 인스턴스를 만들어주기 위해
    
    init(viewModel: StarBucksViewModel) {
        self.viewModel = viewModel
    }
    
    var body: some View {
        NavigationView {
            ScrollView {
                LazyVGrid(columns: columns) {
                    ForEach(Beverage.allCases, id: \.self) { beverage in
                        VStack(alignment: .leading) {
                            Image(beverage.imageName)
                                .resizable()
                                .frame(height: 150)
                            Text(beverage.title)
                            HStack(spacing: 1) {
                                Text(beverage.price)
                                Text("원")
                            }
                        }
                        .onTapGesture {
                            Task {
                                await viewModel.itemSelected(item: beverage)
                            }
                        }
                    }
                }
                .padding()
                
                ZStack {
                    RoundedRectangle(cornerRadius: 10)
                        .fill(Color.green)
                        .frame(width: 200, height: 80)
                        .padding()
                    
                    Text(viewModel.selectedItem.title)
                        .font(.largeTitle)
                        .foregroundStyle(viewModel.selectedItem == .espresso ? .black : .pink)
                        .background(backgroundColor)
                }
            }
            .navigationTitle("Starbucks Menu")
        }
    }
}


private struct CaptionColorKey: EnvironmentKey {
    static let defaultValue = Color(.yellow)
}

extension EnvironmentValues {
    var captionBackgroundColor: Color {
        get { self[CaptionColorKey.self] }
        set { self[CaptionColorKey.self] = newValue }
    }
}


#Preview {
    StarBucksView(viewModel: StarBucksViewModel())
}

```
