```swift
//
//  Week5View.swift
//  SwiftUIStudy
//
//  Created by 전민수 on 1/14/24.
//
import SwiftUI

struct Week5View: View {
    // 현재 시스템 컬러 스키마를 감지하는 환경 변수
    @Environment(\.colorScheme) var colorScheme

    // UserDefaults를 사용하여 앱 설정을 저장
    @AppStorage("isDarkMode") private var isDarkMode = false
    @AppStorage("lastTab") private var lastTab = 0

    // 뷰의 상태를 관리하는 변수들
    @State private var date = Date()
    @State private var sliderValue = 0.5
    @State private var stepValue = 0
    @State private var showAlert = false

    var body: some View {
        // 사용자 최근에 선택한 탭에서 시작
        TabView(selection: $lastTab) {
            firstTabView
                .background(Color.yellow.opacity(sliderValue)) // 배경색에 슬라이더 값에 따른 투명도 적용
                .tabItem {
                    Label("Home", systemImage: "house")
                }
                .tag(0) // 첫 번째 탭을 식별하기 위한 태그
                .onAppear { lastTab = 0 } // 첫 번째 탭이 나타날 때 lastTab 업데이트

            secondTabView
                .tabItem {
                    Label("Settings", systemImage: "gear")
                }
                .tag(1) // 두 번째 탭을 식별하기 위한 태그
                .onAppear { lastTab = 1 } // 두 번째 탭이 나타날 때 lastTab 업데이트
        }
        .onAppear {
            isDarkMode = colorScheme == .dark // 시스템의 다크 모드 여부에 따라 isDarkMode 설정
            lastTab = UserDefaults.standard.integer(forKey: "lastTab") // 마지막으로 열린 탭 정보 로드
        }
    }

    var firstTabView: some View {
        VStack {
            Text(dateFormatter(date: date)) // 현재 선택된 날짜 표시
                .font(.largeTitle)
                .padding()

            DatePicker("Select a date", selection: $date) // DatePicker
                .padding()

            Slider(value: $sliderValue, in: 0...1) // Slider
                .padding()

            Stepper("Value: \(stepValue)", value: $stepValue) // Stepper
                .padding()

            Button("Save Current Step Value") {
                UserDefaults.standard.set(stepValue, forKey: "savedValue") // 버튼 클릭 시 Stepper 값 저장
                showAlert = true
            }
            .alert(isPresented: $showAlert) { // 저장 성공 표시
                Alert(
                    title: Text("Success"),
                    message: Text("Step value saved!"), 
                    dismissButton: .default(Text("OK"))
                )
            }

            // if let을 통한 옵셔널 바인딩
            if let savedValue = UserDefaults.standard.object(forKey: "savedValue") as? Int {
                Text("Saved value: \(savedValue)") // 저장된 값 표시
            } else {
                Text("No saved value")
            }
        }
    }

    var secondTabView: some View {
        VStack {
            Text("Settings") // 설정 타이틀
                .font(.largeTitle)

            Text("Adjust your preferences: light/dark") // 설정 설명 텍스트
                .font(.subheadline)
                .padding()

            Toggle("Dark Mode", isOn: $isDarkMode) // 다크 모드 토글
        }
        .background(isDarkMode ? Color.black : Color.white) // 다크 모드에 따른 배경색 변경
        .foregroundColor(isDarkMode ? Color.red : Color.black) // 다크 모드에 따른 글자색 변경
    }

    // Date 값을 String 값으로 변환하기 위한 dateFormatter 함수
    private func dateFormatter(date: Date) -> String {
        let formatter = DateFormatter()
        formatter.dateStyle = .long
        return formatter.string(from: date)
    }
}

```
