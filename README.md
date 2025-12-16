# Just-love
بيع أجهزة الكترونية
import React, { useEffect, useState } from "react";

// متجر المعسلات - مكون React واحد بواجهة RTL (Tailwind) // ملاحظات: هذا مكون قابل للتطوير - يمكنك ربطه بباكند، Stripe أو بوابة دفع أخرى.

export default function HookahShop() { // عينات منتجات (يمكن استبدالها ببيانات من API لاحقًا) const sampleProducts = [ { id: 1, name: "تفاحتين - معسّل 250غ", price: 45, flavor: "تفاح", strength: "متوسط", image: "https://picsum.photos/seed/1/400/300", description: "خلطة تفاح طازجة مع لمسة حلوة.", stock: 20, }, { id: 2, name: "عنب أحمر - معسّل 250غ", price: 50, flavor: "عنب", strength: "قوي", image: "https://picsum.photos/seed/2/400/300", description: "نكهة عنب غنيّة وكثيفة.", stock: 12, }, { id: 3, name: "نعناع بارد - معسّل 250غ", price: 48, flavor: "نعناع", strength: "خفيف", image: "https://picsum.photos/seed/3/400/300", description: "نعناع منعش لحمّاس هادئ.", stock: 30, }, ];

const [products] = useState(sampleProducts); const [query, setQuery] = useState(""); const [filterFlavor, setFilterFlavor] = useState("الكل"); const [cart, setCart] = useState([]); const [showCheckout, setShowCheckout] = useState(false); const [ageVerified, setAgeVerified] = useState(false); const [showProduct, setShowProduct] = useState(null);

useEffect(() => { const v = localStorage.getItem("ageVerified"); if (v === "true") setAgeVerified(true); }, []);

function addToCart(product) { setCart((c) => { const found = c.find((i) => i.id === product.id); if (found) return c.map((i) => (i.id === product.id ? { ...i, qty: i.qty + 1 } : i)); return [...c, { ...product, qty: 1 }]; }); }

function updateQty(id, qty) { setCart((c) => c.map((i) => (i.id === id ? { ...i, qty } : i)).filter((i) => i.qty > 0)); }

function total() { return cart.reduce((s, p) => s + p.price * p.qty, 0); }

function handleCheckout() { if (!ageVerified) { alert("للشراء يجب تأكيد أنك فوق السن القانوني. الرجاء التحقق أولاً."); return; } setShowCheckout(true); }

function confirmAge() { localStorage.setItem("ageVerified", "true"); setAgeVerified(true); }

// تصفية و بحث const flavors = ["الكل", ...Array.from(new Set(products.map((p) => p.flavor)))]; const visible = products.filter((p) => { if (filterFlavor !== "الكل" && p.flavor !== filterFlavor) return false; if (!query) return true; const q = query.trim().toLowerCase(); return ( p.name.toLowerCase().includes(q) || p.description.toLowerCase().includes(q) || p.flavor.toLowerCase().includes(q) ); });

return ( <div className="min-h-screen bg-gray-50 p-6" dir="rtl"> {/* رأس الصفحة */} <header className="max-w-6xl mx-auto flex items-center justify-between mb-6"> <h1 className="text-3xl font-bold">متجر المعسلات</h1> <div className="flex items-center gap-4"> <div className="text-sm">إجمالي السلة: <strong>{total()} ر.س</strong></div> <button onClick={() => setShowCheckout((s) => !s)} className="bg-green-600 text-white px-4 py-2 rounded-lg" > السلة ({cart.length}) </button> </div> </header>

{/* تنبيهات قانونية و تحقق العمر */}
  {!ageVerified && (
    <div className="max-w-6xl mx-auto mb-4 p-4 rounded-lg bg-yellow-100 border border-yellow-300">
      <div className="flex flex-col sm:flex-row sm:justify-between sm:items-center gap-4">
        <div>
          <strong>تنبيه قانوني:</strong> بيع منتجات التبغ محصور بالبالغين فقط. يرجى التأكد من السن قبل المتابعة.
        </div>
        <div className="flex gap-2">
          <button
            onClick={confirmAge}
            className="px-4 py-2 bg-blue-600 text-white rounded-md"
          >أنا فوق السن القانوني</button>
          <button
            onClick={() => alert("لا يمكنك المتابعة إذا لم تكن فوق السن القانوني.")}
            className="px-4 py-2 bg-gray-200 rounded-md"
          >لست فوق السن</button>
        </div>
      </div>
    </div>
  )}

  {/* أدوات البحث و الفلاتر */}
  <div className="max-w-6xl mx-auto mb-6 grid grid-cols-1 md:grid-cols-4 gap-4">
    <div className="md:col-span-3">
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="ابحث عن نكهات، اسم المنتج..."
        className="w-full p-3 rounded-lg border"
      />
    </div>
    <div>
      <select
        value={filterFlavor}
        onChange={(e) => setFilterFlavor(e.target.value)}
        className="w-full p-3 rounded-lg border"
      >
        {flavors.map((f) => (
          <option key={f} value={f}>{f}</option>
        ))}
      </select>
    </div>
  </div>

  {/* منتجات */}
  <main className="max-w-6xl mx-auto grid grid-cols-1 md:grid-cols-3 gap-6">
    {visible.map((p) => (
      <article key={p.id} className="bg-white rounded-xl shadow p-4 flex flex-col">
        <img src={p.image} alt={p.name} className="rounded-md mb-3 h-44 object-cover" />
        <h3 className="text-xl font-semibold">{p.name}</h3>
        <p className="text-sm text-gray-600">{p.description}</p>
        <div className="mt-2 flex items-center justify-between">
          <div className="text-lg font-bold">{p.price} ر.س</div>
          <div className="text-sm text-gray-500">المخزون: {p.stock}</div>
        </div>
        <div className="mt-4 flex gap-2">
          <button
            onClick={() => addToCart(p)}
            className="flex-1 bg-indigo-600 text-white py-2 rounded-lg"
          >أضف إلى السلة</button>
          <button
            onClick={() => setShowProduct(p)}
            className="flex-1 border rounded-lg py-2"
          >تفاصيل</button>
        </div>
      </article>
    ))}

    {/* حالة عدم وجود منتجات */}
    {visible.length === 0 && (
      <div className="md:col-span-3 text-center py-20 text-gray-500">لا توجد منتجات تطابق بحثك.</div>
    )}
  </main>

  {/* سلة جانبية / شاشة الدفع */}
  {showCheckout && (
    <aside className="fixed bottom-6 left-6 right-6 md:right-auto md:left-auto md:top-20 md:right-6 md:w-96 bg-white rounded-xl shadow-lg p-4">
      <h4 className="text-lg font-semibold mb-2">محتويات السلة</h4>
      {cart.length === 0 && <div className="text-gray-500">السلة فارغة.</div>}
      {cart.map((i) => (
        <div key={i.id} className="flex items-center justify-between gap-2 py-2 border-b">
          <div>
            <div className="font-semibold">{i.name}</div>
            <div className="text-sm text-gray-500">{i.price} ر.س</div>
          </div>
          <div className="flex items-center gap-2">
            <input type="number" value={i.qty} min={1} max={99} onChange={(e) => updateQty(i.id, Number(e.target.value))} className="w-16 p-1 border rounded" />
          </div>
        </div>
      ))}

      <div className="mt-3 flex justify-between items-center">
        <div className="font-bold">المجموع: {total()} ر.س</div>
        <div className="flex gap-2">
          <button onClick={() => setShowCheckout(false)} className="px-4 py-2 border rounded">إغلاق</button>
          <button onClick={handleCheckout} className="px-4 py-2 bg-green-600 text-white rounded">المتابعة للدفع</button>
        </div>
      </div>
    </aside>
  )}

  {/* مودال منتج */}
  {showProduct && (
    <div className="fixed inset-0 bg-black/40 flex items-center justify-center p-4">
      <div className="bg-white rounded-xl max-w-2xl w-full p-6">
        <div className="flex justify-between items-start">
          <h3 className="text-2xl font-bold">{showProduct.name}</h3>
          <button onClick={() => setShowProduct(null)} className="text-gray-500">✕</button>
        </div>
        <div className="mt-4 grid md:grid-cols-2 gap-4">
          <img src={showProduct.image} alt={showProduct.name} className="rounded-md w-full h-64 object-cover" />
          <div>
            <p className="text-gray-700">{showProduct.description}</p>
            <p className="mt-3 font-bold">{showProduct.price} ر.س</p>
            <p className="text-sm text-gray-500 mt-2">النكهة: {showProduct.flavor} — القوة: {showProduct.strength}</p>
            <div className="mt-4 flex gap-2">
              <button onClick={() => { addToCart(showProduct); setShowProduct(null); }} className="px-4 py-2 bg-indigo-600 text-white rounded">أضف إلى السلة</button>
              <button onClick={() => setShowProduct(null)} className="px-4 py-2 border rounded">إغلاق</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  )}

  {/* شاشة الدفع الوهمية - نموذج بسيط */}
  {showCheckout && (
    <div className="fixed inset-0 pointer-events-none md:static">
      {/* عند الضغط على متابعة، سنعرض نموذجًا ضمن السلة (يمكن تغييره لصفحة منفصلة) */}
    </div>
  )}

  {/* تذييل */}
  <footer className="max-w-6xl mx-auto mt-12 text-sm text-gray-600">
    <div className="border-t pt-4">ملاحظة: هذا المتجر نموذج تعليمي. قبل تشغيله فعليًا تأكد من:</div>
    <ul className="mt-2 list-disc pr-6">
      <li>التأكد من التراخيص والقوانين المحلية المتعلقة ببيع منتجات التبغ.</li>
      <li>تنفيذ تحقق عمر حقيقي (مثل طلب هويات أو استخدام بوابات تحقق مرخّصة).</li>
      <li>ربط بوابة دفع موثوقة والتحقق من قيود الدفع/التسويق حول منتجات التبغ.</li>
      <li>شروط وأحكام واضحة، سياسة استرجاع وشحن.</li>
    </ul>
  </footer>
</div>

); }

