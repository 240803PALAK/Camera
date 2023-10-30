# Camera
Camera using tkinter and opencv.


    import tkinter as tk
    import cv2
    from tkinter import messagebox
    from PIL import Image, ImageTk
    import os
    from tkinter import PhotoImage
    
    class CameraCaptureApp:
        def __init__(self, window, window_title):
            self.window = window
            self.window.title(window_title)
    
            self.video_source = 0  # Use the default camera (change this if you have multiple cameras)
            self.vid = cv2.VideoCapture(self.video_source)
    
            self.canvas = tk.Canvas(window, width=self.vid.get(3), height=self.vid.get(4))
            self.canvas.pack()
    
            # Create a canvas for the circular button
            canvas = tk.Canvas(root, width=100, height=100, highlightthickness=0)
            canvas.pack()
            
            # Draw the circular button with a border
            button = canvas.create_oval(10, 10, 90, 90, outline="black", width=2, fill="lightgray")
            
            # Bind the button click event
            canvas.tag_bind(button, "<Button-1>", lambda event: self.capture())
    
    
            self.photo = None
            self.update()
            self.window.mainloop()
    
        def capture(self):
            ret, frame = self.vid.read()
            if ret:
                cv2.imwrite("captured_image.png", cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY))
                messagebox.showinfo("Capture", "Image saved as 'captured_image.png'")
                # Load the original image using PIL
                original_image = Image.open("captured_image.png")
                
                # Define the custom width and height
                custom_width = 200
                custom_height = 150
                
                # Resize the image using PIL to the custom width and height
                resized_image = original_image.resize((custom_width, custom_height))
                
                # Convert the resized PIL image to a PhotoImage for Tkinter
                photo = ImageTk.PhotoImage(resized_image)
                
                # Create a label to display the resized image with custom width and height
                label = tk.Label(root, image=photo, width=custom_width, height=custom_height)
                label.pack(pady=10,side=tk.LEFT)
    
                # Keep a reference to the image to prevent it from being garbage collected
                label.image = photo
        def update(self):
            ret, frame = self.vid.read()
            if ret:
                self.photo = ImageTk.PhotoImage(image=Image.fromarray(cv2.cvtColor(frame, cv2.COLOR_RGB2BGR)))
                self.canvas.create_image(0, 0, image=self.photo, anchor=tk.NW)
            self.window.after(10, self.update)
    
        def __del__(self):
            if self.vid.isOpened():
                self.vid.release()
    
    # Create a Tkinter window
    root = tk.Tk()
    app = CameraCaptureApp(root, "Camera Capture App")
