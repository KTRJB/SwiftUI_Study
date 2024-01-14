```swift
struct Weekly_5: View {
    @Environment(\.colorScheme) var colorScheme

    @State private var selectedDate: Date = Date()
    @State private var slider: Double = 5
    
    private var dateFormatter: DateFormatter {
        let formatter = DateFormatter()
        formatter.timeStyle = .medium
        return formatter
    }
    
    var body: some View {
        TabView {
            NavigationView {
                VStack {
                    NavigationLink("Link") {
                        Text("HI")
                    }
                    VStack {
                        DatePicker("DatePicker", selection: $selectedDate)
                        
                        Text("Today \(dateFormatter.string(from: selectedDate)) Rading: \(slider)")
                        
                        Slider(value: $slider, in: 0...10, step: 1.0)
                            .tint(colorScheme == .light ? .green : .red)
                    }
                }
            }
            .padding()
            .onAppear {
                print("Picker View Appear!!!")
            }
            .onDisappear {
                print("Picker View Disappear!!!")
            }
            .tabItem { Text("Picker") }
            
            VStack {
                Stepper("Stepper Value: \(slider)") {
                    if slider > 9 {
                        return
                    }
                    withAnimation(.easeInOut) {
                        slider += 1
                    }
                } onDecrement: {
                    if slider < 1 {
                        return
                    }
                    withAnimation(.easeInOut) {
                        slider -= 1
                    }
                }
            }
            .padding()
            .onAppear {
                print("Stepper View Appear!!!")
            }
            .onDisappear {
                print("Stepper View Disappear!!!")
            }
            .tabItem { Text("Stepper") }
        }
    }
    
    func fetchItem() async {
        
    }
}

#Preview {
    Weekly_5()
}
```