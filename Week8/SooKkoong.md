```swift
//
//  Week8View.swift
//  SwiftUIStudy
//
//  Created by 전민수 on 1/28/24.
//

import SwiftUI
import Combine

struct Week8View: View {
    @StateObject private var viewModel = Week8ViewModel()
    @State private var isSheetPresented = false

    var body: some View {
        VStack {
            // 사용자 입력을 받기 위한 텍스트 필드
            TextField("남기고 싶은 말을 적어보세요!", text: $viewModel.input)
                .textFieldStyle(RoundedBorderTextFieldStyle())
                .padding()

            // 사용자 입력에 따라 업데이트되는 리스트
            // Hashable을 직접 사용하는 부분은 없지만,
            // List에서 데이터를 사용할 때 Hashable 프로토콜이 요구되므로
            // 현재는 Hashable 프로토콜을 채택한 String으로 대체
            List(viewModel.posts, id: \.self) { post in
                Text(post)
            }
            Spacer()

            // Custom shape 사용 예시
            CustomShapeView()
                .frame(width: 100, height: 100)
            Spacer()

            // Group 및 Custom 버튼 예시
            // Swift UI는 기본적으로 직접적인 자식 뷰의 개수가 10개 넘어가면 오류 발생
            // 따라서 여러 개의 뷰 일괄적으로 수행하기 위해 Group 사용
            Group {
                // Label 커스텀 한 버튼
                Button {
                    isSheetPresented.toggle()
                } label: {
                    Text("시트 꺼내기")
                        .frame(height: 55)
                        .frame(maxWidth: .infinity)
                }
                .buttonStyle(DefaultButtonStyle())
                .controlSize(.large)
                .foregroundColor(.white) // 텍스트 색상
                .background(Color.blue) // 배경 색상
                .sheet(isPresented: $isSheetPresented) {
                    SheetView()
                }

                Button {} label: {
                    Text("가짜 버튼")
                        .frame(height: 40)
                        .frame(maxWidth: 150)
                }
                .buttonStyle(BorderedButtonStyle())
                .controlSize(.mini)
                .foregroundColor(.red) // 텍스트 색상
                .background(Color.yellow) // 배경 색상
            }
            // Group을 통해 하위 뷰들에 모두 Capsule UI 적용
            .overlay(
                Capsule() // Capsule 모양의 overlay 추가
                    .stroke(Color.white, lineWidth: 2)
            )
            .clipShape(Capsule()) // 버튼 모양을 Capsule로 자름

            Spacer()
        }
    }
}

// 사용자 입력 및 타이머 처리를 위한 ViewModel 정의
class Week8ViewModel: ObservableObject {
    @Published var input = "" // 사용자 입력 추적 상태 변수
    @Published var posts: [String] = [] // 게시글 목록 추적 상태 변수
    private var cancellables = Set<AnyCancellable>() // DisposeBag
    private let startTime = Date() // 앱 시작 시간
    private var lastInput = "" // 마지막 입력 값

    init() {
        setupCombine()
    }

    // Combine을 사용하여 입력 및 타이머 로직 설정
    private func setupCombine() {
        // Timer Publish
        let timerPublisher = Timer.publish(every: 1, on: .main, in: .common).autoconnect()

        $input
            // 0.5초 간격의 이벤트 흐름 제어 debounce
            .debounce(for: .seconds(0.5), scheduler: RunLoop.main)
            // 다른 입력의 이벤트만 받기
            .removeDuplicates()
            // publisher 결합
            .combineLatest(timerPublisher)
            .filter { [weak self] input, _ in
                // 중복 입력 및 빈 입력 필터링
                guard let self = self, !input.isEmpty, input != self.lastInput else { return false }
                self.lastInput = input // 마지막 입력 업데이트
                return true
            }
            .map { input, _ in
                // 경과 시간과 입력 텍스트 결합
                let elapsedTime = Int(Date().timeIntervalSince(self.startTime))
                return "앱 시작 \(elapsedTime) 이후 작성된 텍스트는 \(input)입니다."
            }
            .sink { [weak self] post in
                // 게시글 목록 업데이트 및 텍스트 필드 초기화
                self?.posts.append(post)
                self?.input = ""
            }
            .store(in: &cancellables) // dispose
    }
}

// custom shape를 보여주는 뷰
struct CustomShapeView: View {
    @State private var endAngle: CGFloat = 0.0 // 마지막 끝나는 지점 각도

    var body: some View {
        CustomShape(endAngle: endAngle)
            .stroke(Color.blue, lineWidth: 2)
            .frame(width: 100, height: 100)
            .onAppear {
                // 뷰가 나타날 때 애니메이션 적용
                withAnimation(.linear(duration: 2).repeatForever(autoreverses: false)) {
                    endAngle = 360
                }
            }
    }
}

// custom shape
struct CustomShape: Shape {
    var endAngle: CGFloat

    // Shape 프로토콜 채택을 통한 애니메이션 가능한 데이터 정의
    var animatableData: CGFloat {
        get { endAngle }
        set { endAngle = newValue }
    }

    // 경로 정의
    func path(in rect: CGRect) -> Path {
        var path = Path()
        path.move(to: CGPoint(x: rect.midX, y: rect.midY)) // 커서 이동
        path.addLine(to: CGPoint(x: rect.maxX, y: rect.maxY)) // 직선
        path.addArc(center: CGPoint(x: rect.midX, y: rect.midY), radius: 50, startAngle: .zero, endAngle: .degrees(Double(endAngle)), clockwise: true) // 곡선
        path.addCurve(to: CGPoint(x: rect.minX, y: rect.minY), control1: CGPoint(x: rect.midX, y: rect.minY), control2: CGPoint(x: rect.minX, y: rect.midY)) // 3차 베지어 곡선
        path.addQuadCurve(to: CGPoint(x: rect.maxX, y: rect.maxY), control: CGPoint(x: rect.midX, y: rect.maxY)) // 2차 베지어 곡선
        return path
    }
}

// 시트 내용을 보여주는 뷰
struct SheetView: View {
    var body: some View {
        Text("시트 내용")
            .presentationDetents([.fraction(0.2), .medium, .large]) // Sheet의 사이즈 비율
            .presentationDragIndicator(.visible) // drag indicator 표시
    }
}

struct Week8View_Previews: PreviewProvider {
    static var previews: some View {
        Week8View()
    }
}
```
