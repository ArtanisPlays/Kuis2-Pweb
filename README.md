# Kuis2-Pweb

```
<%@ include file="db.jsp" %>
<%
    Statement stmt = con.createStatement();
    stmt.executeUpdate("TRUNCATE TABLE tasks"); // Deletes everything and resets ID counter
    response.sendRedirect("index.jsp");
%>
```

```
<%@ include file="db.jsp" %>
<%
    String id = request.getParameter("id");
    if(id != null) {
        PreparedStatement ps = con.prepareStatement("DELETE FROM tasks WHERE id=?");
        ps.setString(1, id);
        ps.executeUpdate();
    }
    response.sendRedirect("index.jsp");
%>
```

```
<%@ include file="db.jsp" %>
<%
    String id = request.getParameter("id");
    String title = request.getParameter("title");
    String date = request.getParameter("date");
    String time = request.getParameter("time");
    String category = request.getParameter("category");
    String status = request.getParameter("status");
    String desc = request.getParameter("description");

    if (id == null || id.isEmpty()) {
        // CREATE
        PreparedStatement ps = con.prepareStatement("INSERT INTO tasks (title, t_date, t_time, category, status, description) VALUES (?,?,?,?,?,?)");
        ps.setString(1, title);
        ps.setString(2, date);
        ps.setString(3, time);
        ps.setString(4, category);
        ps.setString(5, status);
        ps.setString(6, desc);
        ps.executeUpdate();
    } else {
        // UPDATE
        PreparedStatement ps = con.prepareStatement("UPDATE tasks SET title=?, t_date=?, t_time=?, category=?, status=?, description=? WHERE id=?");
        ps.setString(1, title);
        ps.setString(2, date);
        ps.setString(3, time);
        ps.setString(4, category);
        ps.setString(5, status);
        ps.setString(6, desc);
        ps.setString(7, id);
        ps.executeUpdate();
    }
    response.sendRedirect("index.jsp");
%>
```

```
<%@ include file="db.jsp" %>
<%
    String id = request.getParameter("id");
    String title = "", date = "", time = "", status = "", cat = "", desc = "";
    String action = "Create";

    if(id != null) {
        action = "Update";
        PreparedStatement ps = con.prepareStatement("SELECT * FROM tasks WHERE id=?");
        ps.setString(1, id);
        ResultSet rs = ps.executeQuery();
        if(rs.next()){
            title = rs.getString("title");
            date = rs.getString("t_date");
            time = rs.getString("t_time");
            status = rs.getString("status");
            cat = rs.getString("category");
            desc = rs.getString("description");
        }
    }
%>
<!DOCTYPE html>
<html>
<head>
    <script src="[https://cdn.tailwindcss.com](https://cdn.tailwindcss.com)"></script>
</head>
<body class="bg-slate-100 flex items-center justify-center min-h-screen">
    <div class="bg-white rounded-2xl shadow-xl w-full max-w-lg overflow-hidden">
        <div class="px-6 py-4 border-b bg-slate-50 flex justify-between items-center">
            <h2 class="text-lg font-bold text-slate-800"><%= action %> Schedule</h2>
            <!-- Might caues a popup due to the unconventional X -->
            <a href="index.jsp" class="text-slate-400 hover:text-slate-600">✕</a>
        </div>
        
        <form action="save.jsp" method="POST" class="p-6 space-y-4">
            <% if(id != null) { %> <input type="hidden" name="id" value="<%= id %>"> <% } %>
            
            <div>
                <label class="block text-sm font-medium text-slate-700 mb-1">Title</label>
                <input type="text" name="title" value="<%= title %>" required class="w-full px-4 py-2 border rounded-lg focus:ring-2 focus:ring-indigo-500 outline-none">
            </div>
            
            <div class="grid grid-cols-2 gap-4">
                <div>
                    <label class="block text-sm font-medium text-slate-700 mb-1">Date</label>
                    <input type="date" name="date" value="<%= date %>" required class="w-full px-4 py-2 border rounded-lg outline-none">
                </div>
                <div>
                    <label class="block text-sm font-medium text-slate-700 mb-1">Time</label>
                    <input type="time" name="time" value="<%= time %>" required class="w-full px-4 py-2 border rounded-lg outline-none">
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                 <div>
                    <label class="block text-sm font-medium text-slate-700 mb-1">Category</label>
                    <select name="category" class="w-full px-4 py-2 border rounded-lg outline-none">
                        <option <%= "General".equals(cat)?"selected":"" %>>General</option>
                        <option <%= "Work".equals(cat)?"selected":"" %>>Work</option>
                        <option <%= "Academic".equals(cat)?"selected":"" %>>Academic</option>
                    </select>
                 </div>
                 <div>
                    <label class="block text-sm font-medium text-slate-700 mb-1">Status</label>
                    <select name="status" class="w-full px-4 py-2 border rounded-lg outline-none">
                        <option <%= "Pending".equals(status)?"selected":"" %>>Pending</option>
                        <option <%= "In Progress".equals(status)?"selected":"" %>>In Progress</option>
                        <option <%= "Done".equals(status)?"selected":"" %>>Done</option>
                    </select>
                 </div>
            </div>

            <div>
                <label class="block text-sm font-medium text-slate-700 mb-1">Description</label>
                <textarea name="description" class="w-full px-4 py-2 border rounded-lg h-24 resize-none"><%= desc %></textarea>
            </div>

            <div class="flex justify-end gap-3 mt-6">
                <a href="index.jsp" class="px-4 py-2 text-slate-600 hover:bg-slate-100 rounded-lg">Cancel</a>
                <button type="submit" class="px-6 py-2 bg-indigo-600 hover:bg-indigo-700 text-white rounded-lg shadow-lg">
                    <%= action %> Schedule
                </button>
            </div>
        </form>
    </div>
</body>
</html>
```

```
<%@ include file="db.jsp" %>
<!DOCTYPE html>
<html>
<head>
    <title>SchedulerPro</title>\
    <script src="[https://cdn.tailwindcss.com](https://cdn.tailwindcss.com)"></script>\
    <link href="[https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css](https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css)" rel="stylesheet">
</head>
<body class="bg-slate-50 font-sans text-slate-800">

    <!-- Navbar -->
    <nav class="bg-white border-b border-slate-200 sticky top-0 z-10 px-6 py-4 flex items-center justify-between shadow-sm">
        <div class="flex items-center gap-3">
            <div class="bg-indigo-600 p-2 rounded-lg shadow-lg shadow-indigo-200">
                <i class="fas fa-calendar text-white"></i>
            </div>
            <h1 class="text-xl font-bold text-transparent bg-clip-text bg-gradient-to-r from-indigo-600 to-violet-600">
                Scheduler<span class="font-light text-slate-400">Pro</span>
            </h1>
        </div>
        <a href="form.jsp" class="flex items-center gap-2 px-5 py-2 bg-indigo-600 hover:bg-indigo-700 text-white rounded-lg font-medium shadow-lg transition-all">
            <i class="fas fa-plus"></i> New Task
        </a>
    </nav>

    <main class="max-w-7xl mx-auto p-6">
        
        <%
            // --- LOGIC: SEARCH & FILTER ---
            String search = request.getParameter("q");
            String filter = request.getParameter("filter");
            String sql = "SELECT * FROM tasks WHERE 1=1";

            if (search != null && !search.trim().isEmpty()) {
                sql += " AND (title LIKE '%" + search + "%' OR category LIKE '%" + search + "%')";
            }
            if (filter != null && !filter.equals("All") && !filter.isEmpty()) {
                sql += " AND status = '" + filter + "'";
            }
            sql += " ORDER BY t_date ASC";

            // --- LOGIC: COUNTS (Feature 7) ---
            int total = 0, pending = 0, progress = 0, done = 0;
            Statement statStat = con.createStatement();
            ResultSet rsStat = statStat.executeQuery("SELECT status, COUNT(*) FROM tasks GROUP BY status");
            while(rsStat.next()){
                String s = rsStat.getString(1);
                int c = rsStat.getInt(2);
                total += c;
                if("Pending".equals(s)) pending = c;
                else if("In Progress".equals(s)) progress = c;
                else if("Done".equals(s)) done = c;
            }
        %>

        <!-- Header Stats -->
        <div class="grid grid-cols-1 md:grid-cols-4 gap-4 mb-8">
            <div class="bg-white p-4 rounded-xl border border-slate-100 shadow-sm flex items-center gap-4">
                <div class="bg-blue-500 w-12 h-12 rounded-lg flex items-center justify-center text-white"><i class="fas fa-layer-group"></i></div>
                <div><p class="text-slate-400 text-xs font-bold uppercase">Total Tasks</p><p class="text-2xl font-bold"><%= total %></p></div>
            </div>
            <div class="bg-white p-4 rounded-xl border border-slate-100 shadow-sm flex items-center gap-4">
                <div class="bg-slate-500 w-12 h-12 rounded-lg flex items-center justify-center text-white"><i class="fas fa-clock"></i></div>
                <div><p class="text-slate-400 text-xs font-bold uppercase">Pending</p><p class="text-2xl font-bold"><%= pending %></p></div>
            </div>
            <div class="bg-white p-4 rounded-xl border border-slate-100 shadow-sm flex items-center gap-4">
                <div class="bg-amber-500 w-12 h-12 rounded-lg flex items-center justify-center text-white"><i class="fas fa-spinner"></i></div>
                <div><p class="text-slate-400 text-xs font-bold uppercase">In Progress</p><p class="text-2xl font-bold"><%= progress %></p></div>
            </div>
            <div class="bg-white p-4 rounded-xl border border-slate-100 shadow-sm flex items-center gap-4">
                <div class="bg-emerald-500 w-12 h-12 rounded-lg flex items-center justify-center text-white"><i class="fas fa-check-circle"></i></div>
                <div><p class="text-slate-400 text-xs font-bold uppercase">Completed</p><p class="text-2xl font-bold"><%= done %></p></div>
            </div>
        </div>

        <!-- Search & Filter Form -->
        <div class="flex flex-col md:flex-row justify-between items-center gap-4 mb-6 bg-white p-4 rounded-xl border shadow-sm">
            <form action="index.jsp" method="GET" class="flex flex-1 w-full gap-3">
                <input type="text" name="q" placeholder="Search tasks..." value="<%= search != null ? search : "" %>"
                       class="w-full px-4 py-2 bg-slate-50 border rounded-lg focus:ring-2 focus:ring-indigo-500 outline-none">
                
                <select name="filter" onchange="this.form.submit()" class="px-4 py-2 bg-slate-50 border rounded-lg outline-none">
                    <option value="All">All Status</option>
                    <option value="Pending" <%= "Pending".equals(filter) ? "selected" : "" %>>Pending</option>
                    <option value="In Progress" <%= "In Progress".equals(filter) ? "selected" : "" %>>In Progress</option>
                    <option value="Done" <%= "Done".equals(filter) ? "selected" : "" %>>Done</option>
                </select>
                <button type="submit" class="hidden">Search</button> <!-- Submit on enter -->
            </form>

            <!-- Feature 8: Clear All -->
            <a href="delete_all.jsp" onclick="return confirm('Warning: Delete ALL tasks?')" 
               class="px-4 py-2 text-rose-600 hover:bg-rose-50 rounded-lg text-sm font-medium">
               Clear All
            </a>
        </div>

        <!-- Task Grid -->
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
            <%
                Statement stmt = con.createStatement();
                ResultSet rs = stmt.executeQuery(sql);
                
                if (!rs.isBeforeFirst()) { 
                    out.println("<div class='col-span-3 text-center py-10 text-slate-400'>No tasks found.</div>");
                }

                while(rs.next()) {
                    String statusColor = "bg-slate-100 text-slate-600";
                    String barColor = "bg-indigo-500";
                    if("Done".equals(rs.getString("status"))) { statusColor = "bg-emerald-100 text-emerald-700"; barColor = "bg-emerald-500"; }
                    else if("In Progress".equals(rs.getString("status"))) { statusColor = "bg-amber-100 text-amber-700"; barColor = "bg-amber-500"; }
            %>
            <div class="group bg-white border border-slate-200 rounded-2xl p-5 hover:shadow-xl transition-all relative overflow-hidden">
                <div class="absolute left-0 top-0 bottom-0 w-1 <%= barColor %>"></div>
                
                <div class="flex justify-between items-start mb-3">
                    <span class="text-xs font-bold px-2 py-1 rounded-full <%= statusColor %>">
                        <%= rs.getString("status") %>
                    </span>
                    <div class="flex gap-1 opacity-0 group-hover:opacity-100 transition-opacity">
                        <!-- Feature 3 & 4: Edit and Delete Links -->
                        <a href="form.jsp?id=<%= rs.getString("id") %>" class="p-1.5 text-slate-400 hover:text-indigo-600"><i class="fas fa-edit"></i></a>
                        <a href="delete.jsp?id=<%= rs.getString("id") %>" onclick="return confirm('Delete this?')" class="p-1.5 text-slate-400 hover:text-rose-600"><i class="fas fa-trash"></i></a>
                    </div>
                </div>

                <h3 class="font-bold text-lg text-slate-800 mb-1"><%= rs.getString("title") %></h3>
                <p class="text-sm text-slate-500 mb-4 h-10 line-clamp-2"><%= rs.getString("description") %></p>
                
                <div class="flex items-center gap-4 text-sm text-slate-400">
                    <span><i class="far fa-calendar mr-1"></i> <%= rs.getString("t_date") %></span>
                    <span><i class="far fa-clock mr-1"></i> <%= rs.getString("t_time") %></span>
                </div>
            </div>
            <% } %>
        </div>
    </main>
</body>
</html>
```


```
<%@ page import="java.sql.*" %>
<%
    // DATABASE CONFIGURATION
    String url = "jdbc:mysql://localhost:3306/scheduler_db";
    String username = "root";
    String password = ""; // Put your MySQL password here if you have one

    Connection con = null;
    try {
        Class.forName("com.mysql.jdbc.Driver");
        con = DriverManager.getConnection(url, username, password);
    } catch (Exception e) {
        out.println("<div style='color:red'>Database Connection Error: " + e.getMessage() + "</div>");
    }
%>
```



THE MYSQL COMMAND(?) TO CREATE TABLE
```
CREATE TABLE tasks (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(100) NOT NULL,
    t_date DATE NOT NULL,
    t_time VARCHAR(10),
    category VARCHAR(50),
    status VARCHAR(20),
    description TEXT
);
```
