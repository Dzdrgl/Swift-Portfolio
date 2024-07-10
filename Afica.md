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

These learnings have not only enhanced the functionality of the Africa app but also contributed to my overall growth as a software developer, particularly in the areas of UI design and multimedia content management.

## Important Code Snippets

Below are specific code examples demonstrating key functionalities and solutions implemented in the Africa App:



```swift
/ Custom Cover Button for CoverTabView. This component is used with TabView to enable navigation between tabs by swiping or tapping the buttons. It automatically hides when reaching the end of the tabs.


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
