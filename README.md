# Converting pdf to png with frontend

## Getting Started
To do this task you’ll need to have basic knowledge of ReactJS and JavaScript. We’ll use Create React App to scaffold our project, so make sure it’s installed on your machine.

## Steps
Here are the steps we will follow to build this app:

1) Set up a new React project using Create React App.
2) Download the required dependencies: pdfjs.
3) Create a form for users to upload a PDF file.
4) Use pdfjs to convert the PDF to a series of images.
5) Display the images and allow users to download or share them.

### Step 1: Create a new React app

First, open your terminal and run the following command to create a new React app:

`npx create-react-app pdf-to-image-converter`

Once the app is created, navigate into the project directory:

`cd pdf-to-image-converter`

### Step 2: Download required files

Next, download the pdf.js files for the project from https://cdnjs.com/libraries/pdf.js/2.6.347

pdfjs is a JavaScript library for working with PDF files.

### Step 3: Set up the file upload form

In the src/App.js file, add the following code to set up the file upload form:
```Python
import React, { useState } from "react";
import "./App.css";
import { Grid } from "@mui/material";
import FileInput from "./Components/file-input";
import FileConverter from "./Components/file-converter";

export const primary = "#176ede";

function App() {
  const [pdfFile, setPdfFile] = useState(null);
  return (
    <div style={{ height: "100dvh" }}>
      <Grid container className="d-flex" sx={{ py: 6, px: 4 }}>
        <Grid item className="box">
          <FileInput onFileChange={(file) => setPdfFile(file)} />
        </Grid>
        {pdfFile && (
          <Grid item sx={{ width: "100%" }}>
            <FileConverter
              pdfUrl={URL.createObjectURL(pdfFile)}
              fileName={pdfFile.name}
            />
          </Grid>
        )}
      </Grid>
    </div>
  );
}

export default App;
```
This file renders a FileInput and a FileConverter component within a Grid layout. The state variable pdfFile is initialized as null using the useState hook.

When a PDF file is selected using the FileInput component, the state variable is updated using the setPdfFile function. If a PDF file is present, the FileConverter component is rendered passing in the PDF file URL and name as props.

Next, create a file /src/Components/file-input.js

```Python
import { CloseOutlined } from "@mui/icons-material";
import { Button,IconButton,Snackbar,Stack,Typography,useTheme} from "@mui/material";
import MuiAlert from "@mui/material/Alert";
import PropTypes from "prop-types";
import React, { useEffect, useRef, useState } from "react";
import uploadImg from "../assets/cloud-upload-regular-240.png";
import filePdf from "../assets/file-pdf-solid-240.png";
import { primary } from "../App";

const Alert = React.forwardRef(function Alert(props, ref) {
  return <MuiAlert elevation={6} ref={ref} variant="filled" {...props} />;
});

const FileInput = (props) => {
  const theme = useTheme();
  const wrapperRef = useRef(null);
  const bpSMd = theme.breakpoints.down("sm");

  const [file, setFile] = useState(null);
  const [open, setOpen] = React.useState(false);

  useEffect(() => {
    props.onFileChange(file);
  }, [file]);

  const handleClose = (event, reason) => {
    if (reason === "clickaway") {
      return;
    }
    setOpen(false);
  };

  const onDragEnter = () => wrapperRef.current.classList.add("dragover");

  const onDragLeave = () => wrapperRef.current.classList.remove("dragover");

  const onDrop = () => wrapperRef.current.classList.remove("dragover");

  const onFileDrop = (e) => {
    const newFile = e.target.files[0];
    if (newFile && newFile.type === "application/pdf") {
      setFile(newFile);
    } else {
      setOpen(true);
    }
  };

  const returnSize = (file) => {
    const fileSizeInBytes = file.size; // Example file size in bytes
    let fileSize;

    if (fileSizeInBytes >= 1048576) {
      fileSize = (fileSizeInBytes / 1048576).toFixed(2) + " MB";
    } else {
      fileSize = (fileSizeInBytes / 1024).toFixed(2) + " KB";
    }
    return fileSize;
  };

  return (
    <>
      <Stack
        direction="row"
        spacing={1}
        justifyContent="center"
        alignItems="center"
        sx={{ marginBottom: 3 }}
      >
        <Typography
          variant="h1"
          sx={{
            fontSize: "40px",
            textTransform: "uppercase",
            fontWeight: "bold",
            letterSpacing: 1,
            color: primary,
            [bpSMd]: { fontSize: "20px" },
          }}
        >
          PDF
        </Typography>
        <Typography
          variant="h1"
          sx={{
            fontSize: "40px",
            textTransform: "uppercase",
            fontWeight: "bold",
            letterSpacing: 1,
            color: "text.primary",
            [bpSMd]: { fontSize: "20px" },
          }}
        >
          to Image
        </Typography>
      </Stack>
      {!file && (
        <Button
          ref={wrapperRef}
          className="drop-file-input"
          onDragEnter={onDragEnter}
          onDragLeave={onDragLeave}
          onDrop={onDrop}
        >
          <div className="drop-file-input__label">
            <img src={uploadImg} alt="" />
            <p>Drag & Drop your files here</p>
          </div>
          <input type="file" accept=".pdf" value="" onChange={onFileDrop} />
        </Button>
      )}
      {file ? (
        <div className="drop-file-preview">
          <p className="drop-file-preview__title">Uploaded file</p>
          <div className="drop-file-preview__item">
            <img src={filePdf} alt="PDF Icon" />
            <div className="drop-file-preview__item__info">
              <p>{file.name}</p>
              <p>{returnSize(file)}</p>
            </div>
            <IconButton onClick={()=>setFile(null)}>
              <CloseOutlined />
            </IconButton>
          </div>
        </div>
      ) : null}
      <Snackbar
        anchorOrigin={{
          vertical: "top",
          horizontal: "right",
        }}
        open={open}
        autoHideDuration={4000}
        onClose={handleClose}
      >
        <Alert onClose={handleClose} severity="error" sx={{ width: "100%" }}>
          Please upload pdf only
        </Alert>
      </Snackbar>
    </>
  );
};

FileInput.propTypes = {
  onFileChange: PropTypes.func,
};

export default FileInput;
```
This code defines a component that allows the user to drag and drop a PDF file onto a button to upload it. Here’s what the code does:
1. It defines three functions to handle events that occur when the user interacts with the button:
   
   -`onDragEnter` adds the CSS class "dragover" to the button's wrapper element when the user drags a file over the button.
 
   -`onDragLeave` removes the "dragover" class from the wrapper element when the user drags a file away from the button.
 
   -`onDrop` removes the "dragover" class from the wrapper element when the user drops a file onto the button.
 
2. It defines a fourth function, `onFileDrop`, which is called when the user drops a file onto the button. This function:
   
   -Gets the dropped file from the event object.
 
   -Checks if the file is a PDF file.
 
   -If the file is a PDF file, it sets the file as the new file for the component using the `setFile` function.
 
   -If the file is not a PDF file, it opens a modal dialog using the `setOpen` function.
 
3. It renders a `Button` component with the following properties:
   
   -A `ref` property that sets `wrapperRef` to refer to the `Button` element's wrapper element. This is used to add and remove the "dragover" class to the wrapper element when the user drags a file over the button.
 
   -A `className` property that sets the CSS class "drop-file-input" on the `Button` element.
 
   -Three event handler properties (`onDragEnter`, `onDragLeave`, and `onDrop`) that call the functions defined in step 1 when the corresponding events occur.
 
   -A child `div` element with a `className` of "drop-file-input__label", which contains an image and a text label.
 
   -An `input` element with `type="file"`, `accept=".pdf"`, and a `onChange` property that calls the `onFileDrop` function when the user selects a file.
 
Once the file is successfully uploaded, then it will be displayed on the page and also it will be sent back to the App.js file as a callback

### Step 4: Use pdfjs to convert the PDF to a series of images.
Next, create a file src/Components/file-converter.js and inside it add the below two functions above the render function.
```Python
var pdfjsLib = window["pdfjs-dist/build/pdf"];
pdfjsLib.GlobalWorkerOptions.workerSrc = "./assets/js/pdf.worker.js";

const UrlUploader = (url) => {
  fetch(url).then((response) => {
    response.blob().then((blob) => {
      let reader = new FileReader();
      reader.onload = (e) => {
        const data = atob(e.target.result.replace(/.*base64,/, ""));
        renderPage(data);
      };
      reader.readAsDataURL(blob);
    });
  });
};

useMemo(() => {
  UrlUploader(props.pdfUrl);
}, []);

const renderPage = async (data) => {
  setLoading(true);
  const imagesList = [];
  const canvas = document.createElement("canvas");
  canvas.setAttribute("className", "canv");
  const pdf = await pdfjsLib.getDocument({ data }).promise;
  for (let i = 1; i <= pdf.numPages; i++) {
    var page = await pdf.getPage(i);
    var viewport = page.getViewport({ scale: 1.5 });
    canvas.height = viewport.height;
    canvas.width = viewport.width;
    var render_context = {
      canvasContext: canvas.getContext("2d"),
      viewport: viewport,
    };
    await page.render(render_context).promise;
    let img = canvas.toDataURL("image/png");
    imagesList.push(img);
  }
  setNumOfPages((e) => e + pdf.numPages);
  setImageUrls((e) => [...e, ...imagesList]);
};
```
This code defines two functions `UrlUploader` and `renderPage` that work together to fetch a PDF file from a given URL, convert each page of the PDF into an image, and then render those images to a canvas element.

The first two lines of the code import the PDF.js library and set the path for the PDF worker script.

The `UrlUploader` function takes a URL as input and fetches the file using the `fetch` method. Once the file is fetched, it is converted into a blob object and read as a Data URL using the `FileReader` object. Then the `renderPage` function is called, which takes the data URL as input and

1. Get the page object using `await pdf.getPage(i)`.
2. Get the viewport of the page using `page.getViewport({ scale: 1.5 })`.
3. Set the dimensions of the canvas using `canvas.height = viewport.height` and `canvas.width = viewport.width`.
4. Create a render context object containing the canvas context and viewport using `{ canvasContext: canvas.getContext("2d"), viewport: viewport }`.
5. Render the page onto the canvas using `await page.render(render_context).promise`.
6. Convert the canvas data to a data URL using `let img = canvas.toDataURL("image/png")`.
7. Append the data URL to the `imagesList` array using `imagesList.push(img)`

### Step 5: Display the images and allow users to download or share them.

```Python
<Grid container spacing={3}>
  {imageUrls.map((url, index) => (
    <Grid item xs={12} sm={4} key={index}>
      <Box sx={{ width: "100%", height: "250px" }} className="img-card">
        <img
          src={url}
          alt={`Page ${index + 1}`}
          style={{
            width: "100%",
            height: "100%",
            objectFit: "cover",
          }}
        />
        <Stack
          direction="row"
          spacing={1}
          sx={{ position: "absolute", top: 2, right: 2 }}
        >
          <IconButton
            onClick={() => handleClickOpen(url, index)}
            className="btn-bg"
          >
            <RemoveRedEye />
          </IconButton>
          <IconButton
            onClick={() => downloadImage(url, index)}
            className="btn-bg"
          >
            <Download />
          </IconButton>
        </Stack>
      </Box>
    </Grid>
  ))}
</Grid>;
```
This code generates a grid layout with images using an array of image URLs. Each image has a fixed height and width and is accompanied by two icons: one to preview the image and another to download it.

When the preview icon is clicked, a modal with the image is displayed, and when the download icon is clicked, the image is downloaded to the user’s device.

```Python
const downloadImage = (url, index) => {
    const a = document.createElement("a");
    a.href = url;
    a.download = `${fileName}_${index + 1}.png`;
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
    handleClose();
  };
```

1. The function creates a new `<a>` (anchor) element using `document.createElement("a")`.
2. The `href` property of the anchor element is set to the `url` parameter passed to the function.
3. The `download` property of the anchor element is set to `${fileName}_${index + 1}.png`, which generates a file name based on the value of a `fileName` variable which will get from props and the `index` parameter passed to the function.
4. The anchor element is appended to the `<body>` element of the document using `document.body.appendChild(a)`.
5. The `click()` method of the anchor element is called, which triggers the download of the file.
6. The anchor element is removed from the document using `document.body.removeChild(a)` to clean up after the download is complete.


