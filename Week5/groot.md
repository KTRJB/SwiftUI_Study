```swift
import SwiftUI

struct Week5DayView: View {
    var body: some View {
        VStack {
            NavigationStack {
                TabView {
                    CalendarView()
                        .tabItem {
                            Image(systemName: "calendar")
                            Text("날짜")
                        }
                        .onAppear {
                            print("CalendarView TabView Appear")
                        }
                        .onDisappear {
                            print("CalendarView TabView Disappear")
                        }
                    
                    AlramView()
                        .tabItem {
                            Image(systemName: "bell.and.waves.left.and.right")
                            Text("알람")
                        }
                        .onAppear {
                            print("AlramView TabView Appear")
                        }
                        .onDisappear {
                            print("AlramView TabView Disappear")
                        }
                }
            }
        }
    }
}

struct CalendarView: View {
    @State var date = Date()
    @AppStorage("time") var lastTime: Date?
    
    var body: some View {
        VStack {
            DatePicker(
                "Start Date",
                selection: $date,
                displayedComponents: [.date]
            )
            .datePickerStyle(.graphical)
            
            Spacer()
            
            HStack {
                Text("마지막 알람정보 : ")
                
                Text(lastTime?.formatted(date: .long, time: .standard) ?? "없음.")
            }
            .background(
                RoundedRectangle(cornerRadius: 30)
                    .fill(.white)
            )
            
            Spacer()
        }
        .onAppear {
            print("CalendarView Appear")
        }
        .onDisappear {
            print("CalendarView Disappear")
        }
    }
}

struct AlramView: View {
    @State var soundValue: Double = 0
    @State var isShowSoundView = false
    @State var isSelectedSound = false
    @Environment(\.colorScheme) var colorScheme
    @AppStorage("time") var time: Date?
    
    var body: some View {
        VStack {
            
            Spacer()
            
            DatePicker("알림 시간", selection: $time.toUnwrapped(defaultValue: Date()),
                       displayedComponents: [.hourAndMinute]
            )
            .datePickerStyle(.wheel)
            
            Spacer()
            
            HStack {
                Text("알림 소리 조절")
                
                Spacer()
                
                Slider(value: $soundValue, in: 0...100) // slider의 가동범위는 0부터 100까지
                    .tint(
                        colorScheme == .light ? .red : .orange
                    )
                
                Spacer()
                
                Stepper("", value: $soundValue)
            }
            
            
            Spacer()
            
            Text("벨소리 선택")
                .onTapGesture {
                    isShowSoundView.toggle()
                }
                .sheet(isPresented: $isShowSoundView, content: {
                    SoundView(
                        isShowSoundView: $isShowSoundView,
                        isSelectedSound: $isSelectedSound
                    )
                })
            
            Spacer()
        }
        .foregroundStyle(
            colorScheme == .light ? .black : .red
        )
        .alert(isPresented: $isSelectedSound) {
            guard let time = time else {
                return Alert(title: Text("알람설정 실패"))
            }
            
            return Alert(title: Text("\(time.description(with: .current)) 알람설정 완료"))
        }
        .onAppear {
            print("AlramView Appear")
        }
        .onDisappear {
            print("AlramView Disappear")
        }
    }
}

struct SoundView: View {
    @Binding var isShowSoundView: Bool
    @Binding var isSelectedSound: Bool
    @Environment(\.dismiss) var dismiss
    @State var sounds = ["새소리", "물소리"]
    
    var body: some View {
        List {
            ForEach(sounds, id: \.self) { sound in
                Text(sound)
                    .onTapGesture {
                        isSelectedSound.toggle()
                    }
            }
        }
        .onTapGesture {
            dismiss()
        }
        .onAppear {
            print("SoundView Appear")
        }
        .onDisappear {
            print("SoundView Disappear")
        }
    }
}

extension Date: RawRepresentable {
    public var rawValue: String {
        self.timeIntervalSinceReferenceDate.description
    }
    
    public init?(rawValue: String) {
        self = Date(timeIntervalSinceReferenceDate: Double(rawValue) ?? 0.0)
    }
}

extension Binding {
    func toUnwrapped<T>(defaultValue: T) -> Binding<T> where Value == Optional<T>  {
        Binding<T>(get: { self.wrappedValue ?? defaultValue }, set: { self.wrappedValue = $0 })
    }
}
```
