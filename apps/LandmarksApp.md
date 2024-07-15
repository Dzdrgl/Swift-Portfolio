# Landmarks App

## Table of Contents

- [Project Objective](#project-objective)
- [Screenshots](#screenshots)
- [Frameworks Used](#frameworks-used)
- [Key Learnings](#key-learnings)
- [Important Code Snippets](#important-code-snippets)

## Project Objective

The Landmarks App is designed to help users discover prominent natural features such as lakes, rivers, and mountains. The app offers two main views—grid and list—categorizing places for easy accessibility. Clicking on a place takes users to a detailed page where they can find information about the location and view it on a map using MapKit integration.

## Screenshots

<div style="display:flex; justify-content:space-between; align-items:center;">
    <img width="300" alt="featured view" src="https://github.com/Dzdrgl/SwiftPortfolio/blob/main/assests/landmarks-featured.png?raw=true">
    <img width="300" alt="detail view" src="https://github.com/Dzdrgl/SwiftPortfolio/blob/main/assests/landmarks-detail.png?raw=true">
    <img width="300" alt="settings view" src="https://github.com/Dzdrgl/SwiftPortfolio/blob/main/assests/landmarks-settings.png?raw=true">
    <img width="300" alt="settings view" src="https://github.com/Dzdrgl/SwiftPortfolio/blob/main/assests/landmarks-badge.png?raw=true">
</div>

## Frameworks Used

- **SwiftUI**: Used for building the user interface with a declarative syntax.
- **UIKit**: Integrated within SwiftUI for more complex UI components not readily available in SwiftUI.
- **CoreLocation**: Utilized for transforming latitude and longitude data for map display.
- **MapKit**: Employed to provide interactive mapping functionalities.
- **CoreGraphics**: Applied for creating vector-based graphical elements.

## Key Learnings

During the development of the Landmarks app, I enhanced my skills in several crucial areas of iOS application development:

- **UIKit Integration in SwiftUI**: Gained experience in creating UIKit-based classes and views using `UIViewRepresentable` and `UIViewControllerRepresentable`, facilitating the integration of UIKit components such as `TextView`, `ButtonView`, `TableView`, and `ScrollView` within SwiftUI.
  
- **Data Management and Optimization**: Leveraged the `Equatable` protocol for optimizations and utilized `ObservableObject` and `@Published` for effective state management and reactive UI updates.

- **Advanced UI Components**: Developed proficiency in using various SwiftUI components such as pickers, sliders, and progress views, and created custom views to tailor the UI to specific requirements.

- **Design Patterns Mastery**: Achieved a deeper understanding of design patterns such as MVVM and MVC, which helped in writing cleaner, more maintainable code with reduced redundancy.

- **Custom Graphics and Shapes Creation**: Mastered using CoreGraphics to draw custom shapes and utilized `GeometryReader` and `Path` for intricate vector drawing, enhancing the visual appeal of the app.

## Important Code Snippets

Below are specific code examples demonstrating key functionalities and solutions implemented in the Africa App:

### Tab View Integration
This snippet sets up a tabbed navigation interface within the app, using TabView to switch between the "Featured" and "List" views, enhancing the app's navigational efficiency. 
```swift
struct ContentView: View {
    @State private var selection: Tab = .featured

    enum Tab {
        case featured
        case list
    }

    var body: some View {
        TabView(selection: $selection) {
            CategoryHome()
                .tabItem {
                    Label("Featured", systemImage: "star")
                }
                .tag(Tab.featured)

            LandmarkList()
                .tabItem {
                    Label("List", systemImage: "list.bullet")
                }
                .tag(Tab.list)
        }
    }
}
```
###Model Data Management
This class manages the data model of the app, leveraging ObservableObject to handle dynamic UI updates. It loads and categorizes landmark data, making extensive use of Swift's @Published for reactive updates.
```swift
final class ModelData: ObservableObject {
    @Published var landmarks: [Landmark] = load("landmarkData.json")
    var hikes: [Hike] = load("hikeData.json")
    @Published var profile = Profile.default

    var features: [Landmark] {
        landmarks.filter { $0.isFeatured }
    }

    var categories: [String: [Landmark]] {
        Dictionary(
            grouping: landmarks,
            by: { $0.category.rawValue }
        )
    }
}
```
###Custom Integration
This snippet demonstrates how to create a custom UIButton and UIPageView in SwiftUI, using UIViewRepresentable to integrate UIKit components effectively into SwiftUI, bridging the gap between the two UI frameworks.
```swift
struct MyButtonView:UIViewRepresentable{
    let buttonTitle:String
    let action:() -> Void
    func makeUIView(context: Context) -> UIButton {
        let button = UIButton(type: .system)
        button.setTitle(buttonTitle, for: .normal) // Buton başlığını ayarlayın.
        button.titleLabel?.font = .systemFont(ofSize: 22, weight: .bold) // Font ve boyutunu ayarlayın.
        button.backgroundColor = .lightGray // Arka plan rengini ayarlayın.
        button.layer.cornerRadius = 10 // Köşe yuvarlaklığını ayarlayın.
        
        // Button'a basıldığında ne olacağını tanımlayın.
        button.addTarget(context.coordinator, action: #selector(Coordinator.tapped), for: .touchUpInside)
        
        return button
    }
    func updateUIView(_ uiView: UIButton, context: Context) {
        uiView.setTitle(buttonTitle, for: .normal)
        uiView.setTitleColor(.red, for: .highlighted)
        uiView.setTitleColor(.gray, for: .disabled)
        uiView.setTitleColor(.green, for: .selected)
    }
    func makeCoordinator() -> Coordinator {
        Coordinator(parent: self)
    }
    
    class Coordinator:NSObject{
        var parent: MyButtonView
        
        init(parent: MyButtonView) {
            self.parent = parent
        }
        @objc func tapped(){
            self.parent.action()
        }
    }
}
struct PageControl: UIViewRepresentable {
    var numberOfPages: Int
    @Binding var currentPage: Int

    func makeCoordinator() -> Coordinator {
        Coordinator(self)
    }

    func makeUIView(context: Context) -> UIPageControl {
        let control = UIPageControl()
        control.numberOfPages = numberOfPages
        control.addTarget(
            context.coordinator,
            action: #selector(Coordinator.updateCurrentPage(sender:)),
            for: .valueChanged)

        return control
    }

    func updateUIView(_ uiView: UIPageControl, context: Context) {
        uiView.currentPage = currentPage
    }

    class Coordinator: NSObject {
        var control: PageControl

        init(_ control: PageControl) {
            self.control = control
        }

        @objc
        func updateCurrentPage(sender: UIPageControl) {
            control.currentPage = sender.currentPage
        }
    }
}
```
###Dynamic Shape Drawing
This view defines a custom shape using Path and GeometryReader, illustrating advanced graphical rendering capabilities within SwiftUI.
```swift
struct BadgeSymbol: View {
    static let symbolColor = Color(red: 79.0 / 255, green: 79.0 / 255, blue: 191.0 / 255)

    var body: some View {
        GeometryReader { geometry in
            let size = min(geometry.size.width, geometry.size.height)
            let scaleX = size / 12
            let xMid = size * 0.5
            let height = (size / 2) * sqrt(3)
            let scaleY = height / 12
            let yMid = height * 0.5

            Path { path in
                
                path.addLines([
                    CGPoint(x: xMid, y: yMid - 6 * scaleY),
                    CGPoint(x: xMid - 3 * scaleX, y: yMid),
                    CGPoint(x: xMid, y:yMid - 2 * scaleY),
                    CGPoint(x: xMid + 3 * scaleX, y: yMid),
                    CGPoint(x: xMid, y: yMid - 6 * scaleY)
                ])
                path.move(to: CGPoint(x: xMid , y: yMid))
                
                path.addLines([
                    CGPoint(x: xMid - 2 * scaleX , y: yMid),
                    CGPoint(x: xMid, y: yMid - scaleX),
                    CGPoint(x: xMid + 2 * scaleX, y: yMid),
                    CGPoint(x: xMid, y: yMid + scaleX),
                ])
                
                
                path.move(to: CGPoint(x: xMid - 3 * scaleX, y: yMid))
                
                path.addLines([
                    CGPoint(x: xMid - 3 * scaleX, y: yMid),
                    CGPoint(x: xMid, y: yMid + 6 * scaleY),
                    CGPoint(x: xMid + 3 * scaleX, y: yMid ),
                    CGPoint(x: xMid, y: yMid + 2 * scaleY),
                    CGPoint(x: xMid - 3 * scaleX, y: yMid),
                ])
            }
            
            .fill(Self.symbolColor)
        }
    }
}
```

###Transition and Animation Extensions
These extensions enhance the UI with custom transitions and animations, providing a richer interactive experience by defining a move-and-fade transition and a ripple effect animation, respectively.
```swift
extension AnyTransition {
    static var moveAndFade: AnyTransition {
        .asymmetric(
            insertion: .move(edge: .trailing).combined(with: .opacity),
            removal: .scale.combined(with: .opacity)
        )
    }
}
extension Animation {
    static func ripple(index: Int) -> Animation {
        Animation.spring(dampingFraction: 0.5)
            .speed(2)
            .delay(0.03 * Double(index))
    }
}
```


