---
Title: Styling RadAutoCompleteTextView with Nativescript and Angular2
Published: 2017-11-14 11:55:00 +0000
Tags: 
  - TypeScript
  - NativeScript
  - Angular
---
Lately I’ve been working a lot with [Nativescript](https://www.nativescript.org/) to develop mobile apps able to run in Android and iOS devices.

One of the apps that I just finished required me to use the [nativescript-pro-ui controls](https://www.nativescript.org/ui-for-nativescript), which proven themselves to be quite useful for sure. In this particular case I had to use the AutoComplete control. Everything was going ok until the moment I had to apply some styling to that control using CSS.

As expected I created a class in my stylesheet and applied it to the RadAutoCompleteTextView but unfortunately that wasn’t enough. Some of the properties such as the border color and width were applied correctly but for some reason font size and font face were just being ignored. After some research I found an opened issue on their Github repo and found out someone else with a similar problem. The presented workaround seemed to be using Nativescript Core instead of Angular2 so I just tried to do something similar.

Once again I had to face the fact that developing in frameworks such as NativeScript, Xamarin and others still require you to understand how Android and iOS native code works, otherwise you’ll have a real hard time trying to figure out how you can apply some transformations to the native controls that are generated when the app is running.

Below are some code snippets that worked like a charm. I think the code speaks for itself but in those I’m basically getting the references to the native controls on each platform and setting up the font face and the padding of the text field control that RadAutoCompleteTextView generates.

*home.component.html*

``` html
<RadAutoCompleteTextView [items]="customersList" class="form-input" hint="Search" suggestMode="Suggest" displayMode="Plain" (didAutoComplete)="onCustomerSelected($event)" (loaded)="onAutoCompleteLoad($event)">
    <SuggestionView tkAutoCompleteSuggestionView>
        <ng-template tkSuggestionItemTemplate let-customer="item">
            <StackLayout orientation="vertical" class="picker-entry">
                <Label [text]="customer.text"></Label>
            </StackLayout>
        </ng-template>
    </SuggestionView>
</RadAutoCompleteTextView> 
```

*home.component.ts*

``` javascript
import { TokenModel, AutoCompleteEventData, RadAutoCompleteTextView } from "nativescript-pro-ui/autocomplete";

// (...)

public onAutoCompleteLoad(args: AutoCompleteEventData) {
    var autocomplete = args.object;

    if (isAndroid) {
        let rad = autocomplete.android;
        let nativeEditText = rad.getTextField();
        let currentApp = fs.knownFolders.currentApp();
        let fontPath = currentApp.path + "/fonts/futurat.ttf";

        nativeEditText.setTextSize(14);
        nativeEditText.setTypeface(android.graphics.Typeface.createFromFile(fontPath));
        nativeEditText.setPadding(7, 16, 7, 16);
     } else if (isIOS) {
        let rad = autocomplete.ios; 
        let nativeEditText: UITextField = rad.textField;

        nativeEditText.font = UIFont.fontWithNameSize("FuturaT", 14);
        nativeEditText.leftView = new UIView({ frame: CGRectMake(7,16,7,16) });
        nativeEditText.leftViewMode = UITextFieldViewMode.Always;
     }
} 
```

Happy coding and see you on a next post!