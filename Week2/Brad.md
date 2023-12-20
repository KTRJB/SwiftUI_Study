## Code

```swift
struct Address {
    let image: String
    let name: String
}

final class ContentViewModel: ObservableObject {
    @Published var address: [Address] = []
    
    private var store = ["아빠", "엄마", "형", "나"]
    
    func request() {
        address.append(
            .init(image: "person.circle.fill", name: store.randomElement() ?? "")
        )
    }
    
    func remove(indexSet: IndexSet) {
        address.remove(atOffsets: indexSet)
    }
}

struct ContentView: View {
    @StateObject private var vm = ContentViewModel()
    
    @State private var isTap = false
   
    var body: some View {
        ZStack {
            Color.white
                .ignoresSafeArea()
            
            NavigationStack {
                if isTap {
                    List {
                        ForEach(
                            vm.address,
                            id: \.name
                        ) { info in
                            AddressCell(info: info)
                        }
                        .onDelete { indexSet in
                            vm.remove(indexSet: indexSet)
                        }
                    }
                    .listStyle(.plain)
                } else {
                    Text("\(isTap.description)")
                }
            }
        }
        .overlay(alignment: .bottom) {
            Button {
                isTap.toggle()
            } label: {
                Text("Hidden Button")
                    .padding()
                    .overlay {
                        RoundedRectangle(cornerRadius: 10).stroke(lineWidth: 1)
                    }
            }
            
        }
       
        .navigationTitle("즐겨찾기")
        .toolbar {
            ToolbarItem(placement: .topBarTrailing) {
                Button {
                    vm.request()
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