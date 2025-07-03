import React, { useState, useEffect, useRef } from 'react';

// Firebase Imports
import { initializeApp } from "firebase/app";
import { getAuth, signInAnonymously, onAuthStateChanged, signInWithCustomToken } from "firebase/auth";
import { getFirestore, doc, setDoc, onSnapshot, collection, addDoc, deleteDoc, serverTimestamp } from "firebase/firestore";

// ============================================================================
// Firebase Configuration (Akan diisi oleh environment)
// ============================================================================
const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {};
const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';

// ============================================================================
// Konfigurasi dan Data
// ============================================================================
const navLinks = [
    { name: 'Beranda', page: 'home' },
    { name: 'Keunggulan', page: 'keunggulan' },
    { name: 'Produk', page: 'produk' },
    { name: 'Alat Bantu ✨', page: 'alatbantu' },
    { name: 'Testimoni', page: 'testimoni' },
    { name: 'FAQ', page: 'faq' },
];

// ============================================================================
// Komponen Hook Kustom
// ============================================================================
const useIntersectionObserver = (options) => {
    const [elements, setElements] = useState([]);
    const [entries, setEntries] = useState([]);
    const observer = useRef(null);

    useEffect(() => {
        if (observer.current) observer.current.disconnect();
        observer.current = new IntersectionObserver((observedEntries) => setEntries(observedEntries), options);
        const currentObserver = observer.current;
        if (elements.length > 0) {
            elements.forEach(element => currentObserver.observe(element));
        }
        return () => currentObserver?.disconnect();
    }, [elements, options]);

    return [setElements, entries];
};

// ============================================================================
// Komponen UI (Reusable)
// ============================================================================
const AnimatedSection = ({ children, className = '' }) => {
    const [setElements, entries] = useIntersectionObserver({ threshold: 0.1, rootMargin: "0px" });

    useEffect(() => {
        const childNodes = Array.from(document.querySelectorAll('.fade-in-up'));
        setElements(childNodes);
    }, [setElements, children]);

    entries.forEach(entry => {
        if (entry.isIntersecting) entry.target.classList.add('visible');
    });

    return <div className={className}>{children}</div>;
};

const LoadingSpinner = ({ text = "AI sedang berpikir..." }) => (
    <div className="flex justify-center items-center p-4">
        <div className="animate-spin rounded-full h-10 w-10 border-b-2 border-brand-primary"></div>
        <p className="ml-4 text-gray-600">{text}</p>
    </div>
);

const Notification = ({ message, show }) => {
    if (!show) return null;
    return (
        <div className="fixed bottom-5 right-5 bg-green-500 text-white px-6 py-3 rounded-lg shadow-lg animate-fade-in">
            {message}
        </div>
    );
};

// ============================================================================
// Komponen Halaman (Pages)
// ============================================================================
const HomePage = ({ onNavigate }) => (
    <>
        <section className="hero-bg pt-32 pb-20 md:pt-48 md:pb-32 text-white">
            <div className="container mx-auto px-6 text-center">
                <h1 className="text-3xl md:text-5xl lg:text-6xl font-extrabold leading-tight mb-4">
                    Bukan Sekadar Supplier. Kami <span className="text-brand-secondary">Partner Pertumbuhan Bisnis</span> Anda.
                </h1>
                <p className="text-lg md:text-xl max-w-3xl mx-auto mb-8 text-gray-200">
                    Stok lengkap, harga kompetitif, dan kini dengan alat bantu AI yang bisa menyimpan ide-ide terbaik Anda.
                </p>
                <div className="flex justify-center flex-col sm:flex-row space-y-4 sm:space-y-0 sm:space-x-4">
                    <button onClick={() => onNavigate('kontak')} className="bg-brand-secondary text-white font-bold px-8 py-4 rounded-lg shadow-xl hover:bg-yellow-500 transition duration-300 transform hover:scale-105 text-lg">
                        Daftar Sebagai Mitra
                    </button>
                    <button onClick={() => onNavigate('alatbantu')} className="bg-white/20 backdrop-blur-sm text-white font-semibold px-8 py-4 rounded-lg hover:bg-white/30 transition duration-300 text-lg">
                        Coba Alat Bantu AI ✨
                    </button>
                </div>
            </div>
        </section>
        <section className="bg-white py-12">
            <div className="container mx-auto px-6">
                <div className="text-center">
                    <h3 className="text-sm font-semibold text-gray-500 uppercase tracking-wider">Dipercaya oleh Ratusan Pebisnis di Seluruh Nusantara</h3>
                    <div className="flex flex-wrap justify-center items-center mt-6 space-x-8 md:space-x-12 opacity-70">
                        <img src="https://placehold.co/120x60/cccccc/999999?text=Warung+Jaya" alt="Logo Mitra Warung Jaya" className="h-10 my-2"/>
                        <img src="https://placehold.co/120x60/cccccc/999999?text=Resto+Sedap" alt="Logo Mitra Resto Sedap" className="h-10 my-2"/>
                        <img src="https://placehold.co/120x60/cccccc/999999?text=Catering+Berkah" alt="Logo Mitra Catering Berkah" className="h-12 my-2"/>
                        <img src="https://placehold.co/120x60/cccccc/999999?text=Toko+Makmur" alt="Logo Mitra Toko Makmur" className="h-10 my-2"/>
                        <img src="https://placehold.co/120x60/cccccc/999999?text=Hotel+Nusantara" alt="Logo Mitra Hotel Nusantara" className="h-12 my-2"/>
                    </div>
                </div>
            </div>
        </section>
    </>
);

const KeunggulanPage = () => ( <AnimatedSection className="py-24 md:py-32 bg-brand-light"> <div className="container mx-auto px-6"> <div className="text-center mb-12 fade-in-up"> <h2 className="text-3xl md:text-4xl font-bold text-brand-primary">Mengapa Memilih Kami?</h2> <p className="mt-4 text-lg text-gray-600 max-w-2xl mx-auto">Kami memahami tantangan bisnis Anda. Inilah solusi yang kami tawarkan untuk memastikan usaha Anda berjalan lancar dan lebih menguntungkan.</p> </div> <div className="grid md:grid-cols-2 lg:grid-cols-3 gap-8"> <div className="bg-white p-8 rounded-xl shadow-lg hover:shadow-2xl transition-shadow duration-300 transform hover:-translate-y-2 fade-in-up"> <div className="bg-blue-100 text-brand-primary rounded-full w-16 h-16 flex items-center justify-center mb-6"><svg xmlns="http://www.w3.org/2000/svg" className="h-8 w-8" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M17 9V7a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2m2 4h10a2 2 0 002-2v-6a2 2 0 00-2-2H9a2 2 0 00-2 2v6a2 2 0 002 2zm7-5a2 2 0 11-4 0 2 2 0 014 0z" /></svg></div> <h3 className="text-xl font-bold mb-3 text-brand-dark">Harga Kompetitif & Transparan</h3> <p className="text-gray-600">Dapatkan harga terbaik langsung dari sumbernya. Tidak ada biaya tersembunyi, membantu Anda memaksimalkan margin profit.</p> </div> <div className="bg-white p-8 rounded-xl shadow-lg hover:shadow-2xl transition-shadow duration-300 transform hover:-translate-y-2 fade-in-up" style={{transitionDelay: '0.1s'}}> <div className="bg-blue-100 text-brand-primary rounded-full w-16 h-16 flex items-center justify-center mb-6"><svg xmlns="http://www.w3.org/2000/svg" className="h-8 w-8" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" /></svg></div> <h3 className="text-xl font-bold mb-3 text-brand-dark">Kualitas Produk Terjamin</h3> <p className="text-gray-600">Kami menerapkan kontrol kualitas yang ketat. Hanya produk terbaik yang sampai ke tangan Anda dan pelanggan Anda.</p> </div> <div className="bg-white p-8 rounded-xl shadow-lg hover:shadow-2xl transition-shadow duration-300 transform hover:-translate-y-2 fade-in-up" style={{transitionDelay: '0.2s'}}> <div className="bg-blue-100 text-brand-primary rounded-full w-16 h-16 flex items-center justify-center mb-6"><svg xmlns="http://www.w3.org/2000/svg" className="h-8 w-8" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path d="M9 17a2 2 0 11-4 0 2 2 0 014 0zM19 17a2 2 0 11-4 0 2 2 0 014 0z" /><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M13 16V6a1 1 0 00-1-1H4a1 1 0 00-1 1v10l2 2h8a1 1 0 001-1zM3 11h10" /></svg></div> <h3 className="text-xl font-bold mb-3 text-brand-dark">Pengiriman Cepat & Andal</h3> <p className="text-gray-600">Hindari stok kosong. Armada kami siap mengantar pesanan Anda tepat waktu, sesuai jadwal yang Anda tentukan.</p> </div> </div> </div> </AnimatedSection> );
const ProdukPage = ({ onNavigate }) => ( <AnimatedSection className="py-24 md:py-32 bg-white"> <div className="container mx-auto px-6"> <div className="text-center mb-12 fade-in-up"> <h2 className="text-3xl md:text-4xl font-bold text-brand-primary">Kategori Produk Terlengkap</h2> <p className="mt-4 text-lg text-gray-600 max-w-2xl mx-auto">Semua kebutuhan pokok untuk bisnis Anda tersedia dalam satu tempat.</p> </div> <div className="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6"> <div className="group bg-gray-50 rounded-lg shadow-md overflow-hidden transform hover:scale-105 transition-transform duration-300 fade-in-up"><img src="https://images.unsplash.com/photo-1599940778173-024d61060d37?q=80&w=1964&auto=format&fit=crop" alt="Beras dan Biji-bijian" className="h-40 w-full object-cover"/><div className="p-4"><h4 className="font-bold text-lg text-brand-dark">Beras & Biji-bijian</h4><p className="text-sm text-gray-500">Kualitas premium & medium</p></div></div> <div className="group bg-gray-50 rounded-lg shadow-md overflow-hidden transform hover:scale-105 transition-transform duration-300 fade-in-up" style={{transitionDelay: '0.1s'}}><img src="https://images.unsplash.com/photo-1625772659958-d35b7f70623e?q=80&w=1974&auto=format&fit=crop" alt="Minyak Goreng" className="h-40 w-full object-cover"/><div className="p-4"><h4 className="font-bold text-lg text-brand-dark">Minyak & Lemak</h4><p className="text-sm text-gray-500">Berbagai merek terpercaya</p></div></div> <div className="group bg-gray-50 rounded-lg shadow-md overflow-hidden transform hover:scale-105 transition-transform duration-300 fade-in-up" style={{transitionDelay: '0.2s'}}><img src="https://images.unsplash.com/photo-1587122194318-2150a1811529?q=80&w=1974&auto=format&fit=crop" alt="Gula dan Pemanis" className="h-40 w-full object-cover"/><div className="p-4"><h4 className="font-bold text-lg text-brand-dark">Gula & Pemanis</h4><p className="text-sm text-gray-500">Gula pasir, gula merah, dll.</p></div></div> <div className="group bg-gray-50 rounded-lg shadow-md overflow-hidden transform hover:scale-105 transition-transform duration-300 fade-in-up" style={{transitionDelay: '0.3s'}}><img src="https://images.unsplash.com/photo-1604579412399-548a59c3539c?q=80&w=1964&auto=format&fit=crop" alt="Tepung dan Bahan Kue" className="h-40 w-full object-cover"/><div className="p-4"><h4 className="font-bold text-lg text-brand-dark">Tepung & Bahan Kue</h4><p className="text-sm text-gray-500">Lengkap untuk produksi</p></div></div> </div> <div className="text-center mt-12 fade-in-up"> <button onClick={() => onNavigate('kontak')} className="bg-brand-primary text-white font-semibold px-8 py-3 rounded-lg shadow-md hover:bg-blue-800 transition duration-300"> Unduh Katalog & Pricelist Lengkap </button> </div> </div> </AnimatedSection> );
const TestimoniPage = () => ( <AnimatedSection className="py-24 md:py-32 bg-brand-light"> <div className="container mx-auto px-6"> <div className="text-center mb-12 fade-in-up"> <h2 className="text-3xl md:text-4xl font-bold text-brand-primary">Apa Kata Mitra Kami?</h2> <p className="mt-4 text-lg text-gray-600 max-w-2xl mx-auto">Kami bangga menjadi bagian dari kesuksesan mereka.</p> </div> <div className="grid md:grid-cols-2 lg:grid-cols-3 gap-8"> <div className="bg-white p-8 rounded-xl shadow-lg fade-in-up"><p className="text-gray-600 italic mb-6">"Sejak bermitra dengan Mitra Sembako Nusantara, saya tidak pernah pusing lagi soal stok. Pengiriman selalu on-time, harganya juga bagus. Omzet toko saya jadi lebih stabil."</p><div className="flex items-center"><img src="https://placehold.co/50x50/E0E7FF/1E3A8A?text=B" alt="Budi Santoso" className="w-12 h-12 rounded-full mr-4"/><div><p className="font-bold text-brand-dark">Budi Santoso</p><p className="text-sm text-gray-500">Pemilik Toko Kelontong Makmur</p></div></div></div> <div className="bg-white p-8 rounded-xl shadow-lg fade-in-up" style={{transitionDelay: '0.1s'}}><p className="text-gray-600 italic mb-6">"Kualitas bahan baku sangat penting untuk restoran kami. MSN selalu memberikan produk dengan kualitas terbaik. Tamu kami puas, bisnis kami pun lancar."</p><div className="flex items-center"><img src="https://placehold.co/50x50/FEF3C7/F59E0B?text=S" alt="Siti Aminah" className="w-12 h-12 rounded-full mr-4"/><div><p className="font-bold text-brand-dark">Siti Aminah</p><p className="text-sm text-gray-500">Chef & Owner Restoran Sedap Rasa</p></div></div></div> <div className="bg-white p-8 rounded-xl shadow-lg fade-in-up" style={{transitionDelay: '0.2s'}}><p className="text-gray-600 italic mb-6">"Untuk bisnis katering, ketepatan pasokan adalah segalanya. MSN sangat bisa diandalkan. Tim sales-nya juga responsif dan sangat membantu."</p><div className="flex items-center"><img src="https://placehold.co/50x50/D1FAE5/065F46?text=A" alt="Agus Wijaya" className="w-12 h-12 rounded-full mr-4"/><div><p className="font-bold text-brand-dark">Agus Wijaya</p><p className="text-sm text-gray-500">Manajer Berkah Catering</p></div></div></div> </div> </div> </AnimatedSection> );
const FaqPage = () => ( <AnimatedSection className="py-24 md:py-32 bg-white"> <div className="container mx-auto px-6 max-w-3xl"> <div className="text-center mb-12 fade-in-up"> <h2 className="text-3xl md:text-4xl font-bold text-brand-primary">Pertanyaan yang Sering Diajukan</h2> </div> <div className="space-y-4 fade-in-up"> <details className="group bg-gray-50 p-6 rounded-lg shadow-sm"><summary className="flex justify-between items-center font-semibold cursor-pointer text-lg text-brand-dark">Bagaimana cara menjadi mitra?<svg className="w-5 h-5 text-gray-500 group-open:rotate-180 transition-transform" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M19 9l-7 7-7-7" /></svg></summary><p className="mt-4 text-gray-600">Sangat mudah! Cukup isi form di halaman kontak kami. Tim kami akan segera menghubungi Anda untuk verifikasi data dan penjelasan lebih lanjut. Prosesnya cepat dan tidak dipungut biaya.</p></details> <details className="group bg-gray-50 p-6 rounded-lg shadow-sm"><summary className="flex justify-between items-center font-semibold cursor-pointer text-lg text-brand-dark">Apakah ada minimum order?<svg className="w-5 h-5 text-gray-500 group-open:rotate-180 transition-transform" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M19 9l-7 7-7-7" /></svg></summary><p className="mt-4 text-gray-600">Ya, kami memiliki minimum order yang sangat fleksibel dan disesuaikan dengan skala bisnis Anda. Tujuannya adalah untuk efisiensi biaya pengiriman. Detailnya akan dijelaskan oleh tim sales kami saat proses pendaftaran.</p></details> <details className="group bg-gray-50 p-6 rounded-lg shadow-sm"><summary className="flex justify-between items-center font-semibold cursor-pointer text-lg text-brand-dark">Area mana saja yang dijangkau oleh pengiriman?<svg className="w-5 h-5 text-gray-500 group-open:rotate-180 transition-transform" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M19 9l-7 7-7-7" /></svg></summary><p className="mt-4 text-gray-600">Saat ini kami melayani seluruh area Jabodetabek, Bandung Raya, dan Surabaya. Kami terus berekspansi untuk menjangkau lebih banyak kota di Indonesia. Hubungi kami untuk memastikan jangkauan di area Anda.</p></details> </div> </div> </AnimatedSection> );
const KontakPage = () => ( <AnimatedSection className="cta-bg py-24 md:py-32 text-white"> <div className="container mx-auto px-6 text-center fade-in-up"> <h2 className="text-3xl md:text-4xl font-extrabold mb-4">Siap Tingkatkan Profit Bisnis Anda?</h2> <p className="text-lg max-w-2xl mx-auto mb-8">Hanya butuh 2 menit untuk mendaftar. Dapatkan akses ke harga mitra, promo eksklusif, dan layanan prioritas. Mari tumbuh bersama!</p> <form className="max-w-xl mx-auto bg-white/10 backdrop-blur-sm p-8 rounded-xl"> <div className="flex flex-col gap-4"> <input type="text" placeholder="Nama Anda" className="w-full px-5 py-3 rounded-lg text-gray-800 focus:outline-none focus:ring-2 focus:ring-brand-primary" required /> <input type="tel" placeholder="Nomor WhatsApp Aktif" className="w-full px-5 py-3 rounded-lg text-gray-800 focus:outline-none focus:ring-2 focus:ring-brand-primary" required /> <input type="text" placeholder="Nama Usaha (Toko/Resto/Catering)" className="w-full px-5 py-3 rounded-lg text-gray-800 focus:outline-none focus:ring-2 focus:ring-brand-primary" required /> </div> <button type="submit" className="mt-6 w-full bg-brand-primary text-white font-bold px-12 py-4 rounded-lg shadow-xl hover:bg-blue-900 transition duration-300 transform hover:scale-105 text-lg"> Hubungi Tim Ahli Kami </button> <p className="text-xs mt-4 text-blue-100">Tim kami akan segera menghubungi Anda untuk proses verifikasi.</p> </form> </div> </AnimatedSection> );

// ============================================================================
// Halaman Alat Bantu Mitra (dengan Firebase & Fitur Baru)
// ============================================================================
const AlatBantuPage = ({ db, userId, showNotification }) => {
    // State untuk Perencana Menu
    const [menuPrompt, setMenuPrompt] = useState('');
    const [menuResult, setMenuResult] = useState(null);
    const [isMenuLoading, setMenuLoading] = useState(false);
    const [menuError, setMenuError] = useState('');

    // State untuk Asisten Copywriting
    const [copyPrompt, setCopyPrompt] = useState('');
    const [copyResult, setCopyResult] = useState('');
    const [isCopyLoading, setCopyLoading] = useState(false);
    const [copyError, setCopyError] = useState('');

    // State untuk Analisis Tren
    const [trendPrompt, setTrendPrompt] = useState('');
    const [trendResult, setTrendResult] = useState('');
    const [isTrendLoading, setTrendLoading] = useState(false);
    const [trendError, setTrendError] = useState('');
    
    // State untuk data dari Firestore
    const [savedMenus, setSavedMenus] = useState([]);
    const [savedPromos, setSavedPromos] = useState([]);

    // Listener untuk data Firestore
    useEffect(() => {
        if (!db || !userId) return;
        
        // Listener untuk menu yang disimpan
        const menusRef = collection(db, `/artifacts/${appId}/users/${userId}/saved_menus`);
        const unsubscribeMenus = onSnapshot(menusRef, (snapshot) => {
            const menusData = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
            setSavedMenus(menusData);
        });

        // Listener untuk promo yang disimpan
        const promosRef = collection(db, `/artifacts/${appId}/users/${userId}/saved_promos`);
        const unsubscribePromos = onSnapshot(promosRef, (snapshot) => {
            const promosData = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
            setSavedPromos(promosData);
        });

        return () => {
            unsubscribeMenus();
            unsubscribePromos();
        };
    }, [db, userId]);

    // Fungsi untuk menyimpan hasil ke Firestore
    const handleSaveMenu = async (paket) => {
        if (!db || !userId) return;
        try {
            const menusRef = collection(db, `/artifacts/${appId}/users/${userId}/saved_menus`);
            await addDoc(menusRef, { ...paket, createdAt: serverTimestamp() });
            showNotification("Rencana menu berhasil disimpan!");
        } catch (e) {
            console.error("Error adding document: ", e);
            showNotification("Gagal menyimpan menu.", true);
        }
    };

    const handleSavePromo = async (promoText) => {
        if (!db || !userId) return;
        try {
            const promosRef = collection(db, `/artifacts/${appId}/users/${userId}/saved_promos`);
            await addDoc(promosRef, { text: promoText, createdAt: serverTimestamp() });
            showNotification("Teks promo berhasil disimpan!");
        } catch (e) {
            console.error("Error adding document: ", e);
            showNotification("Gagal menyimpan promo.", true);
        }
    };

    // Fungsi untuk menghapus dari Firestore
    const handleDelete = async (collectionName, id) => {
        if (!db || !userId) return;
        try {
            const docRef = doc(db, `/artifacts/${appId}/users/${userId}/${collectionName}`, id);
            await deleteDoc(docRef);
            showNotification("Berhasil dihapus!");
        } catch (e) {
            console.error("Error deleting document: ", e);
            showNotification("Gagal menghapus.", true);
        }
    };
    
    // Fungsi untuk memanggil Gemini API
    const callGeminiAPI = async (prompt, schema = null) => {
        let payload = { contents: [{ role: "user", parts: [{ text: prompt }] }] };
        if (schema) {
            payload.generationConfig = {
                responseMimeType: "application/json",
                responseSchema: schema,
            };
        }
        
        const apiKey = ""; // Dibiarkan kosong
        const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=${apiKey}`;
        const response = await fetch(apiUrl, {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(payload)
        });

        if (!response.ok) {
            throw new Error(`API request failed with status ${response.status}`);
        }
        const result = await response.json();
        const textContent = result.candidates?.[0]?.content?.parts?.[0]?.text;
        if (!textContent) {
            throw new Error("Struktur respons dari API tidak valid.");
        }
        return textContent;
    };

    const handleGenerateMenu = async () => {
        if (!menuPrompt) { setMenuError('Mohon masukkan ide atau tema menu.'); return; }
        setMenuLoading(true); setMenuError(''); setMenuResult(null);
        const prompt = `Anda adalah seorang chef profesional dan konsultan bisnis kuliner di Indonesia. Berdasarkan ide berikut: "${menuPrompt}", buatkan 3 rekomendasi paket menu yang kreatif. Untuk setiap paket menu, berikan nama paket, deskripsi singkat yang menarik, daftar hidangan, dan yang paling penting, daftar kebutuhan sembako (bahan pokok) yang diperlukan untuk membuat semua hidangan di paket tersebut.`;
        const schema = { type: "OBJECT", properties: { rekomendasi_menu: { type: "ARRAY", items: { type: "OBJECT", properties: { nama_paket: { type: "STRING" }, deskripsi: { type: "STRING" }, daftar_hidangan: { type: "ARRAY", items: { type: "STRING" } }, kebutuhan_sembako: { type: "ARRAY", items: { type: "STRING" } } }, required: ["nama_paket", "deskripsi", "daftar_hidangan", "kebutuhan_sembako"] } } } };
        try {
            const resultText = await callGeminiAPI(prompt, schema);
            setMenuResult(JSON.parse(resultText));
        } catch (error) {
            console.error("Error generating menu:", error);
            setMenuError("Maaf, terjadi kesalahan. Coba beberapa saat lagi.");
        } finally {
            setMenuLoading(false);
        }
    };

    const handleGenerateCopy = async () => {
        if (!copyPrompt) { setCopyError('Mohon masukkan detail promo Anda.'); return; }
        setCopyLoading(true); setCopyError(''); setCopyResult('');
        const prompt = `Anda adalah seorang ahli copywriting untuk UMKM di Indonesia. Buatkan 3 alternatif teks promosi singkat, menarik, dan persuasif untuk media sosial (seperti Instagram atau WhatsApp Story) berdasarkan detail berikut: "${copyPrompt}". Gunakan bahasa yang santai dan mudah dimengerti. Beri judul untuk setiap alternatif (Contoh: Opsi 1, Opsi 2, Opsi 3).`;
        try {
            const resultText = await callGeminiAPI(prompt);
            setCopyResult(resultText);
        } catch (error) {
            console.error("Error generating copy:", error);
            setCopyError("Maaf, terjadi kesalahan. Coba lagi.");
        } finally {
            setCopyLoading(false);
        }
    };

    const handleGenerateTrend = async () => {
        if (!trendPrompt) { setTrendError('Mohon masukkan pertanyaan Anda.'); return; }
        setTrendLoading(true); setTrendError(''); setTrendResult('');
        const prompt = `Anda adalah seorang analis tren pasar kuliner di Indonesia. Berikan jawaban yang singkat, padat, dan actionable (maksimal 3 poin utama) untuk pertanyaan berikut: "${trendPrompt}". Fokus pada ide yang bisa langsung diterapkan oleh pemilik usaha kecil (warung, resto kecil, katering).`;
        try {
            const resultText = await callGeminiAPI(prompt);
            setTrendResult(resultText.replace(/\*/g, '•'));
        } catch (error) {
            console.error("Error generating trend:", error);
            setTrendError("Maaf, terjadi kesalahan. Coba lagi.");
        } finally {
            setTrendLoading(false);
        }
    };

    return (
        <AnimatedSection className="py-24 md:py-32 bg-brand-light">
            <div className="container mx-auto px-6">
                <div className="text-center mb-12 fade-in-up">
                    <h2 className="text-3xl md:text-4xl font-bold text-brand-primary">Alat Bantu Mitra ✨</h2>
                    <p className="mt-4 text-lg text-gray-600 max-w-2xl mx-auto">Manfaatkan teknologi AI untuk mempermudah dan memajukan bisnis Anda. Eksklusif untuk mitra kami.</p>
                </div>

                {/* Bagian Favorit Saya */}
                <div className="bg-white p-8 rounded-xl shadow-lg mb-12 fade-in-up">
                    <h3 className="text-2xl font-bold mb-4 text-brand-dark">⭐ Favorit Saya</h3>
                    {(savedMenus.length === 0 && savedPromos.length === 0) ? (
                        <p className="text-gray-500">Anda belum menyimpan apapun. Gunakan alat di bawah dan simpan ide terbaik Anda di sini!</p>
                    ) : (
                        <div className="grid md:grid-cols-2 gap-8">
                            <div>
                                <h4 className="font-semibold text-lg mb-2">Rencana Menu Tersimpan</h4>
                                {savedMenus.length > 0 ? (
                                    <div className="space-y-4 max-h-96 overflow-y-auto pr-2">
                                        {savedMenus.map(menu => (
                                            <div key={menu.id} className="bg-blue-50 p-4 rounded-lg border">
                                                <h5 className="font-bold text-brand-primary">{menu.nama_paket}</h5>
                                                <button onClick={() => handleDelete('saved_menus', menu.id)} className="text-xs text-red-500 hover:underline">Hapus</button>
                                            </div>
                                        ))}
                                    </div>
                                ) : <p className="text-sm text-gray-400">Belum ada menu yang disimpan.</p>}
                            </div>
                            <div>
                                <h4 className="font-semibold text-lg mb-2">Teks Promo Tersimpan</h4>
                                {savedPromos.length > 0 ? (
                                    <div className="space-y-4 max-h-96 overflow-y-auto pr-2">
                                        {savedPromos.map(promo => (
                                            <div key={promo.id} className="bg-yellow-50 p-4 rounded-lg border">
                                                <p className="text-sm text-gray-700 truncate">{promo.text}</p>
                                                <div className="mt-2">
                                                     <button onClick={() => { navigator.clipboard.writeText(promo.text); showNotification("Teks disalin!"); }} className="text-xs text-green-600 hover:underline mr-4">Salin</button>
                                                     <button onClick={() => handleDelete('saved_promos', promo.id)} className="text-xs text-red-500 hover:underline">Hapus</button>
                                                </div>
                                            </div>
                                        ))}
                                    </div>
                                ) : <p className="text-sm text-gray-400">Belum ada promo yang disimpan.</p>}
                            </div>
                        </div>
                    )}
                </div>

                {/* Analisis Tren Pasar */}
                <div className="bg-white p-8 rounded-xl shadow-lg mb-12 fade-in-up">
                    <h3 className="text-2xl font-bold mb-2 text-brand-dark">📈 Analisis Tren Pasar</h3>
                    <p className="text-gray-600 mb-6">Dapatkan wawasan pasar terbaru untuk ide bisnis Anda selanjutnya.</p>
                    <input type="text" value={trendPrompt} onChange={(e) => setTrendPrompt(e.target.value)} className="w-full p-3 border rounded-lg" placeholder="Contoh: Tren minuman kekinian untuk dijual di bazaar?"/>
                    {trendError && <p className="text-red-500 text-sm mt-2">{trendError}</p>}
                    <button onClick={handleGenerateTrend} disabled={isTrendLoading} className="mt-4 bg-brand-secondary text-white font-bold px-8 py-3 rounded-lg shadow-md hover:bg-yellow-600 disabled:bg-gray-400">
                        {isTrendLoading ? 'Menganalisis...' : 'Dapatkan Analisis'}
                    </button>
                    {isTrendLoading && <LoadingSpinner />}
                    {trendResult && <div className="mt-6 p-4 bg-gray-50 rounded-lg whitespace-pre-wrap">{trendResult}</div>}
                </div>

                {/* Perencana Menu Cerdas */}
                <div className="bg-white p-8 rounded-xl shadow-lg mb-12 fade-in-up">
                    <h3 className="text-2xl font-bold mb-2 text-brand-dark">🍽️ Perencana Menu Cerdas</h3>
                    <p className="text-gray-600 mb-6">Butuh ide menu baru? Masukkan tema, dan biarkan AI membantu Anda!</p>
                    <textarea value={menuPrompt} onChange={(e) => setMenuPrompt(e.target.value)} className="w-full p-3 border rounded-lg" rows="3" placeholder="Contoh: menu nasi box harga 25 ribuan, hidangan spesial lebaran..."></textarea>
                    {menuError && <p className="text-red-500 text-sm mt-2">{menuError}</p>}
                    <button onClick={handleGenerateMenu} disabled={isMenuLoading} className="mt-4 bg-brand-secondary text-white font-bold px-8 py-3 rounded-lg shadow-md hover:bg-yellow-600 disabled:bg-gray-400">
                        {isMenuLoading ? 'Membuat...' : 'Buat Rekomendasi Menu'}
                    </button>
                    {isMenuLoading && <LoadingSpinner />}
                    {menuResult && (
                        <div className="mt-8">
                            <h4 className="text-xl font-semibold mb-4 text-brand-dark">Hasil Rekomendasi:</h4>
                            <div className="grid md:grid-cols-1 lg:grid-cols-3 gap-6">
                                {menuResult.rekomendasi_menu.map((paket, index) => (
                                    <div key={index} className="bg-blue-50 p-6 rounded-lg border border-blue-200 flex flex-col">
                                        <div className="flex-grow">
                                            <h5 className="font-bold text-lg text-brand-primary">{paket.nama_paket}</h5>
                                            <p className="text-sm italic text-gray-600 my-2">"{paket.deskripsi}"</p>
                                            <h6 className="font-semibold mt-4">Hidangan:</h6>
                                            <ul className="list-disc list-inside text-gray-700 text-sm">{paket.daftar_hidangan.map((item, i) => <li key={i}>{item}</li>)}</ul>
                                            <h6 className="font-semibold mt-4">Kebutuhan Sembako:</h6>
                                            <ul className="list-disc list-inside text-gray-700 text-sm">{paket.kebutuhan_sembako.map((item, i) => <li key={i}>{item}</li>)}</ul>
                                        </div>
                                        <button onClick={() => handleSaveMenu(paket)} className="mt-4 w-full bg-blue-500 hover:bg-blue-600 text-white text-sm font-semibold py-2 px-4 rounded-lg transition-colors">Simpan Rencana Ini</button>
                                    </div>
                                ))}
                            </div>
                        </div>
                    )}
                </div>

                {/* Asisten Copywriting Promo */}
                <div className="bg-white p-8 rounded-xl shadow-lg fade-in-up">
                    <h3 className="text-2xl font-bold mb-2 text-brand-dark">✍️ Asisten Copywriting Promo</h3>
                    <p className="text-gray-600 mb-6">Bingung cara promosi? Tulis detail promo Anda, AI akan buatkan teksnya!</p>
                    <input type="text" value={copyPrompt} onChange={(e) => setCopyPrompt(e.target.value)} className="w-full p-3 border rounded-lg" placeholder="Contoh: promo beli 2 gratis 1 untuk kopi sachet..."/>
                    {copyError && <p className="text-red-500 text-sm mt-2">{copyError}</p>}
                    <button onClick={handleGenerateCopy} disabled={isCopyLoading} className="mt-4 bg-brand-secondary text-white font-bold px-8 py-3 rounded-lg shadow-md hover:bg-yellow-600 disabled:bg-gray-400">
                        {isCopyLoading ? 'Menulis...' : 'Buat Teks Promosi'}
                    </button>
                    {isCopyLoading && <LoadingSpinner />}
                    {copyResult && (
                        <div className="mt-8">
                            <h4 className="text-xl font-semibold mb-4 text-brand-dark">Opsi Teks Promosi:</h4>
                            <div className="relative bg-gray-50 p-4 rounded-lg border">
                                <textarea readOnly value={copyResult} className="w-full bg-transparent border-none h-48 resize-none focus:ring-0"></textarea>
                                <div className="absolute bottom-3 right-3 flex space-x-2">
                                     <button onClick={() => { navigator.clipboard.writeText(copyResult); showNotification("Teks disalin!"); }} className="bg-gray-200 hover:bg-gray-300 text-gray-700 text-xs font-semibold py-1 px-3 rounded">Salin</button>
                                     <button onClick={() => handleSavePromo(copyResult)} className="bg-blue-500 hover:bg-blue-600 text-white text-xs font-semibold py-1 px-3 rounded">Simpan</button>
                                </div>
                            </div>
                        </div>
                    )}
                </div>
            </div>
        </AnimatedSection>
    );
};


// ============================================================================
// Komponen Layout (Header, Footer)
// ============================================================================
const Header = ({ currentPage, onNavigate }) => { const [isMenuOpen, setMenuOpen] = useState(false); const [isScrolled, setScrolled] = useState(false); useEffect(() => { const handleScroll = () => setScrolled(window.scrollY > 50); window.addEventListener('scroll', handleScroll); return () => window.removeEventListener('scroll', handleScroll); }, []); const handleNavClick = (page) => { onNavigate(page); setMenuOpen(false); }; return ( <header className={`fixed top-0 left-0 right-0 z-50 transition-all duration-300 ${isScrolled || isMenuOpen ? 'bg-white/95 shadow-md' : 'bg-white/80'} backdrop-blur-lg`}> <div className="container mx-auto px-6 py-4"> <div className="flex items-center justify-between"> <button onClick={() => handleNavClick('home')} className="text-2xl font-bold text-brand-primary"> Mitra Sembako Nusantara </button> <nav className="hidden md:flex items-center space-x-8"> {navLinks.map(link => ( <button key={link.page} onClick={() => handleNavClick(link.page)} className={`transition duration-300 ${currentPage === link.page ? 'text-brand-primary font-semibold' : 'text-gray-600 hover:text-brand-primary'}`}> {link.name} </button> ))} </nav> <button onClick={() => handleNavClick('kontak')} className="hidden md:inline-block bg-brand-secondary text-white font-semibold px-5 py-2 rounded-lg shadow-md hover:bg-yellow-600 transition duration-300 transform hover:scale-105"> Hubungi Kami </button> <button onClick={() => setMenuOpen(!isMenuOpen)} className="md:hidden text-brand-primary focus:outline-none"> <svg className="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M4 6h16M4 12h16m-7 6h7"></path></svg> </button> </div> </div> <div className={`md:hidden bg-white shadow-lg ${isMenuOpen ? 'block' : 'hidden'}`}> {navLinks.map(link => ( <button key={link.page} onClick={() => handleNavClick(link.page)} className={`block w-full text-left px-6 py-3 transition duration-300 ${currentPage === link.page ? 'bg-brand-light text-brand-primary font-semibold' : 'text-gray-600 hover:bg-brand-light'}`}> {link.name} </button> ))} <div className="p-4"> <button onClick={() => handleNavClick('kontak')} className="block w-full text-center bg-brand-secondary text-white font-semibold px-5 py-3 rounded-lg shadow-md hover:bg-yellow-600 transition duration-300"> Hubungi Kami </button> </div> </div> </header> ); };
const Footer = ({ onNavigate }) => ( <footer className="bg-brand-primary text-white"> <div className="container mx-auto px-6 py-12"> <div className="grid md:grid-cols-2 lg:grid-cols-4 gap-8"> <div> <h3 className="text-xl font-bold mb-4">Mitra Sembako Nusantara</h3> <p className="text-blue-200">Partner terpercaya untuk pemenuhan kebutuhan pokok bisnis Anda. Kami berkomitmen pada kualitas, harga, dan pelayanan terbaik.</p> </div> <div> <h3 className="text-lg font-semibold mb-4">Navigasi</h3> <ul className="space-y-2"> {navLinks.map(link => (<li key={link.page}><button onClick={() => onNavigate(link.page)} className="text-blue-200 hover:text-white">{link.name}</button></li>))} <li><button onClick={() => onNavigate('kontak')} className="text-blue-200 hover:text-white">Hubungi Kami</button></li> </ul> </div> <div> <h3 className="text-lg font-semibold mb-4">Hubungi Kami</h3> <ul className="space-y-2 text-blue-200"> <li>Email: support@mitrasembako.co.id</li> <li>Telepon: (021) 1234 5678</li> <li>Alamat: Jl. Industri Raya No. 1, Jakarta</li> </ul> </div> <div> <h3 className="text-lg font-semibold mb-4">Ikuti Kami</h3> <div className="flex space-x-4"> <a href="#" className="text-blue-200 hover:text-white"><svg className="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M22.675 0h-21.35c-.732 0-1.325.593-1.325 1.325v21.351c0 .731.593 1.324 1.325 1.324h11.494v-9.294h-3.128v-3.622h3.128v-2.671c0-3.1 1.893-4.788 4.659-4.788 1.325 0 2.463.099 2.795.143v3.24l-1.918.001c-1.504 0-1.795.715-1.795 1.763v2.313h3.587l-.467 3.622h-3.12v9.293h6.116c.73 0 1.323-.593 1.323-1.325v-21.35c0-.732-.593-1.325-1.325-1.325z"/></svg></a> <a href="#" className="text-blue-200 hover:text-white"><svg className="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M12 2.163c3.204 0 3.584.012 4.85.07 3.252.148 4.771 1.691 4.919 4.919.058 1.265.069 1.645.069 4.849 0 3.205-.012 3.584-.069 4.849-.149 3.225-1.664 4.771-4.919 4.919-1.266.058-1.644.07-4.85.07-3.204 0-3.584-.012-4.849-.07-3.26-.149-4.771-1.699-4.919-4.92-.058-1.265-.07-1.644-.07-4.849 0-3.204.013-3.583.07-4.849.149-3.227 1.664-4.771 4.919-4.919 1.266-.057 1.645-.069 4.849-.069zm0-2.163c-3.259 0-3.667.014-4.947.072-4.358.2-6.78 2.618-6.98 6.98-.059 1.281-.073 1.689-.073 4.948 0 3.259.014 3.668.072 4.948.2 4.358 2.618 6.78 6.98 6.98 1.281.058 1.689.072 4.948.072 3.259 0 3.668-.014 4.948-.072 4.354-.2 6.782-2.618 6.979-6.98.059-1.28.073-1.689.073-4.948 0-3.259-.014-3.667-.072-4.947-.196-4.354-2.617-6.78-6.979-6.98-1.281-.059-1.69-.073-4.949-.073zm0 5.838c-3.403 0-6.162 2.759-6.162 6.162s2.759 6.162 6.162 6.162 6.162-2.759 6.162-6.162-2.759-6.162-6.162-6.162zm0 10.162c-2.209 0-4-1.79-4-4s1.791-4 4-4 4 1.79 4 4-1.791 4-4 4zm6.406-11.845c-.796 0-1.441.645-1.441 1.44s.645 1.44 1.441 1.44 1.441-.645 1.441-1.44-.645-1.44-1.441-1.44z"/></svg></a> </div> </div> </div> <div className="mt-8 border-t border-blue-700 pt-6 text-center"> <p className="text-blue-200">&copy; {new Date().getFullYear()} Mitra Sembako Nusantara. All Rights Reserved.</p> </div> </div> </footer> );

// ============================================================================
// Komponen Utama Aplikasi (App)
// ============================================================================
export default function App() {
    const [currentPage, setCurrentPage] = useState('home');
    const [isTransitioning, setTransitioning] = useState(false);
    const [firebase, setFirebase] = useState({ auth: null, db: null });
    const [userId, setUserId] = useState(null);
    const [isAuthReady, setAuthReady] = useState(false);
    const [notification, setNotification] = useState({ show: false, message: '', isError: false });

    // Initialize Firebase and Auth listener
    useEffect(() => {
        try {
            if (Object.keys(firebaseConfig).length > 0) {
                const app = initializeApp(firebaseConfig);
                const auth = getAuth(app);
                const db = getFirestore(app);
                setFirebase({ auth, db });

                const unsubscribe = onAuthStateChanged(auth, async (user) => {
                    if (user) {
                        setUserId(user.uid);
                    } else {
                        // Sign in logic
                        const token = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;
                        if (token) {
                            await signInWithCustomToken(auth, token);
                        } else {
                            await signInAnonymously(auth);
                        }
                    }
                    setAuthReady(true);
                });
                return () => unsubscribe();
            } else {
                 setAuthReady(true); // Proceed without firebase if config is missing
            }
        } catch (error) {
            console.error("Firebase Initialization Error:", error);
            setAuthReady(true); // Ensure app doesn't hang
        }
    }, []);

    const showNotification = (message, isError = false) => {
        setNotification({ show: true, message, isError });
        setTimeout(() => {
            setNotification({ show: false, message: '', isError: false });
        }, 3000);
    };

    const handleNavigate = (page) => {
        if (page === currentPage) return;
        setTransitioning(true);
        setTimeout(() => {
            setCurrentPage(page);
            window.scrollTo(0, 0);
            setTransitioning(false);
        }, 300);
    };

    const renderPage = () => {
        if (!isAuthReady && Object.keys(firebaseConfig).length > 0) {
            return <div className="h-screen flex items-center justify-center"><LoadingSpinner text="Menyiapkan sesi Anda..." /></div>;
        }
        
        switch (currentPage) {
            case 'home': return <HomePage onNavigate={handleNavigate} />;
            case 'keunggulan': return <KeunggulanPage />;
            case 'produk': return <ProdukPage onNavigate={handleNavigate} />;
            case 'alatbantu': return <AlatBantuPage db={firebase.db} userId={userId} showNotification={showNotification} />;
            case 'testimoni': return <TestimoniPage />;
            case 'faq': return <FaqPage />;
            case 'kontak': return <KontakPage />;
            default: return <HomePage onNavigate={handleNavigate} />;
        }
    };

    return (
        <div className="font-poppins bg-brand-light text-brand-dark antialiased">
            <style>{`
                .hero-bg { background-image: linear-gradient(to right, rgba(30, 58, 138, 0.9), rgba(30, 58, 138, 0.7)), url('https://images.unsplash.com/photo-1580913428023-02c695666d61?q=80&w=2070&auto=format&fit=crop'); background-size: cover; background-position: center; }
                .cta-bg { background-image: linear-gradient(to right, rgba(245, 158, 11, 0.9), rgba(245, 158, 11, 0.95)), url('https://images.unsplash.com/photo-1606859191214-25808c434484?q=80&w=1974&auto=format&fit=crop'); background-size: cover; background-position: center; }
                .fade-in-up { opacity: 0; transform: translateY(20px); transition: opacity 0.6s ease-out, transform 0.6s ease-out; }
                .fade-in-up.visible { opacity: 1; transform: translateY(0); }
                .page-transition { transition: opacity 0.3s ease-in-out; }
                .animate-fade-in { animation: fadeIn 0.5s ease-out forwards; }
                @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
            `}</style>
            
            <Header currentPage={currentPage} onNavigate={handleNavigate} />
            
            <main className={`page-transition ${isTransitioning ? 'opacity-0' : 'opacity-100'}`}>
                {renderPage()}
            </main>

            <Footer onNavigate={handleNavigate} />
            <Notification message={notification.message} show={notification.show} />
        </div>
    );
}
