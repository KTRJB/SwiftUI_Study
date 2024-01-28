```swift
//
//  Week7DayView.swift
//  SwiftUIStudy
//
//  Created by Groot on 1/22/24.
//

import SwiftUI

struct RandomModel: Identifiable {
    let id = UUID().uuidString
    let title:String
}

struct Week7DayView: View {
    @State var selectedModel: RandomModel? = nil  //옵셔널 사용
    @State var rating: Int = 3
    @State var angle: Angle = Angle(degrees: 0)
    @State var offset: CGSize = .zero        // 드래그 값을 0으로 초기화
    @State var isComplete: Bool = false
    @State var currentAmount: CGFloat = 0
    
    var body: some View {
        VStack(spacing:20) {
//            ForEach(0..<10) { index in
//                Button("Button \(index)") {
//                    selectedModel = RandomModel(title: "\(index)")
//                }
//            }
            
            ZStack {
                starsView
                    .overlay(overlayView.mask(starsView))
            }
            .rotationEffect(angle) // 실제로 입력된 각도에 따라 rotation 인터랙션을 실행시키는 함수
            .gesture(
                RotationGesture()
                    .onChanged { value in  // 두 손가락으로 돌리면 그 각도를 받아옴
                        angle = value
                    }
                    .onEnded { value in    // 손가락을 떼면 튕기는 애니메이션과 함께 각도값 0을 받아옴
                        withAnimation(.spring()) {
                            angle = Angle(degrees: 0)
                        }
                    }
                    .onEnded { value in     // 손을 뗐을 때, 원래 위치로 이동
                        withAnimation(.spring()) {
                            offset = .zero  // CGSize(width: 0, height: 0)와 같은 말
                        }
                    }
            )
            
            Text(rating.description)
                .offset(offset)
                .gesture(
                    DragGesture()
                        .onChanged({ value in
                            withAnimation {
                                offset = value.translation
                            }
                        })
                )
            
            ScrollView {
                ScrollViewReader { proxy in
                    Button("Click here to go to #49") {
                        withAnimation(.spring()) {
                            // 특정 id값의 위치로 스크롤
                            proxy.scrollTo(49, anchor: .center)
                        }
                    }
                    ForEach(0..<50) { index in
                        Text("This is item #\(index)")
                            .font(.headline)
                            .frame(height: 200)
                            .frame(maxWidth: .infinity)
                            .background(Color.white)
                            .cornerRadius(16)
                            .shadow(radius: 10)
                            .padding()
                            .id(index) // id값 생성
                    }
                }
            }
            
            ScrollView(.horizontal, showsIndicators: false) {
                        HStack {
                            ForEach(0..<20) { index in
                                GeometryReader { geometry in
                                    RoundedRectangle(cornerRadius: 20)
                                        .rotation3DEffect(
                                            // 카드의 현재 위치에 따라 각도를 다르게 조절
                                            Angle(degrees: getPercentage(geo: geometry)) * 20,
                                            axis: (x: 0.0, y: 1.0, z: 0.0))
                                }
                                .frame(width: 300, height: 250)
                                .padding()
                            }
                        }
                    }
            
            Text(isComplete ? "COMPLETED" : "NOT COMPLETE")
                        .padding()
                        .padding(.horizontal)
                        .background(isComplete ? Color.green : Color.gray)
                        // 삼항연산자로 isComplete가 true일때 그린 false일때 gray
                        .cornerRadius(10)


                         // 오랫동안 누르고 있어야 작동하는 제스쳐
                        .onLongPressGesture(minimumDuration: 1.0, maximumDistance: 100) {
                            isComplete.toggle()
                            // isComplete가 true가 되어 위의 background에 변화를 주게됨
                        }
                        .gesture(
                                        MagnificationGesture()
                                            .onChanged { value in // 확대할 때, value의 값이 증가
                                                currentAmount = value - 1
                                                // -1 하는 이유는 -1 하지 않을 경우에 확대시 바로 2배 이상으로 커짐
                                            }
                                    )
        }
        .padding(.top, 15)
        .sheet(item: $selectedModel) { model in
            NextScreen(selectedModel: model)
        }
    }
    
    func getPercentage(geo: GeometryProxy) -> Double {
            // 화면의 중앙 위치
            let maxDistance = UIScreen.main.bounds.width / 2
            // 화면 전체 영역 기준 카드의 현재 중앙 좌표
            let currentX = geo.frame(in: .global).midX
            // 두 위치에 대한 비율 계산
            return Double(1 - (currentX / maxDistance))
        }
    
    private var overlayView: some View {
        GeometryReader{ geometry in
            ZStack(alignment: .leading) {
                Rectangle()
                    .foregroundColor(.yellow)
                    .frame(width: CGFloat(rating)/5*geometry.size.width)
            }
        }.allowsHitTesting(false)    //onTapgesture가 사각형에는 없으므로, 유저가 사각형은 클릭하지 못하도록 해야함.
    }
    
    
    private var starsView: some View {
        HStack{
            ForEach(1..<6) { index in
                Image(systemName: "star.fill")
                    .font(.largeTitle)
                    .foregroundColor(.gray)
                    .onTapGesture {
                        withAnimation(.easeIn) {
                            rating = index
                        }
                    }
            }
        }
    }
}

struct NextScreen: View {
    
    let selectedModel: RandomModel
    
    var body: some View {
        Text(selectedModel.title)
            .font(.largeTitle)
    }
}

#Preview {
    Week7DayView()
}

```
