# Image Tool

A premium, modern image manipulation tool built with **Vue 3**, **TypeScript**, and **Vite**. This application runs entirely in the browser and offers a slick glassmorphism UI.

## Features

-   **Image Upload**: Drag & Drop or click to upload images.
-   **Cropping**: Advanced cropping functionality using `cropperjs` logic with a custom UI.
    -   Freeform crop.
    -   Resize crop box.
-   **Transformations**:
    -   **Rotate**: 90-degree increments (Left/Right).
    -   **Flip**: Horizontal and Vertical mirroring.
    -   **Zoom**: Zoom in and out of the canvas.
-   **Export**: Download the manipulated image as high-quality PNG/JPG.
-   **Dark Mode UI**: Professional dark theme with glassmorphism effects and smooth animations.

## Tech Stack

-   **Framework**: Vue 3 (Composition API + `<script setup>`)
-   **Language**: TypeScript
-   **Build Tool**: Vite
-   **Styling**: Vanilla CSS (Variables, Flexbox, Transitions)
-   **Icons**: Phosphor Icons (`@phosphor-icons/web`)
-   **Core Logic**: `cropperjs` (v1.6.2)
-   **Package Manager**: `pnpm`

## Project Setup

Ensure you have `pnpm` installed.

### 1. Install Dependencies

```bash
pnpm install
```

### 2. Run Development Server

```bash
pnpm run dev
```

### 3. Build for Production

```bash
pnpm run build
```

## Project Structure

-   `src/components/ImageUploader.vue`: Handles file drag-and-drop and validation.
-   `src/components/ImageEditor.vue`: Wraps `cropperjs` instance and manages canvas/image state.
-   `src/components/Controls.vue`: UI toolbar for interacting with the editor (rotate, flip, crop toggles).
-   `src/style.css`: Global design system (colors, glass effects, typography).
