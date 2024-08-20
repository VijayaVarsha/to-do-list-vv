# to-do-list-vv
import sqlite3
from datetime import datetime

# Database setup
def setup_database():
    conn = sqlite3.connect('todo_list.db')
    cursor = conn.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS tasks (
            id INTEGER PRIMARY KEY,
            title TEXT NOT NULL,
            description TEXT,
            due_date TEXT,
            priority TEXT,
            status TEXT DEFAULT 'incomplete'
        )
    ''')
    conn.commit()
    conn.close()

# Add a new task
def add_task(title, description, due_date, priority):
    conn = sqlite3.connect('todo_list.db')
    cursor = conn.cursor()
    cursor.execute('''
        INSERT INTO tasks (title, description, due_date, priority)
        VALUES (?, ?, ?, ?)
    ''', (title, description, due_date, priority))
    conn.commit()
    conn.close()
    print(f"Task '{title}' added successfully.")

# View tasks with optional filters
def view_tasks(show_completed=True, show_incomplete=True, sort_by_due_date=False):
    conn = sqlite3.connect('todo_list.db')
    cursor = conn.cursor()

    query = 'SELECT * FROM tasks WHERE '
    conditions = []
    if show_completed:
        conditions.append("status = 'complete'")
    if show_incomplete:
        conditions.append("status = 'incomplete'")
    
    query += " OR ".join(conditions)

    if sort_by_due_date:
        query += " ORDER BY due_date ASC"
    
    cursor.execute(query)
    tasks = cursor.fetchall()
    conn.close()

    if not tasks:
        print("No tasks found.")
        return

    print("\nTasks List:")
    for task in tasks:
        print(f"{task[0]}. {task[1]} [Priority: {task[4]}, Due: {task[3]}, Status: {task[5]}]")
        print(f"   Description: {task[2]}")

# Mark a task as completed
def mark_task_completed(task_id):
    conn = sqlite3.connect('todo_list.db')
    cursor = conn.cursor()
    cursor.execute('''
        UPDATE tasks SET status = 'complete' WHERE id = ?
    ''', (task_id,))
    conn.commit()
    conn.close()
    print(f"Task {task_id} marked as completed.")

# Mark a task as incomplete
def mark_task_incomplete(task_id):
    conn = sqlite3.connect('todo_list.db')
    cursor = conn.cursor()
    cursor.execute('''
        UPDATE tasks SET status = 'incomplete' WHERE id = ?
    ''', (task_id,))
    conn.commit()
    conn.close()
    print(f"Task {task_id} marked as incomplete.")

# Delete a task
def delete_task(task_id):
    conn = sqlite3.connect('todo_list.db')
    cursor = conn.cursor()
    cursor.execute('DELETE FROM tasks WHERE id = ?', (task_id,))
    conn.commit()
    conn.close()
    print(f"Task {task_id} deleted.")

# Main function to run the to-do list manager
def main():
    setup_database()
    while True:
        print("\nTo-Do List Manager")
        print("1. Add Task")
        print("2. View Tasks")
        print("3. Mark Task as Completed")
        print("4. Mark Task as Incomplete")
        print("5. Delete Task")
        print("6. Exit")
        choice = input("Enter your choice: ")

        if choice == '1':
            title = input("Enter task title: ")
            description = input("Enter task description: ")
            due_date = input("Enter due date (YYYY-MM-DD): ")
            priority = input("Enter priority (Low, Medium, High): ")
            add_task(title, description, due_date, priority)
        elif choice == '2':
            show_completed = input("Show completed tasks? (y/n): ").lower() == 'y'
            show_incomplete = input("Show incomplete tasks? (y/n): ").lower() == 'y'
            sort_by_due_date = input("Sort by due date? (y/n): ").lower() == 'y'
            view_tasks(show_completed, show_incomplete, sort_by_due_date)
        elif choice == '3':
            try:
                task_id = int(input("Enter task ID to mark as completed: "))
                mark_task_completed(task_id)
            except ValueError:
                print("Please enter a valid task ID.")
        elif choice == '4':
            try:
                task_id = int(input("Enter task ID to mark as incomplete: "))
                mark_task_incomplete(task_id)
            except ValueError:
                print("Please enter a valid task ID.")
        elif choice == '5':
            try:
                task_id = int(input("Enter task ID to delete: "))
                delete_task(task_id)
            except ValueError:
                print("Please enter a valid task ID.")
        elif choice == '6':
            print("Exiting To-Do List Manager.")
            break
        else:
            print("Invalid choice. Please try again.")

if __name__ == "__main__":
    main()
