import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt

# Page Configuration
st.set_page_config(page_title="Real Estate Analysis", layout="wide")

# Navigation
st.sidebar.title("Navigation")
section = st.sidebar.radio("Go to:", ["Home", "Add Property", "Comparison", "Reports"])

# Placeholder for property data
data = []

if section == "Home":
    st.title("Welcome to Real Estate Analyzer")
    st.write("Use this app to analyze and compare investment properties easily.")

    st.markdown("### Features:")
    st.markdown("- Input property details manually or upload a spreadsheet.")
    st.markdown("- Calculate key metrics like Price/SF, ROI, and Cap Rate.")
    st.markdown("- Compare properties side by side.")
    st.markdown("- Generate detailed reports.")

if section == "Add Property":
    st.title("Add Property")

    # Input Form for Adding Property Data
    st.subheader("Enter Property Details")
    with st.form(key="property_form"):
        address = st.text_input("Property Address")
        sqft = st.number_input("Square Footage", min_value=0, step=1)
        price = st.number_input("Purchase Price ($)", min_value=0.0, step=1000.0)
        hoa = st.number_input("HOA Fees ($)", min_value=0.0, step=10.0)

        # Submit Button
        submitted = st.form_submit_button("Add Property")

        if submitted:
            data.append({
                "Address": address,
                "Square Footage": sqft,
                "Price": price,
                "HOA": hoa,
                "Price/SF": round(price / sqft, 2) if sqft else None
            })
            st.success(f"Property at {address} added successfully!")

    st.markdown("### Current Properties")
    if data:
        st.table(pd.DataFrame(data))
    else:
        st.write("No properties added yet.")

if section == "Comparison":
    st.title("Property Comparison")

    if not data:
        st.warning("Please add properties to compare.")
    else:
        # DataFrame for Comparisons
        df = pd.DataFrame(data)
        st.table(df)

        # Visualization: Price/SF Comparison
        st.subheader("Price per Square Foot Comparison")
        fig, ax = plt.subplots()
        ax.bar(df["Address"], df["Price/SF"], color='skyblue')
        ax.set_ylabel("Price/SF ($)")
        ax.set_xlabel("Property")
        ax.set_title("Comparison of Price/SF")
        st.pyplot(fig)

if section == "Reports":
    st.title("Reports")

    if not data:
        st.warning("Please add properties to generate reports.")
    else:
        df = pd.DataFrame(data)
        st.download_button(
            label="Download Report",
            data=df.to_csv(index=False),
            file_name="property_report.csv",
            mime="text/csv"
        )
        st.write("Preview:")
        st.dataframe(df)
