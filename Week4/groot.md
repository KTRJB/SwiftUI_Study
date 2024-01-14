```swift
import SwiftUI


//Animations
//Animation Curve Timing
//Sheets
//NavigationView
//Toggle
//Picker
//ColorPicker
//Transition
//FocusState
//Custom Return KeyBoard Button

struct Week4DayView: View {
    enum Signal {
        case green
        case yellow
        case red
        case gray
        
        var color: Color {
            switch self {
            case .green:
                return .green
            case .yellow:
                return .yellow
            case .red:
                return .red
            case .gray:
                return .gray
            }
        }
    }
    
    @State var signal: Signal = .green
    @State var isPresentSheet1 = false
    @State var isPresentSheet2 = false
    @State var isPresentSheet3 = false
    @State var backgroundColor:Color = .green
    @State var bestBabo = "수꿍 바보"
    @State var inputBabo = ""
    @State var babos = ["주디 바보", "예톤 바보", "수꿍 바보", "브래드 바보"]
    
    var body: some View {
        NavigationView {
            VStack(alignment: .center) {
                HStack {
                    RoundedRectangle(cornerRadius: 100)
                        .fill(signal.color)
                        .frame(width: 50, height: 50)
                    
                    RoundedRectangle(cornerRadius: 100)
                        .fill(signal.color)
                        .frame(width: 50, height: 50)
                    
                    RoundedRectangle(cornerRadius: 100)
                        .fill(signal.color)
                        .frame(width: 50, height: 50)
                }
                
                HStack {
                    Button("green") {
                        withAnimation (
                        ){
                            signal = .green
                        }
                    }
                    
                    Button("red") {
                        withAnimation (
                        ){
                            signal = .red
                        }
                    }
                    
                    Button("yellow") {
                        if signal == .yellow {
                            withAnimation (
                                .default
                                    .delay(0.3)
                                    .repeatCount(3)
                            ) {
                                signal = .gray
                                signal = .yellow
                                signal = .gray
                                signal = .yellow
                                signal = .gray
                                signal = .yellow
                            }
                        } else {
                            withAnimation (
                            ){
                                signal = .yellow
                            }
                        }
                    }
                }
                
                Spacer()
                
                ColorPicker("Select a Color",
                            selection: $backgroundColor,supportsOpacity: true)
                .padding()
                .background(.black)
                .cornerRadius(10)
                .foregroundColor(.white)
                
                Spacer()
                
                TextField("바보 입력기", text: $inputBabo)
                    .textFieldStyle(.plain)
                    .background(backgroundColor)
                
                Spacer()
                
                Button("바보 추가") {
                    babos.append(inputBabo)
                }
                .background(backgroundColor)
                
                Spacer()
                
                Picker("바보 선택기", selection: $bestBabo) {
                    ForEach(babos, id: \.self) { babo in
                        Text(babo)
                    }
                }
                .background(backgroundColor)
                .pickerStyle(.segmented)
                
                Spacer()
                
                HStack {
                    if isPresentSheet3 {
                        RoundedRectangle(cornerRadius: 30)
                            .fill(backgroundColor)
                            .overlay(content: {
                                Text(bestBabo)
                                    .tint(.white)
                            })
                            .frame(
                                width: UIScreen.main.bounds.width,
                                height: UIScreen.main.bounds.height/2
                            )
                            .transition(.asymmetric(insertion: .slide,
                                                    removal: AnyTransition.opacity.animation(.easeInOut)))
                    }
                    Text(bestBabo)
                        .sheet(isPresented: $isPresentSheet1, content: {
                            Sookkoongbabo(babo: $bestBabo, background: $backgroundColor)
                                .presentationDetents([.medium])
                        })
                    
                    Text(bestBabo)
                        .fullScreenCover(isPresented: $isPresentSheet2, content: {
                            Sookkoongbabo(babo: $bestBabo, background: $backgroundColor)
                                .presentationDetents([.medium])
                        })
                }
                
                HStack {
                    Button("살짝 바보") {
                        isPresentSheet3.toggle()
                    }
                    
                    Button("약간 바보") {
                        isPresentSheet1.toggle()
                    }
                    
                    NavigationLink("진짜 바보") {
                        Sookkoongbabo(babo: $bestBabo, background: $backgroundColor, isNaviagtion: true)
                    }
                    
                    Button("완전 바보") {
                        isPresentSheet2.toggle()
                    }
                }
            }
            .animation(.easeOut, value: isPresentSheet3)
        }
    }
}

struct Sookkoongbabo: View {
    @Binding var babo: String
    @Binding var background: Color
    var isNaviagtion = false
    @Environment(\.dismiss) var dismiss
    var body: some View {
        VStack {
            Button(babo) {
                dismiss()
            }
        }
        .background(background)
    }
}

#Preview {
    Week4DayView()
}
```

![simulator_screenshot_E4049E17-1382-4995-BD6A-8E68B8830277](https://github.com/KTRJB/SwiftUI_Study/assets/96932116/f97006bb-4072-4c46-ae69-d4faacd3a269)

