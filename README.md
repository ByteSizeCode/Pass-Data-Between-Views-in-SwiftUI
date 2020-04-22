# Passing Data Between Views in SwiftUI
Tutorial on how to pass data between Views in SwiftUI

1. Add these properties to your SwiftUI View:
```Swift
@State var presentingModal = false //This will control the view presentation
@State var dataPassedBack = "Nothing. Click button ^" //The data passed back will show up here
```

2. Add a button in body to toggle showing the other view
```Swift
Button(action: {self.presentingModal.toggle()}) {Text("Get Input From Other View")}
```

3. In body, create a VStack, and add this directly below it. Also move the button inside the VStack
```Swift
.sheet(isPresented: $presentingModal) { ModalView(presentedAsModal: self.$presentingModal, input: self.$dataPassedBack) }
```
Let's take a closer look: .sheet() will present a popover view when $presentingModal is true. Then ModalView will be called, and $dataPassedBack is passed in and it will be modified globally when we modify it in ModalView (passed in by reference). Lastly, $presentingModal is passed in by reference as well, giving us the ability to set it as false at any time to dismiss the view.

What you now have should look something like this:

```Swift
var body: some View {
        VStack(spacing: 30) {
            Button(action: {self.presentingModal.toggle()}) {Text("Get Input From Other View")}
        }
        .sheet(isPresented: $presentingModal) { ModalView(presentedAsModal: self.$presentingModal, input: self.$dataPassedBack) } //3. Present View on toggle and pass in $dataPassedBack by reference
 }
```

4. Add a textfield to show the current value of your updatable variable $dataPassedBack that you have passed in by reference.

```Swift
Text(self.dataPassedBack)
```

Here is a working project demonstrating this concept:

<p>&nbsp;</p>
<p>&nbsp;</p>

ContentView.swift:
```swift
import SwiftUI

struct ContentView: View {
   @State var presentingModal = false
   @State var dataPassedBack = "Nothing. Click button ^"
    
    var body: some View {
        VStack(spacing: 30) {
            Button(action: {self.presentingModal.toggle()}) {Text("Get Input From Other View")}
            Text(self.dataPassedBack) //4. The text gets passed back here
        }
        .sheet(isPresented: $presentingModal) { ModalView(presentedAsModal: self.$presentingModal, input: self.$dataPassedBack) }
    }
    
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

ModalView.swift:
```swift
import SwiftUI

struct ModalView: View {
    @Binding var presentedAsModal: Bool //Toggle hiding/showing this view
    @State public var name: String = "" //The TextField binds to this and saves its text here
    @Binding var input: String //Our link to pass back the data
    
    
    var body: some View {
        VStack(spacing: 100) {
            TextField("Type Here", text: $name)
            Button("Dismiss") {
                self.presentedAsModal = false //Hide this view
                self.input = self.name //Set value to pass back by reference
            }
        }
    }
}
```
