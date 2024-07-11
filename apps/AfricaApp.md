# Africa App




## Table of Contents

- [Project Objective](#project-objective)
- [Screenshots](#screenshots)
- [Frameworks Used](#frameworks-used)
- [Key Learnings](#key-learnings)
- [Important Code Snippets](#important-code-snippets)

## Project Objective

The Africa app provides a detailed and interactive experience showcasing the diverse wildlife of Africa. Users can explore various animal species through rich multimedia content including photos, videos, and maps.

## Screenshots


<div style="display:flex; justify-content:space-between; align-items:center;">
    <img width="300" alt="main-view" src="https://github.com/Dzdrgl/SwiftPortfolio/blob/main/assests/africa-main.png?raw=true">
    <img width="300" alt="main-view" src="https://github.com/Dzdrgl/SwiftPortfolio/blob/main/assests/africa-map.png?raw=true">
    <img width="300" alt="main-view" src="https://github.com/Dzdrgl/SwiftPortfolio/blob/main/assests/africa-video-list.png?raw=true">
</div>

## Frameworks Used

- **SwiftUI**: For building the user interface with a declarative syntax.
- **MapKit**: For embedding interactive maps within the app.
- **AVKit**: For handling video playback.
- **Foundation**: Used for handling data and providing support for core components. And decodıng json data to model.


## Key Learnings

Throughout the development of the Africa app, I gained significant insights and improved my skills in several key areas of iOS application development:

- **Complex User Interaction Handling with SwiftUI**: Mastered the use of SwiftUI to manage complex user interactions involving NavigationStack,HStack ,Vstack , Zstack, ForEach, TabView, and Link and  more. This improved the app's navigation and overall user experience.

- **Location-Based Services with MapKit**: Learned to integrate MapKit effectively, enhancing the app with interactive and detailed maps that provide geographic insights into wildlife locations.

- **Video Content Management with AVKit**: Developed proficiency in using AVKit for loading and displaying video content smoothly, ensuring a seamless multimedia experience for users.

- **Data Handling and JSON Integration with Foundation**: Gained expertise in handling JSON data, which involved decoding JSON into model structures efficiently. This skill is crucial for working with data-driven features in iOS apps.
- **State Management in SwiftUI with @Binding and @State**:Enhanced understanding of state management in SwiftUI using `@Binding` and `@State`. These tools helped manage local and shared state within the app effectively, allowing for dynamic updates to the UI in response to user interactions and data changes. `@State` is used for owning and managing local data, while `@Binding` creates a two-way binding to properties owned by parent views, enabling components to share and mutate state:


These learnings have not only enhanced the functionality of the Africa app but also contributed to my overall growth as a software developer, particularly in the areas of UI design and multimedia content management.

## Important Code Snippets

Below are specific code examples demonstrating key functionalities and solutions implemented in the Africa App:


## ContentView Description

The `ContentView` structure defines the main view of the Africa app and offers the user the option to view animals in either a list or grid display. It features the ability to switch between two user interface modes: list view and grid view. Users can toggle between these modes using buttons located on the toolbar.

### Main Features and Functions:

- **Dynamic Grid Layout**: Users can adjust the number of columns in the grid by tapping the toolbar buttons, which dynamically update the `gridLayout` array based on the selected number of columns.
  
- **View Mode Toggle**: The `isActive` state determines the current view mode (list or grid) of the user interface. This state can be toggled by the buttons, updating the corresponding view.
  
- **Haptic Feedback**: Provides medium-intensity haptic feedback during user interactions, specifically when selections are made.
  
- **View Switching**: Users can switch between list and grid views by clicking icons on the toolbar. This functionality is managed by the `gridSwitch` function.
  
- **Navigation**: Each animal is linked to its detailed information through a `NavigationLink`, which displays the details in `AnimalDetailView`.

This structure offers users a flexible way to explore animals while effectively utilizing SwiftUI's dynamic and responsive user interface capabilities. It provides an interactive and user-friendly interface that allows users to personalize their in-app navigation experience.

```swift
struct ContentView: View {
    //MARK: PROPERTIES
    @State private var isActive:Bool = false
    @State private var selectedImageIndex: Int = 0
    
    let animals:[Animal]
    let haptics = UIImpactFeedbackGenerator(style: .medium)
    
     //DYNAMIC GRID LAYOUT
    @State private var gridLayout:[GridItem] = [GridItem(.flexible())]
    @State private var gridColumn:Int = 1
    @State private var toolBarIcon:String = "square.grid.2x2"
    
    func gridSwitch(){
        gridLayout = Array(repeating: .init(.flexible()), count: gridLayout.count % 3 + 1)
        gridColumn = gridLayout.count
        
        switch gridColumn {
        case 1:
            toolBarIcon = "rectangle.grid.1x2"
        case 2:
            toolBarIcon = "square.grid.2x2"
        case 3:
            toolBarIcon = "square.grid.3x2"
        default:
            toolBarIcon = "square.grid.2x2"
        }
    }
   
    //MARK: BODY
    var body: some View {
        NavigationStack{
            Group{
                if !isActive{
                    List{
                        CoverImageView(selectedImageIndex: $selectedImageIndex)
                            .frame(height: 300)
                            .listRowInsets(EdgeInsets(top: 0, leading: 0, bottom: 0, trailing: 0))
                        ForEach(animals){ animal in
                            NavigationLink(destination:AnimalDetailView(animal: animal)){
                                AnimalListItemView(animal: animal)
                            }// :LINK
                        }// :LOOP
                    } // :LIST
                    .listStyle(.inset)
                }else{
                    ScrollView(.vertical,showsIndicators: false){
                        LazyVGrid(columns: gridLayout,alignment:.center) {
                            ForEach(animals){ animal in
                                NavigationLink(destination: AnimalDetailView(animal: animal)) {
                                    AnimalGridItemView(image: animal.image)
                                }
                            }// :LOOP
                        }// :GRID
                        .padding(10)
                        .animation(.easeIn,value:toolBarIcon)
                    }// :SCROLL
                }// :CONDITION
                    
            }// :GROUP
            .navigationTitle("Africa")
            .navigationBarTitleDisplayMode(.large)
            .toolbar{
                ToolbarItem(placement: .topBarTrailing) {
                    HStack(spacing: 16, content: {
                        //list
                        Button(action: {
                            isActive = false
                            haptics.impactOccurred()
                        }, label: {
                            Image(systemName: "square.fill.text.grid.1x2")
                                .font(.title2)
                                .foregroundStyle(isActive ? .primary : Color.accentColor)
                        })
                        //GRID
                        Button(action: {
                            isActive = true
                            haptics.impactOccurred()
                            gridSwitch()
                        }, label: {
                            Image(systemName: toolBarIcon)
                                .font(.title2)
                                .foregroundStyle(isActive ? Color.accentColor : Color.primary)
                        })
                    })// :HSTACK
                }// :BUTTONS
            }// :TOOLBAR
            
        }// :NAVIGATION
        
    }
}
```

- Custom Cover Button for CoverTabView. This component is used with TabView to enable navigation between tabs by swiping or tapping the buttons. It automatically hides when reaching the end of the tabs.
```swift
struct CoverButtonView: View {
    let systemName: String
    let action: () -> Void
    let isEnabled: Bool
        
    var body: some View {
        Image(systemName: systemName)
            .resizable()
            .frame(width: 20, height: 80)
            .fontWeight(.medium)
            .foregroundStyle(Color(UIColor.secondaryLabel))
            .onTapGesture(count: 1, perform: action)
            .opacity(isEnabled ? 1 : 0)
    }
}

struct CoverImageView: View {
    //MARK: - PROPERTIES
    @Binding var selectedImageIndex: Int
    let coverImages:[CoverImage] = Bundle.main.decode("covers.json")
    
    //MARK: - BODY
    var body: some View {
        ZStack{
            TabView(selection: $selectedImageIndex){
                ForEach(0..<coverImages.count, id: \.self) { index in
                    Image(coverImages[index].name)
                        .resizable()
                        .scaledToFill()
                }//: LOOP
            }// :TAB
            .tabViewStyle(.page)
            HStack{
                CoverButtonView(systemName: "chevron.left", action: {
                    selectedImageIndex -= 1
                }, isEnabled: selectedImageIndex > 0)
                Spacer()
                CoverButtonView(systemName: "chevron.right", action: {
                    selectedImageIndex += 1
                }, isEnabled: selectedImageIndex < (coverImages.count - 1))
            }//: HSTACK
        }//: ZSTACK
    }
}
```


