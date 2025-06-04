# Gemini Fullstack LangGraph Quickstart

This project demonstrates a fullstack application using a React frontend and a LangGraph-powered backend agent. The agent is designed to perform comprehensive research on a user's query by dynamically generating search terms, querying the web using Google Search, reflecting on the results to identify knowledge gaps, and iteratively refining its search until it can provide a well-supported answer with citations. This application serves as an example of building research-augmented conversational AI using LangGraph and Google's Gemini models.

![Gemini Fullstack LangGraph](./app.png)

## Features

- 💬 Fullstack application with a React frontend and LangGraph backend.
- 🧠 Powered by a LangGraph agent for advanced research and conversational AI.
- 🔍 Dynamic search query generation using Google Gemini models.
- 🌐 Integrated web research via Google Search API.
- 🤔 Reflective reasoning to identify knowledge gaps and refine searches.
- 📄 Generates answers with citations from gathered sources.
- 🔄 Hot-reloading for both frontend and backend development during development.

## Project Structure

The project is divided into two main directories:

-   `frontend/`: Contains the React application built with Vite.
-   `backend/`: Contains the LangGraph/FastAPI application, including the research agent logic.

## Getting Started: Development and Local Testing

Follow these steps to get the application running locally for development and testing.

**1. Prerequisites:**

-   Node.js and npm (or yarn/pnpm)
-   Python 3.8+
-   **Local Ollama Instance**:
    -   Ensure you have Ollama installed and running (e.g., from [ollama.com](https://ollama.com/)).
    -   Pull the desired models you intend to use (e.g., `ollama pull llama3`). The default model names in the agent configuration are `ollama-model`, so ensure you have a model with this name or update the configuration.
-   **Environment Variables (`backend/.env`)**:
    1.  Navigate to the `backend/` directory.
    2.  Create or open your `.env` file (you can copy `backend/.env.example` as a starting point).
    3.  **Ollama Configuration (for LLM tasks)**:
        -   `OLLAMA_BASE_URL`: Set this to your Ollama API endpoint. If not set, it defaults to `http://localhost:11434` in the agent's code.
            Example: `OLLAMA_BASE_URL=http://localhost:11434`
        -   `QUERY_GENERATOR_MODEL`, `REFLECTION_MODEL`, `ANSWER_MODEL`: Optionally specify the Ollama models for different agent tasks. If not set, they default to `"ollama-model"` in the code. Ensure these models are available in your Ollama instance.
            Example: `QUERY_GENERATOR_MODEL=llama3:latest` (and similar for other model variables)
    4.  **Google Search Tool Configuration**:
        -   `GEMINI_API_KEY`: This is still required for the Google Search functionality used by the agent for web research.
            Example: `GEMINI_API_KEY="YOUR_ACTUAL_API_KEY"`

**2. Install Dependencies:**

**Backend:**

```bash
cd backend
pip install .
```

**Frontend:**

```bash
cd frontend
npm install
```

**3. Run Development Servers:**

**Backend & Frontend:**

```bash
make dev
```
This will run the backend and frontend development servers.    Open your browser and navigate to the frontend development server URL (e.g., `http://localhost:5173/app`).

_Alternatively, you can run the backend and frontend development servers separately. For the backend, open a terminal in the `backend/` directory and run `langgraph dev`. The backend API will be available at `http://127.0.0.1:2024`. It will also open a browser window to the LangGraph UI. For the frontend, open a terminal in the `frontend/` directory and run `npm run dev`. The frontend will be available at `http://localhost:5173`._

## How the Backend Agent Works (High-Level)

The core of the backend is a LangGraph agent defined in `backend/src/agent/graph.py`. It follows these steps:

![Agent Flow](./agent.png)

1.  **Generate Initial Queries:** Based on your input, it generates a set of initial search queries using a locally configured Ollama model.
2.  **Web Research:** For each query, it uses the Google Search API (leveraging the configured `GEMINI_API_KEY`) to find relevant web pages.
3.  **Reflection & Knowledge Gap Analysis:** The agent analyzes the search results to determine if the information is sufficient or if there are knowledge gaps. It uses a locally configured Ollama model for this reflection process.
4.  **Iterative Refinement:** If gaps are found or the information is insufficient, it generates follow-up queries (using Ollama) and repeats the web research and reflection steps (up to a configured maximum number of loops).
5.  **Finalize Answer:** Once the research is deemed sufficient, the agent synthesizes the gathered information into a coherent answer, including citations from the web sources, using a locally configured Ollama model.

## Deployment

In production, the backend server serves the optimized static frontend build. LangGraph requires a Redis instance and a Postgres database (as described in the LangGraph documentation). Additionally, when deploying:

-   The **Ollama instance** used for LLM tasks must be accessible to the deployed backend server. You will need to configure `OLLAMA_BASE_URL` (and potentially the specific model names) in the backend's environment to point to this accessible Ollama service.
-   The **`GEMINI_API_KEY`** will still be required for the Google Search functionality.

For more details on deploying LangGraph applications generally, refer to the [LangGraph Documentation](https://langchain-ai.github.io/langgraph/concepts/deployment_options/). Below is an example of how to build a Docker image that includes the optimized frontend build and the backend server and run it via `docker-compose`.

_Note: For the docker-compose.yml example you need a LangSmith API key, you can get one from [LangSmith](https://smith.langchain.com/settings). You would also need to ensure your Docker environment for the backend service can reach your Ollama instance and has the `GEMINI_API_KEY` set._

_Note: If you are not running the docker-compose.yml example or exposing the backend server to the public internet, you update the `apiUrl` in the `frontend/src/App.tsx` file your host. Currently the `apiUrl` is set to `http://localhost:8123` for docker-compose or `http://localhost:2024` for development._

**1. Build the Docker Image:**

   Run the following command from the **project root directory**:
   ```bash
   docker build -t gemini-fullstack-langgraph -f Dockerfile .
   ```
**2. Run the Production Server:**

   ```bash
   GEMINI_API_KEY=<your_gemini_api_key> LANGSMITH_API_KEY=<your_langsmith_api_key> docker-compose up
   ```

Open your browser and navigate to `http://localhost:8123/app/` to see the application. The API will be available at `http://localhost:8123`.

## Technologies Used

- [React](https://reactjs.org/) (with [Vite](https://vitejs.dev/)) - For the frontend user interface.
- [Tailwind CSS](https://tailwindcss.com/) - For styling.
- [Shadcn UI](https://ui.shadcn.com/) - For components.
- [LangGraph](https://github.com/langchain-ai/langgraph) - For building the backend research agent.
- [Ollama](https://ollama.com/) - For running local LLMs for query generation, reflection, and answer synthesis.
- [Google Gemini](https://ai.google.dev/models/gemini) - Used for the Google Search tool integration.

## License

This project is licensed under the Apache License 2.0. See the [LICENSE](LICENSE) file for details. 