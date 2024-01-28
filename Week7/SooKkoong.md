```swift
import SwiftUI

// 카드 모델
struct Card {
    let symbol: String // 문양
    let number: Int // 숫자
    var isRevealed: Bool = false // 표시 여부
    var offset: CGSize = .zero // 이동 위치
    var isDragging: Bool = false // 드래그 유무
    var rotation: Angle = .degrees(0) // 회전 각도
    var scale: CGFloat = 1.0 // 크기 조정 비율
}

struct Week7View: View {
    // 찾아야 하는 카드 5장
    @State var targetCards: [Card] = [
        Card(symbol: "heart.fill", number: 1),
        Card(symbol: "diamond.fill", number: 2),
        Card(symbol: "suit.club.fill", number: 3),
        Card(symbol: "suit.spade.fill", number: 4),
        Card(symbol: "suit.spade.fill", number: 5),
    ]

    // 전체 카드: 고차함수 사용
    @State var cards: [Card] = {
        let symbols = ["heart.fill", "diamond.fill", "suit.club.fill", "suit.spade.fill"]
        let numbers = 1...10
        return symbols.flatMap { symbol in
            numbers.map { number in Card(symbol: symbol, number: number) }
        }.shuffled()
    }()

    // 찾은 카드의 수
    @State var rating: Int = 0

    var body: some View {
        VStack {
            // 새 게임 버튼: Long Press Gesture
            ZStack {
                Rectangle()
                    .frame(width: 100, height: 40)
                    .foregroundColor(.blue)
                    .cornerRadius(10)
                    .gesture(
                        LongPressGesture(minimumDuration: 0.5)
                            .onEnded { value in
                                if value {
                                    resetCards() // 카드 리셋 함수 호출
                                }
                            }
                    )
                Text("새 게임")
                    .foregroundColor(.white)
                    .font(.headline)
            }
            .padding(.bottom, 20)

            // 찾아야 하는 카드 목록
            HStack {
                VStack {
                    Image(systemName: "heart.fill")
                        .resizable()
                        .scaledToFit()
                    Text("1")
                }
                .padding(5)
                .background(Color.blue)
                .foregroundColor(.white)
                .cornerRadius(5)

                VStack {
                    Image(systemName: "diamond.fill")
                        .resizable()
                        .scaledToFit()
                    Text("2")
                }
                .padding(5)
                .background(Color.blue)
                .foregroundColor(.white)
                .cornerRadius(5)

                VStack {
                    Image(systemName: "suit.club.fill")
                        .resizable()
                        .scaledToFit()
                    Text("3")
                }
                .padding(5)
                .background(Color.blue)
                .foregroundColor(.white)
                .cornerRadius(5)

                VStack {
                    Image(systemName: "suit.spade.fill")
                        .resizable()
                        .scaledToFit()
                    Text("4")
                }
                .padding(5)
                .background(Color.blue)
                .foregroundColor(.white)
                .cornerRadius(5)

                VStack {
                    Image(systemName: "suit.spade.fill")
                        .resizable()
                        .scaledToFit()
                    Text("5")
                }
                .padding(5)
                .background(Color.blue)
                .foregroundColor(.white)
                .cornerRadius(5)

            }
            .padding(.bottom, 20)

            // 게임 점수 보드: Mask
            MaskView(rating: $rating, cards: $cards, targetCards: $targetCards)
                .padding(.bottom, 20)

            // 카드 스크롤 뷰: DragGesture, RotationGesture, MagnificationGesture
            ScrollView {
                LazyVGrid(columns: [GridItem(.adaptive(minimum: 100))]) {
                    ForEach(0..<cards.count, id: \.self) { index in
                        CardView(card: $cards[index])
                            .offset(cards[index].offset)
                            .rotationEffect(cards[index].rotation) // 회전 효과
                            .scaleEffect(cards[index].scale) // 크기 조정 효과
                            .gesture(
                                DragGesture() // 드레그 제스처
                                    .onChanged { gesture in
                                        cards[index].offset = gesture.translation // 사용자 입력에 따른 offset
                                        cards[index].isDragging = true // 드래그 시작
                                    }
                                    .onEnded { gesture in
                                        if cards[index].isDragging {
                                            withAnimation {
                                                cards[index].isRevealed.toggle() // 카드 표시
                                                checkAndFillStars() // 목표 카드와 동일할 시 점수 추가
                                            }
                                        }
                                        cards[index].offset = .zero // offset 초기화
                                        cards[index].isDragging = false // 드래그 종료
                                    }
                            )
                            .gesture(
                                RotationGesture() // 회전 제스처
                                    .onChanged { angle in
                                        cards[index].rotation = angle
                                    }
                            )
                            .gesture(
                                MagnificationGesture() // 크기 조정 제스처
                                    .onChanged { scale in
                                        cards[index].scale = scale
                                    }
                            )
                    }
                }
            }
        }
    }

    // 카드 리셋 함수
    private func resetCards() {
        cards.indices.forEach { index in
            cards[index].isRevealed = false
            cards[index].offset = .zero
            cards[index].isDragging = false
            cards[index].rotation = .degrees(0)
            cards[index].scale = 1.0
        }
        rating = 0
    }

    // 카드 일치 여부 확인 함수
    private func checkAndFillStars() {
        let revealedCards = cards.filter { $0.isRevealed }
        let matchingCards = targetCards.filter { targetCard in
            revealedCards.contains { revealedCard in
                revealedCard.symbol == targetCard.symbol && revealedCard.number == targetCard.number
            }
        }
        rating = matchingCards.count
    }
}

// 단일 카드 뷰
struct CardView: View {
    @Binding var card: Card

    var body: some View {
        ZStack {
            if card.isRevealed {
                VStack {
                    Image(systemName: card.symbol)
                        .resizable()
                        .scaledToFit()
                    Text("\(card.number)")
                }
                .frame(width: 80, height: 120)
                .background(Color.white)
            } else {
                Rectangle()
                    .fill(Color.black)
                    .frame(width: 80, height: 120)
            }
        }
        .cornerRadius(10)
        .shadow(radius: 5)
    }
}

// 게임 점수 마스크 뷰
struct MaskView: View {
    @Binding var rating: Int // 점수
    @Binding var cards: [Card] // 전체 카드
    @Binding var targetCards: [Card] // 목표 카드

    var body: some View {
        ZStack {
            starsView
                .overlay(overlayView.mask(starsView))
        }
    }

    // 오버레이뷰: Geometry Reader
    private var overlayView: some View {
        GeometryReader { geometry in
            Rectangle()
                .foregroundColor(.yellow)
                .frame(width: CGFloat(rating) / 5 * geometry.size.width) // 평점에 따라 너비 조절
        }
    }

    // 별점 표시 뷰
    private var starsView: some View {
        HStack {
            ForEach(1..<6) { index in
                Image(systemName: "star.fill")
                    .font(.largeTitle)
                    .foregroundColor(.gray)
            }
        }
    }
}
```
