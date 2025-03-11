import json
import streamlit as st
import pandas as pd
from datetime import datetime

# File to store book data
LIBRARY_FILE = "library.json"

def load_library():
    """Load library data from a file."""
    try:
        with open(LIBRARY_FILE, "r") as file:
            return json.load(file)
    except (FileNotFoundError, json.JSONDecodeError):
        return []

def save_library(library):
    """Save library data to a file."""
    with open(LIBRARY_FILE, "w") as file:
        json.dump(library, file, indent=4)

def add_book(library, title, author, year, genre, read_status, date_added):
    """Add a new book to the library."""
    book = {
        "Title": title,
        "Author": author,
        "Year": year,
        "Genre": genre,
        "Read": read_status,
        "Date Added": date_added
    }
    library.append(book)
    save_library(library)

def remove_book(library, title):
    """Remove a book from the library by title."""
    for book in library:
        if book["Title"].lower() == title.lower():
            library.remove(book)
            save_library(library)
            return True
    return False

def search_books(library, query):
    """Search for a book by title or author."""
    return [book for book in library if query.lower() in book["Title"].lower() or query.lower() in book["Author"].lower()]

def display_statistics(library):
    """Display statistics of the library."""
    total_books = len(library)
    read_books = sum(1 for book in library if book["Read"])
    read_percentage = (read_books / total_books) * 100 if total_books > 0 else 0
    return total_books, read_books, read_percentage

# Streamlit UI
st.set_page_config(page_title="Personal Library Manager", layout="wide")
st.title("📚 Personal Library Manager")
library = load_library()

# Sidebar Menu with Toggle
with st.sidebar:
    st.header("Navigation")
    show_advanced = st.toggle("Show Advanced Options")
    menu = st.radio("Menu", ["Add Book", "Remove Book", "Search Books", "View All Books", "Statistics", "Sort Books"])

if menu == "Add Book":
    st.subheader("Add a New Book")
    with st.form("add_form"):
        title = st.text_input("Title")
        author = st.text_input("Author")
        year = st.number_input("Publication Year", min_value=0, step=1)
        genre = st.text_input("Genre")
        read_status = st.checkbox("Have you read this book?")
        date_added = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        submitted = st.form_submit_button("Add Book")
        if submitted:
            add_book(library, title, author, year, genre, read_status, date_added)
            st.success("Book added successfully!")

elif menu == "Remove Book":
    st.subheader("Remove a Book")
    title = st.text_input("Enter book title to remove")
    if st.button("Remove Book"):
        if remove_book(library, title):
            st.success("Book removed successfully!")
        else:
            st.error("Book not found!")

elif menu == "Search Books":
    st.subheader("Search for a Book")
    query = st.text_input("Enter title or author")
    if st.button("Search"):
        results = search_books(library, query)
        if results:
            df = pd.DataFrame(results)
            st.dataframe(df)
        else:
            st.error("No books found!")

elif menu == "View All Books":
    st.subheader("All Books in Library")
    if library:
        df = pd.DataFrame(library)
        st.dataframe(df)
    else:
        st.warning("No books available in the library.")

elif menu == "Statistics":
    st.subheader("Library Statistics")
    total_books, read_books, read_percentage = display_statistics(library)
    col1, col2, col3 = st.columns(3)
    with col1:
        st.metric(label="Total Books", value=total_books)
    with col2:
        st.metric(label="Books Read", value=read_books)
    with col3:
        st.metric(label="Read Percentage", value=f"{read_percentage:.2f}%")

elif menu == "Sort Books":
    st.subheader("Sort Books")
    sort_option = st.selectbox("Sort by", ["Title", "Author", "Year", "Genre", "Date Added"], index=0)
    sort_order = st.radio("Order", ["Ascending", "Descending"], index=0)
    if library:
        reverse_order = sort_order == "Descending"
        df = pd.DataFrame(library).sort_values(by=sort_option, ascending=not reverse_order)
        st.dataframe(df)
    else:
        st.warning("No books available to sort.")

# Advanced Options Toggle
if show_advanced:
    st.sidebar.header("Advanced Options")
    if st.sidebar.button("Clear Library Data"):
        library.clear()
        save_library(library)
        st.sidebar.success("Library data cleared!")
