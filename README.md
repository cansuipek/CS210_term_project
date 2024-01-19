# CS210_term_project
# Pinterest Board Analysis

## Overview
This project is designed to analyze personal Pinterest board about dominant colors and themes of the images by extracting information.


## Color Analysis
In the first part of the project, dominant colors of images in the Pinterest board are analyzed. This is achieved through a combination of techniques:

1. HTML Parsing
  The HTML file containing the Pinterest board information is parsed using BeautifulSoup.
```python
   from bs4 import BeautifulSoup

   # Replace 'PATH_TO_YOUR_HTML_FILE' with the actual path to your HTML file
   html_file_path = "C:/Users/alica/OneDrive - sabanciuniv.edu/Masaüstü/Cansu/Sophmore 2/CS210/ucuncuPinterest.html"

   with open(html_file_path, 'r', encoding='utf-8') as file:
       content = file.read()

   # Parse the HTML content
   soup = BeautifulSoup(content, 'html.parser')
```

2. Image Extraction:
    All image elements in the HTML file are extracted to obtain the image URLs.
   ```
   # Now 'soup' contains the parsed HTML, and you can navigate and extract information from it
   # For example, find all image elements on the page
   image_elements = soup.find_all('img')

   # Extract image URLs
   image_urls = [img['src'] for img in image_elements if 'src' in img.attrs]

   # Display the first few image URLs as an example
   print("Image URLs:")
   for i in range(min(5, len(image_urls))):
       print(image_urls[i])```
   
3. Dominant Color Analysis:
    Using the ColorThief library, the dominant color of each image is determined.
```
    # Analyze dominant colors for each image
    for image_url in image_urls:
        # Construct the full file path
        image_path = os.path.join(html_file_directory, image_url.replace('/', '\\'))
    
        # Using ColorThief for dominant color analysis
        ct = ColorThief(image_path)
    
        dominant_color = ct.get_color(quality=1)
    
        # Add the dominant color to the Counter
        dominant_color_counts[dominant_color] += 1
        all_dominant_colors.append(dominant_color)
```
5. Color Categorization:
   Colors are categorized using the closest_color function and webcolor library, and the counts are displayed.
```def closest_color(rgb):
    differences = {}
    for color_hex, color_name in webcolors.CSS3_HEX_TO_NAMES.items():
        r,g,b = webcolors.hex_to_rgb(color_hex)
        differences[sum([(r- rgb[0]) ** 2,
                         (g - rgb[1]) ** 2,
                         (b - rgb[2]) ** 2])] = color_name
    return differences[min(differences.keys())]
```
   
6. Visualization:
    The dominant colors are displayed in a graph.
```
    plt.xticks(range(len(colors)), colors, rotation=45, ha='right', fontsize=8)
    plt.xlabel('Color')
    plt.ylabel('Count')
    plt.title('Dominant Colors Distribution')
    
    # Show the plot
    plt.show()
```

## Theme Analysis
In the second part of the project, themes of images in the Pinterest board are analyzed using a combination of techniques:

1. HTML Parsing:
   As the previous one, the HTML file is parsed to extract image elements.
   
2. Deep Learning Model -MobileNetV2:
    The MobileNetV2 model(a pre-trained model) is used for predicting categories of images.
```
# Load MobileNetV2 model
model = MobileNetV2(weights='imagenet')


# Function to get image category from TensorFlow
def get_image_category(img_path):
    try:
        img = image.load_img(img_path, target_size=(224, 224))
        img_array = image.img_to_array(img)
        img_array = np.expand_dims(img_array, axis=0)
        img_array = preprocess_input(img_array)

        predictions = model.predict(img_array)
        decoded_predictions = decode_predictions(predictions)
        top_category = decoded_predictions[0][0][1]
        return top_category.lower()  # Convert to lowercase for consistency
```
3. Seraching for Keywords:
    The text under images and filename are analysed to increment the score if there is a word matches with the keywords.
```
def score_themes(text, predicted_category, theme_keywords):
    theme_scores = {theme: 0 for theme in theme_keywords}
    for theme, keywords in theme_keywords.items():
        for keyword in keywords:
            if keyword in text:
                theme_scores[theme] += 1
        if predicted_category and predicted_category in keywords:
            theme_scores[theme] += 2  # Higher weight for TensorFlow prediction match
    return theme_scores
```
5. Visualization: The most common theme is plotted in a bar chart.


## Prerequisites
- Python 3.x
- Required Python packages (specified in requirements.txt)
- A Pinterest board exported as an HTML file


## File Structure
- **color_analysis.py:** Python script for analyzing dominant colors.
- **theme_analysis.py:** Python script for analyzing themes.
- **requirements.txt:** List of required Python packages.
- **ucuncuPinterest.html:** Example Pinterest board HTML file.
- **README.md:** Project documentation.

## Acknowledgments
- [ColorThief](https://github.com/fengsp/color-thief-py): For dominant color analysis.
- [Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/): For HTML parsing.
- [MobileNetV2](https://keras.io/api/applications/mobilenet/#mobilenetv2-function): Pre-trained model for image classification.
