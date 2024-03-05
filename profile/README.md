#  TexVerse
__One Sentence Description__: It's an online knowledge base of HCI textile research 🧶, it walks you through related works in an friendly gallery view of academic papers 🖼️ and has a powerful AI research assistant 🤖 backed with a strong vector database storing context information of past research in this field.

# Technologies used
- __Web scrapers__ (the content of week2): I wrote a Python scraper to collect paper data (pdf file, paper title, abstract, citation numbers, date, etc.) from the ACM digital library.
- __Data storage__ (the content of week3): I stored all paper data on Firebase, because I want to deploy my app directly on GitHub Page, so I want to have a pure front-end app. While Azure doesn’t support data access from front-ends, Firebase supports direct front-end access to the firestore database. For some neccesary backend operations, e.g. using OpenAI API to acquire the response from ChatGPT, I deployed Firebase Cloud Functions to perform backend tasks.
- __Data visualization__ (the content of week5) using D3.js: I used D3 to create some glyphs, e.g. the date filter can be visualized as bar charts with d3.brush to select date range.
- __LLM__ (the content of week6): I converted the content of every research paper to vectors and used the RAG method to provide more contextual information for the LLM to get more accurate and relevant answers. In the data collection phase, I also tried to use the Gemini API (which was free!) to generate keywords (I concluded several aspects I want to know, e.g. fabrication method, machine used to make the fabric, fabric applications, etc.) based on the abstract so I can filter or search with these keywords in the final web app.
- __Front-end development__ (the content of week7): The webpage was developed via HTML, CSS, and JS (using Bootstrap5) and deployed on GitHub Page.

# The problems trying to solve
I'm an HCI researcher focusing on textile fabrication research. I had a hard time organizing my knowledge base on the research field by constantly switching between various apps, including Zotero, Notion, Apple Notes, etc. Now I want to have a "_one-site-for-all_" solution to construct my knowledge base on related works in a clean and organized way. To be specific, this web app integrates several features I really need: it has automatically generate the keywords of several aspects I'm interested in on the research so that I can have a better understanding of whether it's relevant before diving into it, it displays papers in cards so that I can see the teaser image of the research without having to click into any further links, and it also has a powerful AI, which can assist me in the research so that I can come up with better research ideas and have a less stressful time in finding and organizing works when I write the "related works" section.

# How to run
Visit https://texverse-hci.github.io/.
## Featuring
- Get data of HCI textile research paper from the Firebase firestore data base (with an initial amount of 220 relevant papers, which is scraped from the ACM digital library on Feb, 2024). You can also apply filter to paper with several metadata.
    - With the card (gallery) view of papers, you can gain a quick glimpse of all relevant papers all at once. The _teaser image_ part meant to serve as the "memory landmard" for each paper (you see the image, and you recall details on that paper. It works better than titles sometimes).
- After logging in, you can collect papers 🌟.
- Chat with the TexVerse AI bot! It's equipped with all the paper text data (content in the paper's pdfs!) and now an expert in HCI textile research.
    - Using RAG, the app will fetch most relevant context extracted from all the papers (the paper content text was chunked based on paragraphs) and feed to the prompt with your question.

# Reflections
## Lessons learned
- __Firebase database__. I learned how to store data with multiple attributes into the firebase database and acquire data from the firebase on the front-end side, and how not to load data all at once, but divide the data into small batches, loading more on certain triggers (e.g. scrolled to the bottom of the page).
- __Firbase User Login__. I learned how to use the Firebase to handle user Login events, store user data and retrieve user data from the database and render the information in the front-end.
- __Firebase Cloud Functions__. I used the [firebase cloud functions](https://firebase.google.com/docs/functions/get-started?gen=2nd) to run serverless backend scripts. This is required since it is not safe to expose certain API keys directly to the user side (front-end applications). With the cloud function, I can write functions in Python and call it via HTTP request. Other possible scenarios including calling a Python machine learning function, etc.
- __Pincone Vector Database__. I learned how RAG works, how to perform text to vector convertion and store the vector data in the Pinecone database, how to search for relevant context data, and how to taking these all together to get more accurate respond from OpenAI chat completion API.
- __OpenAI API__. I learned to use the `text-embedding-3-small` model and chat completion API from OpenAI using Python.
- __Webpack__. I gained deeper understanding of how modular javascript works. With modular javascript, I can use various npm packages (first `npm install ___` then `import {} from ""`) in the code instead of using `<script>` tags in the html file, which can be faster more efficient. The process is, users write code in the `index.js`, use tools like `webpack` to compile this js file along with other dependent js files into one js file: `main.js` / `bundle.js`. [Tutorial on how to use webpack (along with the Firebase).](https://www.youtube.com/watch?v=rQvOAnNvcNQ)
    - Trick 1: add the devtool inthe `webpack.config.js` allows you to locate the position of the bug in your original `index.js` file rather than somewhere in the unreadable compile js file.
        ```devtool: 'eval-source-map',
        output: {
            path: path.resolve(__dirname, 'dist'), // output directory
            filename: 'bundle.js', // the compiled JavaScript file
            // publicPath: "/TexVerse-HCI/"
        },
        ```
    - Trick 2: you have to compile again everytime to see the actual result. In the development stage, you can use `webpack --watch` command, which will automatically re-compile everything upon you make any changes to the `index.js` file. You can also add this to the `package.json file`, so that you only need to run `npm run watch`. Then right click in vscode - `open with live server`.
        ```
        "scripts": {
            "watch": "webpack --watch",
        },
        ```

- __gh-page deployment__. It should be easy to depoly the `index.html` file to the GitHub pages, but it is definitely more complicated to do so when using the Webpack, where the `index.html`, `index.js`, and other CSS files usually locate in the `src` folder. The tool to do this is [gh-pages](https://www.npmjs.com/package/gh-pages), a npm package to publish files to a gh-pages branch on GitHub. Basically it copies the files under the dist folder (including `bundle.js` and `main.js`) to the gh-pages branch. But we still need the index.html and style.css file! To do it, we need `MiniCssExtractPlugin` and `HtmlWebpackPlugin`. Configure the `webpack.config.js` like this:
    ```const path = require('path');
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
    - Add this line to `package.json` file, and run `npm run deploy` to push changes to the gh-page branch.
        ```"scripts": {
            "deploy": "gh-pages -d dist"
        },```
    - __To debug this step__, first check the gh-pages branch, whether it has all the files (`index.html`, `bundle.js`, `style.css`, etc.). If the page is not loading correctly, open inspector tool on Chrome, refresh the page, and check the resource loading timeline if there are errors and debug if the resources' paths are incorrect or somehow missing.
    - Tutorial on this: https://survivejs.com/webpack/techniques/deploying/, https://github.com/turingschool-examples/webpack-starter-kit/blob/main/gh-pages-procedure.md

## Questions/Uncertainties
- In development, I found in firebase it's hard to apply multiple filters (e.g. keywords contain "sensing", author contain "abc", and year range from 2020 to 2024). Another question is that sometimes there might just not be keyword filter, and the code like `where("keywords", "array-contains-any", filterOption.keywords),` when filterOption.keywords = null will raise an error. It seems to be really complicated if I need first to judge whether `filterOption.keywords` is empty then apply the `where` command (especially I need to apply filer to four or more fields!). I wonder if there's a way to elegantly organize the code.
    - I also wonder while it seems easy to apply multiple filters in SQL command, why in Firestore it is so complicated? I even needed to create index before I could actually use the `where` command. I do not get the rational of having indexes here.


# Future Works
- Add more functions after logging in.
    - Customized collection: you can collect papers into customized folders.
    - Notes: users can write and save notes for each paper.
    - If you are an admin, you will be granted more access, e.g. adding new papers, editing tags, etc (now the adding paper feature can be realized using the same Python script that is used to do the inital scraping).
- Add a _search_ function on the sidebar, also add more filter options, including where the paper is published, the constitution of the paper, etc.