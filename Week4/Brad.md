```swift
import SwiftUI

enum ThreeWeelkyList: CaseIterable {
    case animation
    case sheet
    case toggle
    case picker
    case focusState
    
    var title: String {
        switch self {
        case .animation:
            return "애니메이션"
        case .sheet:
            return "Sheet"
        case .toggle:
            return "Toggle"
        case .picker:
            return "Picker"
        case .focusState:
            return "FocusState"
        }
    }
    
    var destination: AnyView {
        switch self {
        case .animation:
            return AnyView(AnimationView())
        case .sheet:
            return AnyView(SheetView())
        case .toggle:
            return AnyView(ToggleView())
        case .picker:
            return AnyView(PickerView())
        case .focusState:
            return AnyView(FocusStateView())
        }
    }
}

struct ThreeWeelky: View {
    var body: some View {
        NavigationStack {
            List {
                ForEach(ThreeWeelkyList.allCases, id: \.self) { element in
                    NavigationLink {
                        element.destination
                    } label: {
                        Text("\(element.title)")
                    }
                }
            }
        }
    }
}

fileprivate struct AnimationView: View {
    @State var isanimated: Bool = false
    var body: some View {
        VStack {
            Button("Button") {
                withAnimation(.default) {     //defeult는 fade 효과
                    isanimated.toggle()
                }
                
            }
            Spacer()
            RoundedRectangle(cornerRadius: isanimated ? 50 : 25)    //코너값 변화
                .fill(isanimated ? Color.red : Color.gray)    //컬러 변화
                .frame(
                    width: isanimated ? 100 : 300,    //가로 변화
                    height: isanimated ? 100 : 300)    //세로 변화
            Spacer()
        }
    }
}

#Preview {
    ThreeWeelky()
}

fileprivate struct SheetView: View {
    @State private var isShowSheet: Bool = false
    @State private var isShowFullScreent: Bool = false

    var body: some View {
        VStack {
            Button("Show Sheet") {
                isShowSheet.toggle()
            }
            Button("Show FullScreen") {
                isShowFullScreent.toggle()
            }
        }
        .sheet(isPresented: $isShowSheet) {
            ZStack {
                Color.orange
                    .ignoresSafeArea()
                
                
                Text("Sheet!!!")
                
            }
            .background(.orange)
        }
        .fullScreenCover(isPresented: $isShowFullScreent) {
            FullScreenView()
        }
    }
}

fileprivate struct FullScreenView: View {
    @Environment(\.dismiss) private var dismiss

    var body: some View {
        ZStack {
            Color.orange
                .ignoresSafeArea()
            
            Text("Sheet!!!")
                .onTapGesture {
                    dismiss()
                }
        }
        .background(.orange)
    }
}

fileprivate struct ToggleView: View {
    @State private var isToggle: Bool = false
    
    var body: some View {
        VStack {
            Toggle("토글 버튼", isOn: $isToggle)
                .background(isToggle ? .green : .red)
        }
    }
}

fileprivate struct PickerView: View {
    @State private var backgroundColor: Color = .white
    
    var body: some View {
        ZStack {
            backgroundColor
            
            VStack {
                WheelPickerView()
                MenuPickerView()
                SegmentPickerView()
                ColorPickerView(color: $backgroundColor)
            }
        }
    }
}

fileprivate struct WheelPickerView: View {
    @State private var selectedNumber: Int = 0
    
    var body: some View {
        VStack {
            Text("\(selectedNumber)")
            Picker("랜덤 숫자", selection: $selectedNumber) {
                ForEach((0..<10)) { i in
                    Text("\(i)")
                        .foregroundColor(.red)
                        .tag(i)
                }
            }
            .pickerStyle(WheelPickerStyle())
            .background(Color.gray.opacity(0.5))
        }
    }
}

fileprivate struct MenuPickerView: View {
     @State private var selected:String = ""
     
    private let category:[String]  = ["포항","서울","인천"]
          
     var body: some View {
         VStack {
             Text("\(selected)")
             Picker("랜덤 숫자", selection: $selected) {
                 ForEach(category,id: \.self) { cat in
                     Text("\(cat)")
                         .foregroundColor(.red)
                         .tag(cat)
                 }
             }
             .pickerStyle(MenuPickerStyle())
         }
     }
}

fileprivate struct SegmentPickerView: View {
    @State private var selected:String = ""
    
    private let category:[String]  = ["포항","서울","인천"]

    var body: some View {
        VStack {
            Text("\(selected)")
                Picker("랜덤 숫자", selection: $selected) {
                ForEach(category,id: \.self) { cat in
                    Text("\(cat)")
                        .foregroundColor(.red)
                        .tag(cat)
                }
            }
            .pickerStyle(SegmentedPickerStyle())
        }
    }
}

fileprivate struct ColorPickerView: View {
    @Binding var color: Color
    
    var body: some View {
        VStack {
            ColorPicker("Background Color", selection: $color)
        }
        
    }
}

fileprivate struct FocusStateView: View {
    enum FocusField: Hashable {
        case id
        case passsword
    }
    
    @State var id: String = ""
    @State var password: String = ""
    @FocusState private var focusField:  FocusField?
    
    var body: some View {
        VStack {
            TextField("id", text: $id)
                .padding()
                .background(Color(uiColor: .secondarySystemFill))
                .focused($focusField, equals: .id)
                .submitLabel(.next)
            
            TextField("password", text: $password)
                .padding()
                .background(Color(uiColor: .secondarySystemFill))
                .focused($focusField, equals: .passsword)
                .submitLabel(.done)
            
            Button("LogIn") {
                if id.isEmpty {
                    focusField = .id
                } else if password.isEmpty {
                    focusField = .passsword
                } else {
                    print("ok")
                }
            }
        }
        .padding()
    }
}

```