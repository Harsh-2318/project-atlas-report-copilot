# AI-assisted development disclosure

- AI tools used: ChatGPT.
- Tasks where AI assistance was used: code review, debugging, calculation-path fix, Streamlit UI scaffolding, test additions and documentation edits.
- Code personally reviewed and tested: The project owner should review all code before submission and be prepared to explain it.
- Tests run during this preparation: Python compilation; unit/regression tests; FastAPI TestClient checks for health, the R-101 calculation response, citations and C-02 denial for unpurchased R-101.
- Known limitations and unverified areas: Docker Compose could not be executed in the current build environment because Docker CLI was unavailable. The Streamlit UI was syntax-compiled but not browser-tested here. Chroma mode and a full 25-question evaluation were not verified in this pass.
- Live discussion: The project owner should only confirm this after reviewing the implementation and understanding its architecture, limitations and security decisions.
