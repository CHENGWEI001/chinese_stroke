# 中文單字筆順練習本產生器 ✍️
### Chinese Stroke Order & Practice Sheet Generator

A pure frontend, serverless Single Page Application (SPA) designed to generate customizable, high-quality A4 Chinese character writing practice sheets, complete with stroke-by-stroke order sequences and practice grids.

**⚡ Live Demo:** [https://chengwei001.github.io/chinese_stroke/](https://chengwei001.github.io/chinese_stroke/)

---

## 📖 Table of Contents
- [Features](#-features)
- [Design & Architecture](#-design--architecture)
- [Libraries & Technologies Used](#-libraries--technologies-used)
- [How to Use](#-how-to-use)
- [Generating PDFs (Print Settings)](#-generating-pdfs-print-settings)
- [Local Development](#-local-development)

---

## ✨ Features

- **Dynamic Stroke Sequences:** Automatically generates and renders the vertical stroke-by-stroke build-up for any Chinese character.
- **Customizable Layouts:**
  - **Grid Styles:** Choose between **Tianzige (田字格)**, **Mige (米字格)**, or Blank.
  - **Side Boxes (Taiwan Style):** Supports **None**, **1 Box (Pinyin)**, or **2 Boxes (Taiwan Zhuyin + Tone)** to match traditional school practice sheets.
  - **Tracing (描紅):** Configure how many rows should have light-gray character outlines for guided practice before free-hand writing.
  - **New Stroke Highlighting:** Toggle whether to highlight the newest stroke in **red** or keep the sequence in solid black.
- **A4 Smart Pagination:** Automatically calculates layout budgets and handles page breaks if you input more characters than fit on a single page.

---

## 🏗️ Design & Architecture

The application is built with a **Serverless, Print-First** philosophy. It requires no backend database, server-side rendering, or complex build steps.

```
+-------------------------------------------------------------+
|                         User Browser                        |
|                                                             |
|   +------------------+                 +----------------+   |
|   |   User Inputs    |                 |  Tailwind CSS  |   |
|   | & Options (HTML) |                 |   (UI Style)   |   |
|   +--------+---------+                 +-------+--------+   |
|            |                                   |            |
|            v                                   v            |
|   +--------+---------+  Fetch JSON     +-------+--------+   |
|   |  JavaScript App  | --------------->| Hanzi Writer   |   |
|   |  (Core Logic)    |  (on-the-fly)   | Data CDN       |   |
|   +--------+---------+                 +----------------+   |
|            |                                                |
|            v                                                |
|   +--------+---------+                                      |
|   |  Dynamic SVG     |                                      |
|   |  Rendering       |                                      |
|   +--------+---------+                                      |
|            |                                                |
|            v                                                |
|   +--------+---------+                                      |
|   |  @media print    | ===> Perfect A4 PDF / Paper Print    |
|   |  Optimization    |                                      |
|   +------------------+                                      |
+-------------------------------------------------------------+
```

### 1. Pure Frontend & Serverless
All logic resides in a single `index.html` file. When a user inputs characters, the browser dynamically fetches the required stroke data and renders the page. This makes hosting extremely simple, free, and highly secure.

### 2. On-the-Fly Data Fetching
Instead of bundling megabytes of character data, the app queries the **jsDelivr CDN** for individual character data in JSON format only when needed. Fetched data is cached in-memory to prevent redundant network requests.

### 3. Native SVG Rendering
To ensure the output remains razor-sharp when printed or saved to PDF, the app **does not** use canvas or raster images. Instead, it parses the raw SVG paths from the database and programmatically constructs SVG elements.
*   **Coordinate Transformation:** The Hanzi Writer/Make Me a Hanzi dataset uses a 1024x1024 coordinate system with an inverted Y-axis. The app applies a native SVG transform: `scale(1, -1) translate(0, -900)` to render the paths correctly.
*   **Dynamic Scaling:** For the stroke sequence column, the app dynamically calculates the optimal SVG size based on the character's total stroke count to ensure it fits perfectly within the vertical A4 budget.

### 4. Print-First CSS
The app uses a dedicated CSS `@media print` stylesheet. When the browser's print dialog is triggered:
*   All interactive UI elements, sidebars, and headers are hidden (`display: none`).
*   The preview container margins are stripped.
*   Each `.page` element is forced to exactly `210mm x 297mm` (A4 Portrait) with a `page-break-after: always` rule, ensuring a perfect multi-page PDF export.

---

## 🛠️ Libraries & Technologies Used

1.  **[Hanzi Writer Data](https://github.com/chanind/hanzi-writer-data) (via jsDelivr CDN):**
    *   Provides the raw stroke vector data (SVG paths) for over 9,000 Simplified and Traditional Chinese characters.
2.  **[Tailwind CSS](https://tailwindcss.com/) (via CDN):**
    *   Used for rapid prototyping of a clean, modern, and responsive user interface for the control panel and preview area.
3.  **[Google Fonts - Noto Sans/Serif TC](https://fonts.google.com/):**
    *   Loaded to provide high-quality Traditional Chinese fallback fonts.
4.  **System Fonts (Kaiti):**
    *   The app prioritizes system Kaiti fonts (`KaiTi`, `楷体`, `STKaiti`, `BiauKai`) for rendering, as Kaiti is the standard calligraphic style used in Chinese education for stroke practice.

---

## 🚀 How to Use

1.  Open the [Live Demo](https://chengwei001.github.io/chinese_stroke/) in your browser.
2.  **Input Characters:** Type the Chinese characters you want to practice in the input box (e.g., `著細色顏`).
3.  **Customize:**
    *   Adjust the number of columns (characters per page) and practice rows.
    *   Select your preferred grid style (Tianzige or Mige).
    *   Add Pinyin or Taiwan-style Zhuyin boxes.
    *   Set how many rows of "tracing" outlines you want.
4.  Click **"更新預覽" (Update Preview)** to render the sheets.
5.  Click **"列印 / 儲存為 PDF" (Print / Save to PDF)**.

---

## 🖨️ Generating PDFs (Print Settings)

To get a perfect PDF file, configure your browser's print dialog with the following settings:

1.  **Destination (目標):** Select **"Save as PDF"** (另存為 PDF).
2.  **Paper Size (紙張大小):** Select **A4**.
3.  **Layout (版面配置):** Select **Portrait** (直向).
4.  **Margins (邊界):** Select **None** (無) or **Default** (預設).
5.  **Headers and Footers (頁首與頁尾):** **Uncheck** (disable) to remove browser-added URLs and dates.
6.  **Background Graphics (背景圖形):** **CHECK / ENABLE**. This is **critical** to ensure the red grid lines and background colors are printed.

---

## 💻 Local Development

To run or modify this project locally:

1.  Clone the repository:
    ```bash
    git clone https://github.com/CHENGWEI001/chinese_stroke.git
    cd chinese_stroke
    ```
2.  Open `index.html` directly in your browser:
    ```bash
    # On macOS
    open index.html
    # On Linux
    xdg-open index.html
    ```
3.  Alternatively, run a local development server:
    ```bash
    python3 -m http.server 8080
    # Then open http://localhost:8080
    ```
