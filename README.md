# Linux-DevOps
Course practice

A step-by-step guide to create a simple task manager script in Bash, based on the functionalities you described:

---

## Step 1: Create the Script File

Open your terminal and create a new file called `task_manager.sh`:

```bash
nano task_manager.sh
```

---

## Step 2: Add the Shebang Line

At the top of the file, add this line to specify the script should run with Bash:

```bash
#!/bin/bash
```

---

## Step 3: Create or Check the Task File

Add a function to create the task file if it doesn't exist:

```bash
create_task_file() {
  if [ ! -f tasks.txt ]; then
    touch tasks.txt
    echo "Task file created."
  fi
}
```

---

## Step 4: Add a Task

Create a function to add a new task with description and due date:

```bash
add_task() {
  read -p "Enter task description: " desc
  read -p "Enter due date (YYYY-MM-DD): " due
  echo "0|$due|$desc" >> tasks.txt
  echo "Task added."
}
```

Here, `0` means the task is not completed.

---

## Step 5: List Tasks

Create a function to display all tasks with status and sorted by due date:

```bash
list_tasks() {
  echo "Tasks:"
  sort -t"|" -k2 tasks.txt | awk -F"|" '{ status=($1=="1") ? "Done" : "Pending"; print NR ". [" status "] " $3 " (Due: " $2 ")" }'
}
```

---

## Step 6: Mark a Task as Completed

Create a function to mark a task as done by changing the status from `0` to `1`:

```bash
complete_task() {
  list_tasks
  read -p "Enter task number to mark as completed: " num
  sed -i "${num}s/^0/1/" tasks.txt
  echo "Task marked as completed."
}
```

---

## Step 7: Delete a Task

Create a function to delete a task by its number:

```bash
delete_task() {
  list_tasks
  read -p "Enter task number to delete: " num
  sed -i "${num}d" tasks.txt
  echo "Task deleted."
}
```

---

## Step 8: Search Tasks by Keyword

Create a function to search tasks by keyword:

```bash
search_tasks() {
  read -p "Enter keyword to search: " keyword
  grep -i "$keyword" tasks.txt | awk -F"|" '{ status=($1=="1") ? "Done" : "Pending"; print "[" status "] " $3 " (Due: " $2 ")" }'
}
```

---

## Step 9: Display Menu and Handle User Input

Add a menu and loop to interact with the user:

```bash
show_menu() {
  echo "1) Add task"
  echo "2) List tasks"
  echo "3) Mark task as completed"
  echo "4) Delete task"
  echo "5) Search tasks"
  echo "6) Exit"
}

while true; do
  show_menu
  read -p "Choose an option: " choice
  case $choice in
    1) add_task ;;
    2) list_tasks ;;
    3) complete_task ;;
    4) delete_task ;;
    5) search_tasks ;;
    6) echo "Goodbye!"; break ;;
    *) echo "Invalid option." ;;
  esac
  echo ""
done
```

---

## Step 10: Make the Script Executable and Run It

Save the file (`Ctrl+O` in nano), exit (`Ctrl+X`), then make it executable:

```bash
chmod +x task_manager.sh
```

Run the script:

```bash
./task_manager.sh
```


## Optional Enhancements
#1- check if dialog installed

#!/bin/bash

if ! command -v dialog &> /dev/null; then
  echo "Error: 'dialog' is not installed."
  if [ -f /etc/debian_version ]; then
    echo "Install it with: sudo apt update && sudo apt install dialog"
  elif [ -f /etc/fedora-release ]; then
    echo "Install it with: sudo dnf install dialog"
  elif [ -f /etc/arch-release ]; then
    echo "Install it with: sudo pacman -S dialog"
  else
    echo "Please install 'dialog' using your system's package manager."
  fi
  exit 1
fi

# Your script continues here...


# 2- add task with proper msbox handling

add_task() {
  desc=$(dialog --stdout --inputbox "Enter task description:" 8 40)
  ret=$?
  clear
  if [ $ret -ne 0 ] || [ -z "$desc" ]; then
    dialog --msgbox "Task description is required. Operation canceled." 6 40
    clear
    return
  fi

  due=$(dialog --stdout --inputbox "Enter due date (YYYY-MM-DD):" 8 40)
  ret=$?
  clear
  if [ $ret -ne 0 ] || [ -z "$due" ]; then
    dialog --msgbox "Due date is required. Operation canceled." 6 40
    clear
    return
  fi

  echo "0|$due|$desc" >> tasks.txt

  dialog --msgbox "Task added successfully." 6 40
  clear
}
