# Browse-View
SwiftUI RealityKit
# The first part
![IMG_0718](https://github.com/S-way520/Browse-View/assets/95877651/6a725c9e-32c9-4bbe-9281-bfda18cd2401)
# The second part
Code file 1
```swift
import SwiftUI
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```
Code file 2
```swift
import SwiftUI
import RealityKit
struct ContentView: View {
    @State var Browse: Bool = false
    var body: some View {
        ZStack {
            ARViewContainer().edgesIgnoringSafeArea(.all)
            ControlView(Browse: $Browse)
        }
    }
}
struct ARViewContainer: UIViewRepresentable {
    func makeUIView(context: Context) -> ARView {
        let arView = ARView(frame: .zero)
        return arView
    }
    func updateUIView(_ uiView: ARView, context: Context) {
        // 执行额外的内容
    }
}
```
Code file 3
```swift
import SwiftUI
struct ControlView: View {
    @State var Hide = false
    @Binding var Browse: Bool
    var body: some View {
        VStack {
            Spacer()
            ZStack {
                BottomBar(Browse: $Browse)
                    .offset(y: Hide ? 120 : 0)
                    .animation(.easeInOut(duration: 0.5), value: Hide)
                HideBar(Hide: $Hide)
                    .offset(x: -130, y: -5)
            }
        }
        
    }
}
struct HideBar: View {
    @Binding var Hide: Bool
    var body: some View {
        HStack {
            ZStack {
                Button(action: {
                    self.Hide.toggle()
                }, label: {
                    Image(systemName: "greaterthan.circle")
                        .rotationEffect(Angle(degrees: Hide ? 90 : 0))
                        .font(.title)
                        .background(Color.black.opacity(0.8))
                        .cornerRadius(12, antialiased: true)
                        .padding(10)
                })
                
            }
            
        }
    }
}
struct BottomBar: View {
    @Binding var Browse: Bool
    var body: some View {
        HStack {
            //最近放置
            BoButton(name: "clock") {
                print("one Tab")
            }
            Spacer()
                .frame(width: 40)
            //预览
            BoButton(name: "rectangle.grid.3x2") {
                self.Browse.toggle()
            }.sheet(isPresented: $Browse) {
                BrowseView(Browse: $Browse)
                    //.presentationDetents([.medium, .large])
            }
            Spacer()
                .frame(width: 40)
            //设置
            BoButton(name: "gear") {
                print("there Tab")
            }
        }
        .frame(maxHeight: 40)
        .padding(.horizontal, 10)
        .padding(10)
        .background(Color.black.opacity(0.5))
        .cornerRadius(20, antialiased: true)
        .padding(.bottom, 10)
    }
}
struct BoButton: View {
    let name: String
    let action: () -> Void
    var body: some View {
        Button(action: {
            self.action()
        }, label: {
            Image(systemName: name)
                .font(.title)
        })
        
    }
}
```
Code file 4
```swift
import SwiftUI
struct BrowseView: View {
    @Binding var Browse: Bool
    @State var selectedRect = false
    var body: some View {
        NavigationStack {
            ScrollView(showsIndicators: false) {
                ModelsByCategoryGrid(Browse: $Browse)
            }
            .navigationTitle("八年级 上册")
            .navigationBarItems(leading: Menu {
                Picker(selection: $selectedRect, label: Text("Sorting options")) {
                    Text("初中 八年级 上册 人教版").tag(0)
                    Text("初中 八年级 下册 人教版").tag(1)
                    Text("初中 九年级 全一册 人教版").tag(2)
                    Text("高中 必修一 人教版").tag(3)
                    Text("高中 必修二 人教版").tag(4)
                    Text("高中 必修三 人教版").tag(5)
                    Text("高中 选择性必修一 人教版").tag(6)
                    Text("高中 选择性必修二 人教版").tag(7)
                    Text("高中 选择性必修三 人教版").tag(8)
                }
            }label: {
                HStack {
                    Image(systemName: "books.vertical")
                    Text("书籍")
                }
            })
            .navigationBarItems(trailing: Button(action: {
                self.Browse.toggle()
            }, label: {
                Text("完成")
            }))
        }
    }
}

struct ModelsByCategoryGrid: View {
    @Binding var Browse: Bool
    let models = Models()
    var body: some View {
        VStack {
            ForEach(ModelCategory.allCases, id: \.self) { category in
              //  if let modelsByCategory = models.get(category: category) {
                HGrid(Browse: $Browse, title: category.label, items: models.get(category: category))
              //  }
            }
        }
    }
}

struct HGrid: View {
    @Binding var Browse: Bool
    var title: String
    var items: [Model]
    private let gridItemLayout = [GridItem(.fixed(150))]
    var body: some View {
        VStack(alignment: .leading) {
            Separater()
            Text(title)
                .font(.title2).bold()
                .padding(.leading, 20)
                .padding(.top, 20)
            ScrollView(.horizontal, showsIndicators: false) {
                LazyHGrid(rows: gridItemLayout, spacing: 30) {
                    ForEach(0..<items.count, id: \.self) {index in
                        let model = items[index]
                        ItemButton(model: model) {
                            print("预览: 选择\(model.name)放置")
                            self.Browse = false
                        }
                    }
                }
                .padding(.horizontal, 20)
            }
        }
    }
}
struct ItemButton: View {
    let model: Model
    let action: () -> Void
    var body: some View {
        Button(action: {
            self.action()
        }){
            Image(uiImage: self.model.thumbnail)
                .resizable()
                .frame(height: 150)
                .aspectRatio(1/1, contentMode: .fit)
                .background(Color(UIColor.secondarySystemFill))
                .cornerRadius(8.0, antialiased: true)
        }
    }
}
struct Separater: View {
    var body: some View {
        Divider()
            .padding(.horizontal, 20)
            .padding(.vertical, 10)
    }
}
```
Code file 5
```swift
import SwiftUI
import RealityKit
import Combine

enum ModelCategory: CaseIterable {
    case table
    case chair
    case decor
    case light
    var label: String {
        get {
            switch self {
            case .table:
                return "第一章 机械运动"
            case .chair:
                return "第二章 声现象"
            case .decor:
                return "第三章 物态变化"
            case .light:
                return "第四章 光现象"
            }
        }
    }
}

class Model {
    var name: String
    var category: ModelCategory
    var thumbnail: UIImage
    var modelEntity: ModelEntity?
    var scaleCompensation: Float
    
    init(name: String, category: ModelCategory, scaleCompensation: Float = 1.0) {
        self.name = name
        self.category = category
        self.thumbnail = UIImage(named: name) ?? UIImage(systemName: "photo")!
        self.scaleCompensation = scaleCompensation
    }
    
}

struct Models {
    var all: [Model] = []
    init(){
        //Table
        let dinigTable = Model(name: "dining_table", category: .table, scaleCompensation: 0.32/100)
        let familyTable = Model(name: "family_table", category: .table, scaleCompensation: 0.32/100)
        self.all += [dinigTable, familyTable]
        //Chairs
        let diningChair = Model(name: "dining_Chair", category: .chair, scaleCompensation: 0.32/100)
        let eamesChairWhite = Model(name: "eames_chair_white", category: .chair, scaleCompensation: 0.32/100)
        let eamesChairWoodgrain = Model(name: "eames_chair_woodgrain", category: .chair, scaleCompensation: 0.32/100)
        self.all += [diningChair, eamesChairWhite, eamesChairWoodgrain]
        //Decor
        let cupSaucerSet = Model(name: "cup_saucer_set", category: .decor, scaleCompensation: 0.32/100)
        self.all += [cupSaucerSet]
        //Lights
        let floorLampClassic = Model(name: "floor_lamp_classic", category: .light, scaleCompensation: 0.32/100)
        self.all += [floorLampClassic]
    }
    func get(category: ModelCategory) -> [Model] {
        return all.filter({$0.category == category})
    }
}
```
