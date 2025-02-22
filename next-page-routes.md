## 📚 Next.js Routing and Links Explained

In Next.js, routing works differently than traditional React apps. Instead of setting up a router manually, **every file in the `pages/` directory automatically becomes a route**. This system is called **File-Based Routing**.

---

### 🗂️ File-Based Routing

The file structure inside the `pages/` directory directly translates to URL routes.

#### ✅ Example File Structure:

```
/pages
  ├── index.js       →  /
  ├── about.js       →  /about
  └── contact.js     →  /contact
```

#### 🌐 URL Mapping:

| File Name           | Route URL        |
|--------------------|------------------|
| `index.js`         | `/` (Homepage)   |
| `about.js`         | `/about`         |
| `contact.js`       | `/contact`       |

Next.js automatically creates routes based on this file structure, with no additional configuration needed.

---

### 🔗 Using Links in Next.js

Next.js provides a special component called `Link` from `next/link` to handle navigation between pages without a full page reload (client-side navigation).

#### ✅ Basic Example:

```jsx
import Link from 'next/link';

export default function Home() {
  return (
    <div>
      <h1>Hello, Next.js!</h1>
      <nav>
        <Link href="/about">About</Link> |
        <Link href="/contact">Contact</Link>
      </nav>
    </div>
  );
}
```

#### 🔄 How Links Work:
- The `Link` component wraps around any HTML element (like `<a>` tags).
- It **pre-fetches** pages in the background, making navigation faster.
- URLs correspond to the file names inside `pages/`.

---

### 📡 cURL Mapping

You can test your routes using `curl` commands from your terminal:

#### ✅ Example cURL Commands:

1. **Home Page (`/`):**
   ```bash
   curl http://localhost:3000/
   ```

2. **About Page (`/about`):**
   ```bash
   curl http://localhost:3000/about
   ```

3. **Contact Page (`/contact`):**
   ```bash
   curl http://localhost:3000/contact
   ```

Each command fetches the corresponding HTML content served by Next.js on the specified route.

---
 
---

### 🚀 Next Steps

Now that you've mastered basic routing:
- Explore **API Routes** by creating files in `pages/api/`
- Learn about **getStaticProps** and **getServerSideProps** for data fetching
- Integrate styling using **Tailwind CSS** or **styled-components**

