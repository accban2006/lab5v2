STUDENT INFORMATION:
Name: Nguyen Chi Bao
Student ID: ITCSIU24008
Class: Web application

COMPLETED EXERCISES:
[x] Exercise 5: Search
[x] Exercise 6: Validation
[x] Exercise 7: Sorting & Filtering
[ ] Exercise 8: Pagination
[ ] Bonus 1: Export Excel

MVC COMPONENTS:
- Model: Student.java
- It defines a JavaBean model Student with fields like id, studentCode, fullName, email, major, and createdAt.
- A no‑argument constructor is provided, which is required for frameworks and JavaBean compliance.
- A parameterized constructor allows creating new student objects without an id (useful before saving to DB).
- Getters and setters are implemented for each field to encapsulate data and allow controlled access.
- The toString() method is overridden to return a readable string representation of the student object.
DAO: StudentDAO.java
- Database Connection: It sets up connection details (DB_URL, DB_USER, DB_PASSWORD) and provides a getConnection() method to connect to a MySQL database using JDBC.
- CRUD Operations: Implements methods to Create (addStudent), Read (getAllStudents, getStudentById, searchStudents), Update (updateStudent), and Delete (deleteStudent) student records.
- Sorting & Filtering: Provides extra methods (getStudentsSorted, getStudentsByMajor, getStudentsFiltered) to retrieve students with sorting and filtering options, while validating inputs to prevent SQL injection.
- Mapping Utility: Uses mapResultSetToStudent() to convert database rows (ResultSet) into Student objects, ensuring clean separation of data handling.
- Error Handling: Wraps database operations in try-catch blocks, printing stack traces on exceptions, and returns safe defaults (like empty lists or false) when operations fail.
Controller: StudentController.java

Views: student-list.jsp
- Page Setup & Taglib
- Declares the JSP page with UTF‑8 encoding.
- Imports JSTL core tag library (jakarta.tags.core) so you can use <c:if>, <c:forEach>, <c:choose>, etc.
- Styling (CSS)
- Provides a modern UI with gradients, rounded containers, styled buttons, and hover effects.
- Defines reusable classes like .btn-primary, .btn-danger, .container, .empty-state.
- Search Box
- A form sends a GET request to student?action=search with a keyword.
- Displays a Clear button if a search is active.
- Shows a message like “Search results for: keyword” when searching.
- Messages (Success/Error)
- Uses JSTL <c:if> to conditionally display success (param.message) or error (param.error) messages passed from the controller.
- Add New Student Button
- Provides a link to student?action=new to open the student form for adding a new record.
- Student Table
- Uses <c:choose> to check if the students list is not empty.
- If students exist, iterates with <c:forEach> to display each student’s details (id, studentCode, fullName, email, major).
- Provides Edit and Delete action buttons for each student.
- If no students exist, shows an empty state with an icon 📭 and a message

 student-form.jsp
- Dynamic Title & Heading
- Uses <c:choose> to decide whether the page is for editing an existing student (student != null) or adding a new one.
- Displays either “✏️ Edit Student” or “➕ Add New Student” accordingly.
- Form Setup
- The form posts to the student servlet (action="student" method="POST").
- A hidden field sets the action to either "update" or "insert".
- If editing, another hidden field carries the student’s id.
- Form Fields
- Student Code: Required when adding, read‑only when editing. Includes a format hint and optional error message (errorCode).
- Full Name: Text input with validation and error handling (errorName).
- Email: Email input with validation and error handling (errorEmail).
- Major: Dropdown list with predefined options. Automatically selects the student’s current major if editing. Shows error if invalid (errorMajor).
- Validation Feedback
- Uses JSTL <c:if> to conditionally display error messages under each field when validation fails.
- Error messages are styled in red (.error class).
- Buttons
- Submit Button: Label changes dynamically to “💾 Update Student” or “➕ Add Student”.
- Cancel Button: Redirects back to the student list (student?action=list).
- Styling (CSS)
- Provides a modern, centered card layout with gradient background.
- Styled inputs, buttons, and error messages for a clean user experience.

FEATURES IMPLEMENTED:
- Search functionality
   // Search students by keyword
    public List<Student> searchStudents(String keyword) {
        List<Student> students = new ArrayList<>();

        if (keyword == null || keyword.trim().isEmpty()) {
            return getAllStudents();
        }

        String sql = "SELECT * FROM students "
                   + "WHERE student_code LIKE ? "
                   + "OR full_name LIKE ? "
                   + "OR email LIKE ? "
                   + "ORDER BY id DESC";

        String searchPattern = "%" + keyword.trim() + "%";

        try (Connection conn = getConnection();
             PreparedStatement pstmt = conn.prepareStatement(sql)) {

            pstmt.setString(1, searchPattern);
            pstmt.setString(2, searchPattern);
            pstmt.setString(3, searchPattern);

            try (ResultSet rs = pstmt.executeQuery()) {
                while (rs.next()) {
                    students.add(mapResultSetToStudent(rs));
                }
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }

        return students;
    }
- Server-side validation
if (!validateStudent(student, request)) {
            request.setAttribute("student", student);
            RequestDispatcher dispatcher = request.getRequestDispatcher("/views/student-form.jsp");
            dispatcher.forward(request, response);
            return;
        }

        if (studentDAO.addStudent(student)) {
            response.sendRedirect("student?action=list&message=Added successfully");
        } else {
            response.sendRedirect("student?action=list&error=Failed to add student");
        }
    }
- Sorting by columns
// Sort students
    private void sortStudents(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException, SQLException {
        String sortBy = request.getParameter("sortBy");
        String order = request.getParameter("order");

        List<Student> students = studentDAO.getStudentsSorted(sortBy, order);

        request.setAttribute("students", students);
        request.setAttribute("sortBy", sortBy);
        request.setAttribute("order", order);

        RequestDispatcher dispatcher = request.getRequestDispatcher("/views/student-list.jsp");
        dispatcher.forward(request, response);
    }

    - Filter by major
// Filter students by major
    private void filterStudents(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException, SQLException {
        String major = request.getParameter("major");

        List<Student> students = studentDAO.getStudentsByMajor(major);

        request.setAttribute("students", students);
        request.setAttribute("major", major);

        RequestDispatcher dispatcher = request.getRequestDispatcher("/views/student-list.jsp");
        dispatcher.forward(request, response);
    }


KNOWN ISSUES:
- Please more point each question

EXTRA FEATURES:
- [List any bonus features]

TIME SPENT: 100hrs