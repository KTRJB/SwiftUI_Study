# 20231207


## 미리보기

<img width="548" alt="스크린샷 2023-12-07 오후 10 09 38" src="https://github.com/KTRJB/SwiftUI_Study/assets/45350356/14672963-2757-4e4b-b118-c6ea30d910f1">


## 코드

```swift
import SwiftUI

struct Address {
    let image: String
    let name: String
}

struct ContentView: View {
    @State private var address: [Address] = [
        .init(image: "person.circle.fill", name: "아빠"),
        .init(image: "person.circle.fill", name: "엄마"),
        .init(image: "person.circle.fill", name: "형"),
        .init(image: "person.circle.fill", name: "나")
    ]
    
    var body: some View {
        NavigationStack {
            List {
                ForEach(
                    address,
                    id: \.name
                ) { info in
                    AddressCell(info: info)
                }
                .onDelete { indexSet in
                    address.remove(atOffsets: indexSet)
                }
            }
            .listStyle(.plain)
        }
        .navigationTitle("즐겨찾기")
        .toolbar {
            ToolbarItem(placement: .topBarTrailing) {
                Button {
                    
                } label: {
                    Image(systemName: "plus")
                }
            }
            ToolbarItem(placement: .topBarLeading) {
                EditButton()
            }
        }
    }
}

#Preview {
    NavigationStack {
        ContentView()
    }
}

fileprivate struct AddressCell: View {
    var info: Address
    
    init(info: Address) {
        self.info = info
    }
    
    var body: some View {
        HStack {
            Image(systemName: info.image)
                .resizable()
                .frame(width: 25, height: 25)
                .foregroundStyle(.tint)
            
            VStack(alignment: .leading) {
                Text(info.name)
                
                HStack {
                    Image(systemName: "phone.fill")
                    Text("휴대전화")
                }
                .font(.footnote)
                .foregroundColor(.gray)
            }
            Spacer()
            
            Image(systemName: "info.circle")
                .foregroundStyle(.tint)
        }
    }
}

```