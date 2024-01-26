## 코드
```swift
struct SevenWeek: View {
    @State private var angle: Angle = .degrees(0)
    @State private var currentAmount: CGFloat = 0
    @State private var numberModel: NumModel? = NumModel(number: 1)
    @State private var offset: CGSize = CGSize(width: 0, height: 0)
    @State private var lastNumber: Int = 1
    
    var body: some View {
        ScrollViewReader { proxy in
            VStack {
                ScrollView(.horizontal) {
                    HStack {
                        ForEach(1..<11) { num in
                            ZStack {
                                Circle()
                                    .foregroundColor(.green)
                                    .frame(width: 100)
                                    .opacity(CGFloat(num) / 15.0)
                                    .id(num)
                                
                                Text("\(num)")
                            }
                            .onTapGesture {
                                numberModel = NumModel(number: num)
                                lastNumber = num
                            }
                        }
                    }
                }
                .frame(height: 100)

                Spacer()

                RoundWheel()
                    .rotationEffect(angle)
                    .gesture (
                        RotationGesture()
                            .onChanged { value in
                                angle = value
                                proxy.scrollTo(Int(abs(angle.radians) * 3), anchor: .center)
                            }
                    )
                    .scaleEffect(currentAmount + 1)
                    .onLongPressGesture(minimumDuration: 1.0) {
                        withAnimation(.easeInOut(duration: 0.05)) {
                            currentAmount += 1
                        }
                    }
                    .onTapGesture {
                        currentAmount = 0
                    }

                Text("\(Int(angle.radians * 3))")

                Spacer()

                ballListView
                    .overlay(ballView.mask(ballListView))
                    .onAppear {
                        print("\(numberModel?.number ?? -1)")
                    }
            }
            .sheet(item: $numberModel) { model in
                VStack {
                    ZStack {
                        RoundedRectangle(cornerRadius: 15)
                            .foregroundColor(.green)
                            .frame(width: 100, height: 100)
                            .offset(offset)
                            .gesture(
                                DragGesture()
                                    .onChanged { value in
                                        offset = value.translation
                                    }
                            )
                        Text("\(model.number)")
                    }
                }
                .scaleEffect(currentAmount + 1)
                .gesture(
                    MagnificationGesture()
                        .onChanged { amount in
                            currentAmount += amount - 1
                        }
                        .onEnded { _ in
                            currentAmount = 0
                        }
                )
            }
            
        }
    }


    private var ballView: some View {
        GeometryReader { geometry in
            Rectangle()
                .foregroundColor(.cyan)
                .frame(width: geometry.size.width * (Double(lastNumber) / 10.0), height: 100)
                .allowsHitTesting(false)
        }
    }

    private var ballListView: some View {
        HStack {
            ForEach(1..<11) { num in
                Circle()
            }
        }
    }
}

struct NumModel: Identifiable {
    let id = UUID().uuidString
    var number: Int
}

struct RoundWheel: View {
    var body: some View {
        ZStack(alignment: .topLeading) {
            Circle()
                .foregroundColor(.yellow)
                .frame(width: 100, height: 100)

            Circle()
                .foregroundColor(.red)
                .frame(width: 40, height: 30)
                .padding([.top, .leading], 8)
        }
    }
}
```
