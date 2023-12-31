# 코드

```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        ZStack {
            // 전체 배경화면 회색 설정
            Color.gray.ignoresSafeArea()

            // 문제 타이틀
            VStack {
                // 그라데이션 배경 도형
                RoundedRectangle(cornerRadius: /*@START_MENU_TOKEN@*/25.0/*@END_MENU_TOKEN@*/)
                    .fill(                        LinearGradient(gradient: Gradient(colors: [Color.yellow, Color.green]),
                                                                 startPoint: .leading,
                                                                 endPoint: .trailing)
                    )
                    .frame(height: 70)
                    .padding(.horizontal, 15)
                    .overlay {
                        // 문제 텍스트
                        Text("다음 국기를 가진 국가의 이름은?")
                            .font(.title2)
                            .foregroundStyle(.white)
                    }

                Spacer()

                // 국기를 담은 VStack
                VStack {
                    // 4괘 중, 건과 감을 담은 HStack
                    HStack {
                        VStack {
                            Color.black
                                .frame(height: 15)
                            Color.black
                                .frame(height: 15)
                            Color.black
                                .frame(height: 15)
                        }
                        .frame(width: 100)
                        .rotationEffect(Angle(degrees: 130))

                        Spacer()

                        VStack {
                            HStack {
                                Color.black
                                    .frame(height: 15)
                                Color.black
                                    .frame(height: 15)
                            }
                            Color.black
                                .frame(height: 15)
                            HStack {
                                Color.black
                                    .frame(height: 15)
                                Color.black
                                    .frame(height: 15)
                            }
                        }
                        .frame(width: 100)
                        .rotationEffect(Angle(degrees: 50))
                    }
                    .padding(EdgeInsets(top: 40, leading: 20, bottom: 0, trailing: 20))

                    // 태극 문양을 담은 VStack

                    VStack {
                        Color.red
                        Spacer(minLength: 0)
                        Color.blue
                    }
                    .frame(width: 150, height: 150)
                    .clipShape(.circle)
                    .overlay {
                        HStack {
                            Circle()
                                .fill(.red)
                                .frame(width: 75, height: 75)
                            Spacer(minLength: 0)
                            Circle()
                                .fill(.blue)
                                .frame(width: 75, height: 75)
                        }
                    }
                    .rotationEffect(Angle(degrees: 20))

                    // 4괘 중, 곤과 이를 담은 HStack

                    HStack {
                        VStack {
                            Color.black
                                .frame(height: 15)
                            HStack {
                                Color.black
                                    .frame(height: 15)
                                Color.black
                                    .frame(height: 15)
                            }

                            Color.black
                                .frame(height: 15)
                        }
                        .frame(width: 100)
                        .rotationEffect(Angle(degrees: 50))

                        Spacer()

                        HStack {
                            VStack {
                                HStack {
                                    Color.black
                                        .frame(height: 15)
                                    Color.black
                                        .frame(height: 15)
                                }
                                HStack {
                                    Color.black
                                        .frame(height: 15)
                                    Color.black
                                        .frame(height: 15)
                                }

                                HStack {
                                    Color.black
                                        .frame(height: 15)
                                    Color.black
                                        .frame(height: 15)
                                }
                            }
                            .frame(width: 100)
                            .rotationEffect(Angle(degrees: 130))
                        }
                    }
                    .padding(EdgeInsets(top: -20, leading: 20, bottom: 40, trailing: 20))

                }
                .background()

                Spacer()

                // 오답을 담은 HStack

                HStack {
                    Image(systemName: "x.circle.fill")
                        .resizable()
                        .aspectRatio(contentMode: .fit)
                        .foregroundColor(.red)
                        .frame(width: 70, height: 70)
                        .padding(.leading)

                    Capsule()
                        .fill(.white)
                        .stroke(.red, lineWidth: 2)
                        .overlay {
                            Text(
                            """
                            북한이 아닙니다.
                            다시 한번 정답을 생각해 보세요.
                            """
                            )
                            .fontWeight(.bold)
                            .fontDesign(.monospaced)
                            .foregroundStyle(.black)
                            .strikethrough(true, color: .red)
                            .padding(.horizontal)
                        }
                        .frame(height: 70)
                }

                Spacer()

                // 정답을 담은 HStack

                HStack() {
                    Image(systemName: "o.circle.fill")
                        .resizable()
                        .aspectRatio(contentMode: .fit)
                        .foregroundColor(.blue)
                        .frame(width: 70, height: 70)
                        .padding(.leading)

                    Capsule()
                        .fill(.white)
                        .stroke(.blue, lineWidth: 2)
                        .overlay {
                            Text("정답은 바로 대한민국 입니다")
                                .font(.title3)
                                .bold()
                                .foregroundStyle(.cyan)
                                .underline()
                                .kerning(10)
                        }
                        .frame(height: 70)

                }
            }
        }
    }
}

#Preview {
    ContentView()
}
```

# 사진

![SooKkongResultUI](https://github.com/KTRJB/SwiftUI_Study/assets/99063327/87be0d44-3b18-407c-9c60-c71a93bbffbc)

