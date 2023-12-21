<img width="220" alt="스크린샷 2023-12-21 오후 9 42 04" src="https://github.com/KTRJB/SwiftUI_Study/assets/98514397/7e39a23a-4ffc-4f11-9cb1-9b2a0bcbce63">


```swift
//
//  21~30.swift
//  SwiftUI-Playgrounds
//
//  Created by 이예은 on 12/21/23.
//

import SwiftUI

struct StarBucksOrderView: View {
    @ObservedObject private var viewModel: StarBucksViewModel
    @State var showAlert: Bool = false
    @State var showSheet: Bool = false
    @State var menu: String = ""
    
    init(viewModel: StarBucksViewModel) {
        self.viewModel = viewModel
    }
    
    var body: some View {
        NavigationView {
            ScrollView {
                TextField("주문할 메뉴를 입력하세요", text: $menu)
                    .padding()
                    .background(Color(uiColor: .secondarySystemBackground))
                
                Button("주문하기 🎄") {
                    showAlert = true
                }
                .foregroundStyle(.green)
                .alert("주문 완료!", isPresented: $showAlert) {}
                
                Button("눌러보세요 🎅") {
                    showSheet = true
                }
                .foregroundStyle(.pink)
                .sheet(isPresented: $showSheet) {
                    PathView()
                        
                }
                
                LazyVStack {
                    ForEach(Beverage.allCases, id: \.self) { beverage in
                            VStack(alignment: .leading) {
                                Image(beverage.imageName)
                                    .resizable()
                                    .frame(width: 300, height: 200)
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
                    }
                .navigationTitle("Starbucks Menu")
            }
        }
    }
}

#Preview {
    StarBucksOrderView(viewModel: StarBucksViewModel())
}

struct PathView: View {
    var body: some View {
        VStack {
            Path { path in
                path.move(to: CGPoint(x: 200, y: 100))
                path.addLine(to: CGPoint(x: 100, y: 0))
                path.addLine(to: CGPoint(x: 0, y: 100))
                path.addLine(to: CGPoint(x: 200, y: 200))
                path.addLine(to: CGPoint(x: 400, y: 100))
                path.addLine(to: CGPoint(x: 300, y: 0))
                path.addLine(to: CGPoint(x: 200, y: 100))
                path.closeSubpath()
            }
            .fill(.pink)
            .stroke(Color.pink, style: StrokeStyle(lineWidth: 20, lineCap: .round, lineJoin: .round))
            .padding(.leading, 100)
            
            Text("메리 크리스마스다옹~")
                .font(.title)
                .foregroundStyle(.red)
            Image("santa")
                .frame(height: 400)
        }
    }
}
```
