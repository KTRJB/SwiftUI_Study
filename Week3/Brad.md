## 자료

- https://github.com/yongbeomkwak/import-SwiftUI

## 진행 상황

- 20 ~ 30

## 코드

```swift
struct ContentView: View {
    @State private var isTap: Bool = false
    
    var body: some View {
        BindingPropertyWrapperView(
            isTap: $isTap
        )
        
        if isTap {
            Text("hi")
        }
        
//        PathView()
                
//        ListStyleView()
       
//        LazyVStackView()
        
//        ContentMenuView()
        
//        TextFieldTextEditorView()

//        AlertView()
        
//        ActionSheetView()
        
//        PopoverView()
    }
}

fileprivate struct BindingPropertyWrapperView: View {
    @Binding private var isTap: Bool
    
    init(isTap: Binding<Bool>) {
        self._isTap = isTap
    }
    
    var body: some View {
        VStack {
            Button {
                isTap.toggle()
            } label: {
                Text("Button")
            }
        }
    }
}

fileprivate struct PathView: View {
    var body: some View {
        Path { path in
            path.move(to: CGPoint(x: 300, y: 0))
            path.addLine(to: CGPoint(x: 200, y: 200))
            path.addLine(to: CGPoint(x: 0, y: 200))
            path.addLine(to: CGPoint(x: 0, y: 0))
            path.closeSubpath()
        }
        .stroke()
        .padding()
    }
}

fileprivate struct ContentMenuView: View {
    var body: some View {
        
        Text("ContentMenu")
            .contextMenu(ContextMenu(menuItems: {
                Button {
                     
                } label: {
                    Text("Menu Item 1")
                }
                /*@START_MENU_TOKEN@*/Text("Menu Item 2")/*@END_MENU_TOKEN@*/
                /*@START_MENU_TOKEN@*/Text("Menu Item 3")/*@END_MENU_TOKEN@*/
            }))
    }
}

fileprivate struct ListStyleView: View {
    var body: some View {
        List {
            ForEach(0...100, id: \.self) { element in
                Text(element.description)
                    .onAppear {
                        print(element)
                    }
            }
        }
        .listStyle(.sidebar)
    }
}

fileprivate struct LazyVStackView: View {
    var body: some View {
        ScrollView {
            VStack {
                ForEach(0...1000, id: \.self) { element in
                    Text(element.description)
                        .onAppear {
                            print(element)
                        }
                }
            }
        }
    }
}

fileprivate struct TextFieldTextEditorView: View {
    @State private var text: String = ""
    @State private var textEditor: String = ""

    var body: some View {
        VStack {
            Spacer()
            TextField("TextField", text: $text)
            
            ZStack {
                if textEditor.isEmpty {
                    VStack {
                        HStack {
                            Text("Placeholder")
                                .padding(6)
                                .foregroundColor(.secondary)
                            Spacer()
                        }
                        
                        Spacer()
                    }
                }
                    TextEditor(text: $textEditor)
                        .colorMultiply(
                            Color(uiColor: .secondarySystemFill)
                        )
                
            }
            .frame(height: 200)

            Spacer()
        }
    }
}

fileprivate struct AlertView: View {
    @State private var isTap = false
    
    var body: some View {
        Button {
            isTap.toggle()
        } label: {
            Text("show Alert")
        }
        .alert("Hi", isPresented: $isTap) {
            Button(role: .cancel) {
                
            } label: {
                Text("cancel")
            }

            Button(role: .destructive) {
                
            } label: {
                Text("Ok")
            }
        }
    }
}

fileprivate struct ActionSheetView: View {
    @State private var isTap = false

    var body: some View {
        Button {
            isTap.toggle()
        } label: {
            Text("show Action Sheet")
        }
        .confirmationDialog("title", isPresented: $isTap) {
            Button {
                
            } label: {
                Text("Button 1")
            }
        } message: {
            Text("message")
        }
    }
}

fileprivate struct PopoverView: View {
    @State private var isShowPopoverView: Bool = false
    @State private var isShowFullScreenView: Bool = false
    
    var body: some View {
        VStack {
            Button {
                isShowPopoverView.toggle()
            } label: {
                Text("show Popover")
            }
            
            Button {
                isShowFullScreenView.toggle()
            } label: {
                Text("show Full Screen")
            }
        }
        .popover(isPresented: $isShowPopoverView) {
            Text("Hi Pop over View")
        }
        .fullScreenCover(isPresented: $isShowFullScreenView) {
            FullScreenView()
        }
    }
    
}

fileprivate struct FullScreenView: View {
    @Environment(\.dismiss) private var dissmiss

    var body: some View {
        ZStack {
            Color.orange
                .ignoresSafeArea()
            
            HStack {
                Text("Hi Full Screen")
                
                Button {
                    dissmiss()
                } label: {
                    Text("Dissmiss Button")
                }
            }
        }
    }
}
```