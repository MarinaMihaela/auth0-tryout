<%- include('partials/header') -%>

<h1 class="text-4xl mb-4">Welcome <%= user.nickname %></h1>

<% if (user.picture) { %>
  <img class="block py-3" src="<%= user.picture %>" width="300">
<% } %>

<p class="py-3">
  This is the content of <code class="bg-gray-200">req.user</code>.<br>
  <strong>Note:</strong> <code class="bg-gray-200">_raw</code> and <code class="bg-gray-200">_json</code> properties have been omitted.
</p>

<pre class="block bg-gray-300 p-4 text-sm overflow-scroll"><%= JSON.stringify(user, null, 2) %></pre>

<div class="py-4">
  <button id="changePasswordBtn" class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-700">
    Change Password
  </button>
</div>

<script>
  document.getElementById('changePasswordBtn').addEventListener('click', async () => {
    try {
      const response = await fetch('/api/change-password', { method: 'POST' });
      const data = await response.json();
      if (response.ok) {
        alert('Password change link: ' + data.ticketUrl);
      } else {
        alert('Error: ' + data.error);
      }
    } catch (err) {
      console.error('Error:', err);
      alert('An unexpected error occurred.');
    }
  });
</script>

<%- include('partials/footer') -%>
