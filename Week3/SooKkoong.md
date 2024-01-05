```swift
import SwiftUI

// Memo 구조체: Identifiable 프로토콜을 채택하여 각 메모가 고유한 식별자를 갖도록 함
struct Memo: Identifiable {
    var id = UUID() // 각 메모에 대한 고유 식별자
    var content: String // 메모 내용
}

struct Week3View: View {
    // @State: 뷰의 상태를 관리하는 프로퍼티 래퍼. 뷰의 상태가 변경될 때마다 뷰가 업데이트됨
    @State private var memos = [Memo(content: "샘플 메모")] // 메모 목록
    @State private var newMemoContent = "" // 새로 추가할 메모의 내용
    @State private var selectedMemoContent = "" // 선택된 메모의 내용을 저장할 변수
    @State private var showAlert = false // 알림창 표시 여부
    @State private var showConfirmationDialog = false // (구)액션 시트 표시 여부
    @State private var showPopover = false // Popover 표시 여부

    var body: some View {
        NavigationView {
            List {
                // 첫 번째 섹션: 새 메모 추가
                Section {
                    TextField("새 메모 추가", text: $newMemoContent, onCommit: {
                        addNewMemo() // 새 메모 추가 함수 호출
                    })
                }

                // 두 번째 섹션: 기존 메모 목록
                Section {
                    ForEach(memos) { memo in // 메모 목록을 순회하는 ForEach
                        MemoRow(memo: memo) // 메모 표시를 위한 MemoRow 뷰
                            .contextMenu { // 컨텍스트 메뉴 추가
                                Button("상세 정보") {
                                    selectedMemoContent = memo.content // 선택한 메모 텍스트 업데이트
                                    showAlert = true // 알림창 표시
                                }
                                Button("삭제") {
                                    deleteMemo(memo: memo) // 메모 삭제 함수 호출
                                }
                            }
                    }
                    .onDelete(perform: deleteMemo) // 스와이프 삭제 기능
                    .onMove(perform: moveMemo) // 목록 항목 이동 기능
                }
            }
            .listStyle(.insetGrouped) // 리스트 스타일 지정
            .toolbar { // 네비게이션 바 툴바 항목
                ToolbarItem(placement: .navigationBarTrailing) {
                    EditButton() // 편집 버튼
                }
                ToolbarItem(placement: .navigationBarLeading) {
                    Button(action: {
                        showConfirmationDialog = true // (구)액션 시트 표시
                    }) {
                        Image(systemName: "plus") // 'plus(+)' 이미지 표시
                    }
                }
                ToolbarItem(placement: .navigationBarLeading) {
                    if memos.count >= 5 { // 메모가 5개 이상 차면
                        Button(action: {
                            showPopover = true // 팝오버
                        }) {
                            Image(systemName: "star.fill")
                        }
                        .popover(isPresented: $showPopover) {
                            StarShape() // 팝오버하여 별 모양을 그림
                                .stroke(Color.blue, lineWidth: 3)
                                .frame(width: 200, height: 200)
                                .padding()
                        }
                    }
                }
            }
            .confirmationDialog("추가 옵션", isPresented: $showConfirmationDialog) {
                Button("새 메모 추가") { addNewMemo() } // 새 메모 추가 버튼 추가
                Button("취소", role: .cancel) {} // 취소 버튼 추가
            }
            .alert("당신이 입력한 텍스트는 \(selectedMemoContent) 입니다!", isPresented: $showAlert) {
                Button("확인", role: .cancel) {} // 확인 버튼 추가
            }
            .navigationTitle("메모") // 네비게이션 타이틀 설정
        }
    }

    // 새 메모 추가 함수
    private func addNewMemo() {
        // 비어있지 않은지 확인
        guard !newMemoContent.isEmpty else {
            return
        }
        memos.append(Memo(content: newMemoContent)) // 메모 목록에 추가
        newMemoContent = "" // 입력 필드 초기화
    }

    // 메모 삭제 함수 1 (IndexSet 사용)
    private func deleteMemo(at offsets: IndexSet) {
        memos.remove(atOffsets: offsets) // 주어진 인덱스에 해당하는 메모 삭제
    }

    // 메모 삭제 함수 2 (특정 메모 인스턴스 사용)
    private func deleteMemo(memo: Memo) {
        memos.removeAll { $0.id == memo.id } // 특정 ID를 가진 메모 삭제
    }

    // 메모 이동 함수
    private func moveMemo(from source: IndexSet, to destination: Int) {
        memos.move(fromOffsets: source, toOffset: destination) // 메모 위치 변경
    }
}

// MemoRow 구조체: 개별 메모를 표시하는 뷰
struct MemoRow: View {
    var memo: Memo // 표시할 메모 인스턴스

    var body: some View {
        Text(memo.content) // 메모 내용 표시
    }
}

// StarShape 구조체: 별을 그리기 위한 Shape
struct StarShape: Shape {
    func path(in rect: CGRect) -> Path {
        Path { path in
            // 별 모양을 그리기 위한 설정
            let starExtrusion: CGFloat = 30
            let center = CGPoint(x: rect.width / 2, y: rect.height / 2)
            let pointsOnStar = 5

            var angle: CGFloat = -CGFloat.pi / 2
            let angleIncrement = CGFloat.pi * 2 / CGFloat(pointsOnStar)
            let radius: CGFloat = rect.width / 2
            let innerRadius = radius / 2.5

            // 첫 번째 꼭지점으로 이동
            var firstPoint = true

            for _ in 1...pointsOnStar {
                let point = pointFrom(angle: angle, radius: radius, offset: center)
                let nextPoint = pointFrom(angle: angle + angleIncrement / 2.0, radius: innerRadius, offset: center)
                angle += angleIncrement

                if firstPoint {
                    firstPoint = false
                    path.move(to: point)
                }

                path.addLine(to: point)
                path.addLine(to: nextPoint)
            }

            path.closeSubpath()
        }
    }

    private func pointFrom(angle: CGFloat, radius: CGFloat, offset: CGPoint) -> CGPoint {
        return CGPoint(x: offset.x + radius * cos(angle), y: offset.y + radius * sin(angle))
    }
}

struct Week3View_Previews: PreviewProvider {
    static var previews: some View {
        Week3View()
    }
}
```
