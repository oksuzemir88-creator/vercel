import React, { useMemo, useRef, useState, useEffect } from "react";
import { motion, AnimatePresence } from "framer-motion";

// Tek dosya: Paylaşılabilir mini site
// Kullanım: Önizle -> Açılan sayfanın linkini Rumo'ya gönder.
// İstersen URL'ye ?ad=Rumo gibi bir parametre vererek ismi değiştirebilirsin.

export default function RomantiqueConfession() {
  const params = new URLSearchParams(typeof window !== 'undefined' ? window.location.search : "");
  const adParam = params.get("ad");
  const ad = (adParam || "Rumo").trim();

  const [stage, setStage] = useState(0); // 0: giriş, 1: soru, 2: kutlama
  const [hayirPos, setHayirPos] = useState({ x: 0, y: 0 });
  const containerRef = useRef(null);

  // Emoji yağmuru
  const hearts = useMemo(() => Array.from({ length: 40 }).map((_, i) => ({
    id: i,
    left: Math.random() * 100,
    delay: Math.random() * 2,
    duration: 4 + Math.random() * 3,
    size: 18 + Math.random() * 18,
    rotate: (Math.random() * 60) - 30,
    emoji: Math.random() > 0.3 ? "💖" : (Math.random() > 0.5 ? "💘" : "💞"),
  })), []);

  useEffect(() => {
    function handleResize() {
      // Hayır butonu ekran dışına taşmasın diye sıfırla
      setHayirPos({ x: 0, y: 0 });
    }
    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);

  const kaçır = () => {
    const el = containerRef.current;
    if (!el) return;
    const bounds = el.getBoundingClientRect();
    const pad = 60; // buton boşluğu
    const x = (Math.random() * (bounds.width - pad * 2)) - (bounds.width / 2 - pad);
    const y = (Math.random() * (bounds.height - pad * 2)) - (bounds.height / 2 - pad);
    setHayirPos({ x, y });
  };

  const copyLink = async () => {
    try {
      await navigator.clipboard.writeText(window.location.href);
      alert("Link kopyalandı! Rumo'ya gönder.");
    } catch (e) {
      console.log(e);
    }
  };

  return (
    <div ref={containerRef} className="min-h-screen w-full flex items-center justify-center bg-gradient-to-br from-pink-50 via-rose-50 to-fuchsia-50 p-4">
      <div className="max-w-md w-full">
        <motion.div
          className="rounded-2xl shadow-xl bg-white/80 backdrop-blur p-6 md:p-8 border border-pink-100"
          initial={{ y: 20, opacity: 0 }}
          animate={{ y: 0, opacity: 1 }}
          transition={{ type: "spring", stiffness: 120, damping: 14 }}
        >
          <AnimatePresence mode="wait">
            {stage === 0 && (
              <motion.div
                key="stage0"
                initial={{ opacity: 0, scale: 0.98 }}
                animate={{ opacity: 1, scale: 1 }}
                exit={{ opacity: 0, scale: 0.98 }}
                transition={{ duration: 0.3 }}
                className="text-center space-y-5"
              >
                <div className="text-sm uppercase tracking-widest text-rose-500 font-semibold">mini sürpriz ✨</div>
                <h1 className="text-3xl md:text-4xl font-extrabold leading-tight">
                  Merhaba <span className="text-rose-600">{ad}</span> 💐
                </h1>
                <p className="text-gray-600">Sana söylemek istediğim küçük ama kalpten bir şey var…</p>
                <motion.button
                  onClick={() => setStage(1)}
                  whileTap={{ scale: 0.98 }}
                  className="w-full py-3 px-4 rounded-xl bg-gradient-to-r from-rose-500 to-fuchsia-500 text-white font-semibold shadow-md hover:shadow-lg transition"
                >
                  Devam et
                </motion.button>
                <div className="text-xs text-gray-400">İpucu: Sonunda sürpriz var 💫</div>
              </motion.div>
            )}

            {stage === 1 && (
              <motion.div
                key="stage1"
                initial={{ opacity: 0, y: 10 }}
                animate={{ opacity: 1, y: 0 }}
                exit={{ opacity: 0, y: -10 }}
                transition={{ duration: 0.25 }}
                className="relative text-center space-y-6 overflow-hidden"
              >
                <h2 className="text-2xl md:text-3xl font-extrabold">
                  {ad}, benimle <span className="text-rose-600">çıkar mısın</span>? 💞
                </h2>
                <p className="text-gray-600">Bence mükemmel bir ikili oluruz. Ne dersin?</p>

                <div className="h-44 relative flex items-center justify-center">
                  <motion.button
                    onClick={() => setStage(2)}
                    whileTap={{ scale: 0.97 }}
                    className="py-3 px-6 rounded-xl bg-emerald-500 text-white font-semibold shadow hover:shadow-lg"
                  >
                    Evet 💘
                  </motion.button>

                  <motion.button
                    onMouseEnter={kaçır}
                    onMouseDown={kaçır}
                    onTouchStart={kaçır}
                    animate={{ x: hayirPos.x, y: hayirPos.y }}
                    transition={{ type: "spring", stiffness: 120, damping: 10 }}
                    className="py-3 px-6 rounded-xl bg-gray-200 text-gray-700 font-semibold shadow absolute"
                    style={{ left: "50%", transform: `translate(-50%, -50%)` }}
                  >
                    Hayır 😅
                  </motion.button>
                </div>

                <div className="flex gap-2 justify-center text-xs text-gray-400">
                  <button onClick={copyLink} className="underline">Linki kopyala</button>
                  <span>•</span>
                  <a
                    href={`https://wa.me/?text=${encodeURIComponent(ad + ", sana bir şey söylemem lazım…")}`}
                    target="_blank"
                    rel="noreferrer"
                    className="underline"
                  >
                    WhatsApp ile gönder
                  </a>
                </div>
              </motion.div>
            )}

            {stage === 2 && (
              <motion.div
                key="stage2"
                initial={{ opacity: 0, scale: 0.98 }}
                animate={{ opacity: 1, scale: 1 }}
                exit={{ opacity: 0, scale: 0.98 }}
                transition={{ duration: 0.3 }}
                className="text-center space-y-5"
              >
                <h3 className="text-3xl md:text-4xl font-extrabold">
                  Yaşasın! 🎉
                </h3>
                <p className="text-gray-700 text-lg">
                  {ad}, seni gerçekten <span className="text-rose-600 font-semibold">çok</span> seviyorum. ❤️
                </p>
                <p className="text-gray-600">Bunu beraber kutlayalım mı? Dondurma benden 🍦</p>
                <div className="flex flex-col sm:flex-row gap-2 justify-center">
                  <a
                    className="py-3 px-4 rounded-xl bg-rose-500 text-white font-semibold shadow hover:shadow-lg"
                    href={`https://wa.me/?text=${encodeURIComponent(ad + ": Evet dedi! 🎊 İlk buluşmayı planlayalım mı?")}`}
                    target="_blank"
                    rel="noreferrer"
                  >
                    WhatsApp'tan Yaz
                  </a>
                  <button
                    onClick={() => setStage(1)}
                    className="py-3 px-4 rounded-xl bg-gray-200 text-gray-700 font-semibold shadow"
                  >
                    Baştan Oynat ↺
                  </button>
                </div>

                {/* Emoji yağmuru */}
                <div className="pointer-events-none fixed inset-0 overflow-hidden">
                  {hearts.map((h) => (
                    <span
                      key={h.id}
                      className="absolute select-none"
                      style={{
                        left: `${h.left}%`,
                        top: `-40px`,
                        fontSize: `${h.size}px`,
                        animation: `fall ${h.duration}s linear ${h.delay}s infinite`,
                        transform: `rotate(${h.rotate}deg)`,
                      }}
                    >
                      {h.emoji}
                    </span>
                  ))}
                </div>
              </motion.div>
            )}
          </AnimatePresence>
        </motion.div>

        <div className="text-center mt-4 text-xs text-gray-400">
          <span>Made with ❤️ — ismi değiştirmek için URL'ye </span>
          <code className="px-1 py-0.5 bg-gray-100 rounded">?ad=Rumo</code>
          <span> ekleyebilirsin.</span>
        </div>
      </div>

      {/* Stil: düşen emoji animasyonu */}
      <style>{`
        @keyframes fall {
          0% { transform: translateY(-40px) rotate(0deg); opacity: 0; }
          10% { opacity: 1; }
          100% { transform: translateY(110vh) rotate(360deg); opacity: 0; }
        }
      `}</style>
    </div>
  );
}

      
