```swift
//
//  Week6DayView.swift
//  SwiftUIStudy
//
//  Created by Groot on 1/18/24.
//

import SwiftUI

struct Week6DayView: View {
    @StateObject var viewModel = Week6DayViewModel()
    let url = URL(string: "https://i.ytimg.com/vi/R2U0ek7uw-U/maxresdefault.jpg")!
    @State var isClicked = false
    
    var body: some View {
        NavigationView {
            VStack {
                NavigationLink("누르지 마세요") {
                    Week6DaySubView(viewModel: viewModel)
                }
                
                Spacer()
                
                Button(action: {
                    isClicked.toggle()
                }, label: {
                    if isClicked {
                        AsyncImage(
                            url: url) { image in
                                image
                                    .resizable()
                                    .scaledToFit()
                                    .frame(width: 200, height: 300)
                            } placeholder: {
                                ProgressView()
                            }
                    } else {
                        Text("I AM Groot")
                    }
                })
                .frame(height: 55)
                .frame(maxWidth: .infinity)
                .buttonStyle(.plain)
                
                Spacer()
            }
        }
    }
}

struct Week6DaySubView: View {
    @ObservedObject var viewModel: Week6DayViewModel
    
    var body: some View {
        NavigationLink("제발 누르지 마세요") {
            Week6DaySubSubView(viewModel: viewModel)
        }
        .onAppear {
            viewModel.number += 1
        }
        .onDisappear {
            viewModel.number -= 1
        }
    }
}

struct Week6DaySubSubView: View {
    @ObservedObject var viewModel: Week6DayViewModel
    
    var body: some View {
        TabView {
            Text("당신은 \(viewModel.number)번이나 무시하고 들어오셨습니다.\n 고집불통인 당신, 나가주세요")
                .tabItem {
                    Image(systemName: "heart.fill")
                }
                .badge(viewModel.number)
        }
        .onAppear {
            viewModel.number += 1
            HapticManager.instance.impact(style: .soft)
            SoundManager.instance.playSound()
        }
        .onDisappear {
            viewModel.number -= 1
            SoundManager.instance.stopSound()
        }
    }
}

final class Week6DayViewModel: ObservableObject {
    @Published var number = 0
}

#Preview {
    Week6DayView()
}

class HapticManager {
    
    static let instance = HapticManager() // 싱글톤

    
    func notification(type:UINotificationFeedbackGenerator.FeedbackType){
        
        
        let generator = UINotificationFeedbackGenerator()
        generator.notificationOccurred(type) //진동
    }
    
    func impact(style:UIImpactFeedbackGenerator.FeedbackStyle){
        let generator = UIImpactFeedbackGenerator(style: style)
        generator.impactOccurred() // 진동
    }
    
}

import AVKit

class SoundManager {
    
    static let instance = SoundManager() // 싱글톤
    var player: AVAudioPlayer?
    
    func playSound() {
        guard let url = Bundle.main.url(forResource: "sample", withExtension: ".mp3") else { return }
        
        do {
            player = try AVAudioPlayer(contentsOf: url)
            player?.play() // 재생
        }   catch let error {
            print("Error playing sound. \(error.localizedDescription)")
        }
    }
    
    func stopSound() {
        player?.stop()
        player = nil
    }
}

```
