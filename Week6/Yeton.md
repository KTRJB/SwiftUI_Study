<img width="349" alt="스크린샷 2024-01-18 오후 8 27 55" src="https://github.com/KTRJB/SwiftUI_Study/assets/98514397/a8938161-e679-480d-ba6c-5fa7089d35ba">

동작 테스트 정도 ...!

```swift
//
//  51~60.swift
//  SwiftUI-Playgrounds
//
//  Created by 이예은 on 1/18/24.
//

import SwiftUI
import AVKit

enum Theme: String {
  case light
  case dark
  case custom
}

struct View51: View {
  @EnvironmentObject var themeManager: ThemeManager
  let url = URL(string: "https://picsum.photos/400")

  var body: some View {
    //    Text("Current Theme: \(themeManager.currentTheme.rawValue)")

    VStack {
      AsyncImage(url: url) { phase in
        switch phase {
        case .empty:
          ProgressView()
        case .success(let image):
          image
            .resizable()
            .scaledToFit()
            .frame(width: 100, height: 100)
            .cornerRadius(20)
        case .failure:
          Image(systemName: "questionmark")
            .font(.headline)
        default:
          Image(systemName: "questionmark")
            .font(.headline)
        }
      }


      VStack {
        Spacer()

        VStack {
          RoundedRectangle(cornerRadius: 4)
            .frame(width: 50, height: 4)
            .padding()
          Spacer()
        }
        .frame(height: 350)
        .frame(maxWidth: .infinity)
        .background(.ultraThickMaterial)
        .cornerRadius(30)
      }
      .ignoresSafeArea()
      .background(
        Image(systemName: "house.fill")
      )

      Button("Button Title") {
      }
      .frame(height: 55)
      .frame(maxWidth: .infinity)
      .buttonStyle(.bordered)

      TabView {
        Color.red
          .tabItem {
            Image(systemName: "heart.fill")
            Text("Hello")
          }
          .badge(3)
      }

      VStack {
        Button("Play sound 1"){
          SoundManager.instance.playSound(sound: .tada)
        }

        Button("Play sound 2"){
          SoundManager.instance.playSound(sound: .duck)
        }
      }
      .padding(40)

      Button("success") {
        HapticManager.instance.notification(type:.success)
      }
    }
  }
}

class ThemeManager: ObservableObject {
  @Published var currentTheme: Theme = .light
}

class SoundManager {
  static let instance = SoundManager()
  var player: AVAudioPlayer?

  enum SoundOption: String {
    case tada
    case duck
  }

  func playSound(sound:SoundOption) {
    guard let url = Bundle.main.url(forResource: sound.rawValue, withExtension: ".mp3") else {
      return
    }

    do {
      player = try AVAudioPlayer(contentsOf: url)
      player?.play() // 재생
    }   catch let error {
      print("Error playing sound. \(error.localizedDescription)")
    }

  }
}

class HapticManager {
  static let instance = HapticManager() // 싱글톤

  func notification(type:UINotificationFeedbackGenerator.FeedbackType) {
    let generator = UINotificationFeedbackGenerator()
    generator.notificationOccurred(type) //진동
  }

  func impact(style:UIImpactFeedbackGenerator.FeedbackStyle) {
    let generator = UIImpactFeedbackGenerator(style: style)
    generator.impactOccurred() // 진동
  }

}
#Preview {
  View51()
}



```
