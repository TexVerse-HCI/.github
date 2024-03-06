#  TexVerse
__One Sentence Description__: It's an online knowledge base of HCI textile research 🧶, it walks you through related works in a friendly gallery view of academic papers 🖼️ and has a powerful AI research assistant 🤖 backed with a strong vector database storing context information of past research in this field.

# Technologies used
- __Web scrapers__ (the content of week 2 of TECHIN510): I wrote a Python scraper to collect paper data (pdf file, paper title, abstract, citation numbers, date, etc.) from the ACM digital library.
- __Data storage__ (the content of week 3 of TECHIN510): I stored all paper data on Firebase because I want to deploy my app directly on GitHub Page, so I want to have a pure front-end app. While Azure doesn’t support data access from front-ends, Firebase supports direct front-end access to the Firestore database. For some necessary backend operations, e.g. using OpenAI API to acquire the response from ChatGPT, I deployed Firebase Cloud Functions to perform backend tasks.
- __Data visualization__ (the content of week 5 of TECHIN510) using D3.js: I used D3 to create some glyphs, e.g. the date filter can be visualized as bar charts with d3.brush to select a date range.
- __LLM__ (the content of week 6 of TECHIN510): I converted the content of every research paper to vectors and used the RAG method to provide more contextual information for the LLM to get more accurate and relevant answers. In the data collection phase, I also tried to use the Gemini API (which was free!) to generate keywords (I concluded several aspects I want to know, e.g. fabrication method, machine used to make the fabric, fabric applications, etc.) based on the abstract so I can filter or search with these keywords in the final web app.
- __Front-end development__ (the content of week 7 of TECHIN510): The webpage was developed via HTML, CSS, and JS (using Bootstrap5) and deployed on the GitHub Page.

# The problems trying to solve
I'm an HCI researcher focusing on textile fabrication research. I had a hard time organizing my knowledge base in the research field by constantly switching between various apps, including Zotero, Notion, Apple Notes, etc. Now I want to have a "_one-site-for-all_" solution to construct my knowledge base on related works in a clean and organized way. To be specific, this web app integrates several features I really need: it automatically generates the keywords of several aspects I'm interested in on the research so that I can have a better understanding of whether it's relevant before diving into it, it displays papers in cards so that I can see the teaser image of the research without having to click into any further links, and it also has a powerful AI, which can assist me in the research so that I can come up with better research ideas and have a less stressful time in finding and organizing works when I write the "related works" section.

# How to run
Visit https://texverse-hci.github.io/.
## Featuring
- Get data on HCI textile research paper from the Firebase Firestore database (with an initial amount of 220 relevant papers, which is scraped from the ACM digital library in Feb 2024). You can also apply a filter to paper with several metadata.
    - With the card (gallery) view of papers, you can gain a quick glimpse of all relevant papers all at once. The _teaser _image_ part is meant to serve as the "memory landmark" for each paper (you see the image, and you recall details on that paper. It works better than titles sometimes).
- After logging in, you can collect papers 🌟.
- Chat with the TexVerse AI bot! It's equipped with all the paper text data (content in the paper pdfs!) and is now an expert in HCI textile research.
    - Using RAG, the app will fetch the most relevant context extracted from all the papers (the paper content text was chunked based on paragraphs) and feed the prompt with your question.

# Reflections
## Lessons learned
- __Firebase database__. I learned how to store data with multiple attributes in the Firebase database and acquire data from the Firebase on the front-end side, and how not to load data all at once, but divide the data into small batches, loading more on certain triggers (e.g. scrolled to the bottom of the page).
- __Firebase User Login__. I learned how to use the Firebase to handle user Login events, store user data retrieve user data from the database and render the information in the front-end.
- __Firebase Cloud Functions__. I used the [Firebase cloud functions](https://firebase.google.com/docs/functions/get-started?gen=2nd) to run serverless backend scripts. This is required since it is not safe to expose certain API keys directly to the user side (front-end applications). With the cloud function, I can write functions in Python and call them via HTTP request. Other possible scenarios include calling a Python machine-learning function, etc.
- __Pinecone Vector Database__. I learned how RAG works, how to perform text-to-vector conversion and store the vector data in the Pinecone database, how to search for relevant context data, and how to take these all together to get more accurate responses from OpenAI chat completion API.
- __OpenAI API__. I learned to use the `text-embedding-3-small` model and chat completion API from OpenAI using Python.
- __Webpack__. I gained a deeper understanding of how modular JavaScript works. With modular javascript, I can use various npm packages (first `npm install ___` then `import {} from ""`) in the code instead of using `<script>` tags in the html file, which can be faster and more efficient. The process is, that users write code in the `index.js`, and use tools like the `Webpack` to compile this js file along with other dependent js files into one js file: `main.js` / `bundle.js`. [Tutorial on how to use the Webpack (along with the Firebase).](https://www.youtube.com/watch?v=rQvOAnNvcNQ)
    

## Questions/Problems Faced [Selected Typical Ones]
- __Using the OpenAI API without the Backend?__
    - _Situation_: The vision of this project was to develop a pure front-end app. However, exposing the OpenAI API key on the client/user side is not a safe practice.
    - _Task_: Call the OpenAI API key and run the TexVerse AI in a safe environment, but do not include server/backend deployment.
    - _Actions_: The answer is the Firebase Cloud Functions! I mentioned it in the previous section. Basically, the Firebase hosts servers and users can deploy functions on these servers. To use these functions, just use an HTTP request. I can directly use Python to write the backend function code, with the `.env` file (in the same hierarchy as the `main.py` file) storing API information (the `dotenv` package can load the `.env` content correctly in the Firebase Cloud environment).
    - _Result_: Working TexVerse AI ChatBot!
- __Avoid manually compiling index.js every time__
    - _Situation_: After changing the `index.js` file, I need to run `npm run build` to manually update, which is not efficient.
    - _Task_: Find a way to automatically recompile the `index.js` file when detecting the file is changed.
    - _Actions_: In the development stage, you can use the `webpack --watch` command, which will automatically re-compile everything upon you make any changes to the `index.js` file. You can also add this to the `package.json file` so that you only need to run `npm run watch`. Then right-click in vscode - `open with live server`.
        ```javascript
        "scripts": {
            "watch": "webpack --watch",
        },
        ```
    - _Result_: Fast and efficient iteration!

- __Improving readability when debugging the compiled js file__
    - _Situation_: The compiled result (`main.js`) is a mess! When there is an error, it's impossible to find the exact place in your `index.js` file where is going wrong by checking the error report on `main.js`.
    - _Task_: Find a way to locate the bug line in the original `index.js` file on the inspector tool of Chrome.
    - _Actions_: Adding the devtool in the `webpack.config.js` allows you to locate the position of the bug in your original `index.js` file rather than somewhere in the unreadable compile js file. The output is the `bundle.js` file under the `dist` folder.
        ```javascript
        devtool: 'eval-source-map',
        output: {
            path: path.resolve(__dirname, 'dist'), // output directory
            filename: 'bundle.js', // the compiled JavaScript file
            // publicPath: "/TexVerse-HCI/"
        },
        ```
    - _Result_: Easy debugging!

- __gh-page deployment__.
    - _Situation_: Cannot deploy the `index.html` file to the GitHub pages because GitHub requires this file to be located in the root of the repo or inside a docs folder, while my `index.html` file is located in the `src` folder.
    - _Task_: Find a way to deploy the `index.html` in `src` folder.
    - _Actions_
        - I found a npm tool to do this [gh-pages](https://www.npmjs.com/package/gh-pages), which can publish files to a gh-pages branch on GitHub. Basically, it copies the files under the `dist` folder (including `bundle.js` and `main.js`) to the gh-pages branch. But we still need the index.html and style.css file (which is not in the `dist` folder yet)! To do it, we need `MiniCssExtractPlugin` and `HtmlWebpackPlugin`. Configure the `webpack.config.js` like this:
            ```javascript
            const path = require('path');
                const MiniCssExtractPlugin = require('mini-css-extract-plugin');
                const HtmlWebpackPlugin = require('html-webpack-plugin');

                module.exports = {
                    mode: 'development',
                    entry: './src/index.js',  // the main JavaScript file of the app
                    devtool: 'eval-source-map',
                    output: {
                        path: path.resolve(__dirname, 'dist'), // output directory
                        filename: 'bundle.js', // the compiled JavaScript file
                        // publicPath: "/TexVerse-HCI/"
                    },
                    module: {
                        rules: [
                            {
                                test: /\.css$/, // CSS handle
                                use: [MiniCssExtractPlugin.loader, 'css-loader'],
                            },
                        ],
                    },
                    plugins: [
                        new HtmlWebpackPlugin({
                            template: './src/index.html', // Original index.html
                        }),
                        new MiniCssExtractPlugin({
                            filename: 'style.css', // Output CSS name
                        }),
                    ],
                };
            ```
            Using these two plugins, we can copy the updated CSS and HTML file when we run `npm run deploy`.
        - Add this line to the `package.json` file, and run `npm run deploy` to push changes to the gh-page branch.
            ```javascript
            "scripts": {
                "deploy": "gh-pages -d dist"
            },
            ```
        - __How to debug if the page is not showing correctly?__ First check the gh-pages branch, whether it has all the files (`index.html`, `bundle.js`, `style.css`, etc.). If the page is not loading correctly, open the inspector tool on Chrome, refresh the page, and check the resource loading timeline if there are errors and debug if the resources' paths are incorrect or somehow missing (e.g. are there missing js or CSS files).
        - Tutorial on this npm tool: https://survivejs.com/webpack/techniques/deploying/, https://github.com/turingschool-examples/webpack-starter-kit/blob/main/gh-pages-procedure.md

    - _Result_: Success deployment on the GitHub Pages. When the file is changed, run the following command on the terminal to rebuild and push updates to the gh-pages branch.
        ```terminal
        npm run build
        npm run deploy
        ```



    

## Questions/Uncertainties
- In development, I found in Firebase it's hard to apply multiple filters (e.g. keywords contain "sensing", authors contain "abc", and year range from 2020 to 2024). Another question is that sometimes there might just not be a keyword filter, and the code like `where("keywords", "array-contains-any", filterOption.keywords),` when filterOption.keywords = null will raise an error. It seems to be really complicated if I need first to judge whether `filterOption.keywords` are empty and then apply the `where` command (especially if I need to apply a filter to four or more fields!). I wonder if there's a way to elegantly organize the code and realize this function.
    - I also wonder while it seems easy to apply multiple filters in SQL command, why in Firestore it is so complicated? I even needed to create an index before I could actually use the `where` command. I do not get the rationale for having indexes here.


# Future Works
- Add more functions after logging in.
    - Customized collection: you can collect papers into customized folders.
    - Notes: users can write and save notes for each paper.
    - If you are an admin, you will be granted more access, e.g. adding new papers, editing tags, etc.
- While now the adding paper feature can be realized using the same Python script that is used to do the initial scraping, I want to organize and tidy up the code of this pipeline and build a Firebase Cloud Function for adding new papers.
    - _User input_: Papers URL
    - _Actions Pipeline_: 
        1. scrape metadata (title, abstract, year, authors...)
        2. use Gemini (or other LLMs) to generate keywords based on the abstract (or the actual content of the paper)
        3. scrape the PDF, inject the teaser image to the Firebase Storage, and chunk PDF content to create text embeddings to inject into the Pinecone vector database.
- Add a _search_ function on the sidebar, also add more filter options, including where the paper is published, the constitution of the paper, etc.
    - Maybe using LLM to auto-generate queries to be used in Firebase is an interesting direction as well.