# Dig Dug AKA BEST PROJECT EVER
## Desiree, Arturo, Ever
# We are Team Jordan and have built this miniature Dig-Dug Arcade Game using Github, Python, a raspberry Pi, and other harware. 

# Reasons why we should win:
1. We are goated.
2. We are so back.

# import time
import json
import websocket
from gpiozero import MCP3008, Button

# Define the MCP3008 channels for X and Y axes
x_axis = MCP3008(channel=0)
y_axis = MCP3008(channel=1)

# Define the joystick button (SW)
joystick_btn = Button(17)  # GPIO17 (or another GPIO pin of your choice)

# Set up the WebSocket connection to the JavaScript server
ws = websocket.WebSocketApp("ws://localhost:8080", on_open=None, on_message=None, on_error=None, on_close=None)

def send_joystick_data():
    """Function to read joystick data and send it via WebSocket."""
    while True:
        # Read joystick values (range from 0.0 to 1.0)
        x_val = x_axis.value
        y_val = y_axis.value
        button_pressed = joystick_btn.is_pressed

        # Prepare data in a dictionary format to send
        data = {
            "x": x_val,      # X-axis value (0.0 to 1.0)
            "y": y_val,      # Y-axis value (0.0 to 1.0)
            "button": button_pressed  # Boolean for button press
        }

        # Send the data as JSON via WebSocket
        try:
            ws.send(json.dumps(data))  # Send the data as a JSON string
        except Exception as e:
            print(f"Error sending data: {e}")

        # Sleep for a short time before sending the next data point
        time.sleep(0.05)  # 50ms delay to avoid flooding

def start_websocket():
    """Function to initiate the WebSocket connection and start sending data."""
    ws.run_forever()

# Start the WebSocket connection and data sending in the background
import threading
ws_thread = threading.Thread(target=start_websocket)
ws_thread.daemon = True
ws_thread.start()

# Send joystick data
send_joystick_data()
