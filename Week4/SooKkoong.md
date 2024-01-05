```swift
import SwiftUI

struct Week4View: View {

    // MARK: Properties

    @State private var shouldShowContents: Bool = false // 컨텐츠 표시 여부 상태 변수
    @State private var showModal = false // 모달 뷰 표시 여부 상태 변수
    @State private var showTextFieldSheet = false // 텍스트 필드 시트 표시 여부 상태 변수
    @State private var selectedPickerStyle = 0 // 선택된 피커 스타일 상태 변수
    @State private var toggleState = false // 토글 상태 변수
    @State private var themeColor = Color.blue // 테마 색깔 상태 변수
    @State private var textInput = "" // 텍스트 입력 상태 변수

    // MARK: - View

    var body: some View {
        // 네비게이션 뷰
        NavigationView {
            VStack {
                if shouldShowContents {
                    // 모달/네비게이션 토글 스위치
                    Toggle("Toggle Modal/Navigation", isOn: $toggleState)
                        .padding()
                        .transition(.move(edge: .leading)) // 트랜지션 적용

                    // 피커
                    Group {
                        if selectedPickerStyle == 0 {
                            Picker("Picker Style", selection: $selectedPickerStyle) {
                                Text("Wheel").tag(0)
                                Text("Menu").tag(1)
                                Text("Segmented").tag(2)
                            }
                            .pickerStyle(WheelPickerStyle())
                        } else if selectedPickerStyle == 1 {
                            Picker("Picker Style", selection: $selectedPickerStyle) {
                                Text("Wheel").tag(0)
                                Text("Menu").tag(1)
                                Text("Segmented").tag(2)
                            }
                            .pickerStyle(MenuPickerStyle())
                        } else {
                            Picker("Picker Style", selection: $selectedPickerStyle) {
                                Text("Wheel").tag(0)
                                Text("Menu").tag(1)
                                Text("Segmented").tag(2)
                            }
                            .pickerStyle(SegmentedPickerStyle())
                        }
                    }
                    .transition(.move(edge: .leading))
                    .padding()

                    // 컬러 픽커
                    ColorPicker("Choose a color", selection: $themeColor)
                        .transition(.move(edge: .leading))
                        .padding()

                    // 토글 상태에 따른 모달 혹은 네비게이션 링크 표시
                    if toggleState {
                        // 네비게이션 링크
                        NavigationLink(destination: Text("Navigation View")) {
                            Text("Navigation")
                        }
                        .transition(.move(edge: .leading))
                        .padding()
                    } else {
                        Button("Modal") {
                            showModal = true
                        }
                        .transition(.move(edge: .leading))
                        .padding()
                        .sheet(isPresented: $showModal) {
                            VStack {
                                Text("Modal View")
                                    .font(.largeTitle)
                            }
                        }
                    }

                    // 텍스트 필드를 포함한 sheet
                    Button("TextField Sheet") {
                        showTextFieldSheet = true
                    }
                    .transition(.move(edge: .leading))
                    .padding()
                    .sheet(isPresented: $showTextFieldSheet) {
                        TextFieldSheet(textInput: $textInput, showTextFieldSheet: $showTextFieldSheet)
                    }
                }
            }
            // view가 appear될때 컨텐츠를 보여주도록 animation
            .onAppear {
                withAnimation {
                    shouldShowContents = true
                }
            }
            // 컬러 픽커에서 설정한 테마 색상에 따른 foreground 색상 변경
            .foregroundColor(themeColor)
            .navigationTitle("Week4 Practice")
        }
    }
}

// 텍스트 필드를 포함한 sheet
struct TextFieldSheet: View {

    // MARK: - Properties

    @Binding var textInput: String // 텍스트 입력 상태 바인딩
    @Binding var showTextFieldSheet: Bool // 텍스트 필드 시트 표시 여부 바인딩
    @FocusState private var isInputFocused: Bool // 사용자 입력 포커스 확인을 위한 포커스 상태 변수
    @State private var animateCircle = false // 원의 애니메이션 상태 변수

    // MARK: - View

    var body: some View {
        VStack {
            // 포커스에 따라 원의 애니메이션을 반복
            Circle()
                .frame(
                    width: animateCircle ? 150 : 100,
                    height: animateCircle ? 150 : 100
                )
                .foregroundColor(.blue)
                .animation(
                    .easeInOut(duration: 1).repeatForever(autoreverses: true),
                    value: animateCircle
                )
                .onChange(of: isInputFocused) { focused in
                }

            // close 입력시 dismiss 하는 텍스트 필드
            TextField("Enter 'close' to dismiss", text: $textInput)
                .focused($isInputFocused)
                .padding()
                .onChange(of: textInput) { newValue in
                    if newValue.lowercased() == "close" {
                        showTextFieldSheet = false
                    }
                }
        }
        .padding()
    }
}

struct Week4View_Previews: PreviewProvider {
    static var previews: some View {
        Week4View()
    }
}
```
