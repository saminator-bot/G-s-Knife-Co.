import React, { useState, useEffect } from "react";

// G's Knife Co. - Single-file React + Tailwind prototype
// - Editable product pages
// - Team admin editor (protected by a simple password prompt)
// - Reviews pasteable by team
// - Shipping status and payment placeholders
// Persistence: localStorage (replace with real backend for production)

const SAMPLE_PRODUCTS = [
  {
    id: "blade-001",
    name: "Atlas Drop Point",
    price: 249,
    description:
      "Premium S35VN steel blade, G10 handle, hand-finished edge. Classic drop-point with modern flare.",
    images: [],
    sku: "AT-001",
    shippingStatus: "Not shipped",
    published: true,
  },
  {
    id: "blade-002",
    name: "Nova Tanto",
    price: 329,
    description:
      "High-contrast Damascus with a custom tanto profile — bold and refined.",
    images: [],
    sku: "NV-002",
    shippingStatus: "Pre-order",
    published: true,
  },
];

const SAMPLE_REVIEWS = [
  {
    id: "r1",
    author: "M. Carter",
    body: "Insane build quality. Cuts like a dream.",
    date: "2025-09-15",
  },
];

const BRAND = {
  name: "G's Knife Co.",
  colors: {
    black: "#000000",
    odGreen: "#4B5320", // olive drab-ish
    white: "#ffffff",
  },
  logoLines: ["black", "odGreen", "black"],
};

function uid(prefix = "id") {
  return prefix + "-" + Math.random().toString(36).slice(2, 9);
}

function useLocalStorage(key, initial) {
  const [state, setState] = useState(() => {
    try {
      const raw = localStorage.getItem(key);
      return raw ? JSON.parse(raw) : initial;
    } catch (e) {
      return initial;
    }
  });
  useEffect(() => {
    try {
      localStorage.setItem(key, JSON.stringify(state));
    } catch (e) {}
  }, [key, state]);
  return [state, setState];
}

export default function App() {
  // Data stores
  const [products, setProducts] = useLocalStorage("gs_products_v1", SAMPLE_PRODUCTS);
  const [reviews, setReviews] = useLocalStorage("gs_reviews_v1", SAMPLE_REVIEWS);
  const [brand, setBrand] = useLocalStorage("gs_brand_v1", BRAND);

  // UI state
  const [route, setRoute] = useState("home"); // home | product:ID | admin
  const [selectedProductId, setSelectedProductId] = useState(null);
  const [isAdmin, setIsAdmin] = useState(false);

  useEffect(() => {
    // Basic router: check hash
    function onHash() {
      const h = window.location.hash.replace(/^#/, "");
      if (!h) return setRoute("home");
      if (h.startsWith("product/")) {
        setRoute("product");
        setSelectedProductId(h.replace("product/", ""));
      } else if (h === "admin") setRoute("admin");
      else setRoute(h);
    }
    onHash();
    window.addEventListener("hashchange", onHash);
    return () => window.removeEventListener("hashchange", onHash);
  }, []);

  function goToProduct(id) {
    window.location.hash = `product/${id}`;
  }

  function goHome() {
    window.location.hash = "";
  }

  // Admin auth (simple) — replace with SSO / real auth for production
  function promptAdmin() {
    const pw = prompt("Enter admin passcode (temporary)");
    if (pw === "gsknife_admin") {
      setIsAdmin(true);
      window.location.hash = "admin";
    } else {
      alert("Wrong passcode. For production, integrate proper authentication.");
    }
  }

  // Product CRUD
  function createProduct() {
    const newP = {
      id: uid("p"),
      name: "New Product",
      price: 0,
      description: "",
      images: [],
      sku: "",
      shippingStatus: "Not shipped",
      published: false,
    };
    setProducts([newP, ...products]);
    setSelectedProductId(newP.id);
    window.location.hash = `product/${newP.id}`;
  }

  function updateProduct(updated) {
    setProducts(products.map((p) => (p.id === updated.id ? updated : p)));
  }

  function deleteProduct(id) {
    if (!confirm("Delete product? This cannot be undone in local demo.")) return;
    setProducts(products.filter((p) => p.id !== id));
    goHome();
  }

  // Reviews
  function addReview(raw) {
    const r = { id: uid("r"), date: new Date().toISOString().slice(0, 10), ...raw };
    setReviews([r, ...reviews]);
  }

  function bulkPasteReviews(text) {
    // Accept pasted reviews with each line as: Author | Review body
    const lines = text.split(/\n+/).map((l) => l.trim()).filter(Boolean);
    const parsed = lines.map((line) => {
      const parts = line.split("|").map((s) => s.trim());
      return { id: uid("r"), author: parts[0] || "Anonymous", body: parts[1] || parts[0], date: new Date().toISOString().slice(0,10) };
    });
    setReviews([...parsed, ...reviews]);
  }

  // Simple cart + checkout placeholder
  const [cart, setCart] = useLocalStorage("gs_cart_v1", []);
  function addToCart(product) {
    setCart([...cart, { ...product, qty: 1 }]);
    alert("Added to cart (demo)");
  }

  function checkout() {
    alert("Demo checkout — integrate Stripe or other gateway in production. See README in admin.");
  }

  // Simple UI components
  function Logo({ size = 48 }) {
    const L = brand.logoLines || ["black", "odGreen", "black"];
    const colors = {
      black: brand.colors?.black || "#000",
      odGreen: brand.colors?.odGreen || "#4B5320",
      white: brand.colors?.white || "#fff",
    };
    return (
      <div style={{ display: "flex", gap: 4, alignItems: "center" }}>
        <div style={{ width: size, height: size / 3 }} className="flex flex-col justify-between">
          {L.map((c, i) => (
            <div key={i} style={{ background: colors[c], height: size/9, borderRadius: 3 }} />
          ))}
        </div>
        <div style={{ color: colors.white }} className="ml-2 font-bold text-lg">{brand.name}</div>
      </div>
    );
  }

  // Renderers
  function Home() {
    return (
      <div className="min-h-screen bg-black text-white p-6">
        <header className="max-w-6xl mx-auto flex items-center justify-between py-6">
          <div className="flex items-center gap-4">
            <Logo />
            <div className="hidden md:block text-sm opacity-70">Bold. Classy. Crafted.</div>
          </div>
          <nav className="flex items-center gap-4">
            <button onClick={goHome} className="text-sm">Home</button>
            <button onClick={() => window.location.hash = "#reviews"} className="text-sm">Reviews</button>
            <button onClick={() => promptAdmin()} className="text-sm">Team</button>
          </nav>
        </header>

        <section className="max-w-6xl mx-auto grid grid-cols-1 md:grid-cols-2 gap-8 items-center mt-12">
          <div>
            <h1 className="text-4xl font-extrabold">{brand.name}</h1>
            <p className="mt-4 text-lg opacity-80">High-end knives built from premium materials — classic silhouettes with bold, modern details.</p>
            <div className="mt-6 flex gap-4">
              <button onClick={() => window.location.hash = "#products"} className="px-5 py-3 rounded-lg border border-white/20">Shop Knives</button>
              <button onClick={() => promptAdmin()} className="px-5 py-3 rounded-lg bg-[#4B5320]">Team Portal</button>
            </div>
          </div>
          <div className="bg-white/5 rounded-xl p-6 flex items-center justify-center">
            <div className="w-full h-64 rounded-md border border-white/10 flex items-center justify-center">
              <div className="text-center opacity-60">Product spotlight / hero image (replace with real photography)</div>
            </div>
          </div>
        </section>

        <section id="products" className="max-w-6xl mx-auto mt-12">
          <h2 className="text-2xl font-bold mb-4">Featured Knives</h2>
          <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
            {products.filter(p => p.published).map((p) => (
              <article key={p.id} className="bg-white/3 p-4 rounded-lg">
                <div className="h-40 bg-white/5 rounded-md mb-3 flex items-center justify-center">Image</div>
                <h3 className="font-semibold">{p.name}</h3>
                <div className="opacity-70 text-sm">{p.sku}</div>
                <p className="mt-2 text-sm opacity-80">${p.price}</p>
                <div className="mt-4 flex gap-2">
                  <button onClick={() => goToProduct(p.id)} className="px-3 py-2 rounded bg-white/10">View</button>
                  <button onClick={() => addToCart(p)} className="px-3 py-2 rounded border border-white/10">Add</button>
                </div>
              </article>
            ))}
          </div>
        </section>

        <section id="reviews" className="max-w-6xl mx-auto mt-12">
          <h2 className="text-2xl font-bold mb-4">What customers say</h2>
          <div className="grid md:grid-cols-3 gap-4">
            {reviews.map(r => (
              <div key={r.id} className="bg-white/3 p-4 rounded"> 
                <div className="text-sm font-semibold">{r.author}</div>
                <div className="mt-2 text-sm opacity-80">{r.body}</div>
                <div className="mt-3 text-xs opacity-60">{r.date}</div>
              </div>
            ))}
          </div>
        </section>

        <footer className="max-w-6xl mx-auto mt-20 py-8 border-t border-white/5 text-sm opacity-70">© {new Date().getFullYear()} {brand.name}. All rights reserved.</footer>
      </div>
    );
  }

  function ProductPage({ id }) {
    const product = products.find((p) => p.id === id);
    if (!product) return <div className="p-8 text-white">Product not found — <button onClick={goHome}>Go home</button></div>;
    return (
      <div className="min-h-screen bg-black text-white p-6">
        <header className="max-w-6xl mx-auto flex items-center justify-between py-6">
          <div className="flex items-center gap-4"><Logo /><div className="opacity-70">{brand.name}</div></div>
          <div className="flex gap-3">
            <button onClick={goHome} className="text-sm">Home</button>
            <button onClick={() => window.location.hash = "#products"} className="text-sm">Back</button>
          </div>
        </header>

        <main className="max-w-6xl mx-auto grid md:grid-cols-2 gap-8 mt-8">
          <div className="bg-white/5 rounded p-6 flex items-center justify-center">Image / gallery</div>
          <div>
            <h1 className="text-3xl font-bold">{product.name}</h1>
            <div className="opacity-70 mt-2">SKU: {product.sku}</div>
            <p className="mt-4 opacity-80">{product.description}</p>
            <div className="mt-6 flex items-center gap-4">
              <div className="text-2xl font-bold">${product.price}</div>
              <button onClick={() => addToCart(product)} className="px-4 py-2 rounded bg-white/10">Add to cart</button>
            </div>

            <div className="mt-6">
              <div className="text-sm opacity-70">Shipping status: <strong>{product.shippingStatus}</strong></div>
              {isAdmin && (
                <div className="mt-3">
                  <label className="text-xs opacity-70">Update shipping status</label>
                  <select value={product.shippingStatus} onChange={(e) => updateProduct({ ...product, shippingStatus: e.target.value })} className="block mt-2 p-2 bg-black border border-white/10 rounded">
                    <option>Not shipped</option>
                    <option>Processing</option>
                    <option>Shipped</option>
                    <option>Delivered</option>
                    <option>Pre-order</option>
                  </select>
                </div>
              )}

            </div>

            {isAdmin && (
              <div className="mt-6 flex gap-3">
                <button onClick={() => { window.location.hash = "#admin"; setSelectedProductId(product.id); }} className="px-4 py-2 rounded border">Edit product</button>
                <button onClick={() => deleteProduct(product.id)} className="px-4 py-2 rounded border text-red-400">Delete</button>
              </div>
            )}

          </div>
        </main>

        <footer className="max-w-6xl mx-auto mt-20 py-8 border-t border-white/5 text-sm opacity-70">© {new Date().getFullYear()} {brand.name}.</footer>
      </div>
    );
  }

  function Admin() {
    const [editingProduct, setEditingProduct] = useState(() => products.find(p => p.id === selectedProductId) || null);
    useEffect(() => { setEditingProduct(products.find(p => p.id === selectedProductId) || null); }, [selectedProductId, products]);

    return (
      <div className="min-h-screen bg-black text-white p-6">
        <header className="max-w-6xl mx-auto flex items-center justify-between py-6">
          <div className="flex items-center gap-4"><Logo /><div>{brand.name} — Team Portal</div></div>
          <div className="flex gap-3">
            <button onClick={goHome} className="text-sm">Public site</button>
            <button onClick={() => { setIsAdmin(false); window.location.hash = ""; }} className="text-sm">Sign out</button>
          </div>
        </header>

        <main className="max-w-6xl mx-auto grid grid-cols-1 md:grid-cols-3 gap-6 mt-8">
          <aside className="col-span-1 bg-white/3 p-4 rounded">
            <h3 className="font-bold">Products</h3>
            <div className="mt-4 flex flex-col gap-2">
              <button onClick={createProduct} className="px-3 py-2 rounded bg-white/10">+ New product</button>
              <div className="mt-3 max-h-64 overflow-auto">
                {products.map(p => (
                  <div key={p.id} className="p-2 border-b border-white/5 flex items-center justify-between">
                    <div>
                      <div className="font-semibold">{p.name}</div>
                      <div className="text-xs opacity-70">{p.sku}</div>
                    </div>
                    <div className="flex gap-2">
                      <button onClick={() => { setSelectedProductId(p.id); setEditingProduct(p); window.location.hash = `product/${p.id}`; }} className="text-sm">Open</button>
                      <button onClick={() => deleteProduct(p.id)} className="text-sm text-red-400">Del</button>
                    </div>
                  </div>
                ))}
              </div>
            </div>

            <div className="mt-6">
              <h4 className="font-semibold">Bulk reviews</h4>
              <p className="text-xs opacity-70 mt-1">Paste reviews (one per line): Author | Review body</p>
              <textarea id="bulkReviews" className="w-full mt-2 p-2 bg-black border border-white/10 h-24 rounded"></textarea>
              <div className="mt-2 flex gap-2">
                <button onClick={() => {
                  const t = document.getElementById("bulkReviews").value;
                  bulkPasteReviews(t);
                }} className="px-3 py-2 rounded bg-white/10">Paste</button>
                <button onClick={() => { document.getElementById("bulkReviews").value = ""; }} className="px-3 py-2 rounded border">Clear</button>
              </div>
            </div>

            <div className="mt-6">
              <h4 className="font-semibold">Payments</h4>
              <p className="text-xs opacity-70 mt-1">Demo: Connect a payment gateway in production. Recommended: Stripe + webhook to update order/shipping status.</p>
              <button onClick={() => alert("Open Stripe Dashboard and create API keys. In production, server-side integration required.")} className="mt-2 px-3 py-2 rounded bg-[#4B5320]">Stripe setup</button>
            </div>

            <div className="mt-6">
              <h4 className="font-semibold">Brand</h4>
              <div className="mt-2 text-xs">Name</div>
              <input value={brand.name} onChange={(e) => setBrand({ ...brand, name: e.target.value })} className="w-full p-2 bg-black border border-white/10 rounded mt-2" />
              <div className="mt-2 text-xs">Logo lines order (comma separated: black, odGreen, black)</div>
              <input value={(brand.logoLines || []).join(",")} onChange={(e) => setBrand({ ...brand, logoLines: e.target.value.split(",").map(s => s.trim()) })} className="w-full p-2 bg-black border border-white/10 rounded mt-2" />
            </div>

          </aside>

          <section className="col-span-2 bg-white/5 p-4 rounded">
            <h3 className="font-bold">Edit Product</h3>
            {editingProduct ? (
              <div className="mt-4 space-y-3">
                <label className="text-xs">Name</label>
                <input value={editingProduct.name} onChange={(e) => setEditingProduct({ ...editingProduct, name: e.target.value })} className="w-full p-2 bg-black border border-white/10 rounded" />

                <label className="text-xs">SKU</label>
                <input value={editingProduct.sku} onChange={(e) => setEditingProduct({ ...editingProduct, sku: e.target.value })} className="w-full p-2 bg-black border border-white/10 rounded" />

                <label className="text-xs">Price</label>
                <input type="number" value={editingProduct.price} onChange={(e) => setEditingProduct({ ...editingProduct, price: Number(e.target.value) })} className="w-full p-2 bg-black border border-white/10 rounded" />

                <label className="text-xs">Description</label>
                <textarea value={editingProduct.description} onChange={(e) => setEditingProduct({ ...editingProduct, description: e.target.value })} className="w-full p-2 bg-black border border-white/10 rounded h-28" />

                <label className="text-xs">Published</label>
                <input type="checkbox" checked={editingProduct.published} onChange={(e) => setEditingProduct({ ...editingProduct, published: e.target.checked })} />

                <label className="text-xs block mt-2">Shipping status</label>
                <select value={editingProduct.shippingStatus} onChange={(e) => setEditingProduct({ ...editingProduct, shippingStatus: e.target.value })} className="w-full p-2 bg-black border border-white/10 rounded">
                  <option>Not shipped</option>
                  <option>Processing</option>
                  <option>Shipped</option>
                  <option>Delivered</option>
                  <option>Pre-order</option>
                </select>

                <div className="flex gap-2 mt-3">
                  <button onClick={() => { updateProduct(editingProduct); alert('Saved (local demo)'); }} className="px-3 py-2 rounded bg-[#4B5320]">Save</button>
                  <button onClick={() => setEditingProduct(products.find(p => p.id === selectedProductId) || null)} className="px-3 py-2 rounded border">Reset</button>
                </div>

                <div className="mt-6">
                  <h4 className="font-semibold">Team notes</h4>
                  <p className="text-xs opacity-70">Use this area to add QC notes or internal product details (local demo only).</p>
                  <textarea placeholder="QC notes..." className="w-full p-2 bg-black border border-white/10 rounded h-20 mt-2"></textarea>
                </div>

              </div>
            ) : (
              <div className="mt-4">Select a product to edit from the left.</div>
            )}
          </section>

        </main>

        <footer className="max-w-6xl mx-auto mt-20 py-8 border-t border-white/5 text-sm opacity-70">Team portal — demo mode.</footer>
      </div>
    );
  }

  // Router
  if (route === "home") return <Home />;
  if (route === "product") return <ProductPage id={selectedProductId} />;
  if (route === "admin") return isAdmin ? <Admin /> : (<div className="min-h-screen bg-black text-white p-6"><div className="max-w-3xl mx-auto text-center mt-20"><h2>Team portal</h2><p className="mt-4">You must sign in as a team member to access editing tools.</p><div className="mt-6"><button onClick={promptAdmin} className="px-4 py-2 rounded bg-[#4B5320]">Sign in</button></div></div></div>);

  return <Home />;
}

/*
README / Next steps (in-app):
- This is a fully client-side prototype. For production:
  1) Replace localStorage with a backend (Firebase, Supabase, or your custom API) to persist products, orders, and reviews.
  2) Integrate Stripe (or another gateway) server-side to handle payments and webhooks. Webhooks let you update order and shipping status automatically.
  3) Add authentication (Teams should use SSO or role-based auth; consider Firebase Auth / Auth0).
  4) For hosting, use Vercel/Netlify/Cloudflare Pages. Or sign up at B12 and import the project if you prefer their builder.
  5) Replace placeholder images with high-quality product photography.
- Colors: adjust the brand.colors object above or in the admin UI.
*/
