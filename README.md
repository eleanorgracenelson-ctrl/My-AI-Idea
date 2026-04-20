```python
import os
import requests
from PIL import Image
from io import BytesIO

# Set your API key (in production, use environment variables or secrets)
# For Grok Imagine or similar services (e.g., Replicate, OpenAI DALL·E, or xAI API)
API_KEY = os.getenv("GROK_IMAGINE_API_KEY")  # or your service key

def generate_image(prompt, previous_image_url=None):
    """Generate or edit an image using natural language."""
    
    url = "https://api.example.com/v1/images/generate"  # Replace with actual Grok Imagine / Flux / Replicate endpoint
    
    payload = {
        "prompt": prompt,
        "n": 1,                    # number of images
        "size": "1024x1024",
        "response_format": "url"
    }
    
    # If editing an existing image, add it to the payload
    if previous_image_url:
        payload["image"] = previous_image_url
        payload["prompt"] = f"Edit the image: {prompt}"  # natural language editing
    
    headers = {
        "Authorization": f"Bearer {API_KEY}",
        "Content-Type": "application/json"
    }
    
    response = requests.post(url, json=payload, headers=headers)
    response.raise_for_status()
    
    image_url = response.json()["data"][0]["url"]
    
    # Download and display/save the image
    img_response = requests.get(image_url)
    img = Image.open(BytesIO(img_response.content))
    
    print(f"✅ Image generated! Prompt: {prompt}")
    img.show()                    # Opens in default image viewer
    img.save("generated_image.png")
    
    return image_url

# Example usage - this is how a user would interact with Grok Imagine Studio
if __name__ == "__main__":
    # Step 1: Initial generation
    initial_prompt = "A cozy bookstore cat wearing round glasses, reading a fantasy novel, warm lighting, illustration style"
    image_url = generate_image(initial_prompt)
    
    # Step 2: Natural language editing (the magic of iterative creation!)
    edit_prompt = "Turn the cat into a steampunk robot with gears and brass details, cyberpunk city background at night"
    edited_url = generate_image(edit_prompt, previous_image_url=image_url)
    
    print("🎉 Final edited image URL:", edited_url)
