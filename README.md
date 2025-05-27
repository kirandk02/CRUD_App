import mysql.connector
import streamlit as st
import pandas as pd
import re

mydb=mysql.connector.connect(
    host="localhost",
    user="root",
    password="207Kk#1998",
    database="crud_app"
)

mycursor=mydb.cursor()
print("connection established")

def main():
    #st.title("Employee Management");
    st.markdown(
    """
    <h1 style='background-color: #cce5ff; padding: 12px; border-radius: 10px; text-align: center; color: #003366;'>
        Employee Management
    </h1>
    """,strem
    unsafe_allow_html=True)
    
    option=st.sidebar.selectbox("Select an Operation",("Create","Read","Update","Delete"))
    if option=="Create":
        st.subheader("Create a Record")
        id=st.number_input("Enter Employee ID",min_value=1,format="%d")
        name=st.text_input("Enter Name")
        email=st.text_input("Enter Email")
        if st.button("Create"):
            if not name.strip() or not email.strip():
                st.error("Please fill in all the fields before creating a record.")
            elif not re.match(r"^[\w\.-]+@[\w\.-]+\.\w{2,}$", email):
                st.error("Please enter a valid email address (e.g., user@example.com).")
            else:
                mycursor.execute("SELECT * FROM employee WHERE id = %s", (id,))
                existing = mycursor.fetchone()
        
                if existing:
                    st.error("Employee already existed!")
                else:
                    sql= "insert into employee(id,name,email) values(%s,%s,%s)"
                    val= (id,name,email)
                    mycursor.execute(sql,val)
                    mydb.commit()
                    st.success("Record Created Successfully!")


    elif option=="Read":
        st.subheader("Read Records")
        mycursor.execute("select * from employee")
        result = mycursor.fetchall()
        if result:
            data=pd.DataFrame(result,columns=['Empolyee ID','Name','Email'])
            data.index = data.index + 1
            st.table(data)
        else:
            st.warning("There is no employee data to show")


    elif option=="Update":
        st.subheader("Update a Record")
        id=st.number_input("Enter Employee ID",min_value=1,format="%d")
        name=st.text_input("Enter New Name")
        email=st.text_input("Enter New Email")
        if st.button("Update"):
            if not name.strip() or not email.strip():
                st.error("Please fill in all fields before updating.")
            elif not re.match(r"^[\w\.-]+@[\w\.-]+\.\w{2,}$", email):
                st.error("Please enter a valid email address (e.g., user@example.com).")
            else:
                # Check if employee ID exists
                mycursor.execute("SELECT * FROM employee WHERE id = %s", (id,))
                existing = mycursor.fetchone()

                if existing:
                    sql = "UPDATE employee SET name = %s, email = %s WHERE id = %s"
                    val = (name, email, id)
                    mycursor.execute(sql, val)
                    mydb.commit()
                    st.success("Record Updated Successfully!!!")
                else:
                    st.warning("Employee ID not found. Cannot update.")



    elif option=="Delete":
        st.subheader("Delete a Record")
        id=st.number_input("Enter ID",min_value=1)
        if st.button("Delete"):
            sql="delete from employee where id =%s"
            val=(id,)
            mycursor.execute(sql,val)
            mydb.commit()
            if mycursor.rowcount > 0:
                st.success("Record Deleted Successfully!!!")
            else:
                st.warning("Employee ID not found. No record deleted.")
                

if __name__ == "__main__":
    main()
        
                

