# TinyMCE in React.

# 1. How to setup ?
There are two ways to integrate TinyMCE in your react project : -
- Integrate TinyMCE React component as a uncontrolled component.
- Integrate TinyMCE React component as a controlled component. 
  ## 1. Integrate TinyMCE React component as a uncontrolled component.
  ![image](https://github.com/user-attachments/assets/0598483b-0e00-448e-b7c8-a5f578d2a21a)
  ### 🔄 Content Retrieval Flow

  ```
  User types in editor 
      ↓
  onDirty fires → setDirty(true) → User gets visual feedback 
      ↓
  User clicks save button
      ↓
  save() function runs
      ↓
  editorRef.current.getContent() → Get content
      ↓
  Send to server & reset dirty state
  ```
  ## 2. Integrate TinyMCE React component as a controlled component.
  ![image](https://github.com/user-attachments/assets/360508ae-60a7-4251-968b-fd00e482c9de)
  ### 🔄 Content Retrieval Flow
  ```
  User types in editor
    ↓
  onEditorChange fires
      ↓
  setValue(newValue) called
      ↓
  Component re-renders
      ↓
  value prop updated
      ↓
  Editor displays new content
  ```
  # 2. Custom Toolbar buttons
  [Custom toolbar buttons](https://www.tiny.cloud/docs/tinymce/latest/custom-toolbarbuttons/)
  ## Button Registrations
  ### 1. Basic Date Button (basicDateButton)
  ```js
    editor.ui.registry.addButton('basicDateButton', {
    text: 'Insert Date',
    tooltip: 'Insert Current Date',
    onAction: (_) => editor.insertContent(toDateHtml(new Date()))
  });
  ```
  Logic: Simple button that always inserts the current date in standard format when clicked. No conditional logic or state management.
  ### 2. Selective Date Button (selectiveDateButton)
  
  ```js
  editor.ui.registry.addButton('selectiveDateButton', {
    icon: 'insert-time',
    tooltip: 'Insert Current Date',
    enabled: false,
    onAction: (_) => editor.insertContent(toDateHtml(new Date())),
    onSetup: (buttonApi) => {
      const editorEventCallback = (eventApi) => {
        buttonApi.setEnabled(eventApi.element.nodeName.toLowerCase() !== 'time');
      };
      editor.on('NodeChange', editorEventCallback);
      return () => editor.off('NodeChange', editorEventCallback);
    }
  });
  ```
  Logic: Intelligent button that:
  - Starts disabled (enabled: false)
  - Listens for cursor position changes via NodeChange event
  - Only enables when cursor is NOT inside a <time> element
  - Prevents inserting nested time elements
  - Returns cleanup function to remove event listener
  
  ### 3. Toggle Date Button (toggleDateButton)
    ```js
      editor.ui.registry.addToggleButton('toggleDateButton', {
        icon: 'insert-time',
        tooltip: 'Insert Current Date',
        onAction: (_) => editor.insertContent(toDateHtml(new Date())),
        onSetup: (buttonApi) => {
          const editorEventCallback = (eventApi) => {
            buttonApi.setActive(eventApi.element.nodeName.toLowerCase() === 'time');
          };
          editor.on('NodeChange', editorEventCallback);
          return () => editor.off('NodeChange', editorEventCallback);
        }
      });
    ```
    Logic: Visual feedback button that:
  
  - Shows active/pressed state when cursor is inside a <time> element
  - Uses setActive() instead of setEnabled() for visual indication
  - Still inserts date when clicked, but provides user feedback about current context
  
  ### 4. Split Date Button (splitDateButton)
  ```js
  editor.ui.registry.addSplitButton('splitDateButton', {
    text: 'Insert Date',
    onAction: (_) => editor.insertContent('<p>Its Friday!</p>'),
    onItemAction: (buttonApi, value) => editor.insertContent(value),
    fetch: (callback) => {
      const items = [
        {
          type: 'choiceitem',
          text: 'Insert Date',
          value: toDateHtml(new Date())
        },
        {
          type: 'choiceitem',
          text: 'Insert GMT Date',
          value: toGmtHtml(new Date())
        },
        {
          type: 'choiceitem',
          text: 'Insert ISO Date',
          value: toIsoHtml(new Date())
        }
      ];
      callback(items);
    }
  });
  ```
  Logic: Dual-action button with:
  
  - Main button action inserts hardcoded "Its Friday!" text
  - Dropdown arrow reveals 3 date format options
  - fetch() dynamically generates menu items with current date
  - onItemAction() handles dropdown selections by inserting the item's value
  
  ### 5. Menu Date Button (menuDateButton)
    ```js
    editor.ui.registry.addMenuButton('menuDateButton', {
      text: 'Date',
      fetch: (callback) => {
        const items = [
          {
            type: 'menuitem',
            text: 'Insert Date',
            onAction: (_) => editor.insertContent(toDateHtml(new Date()))
          },
          {
            type: 'nestedmenuitem',
            text: 'Other formats',
            getSubmenuItems: () => [
              {
                type: 'menuitem',
                text: 'GMT',
                onAction: (_) => editor.insertContent(toGmtHtml(new Date()))
              },
              {
                type: 'menuitem',
                text: 'ISO',
                onAction: (_) => editor.insertContent(toIsoHtml(new Date()))
              }
            ]
          }
        ];
        callback(items);
      }
    });
    ```
  Logic: Hierarchical menu button with:
  
  - Top-level "Insert Date" menu item for standard format
  - Nested submenu "Other formats" containing GMT and ISO options
  - Each menu item has its own onAction handler
  - getSubmenuItems() returns submenu structure when expanded

### Pattern 1: Conditional Button
```javascript // Button that's only enabled when text is selected
onSetup: (buttonApi) => {
  const updateState = () => {
    const hasSelection = editor.selection.getContent().length > 0;
    buttonApi.setEnabled(hasSelection);
  };
  
  editor.on('SelectionChange', updateState);
  updateState(); // Set initial state
  
  return () => editor.off('SelectionChange', updateState);
}
```
### Pattern 2: Toggle Button
```javascript 
let isActive = false;
editor.ui.registry.addButton('toggleButton', {
  text: 'Off',
  onAction: (buttonApi) => {
    isActive = !isActive;
    buttonApi.setText(isActive ? 'On' : 'Off');
    buttonApi.setIcon(isActive ? 'check' : 'close');
  }
});
```


  
 
