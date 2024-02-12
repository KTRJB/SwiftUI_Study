```swift
//
//  Week9View.swift
//  SwiftUIStudy
//
//  Created by 전민수 on 2/11/24.
//

import SwiftUI

// 모든 safe area를 무시하고 상단에 텍스트를 표시하는 SafeAreaView struct 정의
struct SafeAreaView: View {
    var body: some View {
        Color.red
            .edgesIgnoringSafeArea(.all) // 모든 안전 영역을 무시
            .safeAreaInset(edge: .top, spacing: 0) { // 상단 safe area에 텍스트 삽입
                Text("Safe Area Top")
                    .frame(height: 50)
                    .frame(maxWidth: .infinity)
                    .background(Color.blue)
            }
    }
}

// 두 가지 형태의 뷰 간 전환 효과를 위한 MatchedGeometryEffectView struct 정의
struct MatchedGeometryEffectView: View {
    @Namespace private var namespace // 애니메이션을 위한 namespace 정의
    @State private var isFlipped = false // 뷰 전환 상태를 관리하는 State 변수

    var body: some View {
        VStack {
            if isFlipped {
                Rectangle()
                    .matchedGeometryEffect(id: "shape", in: namespace) // 전환 효과 적용
                    .frame(width: 100, height: 100)
                    .onTapGesture { withAnimation { isFlipped.toggle() } }
            } else {
                Circle()
                    .matchedGeometryEffect(id: "shape", in: namespace) // 전환 효과 적용
                    .frame(width: 100, height: 100)
                    .onTapGesture { withAnimation { isFlipped.toggle() } }
            }
        }
    }
}

// 뷰 간 데이터 전달을 위한 PreferenceKey 프로토콜 구현
struct MyPreferenceKey: PreferenceKey {
    static var defaultValue: Int = 0 // 기본값 설정

    // value 축적 로직 구현
    static func reduce(value: inout Int, nextValue: () -> Int) {
        value += nextValue()
    }
}

// 메인 뷰
struct Week9View: View {
    @Namespace private var namespace // 애니메이션을 위한 namespace 정의
    @State private var showMessage = false // MessageView 상태를 관리하는 State 변수
    @State private var text: String = "" // 사용자 입력 텍스트를 관리하는 State 변수
    @State private var textLength: Int = 0 // 텍스트 길이를 관리하는 State 변수

    var body: some View {
        NavigationView {
            VStack(spacing: 20) {
                SafeAreaView() // 상단 SafeAreaView 삽입
                    .frame(maxWidth: .infinity)
                    .background(Color.blue)

                Text("메시지 길이: \(textLength)") // 입력된 텍스트 길이 표시
                    .font(.headline)
                    .padding(.top)

                // 메시지 토글 버튼
                Button(action: {
                    withAnimation {
                        showMessage.toggle()
                    }
                }) {
                    Label("메시지 토글 버튼", systemImage: showMessage ? "message.fill" : "message")
                        .foregroundColor(.white)
                        .padding()
                        .background(Capsule().fill(showMessage ? Color.green : Color.blue))
                }
                .buttonStyle(PlainButtonStyle())

                // MessageView 표시 조건
                if showMessage {
                    MessageView(namespace: namespace, text: $text, textLength: $textLength)
                }

                Spacer()
            }
            .padding()
            .navigationBarTitle("Message App", displayMode: .large)
            .toolbar {
                ToolbarItem(placement: .bottomBar) {
                    BottomSafeAreaToolbar() // 하단 safe area 툴바 삽입
                }
            }
        }
    }
}

// 실제 메시지와 UIKit 텍스트 필드를 포함하는 MessageView struct 정의
struct MessageView: View {
    var namespace: Namespace.ID // 애니메이션을 위한 namespace 인자
    @Binding var text: String // 바인딩된 사용자 입력 텍스트
    @Binding var textLength: Int // 바인딩된 텍스트 길이

    var body: some View {
        GenericsView(content: Text("예) SwiftUI 스터디 종강을 축하합니다!!")
            .padding()
            .background(Color.blue)
            .cornerRadius(10)
            .matchedGeometryEffect(id: "message", in: namespace), title: "상대방에게 메시지를 전하세요!")
            .transition(.asymmetric(insertion: .rotating(rotation: 180), removal: .opacity)) // 등장 시 rotation, 퇴장 시 opacity 효과를 주는 transition 적용
            .preference(key: MyPreferenceKey.self, value: text.count) // 텍스트 길이 축적을 위한 MyPreferenceKey 사용

        UIKitTextField(text: $text) // UIKit 기반의 텍스트 필드
            .frame(height: 50)
            .padding()
            .background(Color(UIColor.secondarySystemBackground))
            .cornerRadius(8)
            .onChange(of: text) { newValue in
                self.textLength = newValue.count
            }
    }
}

// 하단 safe area 툴바 디자인을 위한 BottomSafeAreaToolbar struct 정의
struct BottomSafeAreaToolbar: View {
    var body: some View {
        Text("Safe Area Bottom") // 하단 안전 영역 텍스트
            .frame(maxWidth: .infinity, maxHeight: 100)
            .background(Color.gray.opacity(0.5))
            .cornerRadius(10)
    }
}

// 회전 전환 효과를 위한 AnyTransition extension 정의
extension AnyTransition {
    static func rotating(rotation: Double) -> AnyTransition {
        .modifier(
            active: RotateViewModifier(angle: Angle(degrees: rotation)),
            identity: RotateViewModifier(angle: Angle(degrees: 0))
        )
    }
}

// 회전 효과를 적용하는 RotateViewModifier struct 정의
struct RotateViewModifier: ViewModifier {
    let angle: Angle // 회전 각도

    func body(content: Content) -> some View {
        content.rotationEffect(angle) // 콘텐츠에 회전 효과 적용
    }
}

// 범용 뷰 컨테이너를 위한 GenericsView struct 정의
struct GenericsView<Content: View>: View {
    let content: Content // 제네릭 컨텐츠
    let title: String // 뷰 제목

    var body: some View {
        VStack {
            Text(title).font(.headline) // 제목 표시
            content // 제네릭 컨텐츠 표시
        }
    }
}

// UIKit의 UITextField를 SwiftUI에서 사용하기 위한 UIViewRepresentable 구현
struct UIKitTextField: UIViewRepresentable {
    @Binding var text: String // 바인딩된 텍스트

    // SwiftUI 뷰에서 사용할 UIView 생성
    func makeUIView(context: Context) -> UITextField {
        let textField = UITextField()
        textField.backgroundColor = .lightGray // 배경색 설정
        textField.delegate = context.coordinator // 델리게이트 설정
        return textField
    }

    // UIView 업데이트
    func updateUIView(_ uiView: UITextField, context: Context) {
        uiView.text = text // 바인딩된 텍스트로 업데이트
    }

    // Coordinator 생성
    func makeCoordinator() -> Coordinator {
        Coordinator(self)
    }

    // UITextField 이벤트 처리를 위한 Coordinator 클래스
    class Coordinator: NSObject, UITextFieldDelegate {
        var parent: UIKitTextField

        init(_ textField: UIKitTextField) {
            self.parent = textField
        }

        // 텍스트 변경 시 호출
        func textFieldDidChangeSelection(_ textField: UITextField) {
            parent.text = textField.text ?? "" // 변경된 텍스트로 상태 업데이트
        }
    }
}
```
