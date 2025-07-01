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

  
 
