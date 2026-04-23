import streamlit as st
import pandas as pd
from streamlit_searchbox import st_searchbox

# Load Excel data (cached to prevent reloading on every keystroke)
@st.cache_data
def load_data():
    # Adjust 'Sheet1' to match your actual sheet name
    return pd.read_excel("database.xlsx", sheet_name="Sheet1")

df = load_data()

# Function called as the user types
def search_excel(searchterm: str):
    if not searchterm or len(searchterm) < 2:
        return []
    
    # Filter the 'ProductName' column (adjust name to match your Excel column)
    matches = df[df['ProductName'].str.contains(searchterm, case=False, na=False)]
    return matches['ProductName'].tolist()[:10] # Return top 10 suggestions

st.title("Excel-Powered Live Search")

# Real-time searchbox component
selected_value = st_searchbox(
    search_excel,
    key="excel_search",
    placeholder="Start typing to search...",
)

# Show all columns for the selected item
if selected_value:
    result_row = df[df['ProductName'] == selected_value]
    st.write("### Matching Record:")
    st.dataframe(result_row, use_container_width=True)
