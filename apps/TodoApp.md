# ToDo App

## Table of Contents

- [Project Objective](#project-objective)
- [Screenshots](#screenshots)
- [Frameworks Used](#frameworks-used)
- [Key Learnings](#key-learnings)
- [Important Code Snippets](#important-code-snippets)

## Project Objective

The ToDo App allows users to easily take simple notes in both dark and light modes. Users can add, edit, and delete these notes while experiencing animations, haptic feedback, and sound effects, enhancing the interactive aspect of the app.

## Screenshots

<div style="display:flex; justify-content:space-between; align-items:center;">
    <img width="300" alt="main-view" src="https://github.com/Dzdrgl/SwiftPortfolio/blob/main/assests/todo-dark.png?raw=true">
    <img width="300" alt="main-view" src="https://github.com/Dzdrgl/SwiftPortfolio/blob/main/assests/todo-light.png?raw=true">
    <img width="300" alt="main-view" src="https://github.com/Dzdrgl/SwiftPortfolio/blob/main/assests/todo-newItem.png?raw=true">
  <img width="300" alt="main-view" src="https://github.com/Dzdrgl/SwiftPortfolio/blob/main/assests/todo-widget.png?raw=true">
</div>

## Frameworks Used

- **SwiftUI**: Used for building the user interface with a declarative syntax.
- **CoreData**: Utilized for storing user persistence data.
- **AVFoundation**: Implemented for managing audio playback for tap sounds.
- **WidgetKit**: Employed for developing widgets that enhance the app’s functionality.

## Key Learnings

Throughout the development of the ToDo app, I gained significant insights and honed my skills in several crucial aspects of iOS application development:

- **User Interface Customization**: Mastered the use of gradients, transitions, and animations to create a visually appealing interface. Learned to create custom Toggle, Button, and TextField styles, which helped avoid code repetition and enhance detailed design execution.

- **Code Structuring and Extensions**: Improved the management of code complexity by using enum structures for cleaner, error-free code. Integrated custom code into views, colors, and other classes through extensions, making my code easier to manage and access.

- **Persistent Data Management with CoreData**: Learned to create and manage my own `xcdatamodels` and entities with CoreData. This included understanding how to maintain data persistence even when the app is closed, and how to generate sample data for previews. Mastered the use of `AppStorage` to easily access data throughout the app.

- **Sound Content Management with AVFoundation**: Implemented AVFoundation to enable sound playback within the app, particularly for button interactions.

- **Widget Development with WidgetKit**: Took my first steps in developing widgets using WidgetKit, gaining valuable experience in this area.

These learnings have not only enhanced the functionality of the ToDo app but also contributed to my overall growth as a software developer, particularly in the areas of UI design and multimedia content management.



## Important Code Snippets

Below are specific code examples demonstrating key functionalities and solutions implemented in the Todo App:



### Main Features and Functions:

- **Data Fetching**: Utilizes CoreData to fetch task data, which is displayed in a list format.
- **Task Management**: Allows users to add new tasks via a modal view, which can be triggered by a dedicated button.
- **User Interface**: Offers a clean and responsive layout that adapts to user interactions, such as hiding the modal view when the user taps outside the active area.




```swift
struct ContentView: View {
    //MARK: - PROPERTIES
    @State var task : String = ""
    @State private var showNewTaskItem:Bool = false
     
    // FETCH DATA
    @Environment(\.managedObjectContext) private var viewContext
    @FetchRequest(
        sortDescriptors: [NSSortDescriptor(keyPath: \Item.timestamp, ascending: true)],
        animation: .easeIn)

    var items: FetchedResults<Item>

    //MARK: - BODY
    var body: some View {
        NavigationStack{
            ZStack{
                //MARK: - MAIN VIEW
                VStack{
                    //MARK: - HEADER
                    HeaderView()
                    Spacer(minLength: 40)
                    //MARK: - NEW TASK BUTTON
                    NewTaskButtonView(showNewTaskItem: $showNewTaskItem)
                    //MARK: - TASKS
                    ScrollView(.vertical,showsIndicators: false){
                        ForEach(items) { item in
                            HStack{
                                ListRowItemView(item: item)
                            }
                        }// :LOOP
                    }// :Scroll
                    .padding(.top,40)
                }// :VSTACK
                //MARK: - NEW TASK ITEM                
            }// :ZSTACK
            .navigationTitle("Daily Task")
            .navigationBarTitleDisplayMode(.large)
            .toolbar(.hidden)
            .background(
                BackgroundImageView()
                   
            )
            .onTapGesture {
                withAnimation {
                    showNewTaskItem = false
                }
            }
            
        }// :NAVSTACK
        .blur(radius: showNewTaskItem ? 20:0,opaque: true)
        .ignoresSafeArea(.all)
        .overlay(alignment:.top){
            if showNewTaskItem{
                NewTaskItemView(isShowing: $showNewTaskItem)
            }
        }
        
    }
}
```
- Defines a custom style for toggle elements used in task completion. It changes the icon based on the toggle state and plays a sound effect when toggled, utilizing SoundManager for audio feedback.
```swift
struct CheckboxStyle: ToggleStyle{
    func makeBody(configuration: Configuration) -> some View {
        return HStack{
            
            Image(systemName: configuration.isOn ? "checkmark.circle.fill" : "circle")
                .font(.system(size: 30,weight: .semibold, design: .rounded))
                .foregroundStyle(configuration.isOn ?  .pink : .primary)
                .onTapGesture {
                    configuration.isOn.toggle()
                    if configuration.isOn{
                        SoundManager.shared.playSound(sound: .rise)
                    }else{
                        SoundManager.shared.playSound(sound: .tap)
                    }
                }
            
            configuration.label
            
        }
    }
}
```

- Describes how to implement a widget for the app using WidgetKit. This widget shows brief task-related data and is customizable in terms of content and appearance.
```swift
struct DevoteWidget: Widget {
    let kind: String = "TodoWidget"

    var body: some WidgetConfiguration {
        StaticConfiguration(kind: kind, provider: Provider()) { entry in
            DevoteWidgetEntryView(entry: entry)
                .containerBackground(.clear, for: .widget)
        }
        .contentMarginsDisabled()
        .configurationDisplayName("Devote")
        .description("This is an example widget.")
    }
}

#Preview(as:.systemMedium){
    DevoteWidget()
} timeline: {
    SimpleEntry(date: .now, emoji: "😀")
    SimpleEntry(date: .now, emoji: "🤩")
}
```
