import tkinter as tk
from tkinter import ttk
import threading
import time
import keyboard

# 오토 키 입력 상태 및 변수
auto_typing = False
type_thread = None
selected_hotkey = "f9"  # 기본 실행/중지 핫키
selected_key = "a"  # 기본 자동 입력 키

# 모든 키 목록
keys = [
    'q', 'w', 'e', 'r', 't', 'y', 'u', 'i', 'o', 'p', 'a', 's', 'd', 'f', 'g', 'h', 'j', 'k', 'l', 
    'z', 'x', 'c', 'v', 'b', 'n', 'm', 'Q', 'W', 'E', 'R', 'T', 'Y', 'U', 'I', 'O', 'P', 'A', 'S', 'D', 
    'F', 'G', 'H', 'J', 'K', 'L', 'Z', 'X', 'C', 'V', 'B', 'N', 'M', 
    '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 
    '-', '=', '[', ']', '\\', ';', "'", ',', '.', '/', '`', 
    'space', 'enter', 'tab', 'shift', 'ctrl', 'alt', 'esc', 'up', 'down', 'left', 'right', 'backspace', 
    'capslock', 'numlock', 'prtsc', 'pause', 'ins', 'del', 
    'f1', 'f2', 'f3', 'f4', 'f5', 'f6', 'f7', 'f8', 'f9', 'f10', 'f11', 'f12'
]

# 오토 키 입력 함수
def auto_type(interval_h, interval_m, interval_s, interval_ms):
    global auto_typing, selected_key
    interval = interval_h * 3600 + interval_m * 60 + interval_s + interval_ms / 1000
    while auto_typing:
        keyboard.write(selected_key)
        time.sleep(interval)

# 오토 키 입력 토글 함수
def toggle_auto_type():
    global auto_typing, type_thread
    if auto_typing:
        auto_typing = False
        if type_thread is not None:
            type_thread.join()
    else:
        auto_typing = True
        interval_h = int(hours_var.get())
        interval_m = int(minutes_var.get())
        interval_s = int(seconds_var.get())
        interval_ms = int(milliseconds_var.get())
        type_thread = threading.Thread(target=auto_type, args=(interval_h, interval_m, interval_s, interval_ms))
        type_thread.daemon = True
        type_thread.start()

# 핫키 설정 함수
def set_hotkey(event=None):
    global selected_hotkey
    selected_hotkey = hotkey_option.get()  # 선택된 핫키 가져오기
    keyboard.clear_all_hotkeys()  # 기존 핫키 제거
    keyboard.add_hotkey(selected_hotkey, toggle_auto_type)  # 새 핫키 등록

# 자동 입력 키 설정 함수
def set_target_key(event=None):
    global selected_key
    selected_key = target_key_option.get()  # 선택된 키 가져오기

# tkinter UI 설정
root = tk.Tk()
root.title("Keyboard Auto Clicker")
root.geometry("500x300")
root.resizable(False, False)

# 설정 섹션
settings_frame = tk.LabelFrame(root, text="Settings", padx=10, pady=10)
settings_frame.pack(fill="x", padx=10, pady=5)

# 대상 키 설정
target_key_label = tk.Label(settings_frame, text="Target Key:")
target_key_label.grid(row=0, column=0, sticky="w")
target_key_option = ttk.Combobox(settings_frame, values=keys, state="readonly", width=15)
target_key_option.current(keys.index(selected_key))
target_key_option.grid(row=0, column=1)

set_target_key_button = tk.Button(settings_frame, text="Set Key", command=set_target_key)
set_target_key_button.grid(row=0, column=2, padx=5)

# 간격 설정
interval_frame = tk.LabelFrame(root, text="Type Interval", padx=10, pady=10)
interval_frame.pack(fill="x", padx=10, pady=5)

# 시간 입력
hours_label = tk.Label(interval_frame, text="Hours:")
hours_label.grid(row=0, column=0, sticky="w")
hours_var = tk.StringVar(value="0")
hours_entry = tk.Entry(interval_frame, textvariable=hours_var, width=5)
hours_entry.grid(row=0, column=1)

minutes_label = tk.Label(interval_frame, text="Minutes:")
minutes_label.grid(row=0, column=2, sticky="w")
minutes_var = tk.StringVar(value="0")
minutes_entry = tk.Entry(interval_frame, textvariable=minutes_var, width=5)
minutes_entry.grid(row=0, column=3)

seconds_label = tk.Label(interval_frame, text="Seconds:")
seconds_label.grid(row=0, column=4, sticky="w")
seconds_var = tk.StringVar(value="1")
seconds_entry = tk.Entry(interval_frame, textvariable=seconds_var, width=5)
seconds_entry.grid(row=0, column=5)

milliseconds_label = tk.Label(interval_frame, text="Milliseconds:")
milliseconds_label.grid(row=0, column=6, sticky="w")
milliseconds_var = tk.StringVar(value="0")
milliseconds_entry = tk.Entry(interval_frame, textvariable=milliseconds_var, width=5)
milliseconds_entry.grid(row=0, column=7)

# 핫키 설정 섹션
hotkey_frame = tk.LabelFrame(root, text="Hotkey Settings", padx=10, pady=10)
hotkey_frame.pack(fill="x", padx=10, pady=5)

hotkey_label = tk.Label(hotkey_frame, text="Toggle Hotkey:")
hotkey_label.grid(row=0, column=0, sticky="w")

hotkey_option = ttk.Combobox(hotkey_frame, values=keys, state="readonly", width=15)
hotkey_option.current(keys.index(selected_hotkey))
hotkey_option.grid(row=0, column=1)

set_hotkey_button = tk.Button(hotkey_frame, text="Set Hotkey", command=set_hotkey)
set_hotkey_button.grid(row=0, column=2, padx=5)

# 실행 및 중지 버튼
control_frame = tk.Frame(root)
control_frame.pack(fill="x", padx=10, pady=5)

start_button = tk.Button(control_frame, text="Start", command=toggle_auto_type, width=10)
start_button.pack(side="left", padx=5)

stop_button = tk.Button(control_frame, text="Stop", command=toggle_auto_type, width=10)
stop_button.pack(side="right", padx=5)

# 초기 핫키 등록
keyboard.add_hotkey(selected_hotkey, toggle_auto_type)

root.mainloop()
