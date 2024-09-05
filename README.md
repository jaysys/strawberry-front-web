To test the FastAPI endpoint using a React app with Next.js (v14), we will create a simple form that allows users to upload an image and sends it to the FastAPI server using the `fetch` API with a `POST` request.

Here’s a sample React component that you can use in your Next.js app, using Next.js 14’s app router (`app` directory) structure.

### Steps:

1. **Create a Next.js project (if not already created)**:  
   Run the following commands to create a new Next.js app:

   ```bash
   npx create-next-app@14 my-fastapi-test-app
   cd my-fastapi-test-app
   ```

2. **Set up your component inside the `app` directory**.

### Next.js App Directory Structure:

Here’s a sample structure of your Next.js app:

```
/app
  /imageinfo
    /page.jsx    # The React component handling image upload and API request
```

### React Component (`app/imageinfo/page.jsx`):

```jsx
"use client";

import { useState } from "react";

export default function ImageInfoPage() {
  const [image, setImage] = useState(null);
  const [response, setResponse] = useState(null);
  const [error, setError] = useState(null);

  // Function to handle file input change
  const handleFileChange = (e) => {
    if (e.target.files && e.target.files[0]) {
      setImage(e.target.files[0]);
    }
  };

  // Function to handle the form submission
  const handleSubmit = async (e) => {
    e.preventDefault();

    if (!image) {
      setError("Please select an image first.");
      return;
    }

    const formData = new FormData();
    formData.append("ImageInfo", image);

    try {
      const res = await fetch("http://127.0.0.1:8000/getimageinfo/", {
        method: "POST",
        headers: {
          accept: "application/json",
        },
        body: formData,
      });

      if (res.ok) {
        const data = await res.json();
        setResponse(data);
        setError(null);
      } else {
        const errorData = await res.json();
        setError(`Error: ${errorData.detail || "Something went wrong"}`);
        setResponse(null);
      }
    } catch (err) {
      setError("Failed to send request. Please check your server.");
      setResponse(null);
    }
  };

  return (
    <div>
      <h1>Upload Image and Get Info</h1>

      <form onSubmit={handleSubmit}>
        <input type="file" accept="image/*" onChange={handleFileChange} />
        <button type="submit">Get Image Info</button>
      </form>

      {error && <p style={{ color: "red" }}>{error}</p>}

      {response && (
        <div>
          <h2>Image Info:</h2>
          <p>
            <strong>Filename:</strong> {response.filename}
          </p>
          <p>
            <strong>Width:</strong> {response.width}px
          </p>
          <p>
            <strong>Height:</strong> {response.height}px
          </p>
        </div>
      )}
    </div>
  );
}
```

### Explanation:

- **State management**:
  - `image` holds the selected image file.
  - `response` stores the server's response (the filename, width, and height).
  - `error` stores any error messages encountered during the upload process.
- **`handleFileChange`**:
  - This function updates the state whenever a user selects a new image.
- **`handleSubmit`**:

  - This function creates a `FormData` object and appends the selected image (`ImageInfo`).
  - It then sends a `POST` request to the FastAPI server using the `fetch` API.

- **Display logic**:
  - If the response is successful, it displays the image's file name, width, and height.
  - If there's an error, it displays an error message.

### How to Run:

1. **Start FastAPI server**:
   Make sure your FastAPI server is running at `http://127.0.0.1:8000`.

   ```bash
   uvicorn main:app --reload
   ```

2. **Run the Next.js app**:
   Run the Next.js app using the following commands:

   ```bash
   npm run dev
   ```

3. **Test the app**:
   - Open your browser and navigate to `http://localhost:3000/imageinfo`.
   - Upload an image using the form, and it should send the request to your FastAPI server.
   - If successful, you will see the image’s dimensions and file name displayed on the page.

### Example Response Display:

```bash
Filename: screenshot.png
Width: 1024px
Height: 768px
```

This example demonstrates how to integrate a React frontend (Next.js) with FastAPI for image upload and response handling.
