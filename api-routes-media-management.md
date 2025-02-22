# 🎬 Building a Media Management API with Next.js

In this tutorial, we'll walk through creating a **Next.js** app from scratch and building API routes to handle media management operations like uploading, fetching, updating, and deleting media files. We'll also cover how to test these routes using `curl` commands.

### 🚀 **Project Setup**

#### 1. **Install Required Packages**

Run the following command to initialize your Next.js project and install dependencies:

```bash
npx create-next-app@latest media-manager-app
cd media-manager-app
```

You'll also need a few additional packages for media handling:

```bash
npm install multer dotenv
```

- **`multer`**: Middleware for handling file uploads.
- **`dotenv`**: To manage environment variables securely.

#### 2. **Start the Development Server**

```bash
npm run dev
```

Your app should now be running on `http://localhost:3000`.

#### 1. **Create a New Next.js App**

First, let's initialize a new Next.js project.

```bash
npx create-next-app@latest media-manager-app
cd media-manager-app
```

#### 2. **Project Directory Structure**

```bash
media-manager-app/
├── app/
│   └── api/
│       ├── media/
│       │   ├── upload/
│       │   │   └── route.js        # Upload new media (POST)
│       │   ├── [id]/
│       │   │   ├── route.js        # Fetch media by ID (GET)
│       │   │   ├── update/
│       │   │   │   └── route.js    # Update media info (PUT)
│       │   │   └── delete/
│       │   │       └── route.js    # Delete media (DELETE)
│       └── medias/
│           └── route.js            # List all media (GET)
├── lib/
│   ├── db.js                        # In-memory database for simplicity
│   └── logger.js                    # Logging setup using pino                        # In-memory database for simplicity
└── package.json
```

Each folder inside the `api/` directory corresponds to an API route. Each file named `route.js` handles requests like `GET`, `POST`, `PUT`, and `DELETE`.

---

### 📂 **Step 1: Setting Up the Database and Logger**

Create a simple in-memory database inside `lib/db.js`:

```js
// lib/db.js (In-memory database)
const mediaFiles = [];

export const db = {
  media: {
    create: async ({ data }) => {
      const newMedia = { id: `${Date.now()}`, ...data };
      mediaFiles.push(newMedia);
      return newMedia;
    },
    findUnique: async ({ where }) => {
      return mediaFiles.find((media) => media.id === where.id) || null;
    },
    update: async ({ where, data }) => {
      const index = mediaFiles.findIndex((media) => media.id === where.id);
      if (index !== -1) {
        mediaFiles[index] = { ...mediaFiles[index], ...data };
        return mediaFiles[index];
      }
      return null;
    },
    delete: async ({ where }) => {
      const index = mediaFiles.findIndex((media) => media.id === where.id);
      if (index !== -1) {
        return mediaFiles.splice(index, 1)[0];
      }
      return null;
    },
    findMany: async () => {
      return mediaFiles;
    }
  }
};
```

---

### 🟢 **Step 2: Setting Up Logging**

First, let's configure logging using [Pino](https://getpino.io/#/):

**Install Pino:**
```bash
npm install pino pino-pretty
```

**Create the logger file:**

```js
// lib/logger.js
import pino from "pino";

const logger = pino({
  transport: {
    target: "pino-pretty",
    options: {
      colorize: true,
    },
  },
});

export default logger;
```

---

### 🟢 **Step 3: API Routes Implementation with Logging**

#### 📥 **Uploading Media**

**Directory:** `app/api/media/upload/route.js`

**URL Path:** `/api/media/upload`

```js
import { db } from "@/lib/db";
import logger from "@/lib/logger";

export async function POST(req) {
  logger.info('Media upload request received');
  const mediaData = await req.json();
  logger.debug({ mediaData }, 'Received media data');
  const newMedia = await db.media.create({ data: mediaData });
  logger.info({ newMedia }, 'New media successfully created');
  return new Response(JSON.stringify(newMedia), { status: 201 });
}
```

**Test using curl:**
```bash
curl -X POST http://localhost:3000/api/media/upload \
-H "Content-Type: application/json" \
-d '{"title": "Sample Video", "type": "video", "url": "http://example.com/video.mp4"}'
```

---

#### 🔍 **Fetching Media by ID**

**Directory:** `app/api/media/[id]/route.js`

**URL Path:** `/api/media/[id]`

```js
import { db } from "@/lib/db";
import logger from "@/lib/logger";

export async function GET(req, { params }) {
  const mediaId = params.id;
  logger.info(`Fetching media with ID: ${mediaId}`);
  const media = await db.media.findUnique({ where: { id: mediaId } });
  return new Response(JSON.stringify(media), { status: 200 });
}
```

**Test using curl:**
```bash
curl http://localhost:3000/api/media/12345
```

---

#### ✏️ **Updating Media Information**

**Directory:** `app/api/media/[id]/update/route.js`

**URL Path:** `/api/media/[id]/update`

```js
import { db } from "@/lib/db";
import logger from "@/lib/logger";

export async function PUT(req, { params }) {
  const mediaId = params.id;
  const updatedData = await req.json();
  const updatedMedia = await db.media.update({ where: { id: mediaId }, data: updatedData });
  return new Response(JSON.stringify(updatedMedia), { status: 200 });
}
```

**Test using curl:**
```bash
curl -X PUT http://localhost:3000/api/media/12345/update \
-H "Content-Type: application/json" \
-d '{"title": "Updated Video Title"}'
```

---

#### ❌ **Deleting Media**

**Directory:** `app/api/media/[id]/delete/route.js`

**URL Path:** `/api/media/[id]/delete`

```js
import { db } from "@/lib/db";
import logger from "@/lib/logger";

export async function DELETE(req, { params }) {
  const mediaId = params.id;
  logger.warn(`Deleting media with ID: ${mediaId}`);
  await db.media.delete({ where: { id: mediaId } });
  return new Response(null, { status: 204 });
}
```

**Test using curl:**
```bash
curl -X DELETE http://localhost:3000/api/media/12345/delete
```

---

#### 📜 **Listing All Media**

**Directory:** `app/api/medias/route.js`

**URL Path:** `/api/medias`

```js
import { db } from "@/lib/db";
import logger from "@/lib/logger";

export async function GET() {
  const medias = await db.media.findMany();
  return new Response(JSON.stringify(medias), { status: 200 });
}
```

**Test using curl:**
```bash
curl http://localhost:3000/api/medias
```

---

### 📦 **Environment Variables Setup**

Create a `.env.local` file in the root directory:

```bash
NEXT_PUBLIC_API_URL=http://localhost:3000/api
```

This environment variable can be used in your frontend to make API requests dynamically.

---

### ✅ **Conclusion**

- 🗂️ **Directory Structure:** Maps to URL routes automatically in Next.js.
- 🔗 **API Endpoints:** Reflect the folder structure inside `/api`.
- 💡 **Testing:** Use `curl` commands to verify your endpoints.

This setup provides a fully functional media management API with Next.js that you can extend with real database integration, file uploads, or cloud storage later. 🚀

