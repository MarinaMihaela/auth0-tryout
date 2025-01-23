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
<script src="https://cdn.auth0.com/js/auth0/9.11/auth0.min.js"> </script>

<script>
  document.getElementById('changePasswordBtn').addEventListener('click', async (req,res) => {
    const { email } = req.body;

    var webAuth = new auth0.WebAuth({
    domain:       'marina-v2.eu.auth0.com',
    clientID:     'xhk3OuCdT8QeTHnpqZlwtfpR4QcbmEdr'
  });
  
  webAuth.changePassword({
    connection: 'Username-Password-Authentication',
    email:   email,
   // organization: 'ORGANIZATION_ID'
  }, function (err, resp) {
    if(err){
      console.log(err.message);
    }else{
      console.log(resp);
    }
  });
  });

</script>

<%- include('partials/footer') -%>
