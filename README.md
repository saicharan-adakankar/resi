# RESI — Resume Intelligence

Web editor: https://saicharan-adakankar.github.io/resi/

Static resume editor with an optional Groq AI Worker. The site runs on GitHub Pages; no laptop server is required.

## One-time Groq setup
1. Create a Groq account at https://console.groq.com and create an API key under API Keys. Never paste the key into the website, chat, or GitHub files.
2. In Cloudflare Workers & Pages, create a Worker named `resi-groq`. Replace its code with `backend/worker.js` and deploy.
3. Under the Worker's Settings → Variables and Secrets, add:
   - Secret `GROQ_API_KEY`: your Groq key.
   - Text `MODEL`: `openai/gpt-oss-20b` (strict structured outputs required).
   - Text `ALLOWED_ORIGIN`: the exact frontend origin, currently `https://saicharan-adakankar.github.io`. For GitHub Pages use `https://YOUR-USER.github.io`, without the repository path or trailing slash.
4. Deploy the Worker changes. Copy its HTTPS URL.
5. In RESI → AI, enter the Worker URL , then Apply. Import your resume or enter real information, choose a role, and click Build resume.
6. Check Assistant for questions or Review complete resume proposal. Review and Apply the draft, edit, then Export PDF.

For CLI deployment, run `npx wrangler deploy` from `backend`, then `npx wrangler secret put GROQ_API_KEY`. The included wrangler.toml sets the current frontend origin and default model. Change ALLOWED_ORIGIN when moving the frontend.

## What is ready and what needs activation
The frontend and backend are wired for Groq. Live generation requires your Groq key and deployed Worker. Automated checks use mocked Groq responses; they do not prove account quota, model entitlement, or live generation quality. Groq's free plan has model-specific quotas. Rate-limit errors appear in chat; no automatic retry or upgrade occurs.

Current model: GPT-OSS 20B hosted by Groq. No ChatGPT subscription is required. The default is an inference, not a live web research service: supply recent job descriptions for current role requirements. Skills are suggestions until the user confirms them. Resume drafts require review before replacing the editor. The score is a local heuristic, not an employer ATS result.

## Team use and privacy
The Worker has no app-password check. Anyone knowing the endpoint can consume its quota; CORS alone is not authentication. Add edge rate limits and account spending controls before broader use. The key stays server-side. Resume/reference/job text and recent chat are sent to the Worker and Groq on Build or Send. The Worker does not write resume content to a database or application logs. Review Groq's data terms for your use case.
Recent chat is held in the tab and limited to the latest 12 messages (up to 3,000 characters each on the backend). New conversation clears that history. A page refresh clears chat. Resume edits and additional information are stored in localStorage on the device; export JSON backups. Resumes are not shared across teammates automatically.

## GitHub Pages
Upload this repository and enable Settings → Pages → Source → GitHub Actions. The included `.github/workflows/pages.yml` deploys `dist` on pushes to main. This does not deploy the separate Worker; configure it once above.

## Import and export
PDF/DOCX/TXT/JSON import; PDF and DOCX extraction requires internet for pinned reader libraries. Scanned PDFs need external OCR. Review imported text and remove the reference section after organizing your resume. Export PDF through browser Print → Save as PDF (A4, browser headers/footers off), TXT, Word-compatible HTML `.doc` (not native DOCX), and structured JSON backups.

## Checks
`node backend/worker.test.mjs` checks authentication, CORS, request handling, structured Groq requests, conversation forwarding, and common upstream failures using mocks.
