<img width="308" alt="스크린샷 2024-01-14 오후 9 23 19" src="https://github.com/KTRJB/SwiftUI_Study/assets/98514397/62e91aa8-cd29-45d9-9942-ee3f59858fac">
<img width="308" alt="스크린샷 2024-01-14 오후 9 23 43" src="https://github.com/KTRJB/SwiftUI_Study/assets/98514397/5e60e34f-4799-4b65-8ea3-e8b4a042f666">


```swift
import SwiftUI

struct _1_40: View {
    var name = ["주디", "그루트", "수꿍", "브래드", "예톤"]
    var imageName = ["heart.fill", "heart.circle.fill", "bolt.heart.fill", "bolt.heart", "heart"]
    let columns = [
        GridItem(.flexible()),
        GridItem(.flexible()),
        GridItem(.flexible())
    ]
    
    @State var selectInfo = [(String, String)]()
    @State var showSheet: Bool = false
    @State var selectName: String = ""
    
    var body: some View {
        NavigationView {
            ScrollView {
                LazyVGrid(columns: columns, spacing: 20) {
                    ForEach(name, id: \.self) { nm in
                        Button(action: {
                            selectName = nm
                            showSheet.toggle()
                        }, label: {
                            Text("\(nm)")
                                .foregroundColor(.black)
                                .font(.headline)
                                .padding(20)
                                .background(Color.white.cornerRadius(10))
                        })
                        .sheet(isPresented: $showSheet, content: {
                            LazyVGrid(columns: columns, spacing: 20) {
                                ForEach(imageName, id: \.self) { imageName in
                                    Image(systemName: imageName)
                                        .onTapGesture {
                                            self.selectInfo.append((selectName, imageName))
                                            
                                            showSheet = false
                                        }
                                }
                            }
                        })
                    }
                }
                .padding(.horizontal)
                
                NavigationLink {
                    InfoView(info: $selectInfo)
                } label: {
                    Text("확인하기")
                }
                
                
            }
        }
    }
}

#Preview {
    _1_40()
}


struct InfoView: View {
    @Binding var info: [(String, String)]
    
    @State var isAnimating: Bool = false
    let timing:Double = 5.0
    
    var body: some View {
        VStack {
            HStack {
                Text(info[0].0)
                Image(systemName: info[0].1)
            }
            
            HStack {
                Text(info[1].0)
                Image(systemName: info[1].1)
            }
            
            HStack {
                Text(info[2].0)
                Image(systemName: info[2].1)
            }
            
            HStack {
                Text(info[3].0)
                Image(systemName: info[3].1)
            }
            
            HStack {
                Text(info[4].0)
                Image(systemName: info[4].1)
            }
            .onTapGesture {
                isAnimating = true
            }
            .frame(width: isAnimating ? 350 : 50, height: 100)
            .animation(.easeInOut, value: isAnimating)
        }
    }
}
```
