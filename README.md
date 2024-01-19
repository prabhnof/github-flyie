<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GitHub Repositories Viewer</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f4f4f4;
        }
        header {
            background-color: #333;
            color: #fff;
            padding: 1em;
            text-align: center;
        }
        main {
            max-width: 800px;
            margin: 20px auto;
            padding: 20px;
            background-color: #fff;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        #repoList {
            list-style: none;
            padding: 0;
        }
        .repoItem {
            border-bottom: 1px solid #ccc;
            padding: 10px 0;
        }
        a {
            text-decoration: none;
            color: #0366d6;
        }
        a:hover {
            text-decoration: underline;
        }
        .loader {
            border: 4px solid #f3f3f3;
            border-radius: 50%;
            border-top: 4px solid #3498db;
            width: 20px;
            height: 20px;
            animation: spin 1s linear infinite;
            display: inline-block;
            margin-right: 5px;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        #pagination {
            margin-top: 20px;
            display: flex;
            justify-content: space-between;
        }
        #searchBar {
            margin-top: 20px;
            padding: 5px;
        }
    </style>
</head>
<body>

    <header>
        <h1>GitHub Repositories Viewer</h1>
    </header>

    <main>
        <div id="searchBar">
            <label for="search">Search Repositories: </label>
            <input type="text" id="search" placeholder="Enter repository name" oninput="filterRepos()">
        </div>

        <label for="username">GitHub Username: </label>
        <input type="text" id="username" placeholder="Enter GitHub username">
        <button onclick="fetchRepos(1)">Get Repositories</button>

        <ul id="repoList"></ul>

        <div id="pagination">
            <div>
                <label for="perPage">Repositories per page: </label>
                <select id="perPage" onchange="fetchRepos(1)">
                    <option value="10">10</option>
                    <option value="20">20</option>
                    <option value="50">50</option>
                    <option value="100">100</option>
                </select>
            </div>
            <div>
                <button onclick="prevPage()">Previous</button>
                <span id="currentPage">Page 1</span>
                <button onclick="nextPage()">Next</button>
            </div>
        </div>

    </main>

    <script>
        let currentPage = 1;

        function fetchRepos(page) {
            const username = document.getElementById('username').value;
            const perPage = document.getElementById('perPage').value;
            const repoList = document.getElementById('repoList');
            const search = document.getElementById('search').value;

            // Clear previous results
            repoList.innerHTML = '';

            // Show loader
            repoList.innerHTML = '<li class="repoItem"><span class="loader"></span>Loading...</li>';

            // Fetch user's repositories from GitHub API
            let apiUrl = `https://api.github.com/users/${username}/repos?page=${page}&per_page=${perPage}`;
            if (search) {
                apiUrl += `&q=${search}`;
            }

            fetch(apiUrl)
                .then(response => response.json())
                .then(repos => {
                    if (repos.length === 0) {
                        repoList.innerHTML = '<li class="repoItem">No repositories found.</li>';
                    } else {
                        repos.forEach(repo => {
                            const listItem = document.createElement('li');
                            listItem.className = 'repoItem';
                            listItem.innerHTML = `<a href="${repo.html_url}" target="_blank">${repo.name}</a> - Topics: ${repo.topics.join(', ')}`;
                            repoList.appendChild(listItem);
                        });
                    }
                    updatePagination(page);
                })
                .catch(error => {
                    console.error('Error fetching repositories:', error);
                    repoList.innerHTML = '<li class="repoItem">Error fetching repositories</li>';
                });
        }

        function updatePagination(page) {
            currentPage = page;
            document.getElementById('currentPage').innerText = `Page ${currentPage}`;
        }

        function prevPage() {
            if (currentPage > 1) {
                fetchRepos(currentPage - 1);
            }
        }

        function nextPage() {
            fetchRepos(currentPage + 1);
        }

        function filterRepos() {
            fetchRepos(1);
        }
    </script>

</body>
</html>
