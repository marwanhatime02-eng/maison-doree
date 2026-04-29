import { useState, useEffect, useRef } from "react";

// ─── DATA ────────────────────────────────────────────────────────────────────
const PRODUCTS = [
  { id: 1, name: "Noir Absolu", price: 850, type: "unisex", notes: "Oud · Ambre · Santal", ml: "100ml", rating: 4.9, reviews: 128, badge: "Bestseller", img: "https://images.unsplash.com/photo-1541643600914-78b084683702?w=600&q=80" },
  { id: 2, name: "Rose Éternelle", price: 720, type: "women", notes: "Rose · Musc · Patchouli", ml: "75ml", rating: 4.8, reviews: 94, badge: "New", img: "https://images.unsplash.com/photo-1592945403244-b3fbafd7f539?w=600&q=80" },
  { id: 3, name: "Cedre Royal", price: 680, type: "men", notes: "Cèdre · Vétiver · Bergamote", ml: "100ml", rating: 4.7, reviews: 76, badge: null, img: "https://images.unsplash.com/photo-1563170351-be82bc888aa4?w=600&q=80" },
  { id: 4, name: "Ambre Doré", price: 950, type: "unisex", notes: "Ambre · Vanille · Bois de Oud", ml: "50ml", rating: 5.0, reviews: 211, badge: "Exclusif", img: "https://images.unsplash.com/photo-1547887538-e3a2f32cb1cc?w=600&q=80" },
  { id: 5, name: "Jasmin Impérial", price: 590, type: "women", notes: "Jasmin · Iris · Cèdre Blanc", ml: "75ml", rating: 4.6, reviews: 53, badge: null, img: "https://images.unsplash.com/photo-1588405748880-12d1d2a59f75?w=600&q=80" },
  { id: 6, name: "Bois Mystique", price: 780, type: "men", notes: "Bois de Santal · Encens · Épices", ml: "100ml", rating: 4.8, reviews: 87, badge: "Limited", img: "https://images.unsplash.com/photo-1541643600914-78b084683702?w=600&q=80" },
];

const CITIES = [
  { name: "Casablanca", fee: 30 }, { name: "Rabat", fee: 30 }, { name: "Marrakech", fee: 35 },
  { name: "Fès", fee: 35 }, { name: "Tanger", fee: 40 }, { name: "Agadir", fee: 40 },
  { name: "Meknès", fee: 35 }, { name: "Oujda", fee: 45 }, { name: "Autres villes", fee: 50 },
];

const PROMO_CODES = { "LUXURY20": 0.20, "WELCOME10": 0.10, "VIP30": 0.30 };

// ─── ICONS ───────────────────────────────────────────────────────────────────
const Icon = ({ name, size = 20 }) => {
  const icons = {
    cart: <svg width={size} height={size} fill="none" stroke="currentColor" strokeWidth="1.5" viewBox="0 0 24 24"><path d="M6 2L3 6v14a2 2 0 002 2h14a2 2 0 002-2V6l-3-4z"/><line x1="3" y1="6" x2="21" y2="6"/><path d="M16 10a4 4 0 01-8 0"/></svg>,
    star: <svg width={size} height={size} fill="currentColor" viewBox="0 0 24 24"><path d="M12 2l3.09 6.26L22 9.27l-5 4.87 1.18 6.88L12 17.77l-6.18 3.25L7 14.14 2 9.27l6.91-1.01L12 2z"/></svg>,
    close: <svg width={size} height={size} fill="none" stroke="currentColor" strokeWidth="2" viewBox="0 0 24 24"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>,
    plus: <svg width={size} height={size} fill="none" stroke="currentColor" strokeWidth="2" viewBox="0 0 24 24"><line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/></svg>,
    minus: <svg width={size} height={size} fill="none" stroke="currentColor" strokeWidth="2" viewBox="0 0 24 24"><line x1="5" y1="12" x2="19" y2="12"/></svg>,
    trash: <svg width={size} height={size} fill="none" stroke="currentColor" strokeWidth="1.5" viewBox="0 0 24 24"><polyline points="3 6 5 6 21 6"/><path d="M19 6l-1 14H6L5 6"/><path d="M10 11v6M14 11v6"/><path d="M9 6V4h6v2"/></svg>,
    menu: <svg width={size} height={size} fill="none" stroke="currentColor" strokeWidth="2" viewBox="0 0 24 24"><line x1="3" y1="6" x2="21" y2="6"/><line x1="3" y1="12" x2="21" y2="12"/><line x1="3" y1="18" x2="21" y2="18"/></svg>,
    whatsapp: <svg width={size} height={size} fill="currentColor" viewBox="0 0 24 24"><path d="M17.472 14.382c-.297-.149-1.758-.867-2.03-.967-.273-.099-.471-.148-.67.15-.197.297-.767.966-.94 1.164-.173.199-.347.223-.644.075-.297-.15-1.255-.463-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.298-.347.446-.52.149-.174.198-.298.298-.497.099-.198.05-.371-.025-.52-.075-.149-.669-1.612-.916-2.207-.242-.579-.487-.5-.669-.51-.173-.008-.371-.01-.57-.01-.198 0-.52.074-.792.372-.272.297-1.04 1.016-1.04 2.479 0 1.462 1.065 2.875 1.213 3.074.149.198 2.096 3.2 5.077 4.487.709.306 1.262.489 1.694.625.712.227 1.36.195 1.871.118.571-.085 1.758-.719 2.006-1.413.248-.694.248-1.289.173-1.413-.074-.124-.272-.198-.57-.347z"/><path d="M11.999 2C6.477 2 2 6.477 2 11.999c0 1.99.583 3.845 1.585 5.4L2 22l4.778-1.544A9.956 9.956 0 0011.999 22C17.52 22 22 17.523 22 12S17.52 2 11.999 2z"/></svg>,
    check: <svg width={size} height={size} fill="none" stroke="currentColor" strokeWidth="2.5" viewBox="0 0 24 24"><polyline points="20 6 9 17 4 12"/></svg>,
    arrow: <svg width={size} height={size} fill="none" stroke="currentColor" strokeWidth="1.5" viewBox="0 0 24 24"><line x1="5" y1="12" x2="19" y2="12"/><polyline points="12 5 19 12 12 19"/></svg>,
    phone: <svg width={size} height={size} fill="none" stroke="currentColor" strokeWidth="1.5" viewBox="0 0 24 24"><path d="M22 16.92v3a2 2 0 01-2.18 2 19.79 19.79 0 01-8.63-3.07A19.5 19.5 0 013.1 10.81a19.79 19.79 0 01-3.07-8.67A2 2 0 012.2 0h3a2 2 0 012 1.72c.127.96.361 1.903.7 2.81a2 2 0 01-.45 2.11L6.91 7.91a16 16 0 006.1 6.1l1.27-.54a2 2 0 012.11.45c.907.339 1.85.573 2.81.7A2 2 0 0122 16.92z"/></svg>,
    mail: <svg width={size} height={size} fill="none" stroke="currentColor" strokeWidth="1.5" viewBox="0 0 24 24"><path d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z"/><polyline points="22,6 12,13 2,6"/></svg>,
    truck: <svg width={size} height={size} fill="none" stroke="currentColor" strokeWidth="1.5" viewBox="0 0 24 24"><rect x="1" y="3" width="15" height="13"/><polygon points="16 8 20 8 23 11 23 16 16 16 16 8"/><circle cx="5.5" cy="18.5" r="2.5"/><circle cx="18.5" cy="18.5" r="2.5"/></svg>,
    tag: <svg width={size} height={size} fill="none" stroke="currentColor" strokeWidth="1.5" viewBox="0 0 24 24"><path d="M20.59 13.41l-7.17 7.17a2 2 0 01-2.83 0L2 12V2h10l8.59 8.59a2 2 0 010 2.82z"/><line x1="7" y1="7" x2="7.01" y2="7"/></svg>,
    shield: <svg width={size} height={size} fill="none" stroke="currentColor" strokeWidth="1.5" viewBox="0 0 24 24"><path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z"/></svg>,
    sparkle: <svg width={size} height={size} fill="currentColor" viewBox="0 0 24 24"><path d="M12 2l2.4 7.6H22l-6.4 4.8 2.4 7.6L12 17.6l-6 4.4 2.4-7.6L2 9.6h7.6z"/></svg>,
  };
  return icons[name] || null;
};

// ─── STYLES ──────────────────────────────────────────────────────────────────
const css = `
  @import url('https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,500;0,600;1,300;1,400&family=Jost:wght@200;300;400;500&display=swap');

  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --gold: #C9A84C;
    --gold-light: #E2C87A;
    --gold-dark: #9A7B3A;
    --black: #0A0A0A;
    --off-black: #111111;
    --charcoal: #1A1A1A;
    --mid: #2A2A2A;
    --border: #2E2E2E;
    --muted: #666;
    --light: #E8E4DC;
    --white: #FAFAF8;
    --serif: 'Cormorant Garamond', Georgia, serif;
    --sans: 'Jost', sans-serif;
  }

  html { scroll-behavior: smooth; }

  body {
    background: var(--black);
    color: var(--white);
    font-family: var(--sans);
    font-weight: 300;
    letter-spacing: 0.02em;
    line-height: 1.6;
    overflow-x: hidden;
  }

  /* SCROLLBAR */
  ::-webkit-scrollbar { width: 4px; }
  ::-webkit-scrollbar-track { background: var(--black); }
  ::-webkit-scrollbar-thumb { background: var(--gold-dark); }

  /* NAV */
  .nav {
    position: fixed; top: 0; left: 0; right: 0; z-index: 100;
    padding: 0 2rem;
    height: 72px;
    display: flex; align-items: center; justify-content: space-between;
    background: rgba(10,10,10,0.92);
    backdrop-filter: blur(16px);
    border-bottom: 1px solid rgba(201,168,76,0.15);
    transition: all 0.3s;
  }
  .nav-logo {
    font-family: var(--serif);
    font-size: 1.5rem;
    font-weight: 400;
    letter-spacing: 0.15em;
    color: var(--gold);
    cursor: pointer;
  }
  .nav-logo span { color: var(--white); }
  .nav-links { display: flex; gap: 2.5rem; list-style: none; }
  .nav-links a {
    font-size: 0.7rem;
    letter-spacing: 0.18em;
    text-transform: uppercase;
    color: var(--light);
    cursor: pointer;
    transition: color 0.2s;
    text-decoration: none;
  }
  .nav-links a:hover, .nav-links a.active { color: var(--gold); }
  .nav-right { display: flex; align-items: center; gap: 1rem; }
  .cart-btn {
    position: relative;
    background: none; border: none; cursor: pointer;
    color: var(--white); padding: 8px;
    transition: color 0.2s;
  }
  .cart-btn:hover { color: var(--gold); }
  .cart-count {
    position: absolute; top: 2px; right: 2px;
    background: var(--gold); color: var(--black);
    font-size: 0.6rem; font-weight: 500;
    width: 16px; height: 16px;
    border-radius: 50%;
    display: flex; align-items: center; justify-content: center;
  }
  .mobile-menu-btn {
    display: none;
    background: none; border: none; cursor: pointer; color: var(--white);
  }

  /* HERO */
  .hero {
    min-height: 100vh;
    display: flex; align-items: center;
    position: relative; overflow: hidden;
    padding: 0 4rem;
    background: var(--black);
  }
  .hero-bg {
    position: absolute; inset: 0;
    background: radial-gradient(ellipse at 70% 50%, rgba(201,168,76,0.07) 0%, transparent 60%),
                radial-gradient(ellipse at 20% 80%, rgba(201,168,76,0.04) 0%, transparent 50%);
  }
  .hero-lines {
    position: absolute; inset: 0; overflow: hidden; pointer-events: none;
  }
  .hero-lines::before {
    content: '';
    position: absolute; top: 0; right: 30%;
    width: 1px; height: 100%;
    background: linear-gradient(to bottom, transparent, rgba(201,168,76,0.2), transparent);
  }
  .hero-content { position: relative; max-width: 600px; }
  .hero-tag {
    font-size: 0.65rem; letter-spacing: 0.35em; text-transform: uppercase;
    color: var(--gold); margin-bottom: 1.5rem;
    display: flex; align-items: center; gap: 0.75rem;
  }
  .hero-tag::before {
    content: '';
    width: 32px; height: 1px; background: var(--gold);
  }
  .hero h1 {
    font-family: var(--serif);
    font-size: clamp(3.5rem, 7vw, 6rem);
    font-weight: 300;
    line-height: 1.05;
    margin-bottom: 1.5rem;
    color: var(--white);
  }
  .hero h1 em {
    font-style: italic;
    color: var(--gold);
    display: block;
  }
  .hero p {
    font-size: 0.9rem;
    color: var(--muted);
    max-width: 420px;
    line-height: 1.8;
    margin-bottom: 2.5rem;
    letter-spacing: 0.04em;
  }
  .hero-btns { display: flex; gap: 1rem; align-items: center; flex-wrap: wrap; }
  .btn-gold {
    padding: 14px 36px;
    background: var(--gold);
    color: var(--black);
    font-size: 0.7rem; font-weight: 500;
    letter-spacing: 0.2em; text-transform: uppercase;
    border: none; cursor: pointer;
    transition: all 0.3s;
    font-family: var(--sans);
  }
  .btn-gold:hover {
    background: var(--gold-light);
    transform: translateY(-1px);
  }
  .btn-outline {
    padding: 13px 36px;
    background: transparent;
    color: var(--white);
    font-size: 0.7rem; font-weight: 400;
    letter-spacing: 0.2em; text-transform: uppercase;
    border: 1px solid rgba(255,255,255,0.2); cursor: pointer;
    transition: all 0.3s;
    font-family: var(--sans);
  }
  .btn-outline:hover {
    border-color: var(--gold);
    color: var(--gold);
  }
  .hero-image {
    position: absolute; right: 0; top: 50%;
    transform: translateY(-50%);
    width: 45vw; max-width: 640px;
    height: 100vh;
    overflow: hidden;
  }
  .hero-image img {
    width: 100%; height: 100%;
    object-fit: cover;
    opacity: 0.6;
  }
  .hero-image::after {
    content: '';
    position: absolute; inset: 0;
    background: linear-gradient(to right, var(--black) 0%, rgba(10,10,10,0.3) 60%, transparent 100%);
  }
  .hero-stats {
    display: flex; gap: 2.5rem; margin-top: 3rem;
    padding-top: 2rem;
    border-top: 1px solid var(--border);
  }
  .hero-stat-val {
    font-family: var(--serif); font-size: 1.8rem; font-weight: 400;
    color: var(--gold); line-height: 1;
  }
  .hero-stat-label {
    font-size: 0.65rem; letter-spacing: 0.15em; text-transform: uppercase;
    color: var(--muted); margin-top: 4px;
  }

  /* MARQUEE */
  .marquee-wrap {
    overflow: hidden; border-top: 1px solid var(--border); border-bottom: 1px solid var(--border);
    background: var(--charcoal);
    padding: 14px 0;
  }
  .marquee { display: flex; gap: 0; animation: marquee 25s linear infinite; white-space: nowrap; }
  .marquee-item {
    display: flex; align-items: center; gap: 1.5rem;
    padding: 0 2.5rem;
    font-size: 0.65rem; letter-spacing: 0.2em; text-transform: uppercase;
    color: var(--muted);
  }
  .marquee-item .dot { color: var(--gold); font-size: 0.5rem; }
  @keyframes marquee { from { transform: translateX(0); } to { transform: translateX(-50%); } }

  /* SECTION HEADERS */
  .section { padding: 5rem 4rem; }
  .section-label {
    font-size: 0.65rem; letter-spacing: 0.35em; text-transform: uppercase;
    color: var(--gold); margin-bottom: 1rem;
    display: flex; align-items: center; gap: 0.75rem;
  }
  .section-label::before { content: ''; width: 24px; height: 1px; background: var(--gold); }
  .section-title {
    font-family: var(--serif); font-size: clamp(2rem, 4vw, 3rem);
    font-weight: 300; line-height: 1.15; color: var(--white);
  }
  .section-title em { font-style: italic; color: var(--gold); }

  /* PRODUCTS GRID */
  .products-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
    gap: 1.5px;
    margin-top: 3rem;
  }
  .product-card {
    background: var(--charcoal);
    cursor: pointer;
    transition: all 0.4s;
    position: relative;
    overflow: hidden;
  }
  .product-card:hover { background: var(--mid); }
  .product-card:hover .product-img img { transform: scale(1.05); }
  .product-img {
    aspect-ratio: 3/4;
    overflow: hidden;
    background: var(--mid);
    position: relative;
  }
  .product-img img {
    width: 100%; height: 100%;
    object-fit: cover;
    transition: transform 0.6s ease;
  }
  .product-badge {
    position: absolute; top: 1rem; left: 1rem;
    background: var(--gold); color: var(--black);
    font-size: 0.6rem; font-weight: 500;
    letter-spacing: 0.15em; text-transform: uppercase;
    padding: 4px 10px;
  }
  .product-overlay {
    position: absolute; inset: 0;
    background: linear-gradient(to top, rgba(10,10,10,0.6) 0%, transparent 50%);
  }
  .product-info { padding: 1.5rem; }
  .product-name {
    font-family: var(--serif); font-size: 1.25rem;
    font-weight: 400; color: var(--white); margin-bottom: 0.25rem;
  }
  .product-notes { font-size: 0.72rem; color: var(--muted); letter-spacing: 0.08em; margin-bottom: 0.75rem; }
  .product-meta { display: flex; align-items: center; justify-content: space-between; }
  .product-price {
    font-family: var(--serif); font-size: 1.2rem;
    color: var(--gold);
  }
  .product-price span { font-size: 0.75rem; color: var(--muted); margin-left: 2px; font-family: var(--sans); }
  .product-rating { display: flex; align-items: center; gap: 4px; color: var(--gold); font-size: 0.72rem; }
  .product-add-btn {
    width: 100%; padding: 12px;
    background: var(--gold);
    color: var(--black);
    font-size: 0.65rem; font-weight: 500;
    letter-spacing: 0.2em; text-transform: uppercase;
    border: none; cursor: pointer;
    transition: background 0.2s;
    font-family: var(--sans);
    margin-top: 1rem;
  }
  .product-add-btn:hover { background: var(--gold-light); }

  /* FILTERS */
  .filters {
    display: flex; gap: 0.5rem; flex-wrap: wrap; margin-top: 2rem;
  }
  .filter-btn {
    padding: 8px 20px;
    background: transparent;
    border: 1px solid var(--border);
    color: var(--muted);
    font-size: 0.65rem; letter-spacing: 0.15em; text-transform: uppercase;
    cursor: pointer; transition: all 0.2s;
    font-family: var(--sans);
  }
  .filter-btn:hover, .filter-btn.active {
    border-color: var(--gold); color: var(--gold);
  }
  .price-range { display: flex; align-items: center; gap: 1rem; }
  .price-range input[type=range] {
    accent-color: var(--gold);
    width: 120px;
  }

  /* BRAND STORY */
  .brand-section {
    padding: 6rem 4rem;
    background: var(--charcoal);
    display: grid; grid-template-columns: 1fr 1fr; gap: 5rem; align-items: center;
  }
  .brand-img-wrap {
    position: relative; aspect-ratio: 4/5;
    overflow: hidden;
  }
  .brand-img-wrap img {
    width: 100%; height: 100%; object-fit: cover;
  }
  .brand-img-wrap::before {
    content: '';
    position: absolute; top: -1.5rem; left: -1.5rem;
    right: 1.5rem; bottom: 1.5rem;
    border: 1px solid rgba(201,168,76,0.3);
    pointer-events: none; z-index: 1;
  }
  .brand-text .section-label { justify-content: flex-start; }

  /* PRODUCT DETAIL MODAL */
  .modal-overlay {
    position: fixed; inset: 0; z-index: 200;
    background: rgba(0,0,0,0.85);
    backdrop-filter: blur(8px);
    display: flex; align-items: center; justify-content: center;
    padding: 2rem;
  }
  .modal {
    background: var(--charcoal);
    max-width: 900px; width: 100%;
    max-height: 90vh; overflow-y: auto;
    display: grid; grid-template-columns: 1fr 1fr;
    position: relative;
  }
  .modal-img { aspect-ratio: 4/5; overflow: hidden; }
  .modal-img img { width: 100%; height: 100%; object-fit: cover; }
  .modal-info { padding: 3rem; display: flex; flex-direction: column; }
  .modal-close {
    position: absolute; top: 1rem; right: 1rem;
    background: rgba(0,0,0,0.5); border: none;
    color: var(--white); cursor: pointer; padding: 8px;
    transition: color 0.2s;
  }
  .modal-close:hover { color: var(--gold); }
  .modal-name {
    font-family: var(--serif); font-size: 2rem; font-weight: 400;
    color: var(--white); margin-bottom: 0.25rem;
  }
  .modal-notes { font-size: 0.8rem; color: var(--muted); margin-bottom: 1.5rem; letter-spacing: 0.08em; }
  .modal-price {
    font-family: var(--serif); font-size: 2rem;
    color: var(--gold); margin-bottom: 1.5rem;
  }
  .modal-desc {
    font-size: 0.85rem; color: rgba(255,255,255,0.6);
    line-height: 1.9; margin-bottom: 1.5rem;
  }
  .qty-row { display: flex; align-items: center; gap: 1rem; margin-bottom: 1.5rem; }
  .qty-btn {
    width: 36px; height: 36px;
    background: var(--mid); border: 1px solid var(--border);
    color: var(--white); cursor: pointer;
    display: flex; align-items: center; justify-content: center;
    transition: border-color 0.2s;
  }
  .qty-btn:hover { border-color: var(--gold); }
  .qty-num {
    font-family: var(--serif); font-size: 1.2rem;
    min-width: 32px; text-align: center;
  }
  .reviews-mini { margin-top: auto; padding-top: 1.5rem; border-top: 1px solid var(--border); }
  .review-item { margin-bottom: 1rem; }
  .review-header { display: flex; justify-content: space-between; margin-bottom: 4px; }
  .review-author { font-size: 0.75rem; letter-spacing: 0.1em; }
  .review-stars { color: var(--gold); display: flex; gap: 2px; }
  .review-text { font-size: 0.8rem; color: var(--muted); font-style: italic; }

  /* CART SIDEBAR */
  .cart-sidebar {
    position: fixed; right: 0; top: 0; bottom: 0; z-index: 150;
    width: 440px; max-width: 100vw;
    background: var(--charcoal);
    border-left: 1px solid var(--border);
    display: flex; flex-direction: column;
    transform: translateX(100%);
    transition: transform 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  }
  .cart-sidebar.open { transform: translateX(0); }
  .cart-header {
    padding: 1.5rem 2rem;
    border-bottom: 1px solid var(--border);
    display: flex; align-items: center; justify-content: space-between;
  }
  .cart-title {
    font-family: var(--serif); font-size: 1.5rem; font-weight: 400;
  }
  .cart-close {
    background: none; border: none; color: var(--white); cursor: pointer; padding: 4px;
  }
  .cart-items { flex: 1; overflow-y: auto; padding: 1.5rem 2rem; }
  .cart-item {
    display: grid; grid-template-columns: 72px 1fr auto;
    gap: 1rem; align-items: start;
    padding-bottom: 1.5rem; margin-bottom: 1.5rem;
    border-bottom: 1px solid var(--border);
  }
  .cart-item-img { width: 72px; height: 90px; object-fit: cover; background: var(--mid); }
  .cart-item-name { font-family: var(--serif); font-size: 1rem; margin-bottom: 4px; }
  .cart-item-notes { font-size: 0.72rem; color: var(--muted); margin-bottom: 0.5rem; }
  .cart-item-price { font-size: 0.9rem; color: var(--gold); }
  .cart-item-remove {
    background: none; border: none; color: var(--muted); cursor: pointer; padding: 4px;
    transition: color 0.2s;
  }
  .cart-item-remove:hover { color: #e55; }
  .cart-footer {
    padding: 1.5rem 2rem;
    border-top: 1px solid var(--border);
  }
  .promo-row {
    display: flex; gap: 0.5rem; margin-bottom: 1rem;
  }
  .promo-input {
    flex: 1; padding: 10px 14px;
    background: var(--mid); border: 1px solid var(--border);
    color: var(--white); font-family: var(--sans); font-size: 0.8rem;
    letter-spacing: 0.08em; text-transform: uppercase;
    outline: none; transition: border-color 0.2s;
  }
  .promo-input:focus { border-color: var(--gold); }
  .promo-input::placeholder { text-transform: none; letter-spacing: 0; color: var(--muted); }
  .promo-apply {
    padding: 10px 20px;
    background: var(--mid); border: 1px solid var(--border);
    color: var(--gold); font-size: 0.65rem; font-weight: 500;
    letter-spacing: 0.15em; text-transform: uppercase;
    cursor: pointer; transition: all 0.2s;
    font-family: var(--sans);
  }
  .promo-apply:hover { border-color: var(--gold); }
  .cart-totals { margin-bottom: 1.5rem; }
  .cart-row {
    display: flex; justify-content: space-between;
    font-size: 0.8rem; margin-bottom: 0.5rem;
    color: var(--muted);
  }
  .cart-row.total {
    font-family: var(--serif); font-size: 1.2rem;
    color: var(--white); margin-top: 0.75rem;
    padding-top: 0.75rem; border-top: 1px solid var(--border);
  }
  .cart-row.total .val { color: var(--gold); }
  .empty-cart { text-align: center; padding: 3rem 0; color: var(--muted); }
  .empty-cart-icon { font-size: 3rem; margin-bottom: 1rem; opacity: 0.3; }

  /* CHECKOUT */
  .checkout-form { padding: 1rem 0; }
  .form-section-title {
    font-family: var(--serif); font-size: 1rem; margin-bottom: 1rem;
    padding-bottom: 0.5rem; border-bottom: 1px solid var(--border);
    color: var(--light);
  }
  .form-group { margin-bottom: 1rem; }
  .form-label { font-size: 0.65rem; letter-spacing: 0.15em; text-transform: uppercase; color: var(--muted); display: block; margin-bottom: 6px; }
  .form-input, .form-select {
    width: 100%; padding: 10px 14px;
    background: var(--mid); border: 1px solid var(--border);
    color: var(--white); font-family: var(--sans); font-size: 0.85rem;
    outline: none; transition: border-color 0.2s;
  }
  .form-input:focus, .form-select:focus { border-color: var(--gold); }
  .form-select { appearance: none; cursor: pointer; }
  .form-select option { background: var(--charcoal); }
  .payment-options { display: flex; flex-direction: column; gap: 0.5rem; margin-bottom: 1rem; }
  .payment-option {
    display: flex; align-items: center; gap: 0.75rem; padding: 12px;
    border: 1px solid var(--border); cursor: pointer;
    transition: border-color 0.2s;
  }
  .payment-option.selected { border-color: var(--gold); }
  .payment-option input { accent-color: var(--gold); }
  .payment-label { font-size: 0.8rem; }
  .payment-sub { font-size: 0.7rem; color: var(--muted); }

  /* CONTACT */
  .contact-section {
    padding: 5rem 4rem;
    display: grid; grid-template-columns: 1fr 1fr; gap: 4rem;
  }
  .contact-info-item {
    display: flex; align-items: flex-start; gap: 1rem; margin-bottom: 2rem;
  }
  .contact-icon {
    width: 48px; height: 48px; border: 1px solid var(--gold);
    display: flex; align-items: center; justify-content: center;
    color: var(--gold); flex-shrink: 0;
  }
  .contact-info-label { font-size: 0.65rem; letter-spacing: 0.2em; text-transform: uppercase; color: var(--muted); margin-bottom: 4px; }
  .contact-info-value { font-size: 0.9rem; color: var(--white); }

  /* ABOUT */
  .about-section { padding: 5rem 4rem; }
  .values-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 2rem; margin-top: 3rem; }
  .value-card {
    padding: 2.5rem;
    border: 1px solid var(--border);
    transition: border-color 0.3s;
  }
  .value-card:hover { border-color: var(--gold); }
  .value-icon { color: var(--gold); margin-bottom: 1.5rem; }
  .value-title { font-family: var(--serif); font-size: 1.2rem; margin-bottom: 0.75rem; }
  .value-text { font-size: 0.82rem; color: var(--muted); line-height: 1.8; }

  /* ORDERS */
  .order-tracker {
    background: var(--mid); padding: 1.5rem;
    margin-bottom: 1rem;
  }
  .order-steps { display: flex; justify-content: space-between; position: relative; margin-top: 1rem; }
  .order-steps::before {
    content: ''; position: absolute; top: 12px; left: 0; right: 0;
    height: 1px; background: var(--border);
  }
  .order-step { display: flex; flex-direction: column; align-items: center; gap: 0.5rem; position: relative; }
  .order-step-dot {
    width: 24px; height: 24px; border-radius: 50%;
    background: var(--border); z-index: 1;
    display: flex; align-items: center; justify-content: center;
    font-size: 0.6rem;
  }
  .order-step-dot.done { background: var(--gold); color: var(--black); }
  .order-step-dot.current { background: var(--gold-dark); color: var(--white); }
  .order-step-label { font-size: 0.6rem; letter-spacing: 0.1em; text-transform: uppercase; color: var(--muted); }

  /* ADMIN */
  .admin-panel {
    padding: 2rem;
    background: var(--charcoal);
    min-height: 100vh;
  }
  .admin-header {
    display: flex; align-items: center; justify-content: space-between;
    margin-bottom: 2rem; padding-bottom: 1rem;
    border-bottom: 1px solid var(--border);
  }
  .admin-title { font-family: var(--serif); font-size: 1.8rem; }
  .admin-tabs { display: flex; gap: 0; margin-bottom: 2rem; }
  .admin-tab {
    padding: 10px 24px; background: var(--mid); border: 1px solid var(--border);
    color: var(--muted); font-size: 0.7rem; font-weight: 500;
    letter-spacing: 0.15em; text-transform: uppercase;
    cursor: pointer; transition: all 0.2s; font-family: var(--sans);
    border-right: none;
  }
  .admin-tab:last-child { border-right: 1px solid var(--border); }
  .admin-tab.active { background: var(--gold); color: var(--black); border-color: var(--gold); }
  .admin-table { width: 100%; border-collapse: collapse; }
  .admin-table th {
    text-align: left; padding: 12px 16px;
    font-size: 0.65rem; letter-spacing: 0.15em; text-transform: uppercase;
    color: var(--muted); border-bottom: 1px solid var(--border);
  }
  .admin-table td {
    padding: 14px 16px; font-size: 0.82rem;
    border-bottom: 1px solid rgba(46,46,46,0.5);
  }
  .status-badge {
    display: inline-block; padding: 3px 10px;
    font-size: 0.65rem; letter-spacing: 0.1em; text-transform: uppercase;
    border-radius: 2px;
  }
  .status-pending { background: rgba(201,168,76,0.15); color: var(--gold); }
  .status-confirmed { background: rgba(40,167,69,0.15); color: #4caf50; }
  .status-delivered { background: rgba(0,123,255,0.15); color: #5b9cf6; }

  /* FOOTER */
  .footer {
    background: var(--off-black);
    padding: 4rem;
    border-top: 1px solid var(--border);
  }
  .footer-grid { display: grid; grid-template-columns: 2fr 1fr 1fr 1.5fr; gap: 3rem; margin-bottom: 3rem; }
  .footer-brand { font-family: var(--serif); font-size: 1.5rem; color: var(--gold); margin-bottom: 1rem; }
  .footer-desc { font-size: 0.82rem; color: var(--muted); line-height: 1.8; max-width: 260px; }
  .footer-col-title {
    font-size: 0.65rem; letter-spacing: 0.2em; text-transform: uppercase;
    color: var(--gold); margin-bottom: 1.25rem;
  }
  .footer-links { list-style: none; display: flex; flex-direction: column; gap: 0.6rem; }
  .footer-links a {
    font-size: 0.82rem; color: var(--muted); cursor: pointer; text-decoration: none;
    transition: color 0.2s;
  }
  .footer-links a:hover { color: var(--white); }
  .footer-bottom {
    display: flex; justify-content: space-between; align-items: center;
    padding-top: 2rem; border-top: 1px solid var(--border);
    font-size: 0.72rem; color: var(--muted);
  }
  .footer-bottom span { color: var(--gold); }

  /* TOAST */
  .toast {
    position: fixed; bottom: 2rem; left: 50%; transform: translateX(-50%) translateY(100px);
    background: var(--charcoal); border: 1px solid var(--gold);
    padding: 14px 24px;
    display: flex; align-items: center; gap: 10px;
    font-size: 0.82rem; z-index: 300;
    transition: transform 0.4s cubic-bezier(0.4, 0, 0.2, 1);
    white-space: nowrap;
  }
  .toast.show { transform: translateX(-50%) translateY(0); }
  .toast-icon { color: var(--gold); }

  /* SUCCESS */
  .success-screen {
    display: flex; flex-direction: column; align-items: center; justify-content: center;
    padding: 4rem 2rem; text-align: center;
  }
  .success-icon {
    width: 72px; height: 72px; border: 1px solid var(--gold);
    border-radius: 50%; display: flex; align-items: center; justify-content: center;
    color: var(--gold); margin-bottom: 1.5rem;
  }
  .success-title { font-family: var(--serif); font-size: 1.8rem; margin-bottom: 0.5rem; }
  .success-sub { font-size: 0.85rem; color: var(--muted); margin-bottom: 2rem; }

  /* RESPONSIVE */
  @media (max-width: 900px) {
    .nav-links { display: none; }
    .mobile-menu-btn { display: flex; }
    .hero { padding: 0 2rem; }
    .hero-image { display: none; }
    .section { padding: 3rem 1.5rem; }
    .brand-section { grid-template-columns: 1fr; padding: 3rem 1.5rem; }
    .contact-section { grid-template-columns: 1fr; padding: 3rem 1.5rem; }
    .about-section { padding: 3rem 1.5rem; }
    .values-grid { grid-template-columns: 1fr; }
    .footer { padding: 3rem 1.5rem; }
    .footer-grid { grid-template-columns: 1fr 1fr; }
    .modal { grid-template-columns: 1fr; }
    .modal-img { aspect-ratio: 16/9; }
    .cart-sidebar { width: 100vw; }
    .admin-panel { padding: 1rem; }
  }
  @media (max-width: 600px) {
    .hero h1 { font-size: 2.8rem; }
    .footer-grid { grid-template-columns: 1fr; }
    .hero-stats { gap: 1.5rem; }
    .products-grid { grid-template-columns: repeat(2, 1fr); gap: 1px; }
  }

  .mobile-nav {
    position: fixed; inset: 0; z-index: 90;
    background: var(--black);
    display: flex; flex-direction: column;
    align-items: center; justify-content: center; gap: 2.5rem;
    transform: translateX(-100%);
    transition: transform 0.4s;
  }
  .mobile-nav.open { transform: translateX(0); }
  .mobile-nav a {
    font-family: var(--serif); font-size: 2rem;
    color: var(--white); cursor: pointer; text-decoration: none;
    transition: color 0.2s;
  }
  .mobile-nav a:hover { color: var(--gold); }
`;

// ─── MAIN APP ─────────────────────────────────────────────────────────────────
export default function App() {
  const [page, setPage] = useState("home");
  const [cart, setCart] = useState([]);
  const [cartOpen, setCartOpen] = useState(false);
  const [selectedProduct, setSelectedProduct] = useState(null);
  const [qty, setQty] = useState(1);
  const [filter, setFilter] = useState({ type: "all", maxPrice: 1000 });
  const [toast, setToast] = useState({ show: false, msg: "" });
  const [promoCode, setPromoCode] = useState("");
  const [discount, setDiscount] = useState(0);
  const [promoMsg, setPromoMsg] = useState("");
  const [checkoutStep, setCheckoutStep] = useState("cart"); // cart | checkout | success
  const [checkoutForm, setCheckoutForm] = useState({ name: "", phone: "", address: "", city: CITIES[0].name, payment: "cod" });
  const [adminTab, setAdminTab] = useState("products");
  const [mobileNavOpen, setMobileNavOpen] = useState(false);
  const [orders, setOrders] = useState([
    { id: "ORD-001", customer: "Youssef Bennani", city: "Casablanca", total: 880, status: "confirmed", date: "28 Avr 2026", items: ["Noir Absolu x1"] },
    { id: "ORD-002", customer: "Fatima Zahra", city: "Rabat", total: 1470, status: "pending", date: "29 Avr 2026", items: ["Ambre Doré x1", "Rose Éternelle x1"] },
    { id: "ORD-003", customer: "Karim Alaoui", city: "Marrakech", total: 715, status: "delivered", date: "27 Avr 2026", items: ["Cedre Royal x1"] },
  ]);

  const showToast = (msg) => {
    setToast({ show: true, msg });
    setTimeout(() => setToast({ show: false, msg: "" }), 2500);
  };

  const addToCart = (product) => {
    setCart(prev => {
      const existing = prev.find(i => i.id === product.id);
      if (existing) return prev.map(i => i.id === product.id ? { ...i, qty: i.qty + qty } : i);
      return [...prev, { ...product, qty }];
    });
    showToast(`${product.name} ajouté au panier`);
    setSelectedProduct(null);
    setQty(1);
  };

  const removeFromCart = (id) => setCart(prev => prev.filter(i => i.id !== id));

  const cartTotal = cart.reduce((s, i) => s + i.price * i.qty, 0);
  const shippingFee = checkoutForm.city ? CITIES.find(c => c.name === checkoutForm.city)?.fee || 40 : 0;
  const discountAmount = cartTotal * discount;
  const orderTotal = cartTotal - discountAmount + (cart.length > 0 ? shippingFee : 0);

  const applyPromo = () => {
    const rate = PROMO_CODES[promoCode.trim().toUpperCase()];
    if (rate) { setDiscount(rate); setPromoMsg(`✓ Code appliqué — ${rate * 100}% de réduction !`); }
    else { setPromoMsg("Code invalide"); setDiscount(0); }
  };

  const placeOrder = () => {
    const newOrder = {
      id: `ORD-00${orders.length + 4}`, customer: checkoutForm.name,
      city: checkoutForm.city, total: Math.round(orderTotal),
      status: "pending", date: "29 Avr 2026",
      items: cart.map(i => `${i.name} x${i.qty}`)
    };
    setOrders(prev => [newOrder, ...prev]);
    setCart([]); setDiscount(0); setPromoCode(""); setPromoMsg("");
    setCheckoutStep("success");
  };

  const filteredProducts = PRODUCTS.filter(p =>
    (filter.type === "all" || p.type === filter.type) && p.price <= filter.maxPrice
  );

  const cartCount = cart.reduce((s, i) => s + i.qty, 0);

  const navItems = [
    { label: "Accueil", page: "home" },
    { label: "Boutique", page: "shop" },
    { label: "À propos", page: "about" },
    { label: "Contact", page: "contact" },
    { label: "Admin", page: "admin" },
  ];

  const navigate = (p) => { setPage(p); setMobileNavOpen(false); window.scrollTo(0, 0); };

  return (
    <>
      <style>{css}</style>

      {/* NAV */}
      <nav className="nav">
        <div className="nav-logo" onClick={() => navigate("home")}>MAISON<span> DORÉE</span></div>
        <ul className="nav-links">
          {navItems.map(n => (
            <li key={n.page}><a className={page === n.page ? "active" : ""} onClick={() => navigate(n.page)}>{n.label}</a></li>
          ))}
        </ul>
        <div className="nav-right">
          <button className="cart-btn" onClick={() => { setCartOpen(true); setCheckoutStep("cart"); }}>
            <Icon name="cart" size={22} />
            {cartCount > 0 && <span className="cart-count">{cartCount}</span>}
          </button>
          <button className="mobile-menu-btn" onClick={() => setMobileNavOpen(v => !v)}>
            {mobileNavOpen ? <Icon name="close" size={22} /> : <Icon name="menu" size={22} />}
          </button>
        </div>
      </nav>

      {/* MOBILE NAV */}
      <div className={`mobile-nav ${mobileNavOpen ? "open" : ""}`}>
        {navItems.map(n => <a key={n.page} onClick={() => navigate(n.page)}>{n.label}</a>)}
      </div>

      {/* PAGES */}
      {page === "home" && <HomePage navigate={navigate} filteredProducts={PRODUCTS.slice(0, 3)} addToCart={addToCart} setSelectedProduct={setSelectedProduct} />}
      {page === "shop" && <ShopPage products={filteredProducts} filter={filter} setFilter={setFilter} addToCart={addToCart} setSelectedProduct={setSelectedProduct} />}
      {page === "about" && <AboutPage />}
      {page === "contact" && <ContactPage />}
      {page === "admin" && <AdminPanel orders={orders} setOrders={setOrders} adminTab={adminTab} setAdminTab={setAdminTab} />}

      {/* PRODUCT MODAL */}
      {selectedProduct && (
        <div className="modal-overlay" onClick={() => setSelectedProduct(null)}>
          <div className="modal" onClick={e => e.stopPropagation()}>
            <div className="modal-img">
              <img src={selectedProduct.img} alt={selectedProduct.name} />
            </div>
            <div className="modal-info">
              <div>
                <div className="section-label" style={{ fontSize: "0.6rem", letterSpacing: "0.3em", color: "var(--gold)", textTransform: "uppercase", marginBottom: "0.5rem" }}>
                  {selectedProduct.ml} · {selectedProduct.type}
                </div>
                <h2 className="modal-name">{selectedProduct.name}</h2>
                <p className="modal-notes">{selectedProduct.notes}</p>
                <div className="modal-price">{selectedProduct.price} MAD</div>
                <p className="modal-desc">
                  Un parfum d'exception, né des terres les plus précieuses du monde. Chaque note est sélectionnée avec une précision infinie pour créer une signature olfactive inimitable qui transcende le temps.
                </p>
                <div className="qty-row">
                  <button className="qty-btn" onClick={() => setQty(q => Math.max(1, q - 1))}><Icon name="minus" size={14} /></button>
                  <span className="qty-num">{qty}</span>
                  <button className="qty-btn" onClick={() => setQty(q => q + 1)}><Icon name="plus" size={14} /></button>
                  <span style={{ fontSize: "0.72rem", color: "var(--muted)" }}>flacons</span>
                </div>
                <button className="product-add-btn" style={{ marginTop: 0 }} onClick={() => addToCart(selectedProduct)}>
                  Ajouter au panier — {selectedProduct.price * qty} MAD
                </button>
              </div>
              <div className="reviews-mini">
                <div style={{ display: "flex", alignItems: "center", gap: "0.5rem", marginBottom: "1rem" }}>
                  <div style={{ display: "flex", gap: "2px", color: "var(--gold)" }}>{[...Array(5)].map((_, i) => <Icon key={i} name="star" size={12} />)}</div>
                  <span style={{ fontSize: "0.75rem", color: "var(--muted)" }}>{selectedProduct.rating} · {selectedProduct.reviews} avis</span>
                </div>
                {[{ name: "A. Bennani", text: "Sillage extraordinaire, tient toute la journée." }, { name: "S. Idrissi", text: "Un luxe abordable. Ma signature depuis 2 ans." }].map((r, i) => (
                  <div key={i} className="review-item">
                    <div className="review-header">
                      <span className="review-author">{r.name}</span>
                      <div className="review-stars">{[...Array(5)].map((_, j) => <Icon key={j} name="star" size={10} />)}</div>
                    </div>
                    <p className="review-text">"{r.text}"</p>
                  </div>
                ))}
              </div>
            </div>
            <button className="modal-close" onClick={() => setSelectedProduct(null)}><Icon name="close" /></button>
          </div>
        </div>
      )}

      {/* CART */}
      {cartOpen && <div style={{ position: "fixed", inset: 0, background: "rgba(0,0,0,0.5)", zIndex: 149 }} onClick={() => setCartOpen(false)} />}
      <div className={`cart-sidebar ${cartOpen ? "open" : ""}`}>
        <div className="cart-header">
          <h2 className="cart-title">Mon Panier</h2>
          <button className="cart-close" onClick={() => setCartOpen(false)}><Icon name="close" /></button>
        </div>

        {checkoutStep === "success" ? (
          <div className="success-screen">
            <div className="success-icon"><Icon name="check" size={32} /></div>
            <h3 className="success-title">Commande Confirmée</h3>
            <p className="success-sub">Nous vous contacterons sous 24h pour confirmer la livraison.</p>
            <button className="btn-gold" onClick={() => { setCartOpen(false); setCheckoutStep("cart"); }}>Continuer les achats</button>
          </div>
        ) : checkoutStep === "checkout" ? (
          <>
            <div className="cart-items">
              <div className="checkout-form">
                <p className="form-section-title">Informations de livraison</p>
                {[["name", "Nom complet", "text"], ["phone", "Téléphone", "tel"], ["address", "Adresse complète", "text"]].map(([k, l, t]) => (
                  <div key={k} className="form-group">
                    <label className="form-label">{l}</label>
                    <input className="form-input" type={t} placeholder={l} value={checkoutForm[k]} onChange={e => setCheckoutForm(f => ({ ...f, [k]: e.target.value }))} />
                  </div>
                ))}
                <div className="form-group">
                  <label className="form-label">Ville</label>
                  <select className="form-select" value={checkoutForm.city} onChange={e => setCheckoutForm(f => ({ ...f, city: e.target.value }))}>
                    {CITIES.map(c => <option key={c.name} value={c.name}>{c.name} — {c.fee} MAD</option>)}
                  </select>
                </div>
                <p className="form-section-title" style={{ marginTop: "1.5rem" }}>Mode de paiement</p>
                <div className="payment-options">
                  {[{ val: "cod", label: "Paiement à la livraison", sub: "Payez en espèces à réception" }, { val: "online", label: "Paiement en ligne", sub: "Bientôt disponible" }].map(opt => (
                    <label key={opt.val} className={`payment-option ${checkoutForm.payment === opt.val ? "selected" : ""}`}>
                      <input type="radio" name="payment" value={opt.val} checked={checkoutForm.payment === opt.val} onChange={e => setCheckoutForm(f => ({ ...f, payment: e.target.value }))} />
                      <div>
                        <div className="payment-label">{opt.label}</div>
                        <div className="payment-sub">{opt.sub}</div>
                      </div>
                    </label>
                  ))}
                </div>
              </div>
            </div>
            <div className="cart-footer">
              <div className="cart-totals">
                <div className="cart-row"><span>Sous-total</span><span>{cartTotal} MAD</span></div>
                {discount > 0 && <div className="cart-row" style={{ color: "#4caf50" }}><span>Remise ({discount * 100}%)</span><span>-{Math.round(discountAmount)} MAD</span></div>}
                <div className="cart-row"><span>Livraison ({checkoutForm.city})</span><span>{shippingFee} MAD</span></div>
                <div className="cart-row total"><span>Total</span><span className="val">{Math.round(orderTotal)} MAD</span></div>
              </div>
              <div style={{ display: "flex", gap: "0.5rem" }}>
                <button className="btn-outline" style={{ flex: 1 }} onClick={() => setCheckoutStep("cart")}>Retour</button>
                <button className="btn-gold" style={{ flex: 2 }} onClick={placeOrder} disabled={!checkoutForm.name || !checkoutForm.phone}>
                  Confirmer la commande
                </button>
              </div>
            </div>
          </>
        ) : (
          <>
            <div className="cart-items">
              {cart.length === 0 ? (
                <div className="empty-cart">
                  <div className="empty-cart-icon"><Icon name="cart" size={48} /></div>
                  <p style={{ fontSize: "0.85rem", color: "var(--muted)" }}>Votre panier est vide</p>
                </div>
              ) : cart.map(item => (
                <div key={item.id} className="cart-item">
                  <img className="cart-item-img" src={item.img} alt={item.name} />
                  <div>
                    <div className="cart-item-name">{item.name}</div>
                    <div className="cart-item-notes">{item.notes}</div>
                    <div style={{ display: "flex", alignItems: "center", gap: "0.5rem" }}>
                      <button className="qty-btn" style={{ width: 24, height: 24 }} onClick={() => setCart(c => c.map(i => i.id === item.id ? { ...i, qty: Math.max(1, i.qty - 1) } : i))}><Icon name="minus" size={10} /></button>
                      <span style={{ fontSize: "0.82rem" }}>{item.qty}</span>
                      <button className="qty-btn" style={{ width: 24, height: 24 }} onClick={() => setCart(c => c.map(i => i.id === item.id ? { ...i, qty: i.qty + 1 } : i))}><Icon name="plus" size={10} /></button>
                    </div>
                    <div className="cart-item-price">{item.price * item.qty} MAD</div>
                  </div>
                  <button className="cart-item-remove" onClick={() => removeFromCart(item.id)}><Icon name="trash" size={16} /></button>
                </div>
              ))}
            </div>
            {cart.length > 0 && (
              <div className="cart-footer">
                <div className="promo-row">
                  <input className="promo-input" placeholder="Code promo" value={promoCode} onChange={e => setPromoCode(e.target.value)} />
                  <button className="promo-apply" onClick={applyPromo}>Appliquer</button>
                </div>
                {promoMsg && <p style={{ fontSize: "0.72rem", color: discount > 0 ? "#4caf50" : "#e55", marginBottom: "0.75rem" }}>{promoMsg}</p>}
                <div className="cart-totals">
                  <div className="cart-row"><span>Sous-total</span><span>{cartTotal} MAD</span></div>
                  {discount > 0 && <div className="cart-row" style={{ color: "#4caf50" }}><span>Remise</span><span>-{Math.round(discountAmount)} MAD</span></div>}
                  <div className="cart-row total"><span>Total estimé</span><span className="val">{Math.round(cartTotal - discountAmount)} MAD</span></div>
                </div>
                <button className="btn-gold" style={{ width: "100%" }} onClick={() => setCheckoutStep("checkout")}>
                  Passer la commande <span style={{ opacity: 0.7 }}>→</span>
                </button>
              </div>
            )}
          </>
        )}
      </div>

      {/* TOAST */}
      <div className={`toast ${toast.show ? "show" : ""}`}>
        <span className="toast-icon"><Icon name="check" size={16} /></span>
        {toast.msg}
      </div>

      {/* FOOTER (not on admin) */}
      {page !== "admin" && <Footer navigate={navigate} />}
    </>
  );
}

// ─── HOME PAGE ────────────────────────────────────────────────────────────────
function HomePage({ navigate, filteredProducts, addToCart, setSelectedProduct }) {
  return (
    <>
      {/* HERO */}
      <section className="hero">
        <div className="hero-bg" />
        <div className="hero-lines" />
        <div className="hero-content">
          <div className="hero-tag">Collection Printemps 2026</div>
          <h1>L'Art du<br />Parfum<em>Précieux</em></h1>
          <p>Des fragrances d'exception, conçues pour les âmes qui refusent l'ordinaire. Chaque flacon, une œuvre. Chaque sillage, une mémoire.</p>
          <div className="hero-btns">
            <button className="btn-gold" onClick={() => navigate("shop")}>Découvrir la collection</button>
            <button className="btn-outline" onClick={() => navigate("about")}>Notre histoire</button>
          </div>
          <div className="hero-stats">
            {[["12+", "Fragrances"], ["5K+", "Clients"], ["100%", "Luxe"]].map(([v, l]) => (
              <div key={l}>
                <div className="hero-stat-val">{v}</div>
                <div className="hero-stat-label">{l}</div>
              </div>
            ))}
          </div>
        </div>
        <div className="hero-image">
          <img src="https://images.unsplash.com/photo-1541643600914-78b084683702?w=800&q=80" alt="Parfum" />
        </div>
      </section>

      {/* MARQUEE */}
      <div className="marquee-wrap">
        <div className="marquee">
          {[...Array(2)].map((_, ri) => (
            <div key={ri} style={{ display: "flex" }}>
              {["Livraison partout au Maroc", "Paiement à la livraison", "Fragrances de luxe", "Collection exclusive", "Qualité supérieure", "Sillage intense"].map((t, i) => (
                <div key={i} className="marquee-item"><span>{t}</span><span className="dot">◆</span></div>
              ))}
            </div>
          ))}
        </div>
      </div>

      {/* FEATURED */}
      <section className="section" style={{ background: "var(--black)" }}>
        <div className="section-label">Sélection du moment</div>
        <h2 className="section-title">Nos <em>Bestsellers</em></h2>
        <div className="products-grid">
          {filteredProducts.map(p => <ProductCard key={p.id} product={p} onAdd={addToCart} onOpen={setSelectedProduct} />)}
        </div>
        <div style={{ marginTop: "3rem", textAlign: "center" }}>
          <button className="btn-outline" onClick={() => navigate("shop")}>Voir toute la collection <span style={{ marginLeft: 8 }}>→</span></button>
        </div>
      </section>

      {/* BRAND */}
      <div className="brand-section">
        <div className="brand-img-wrap">
          <img src="https://images.unsplash.com/photo-1592945403244-b3fbafd7f539?w=800&q=80" alt="Atelier" />
        </div>
        <div className="brand-text">
          <div className="section-label">Notre Savoir-Faire</div>
          <h2 className="section-title">L'Élégance<br />à l'État <em>Pur</em></h2>
          <p style={{ color: "var(--muted)", fontSize: "0.88rem", lineHeight: "1.9", margin: "2rem 0" }}>
            Maison Dorée est née d'une passion pour les matières premières nobles et les formulations intemporelles. Chaque fragrance est créée en petite série, par des nez experts, pour offrir une expérience olfactive unique.
          </p>
          <div style={{ display: "flex", flexDirection: "column", gap: "1rem", marginBottom: "2rem" }}>
            {["Matières premières d'exception", "Formulations artisanales", "Sillage longue durée certifié"].map((f, i) => (
              <div key={i} style={{ display: "flex", alignItems: "center", gap: "0.75rem", fontSize: "0.82rem", color: "var(--light)" }}>
                <span style={{ color: "var(--gold)" }}><Icon name="check" size={14} /></span>{f}
              </div>
            ))}
          </div>
          <button className="btn-gold">Découvrir notre histoire</button>
        </div>
      </div>

      {/* PERKS */}
      <section className="section" style={{ background: "var(--off-black)" }}>
        <div style={{ display: "grid", gridTemplateColumns: "repeat(auto-fit, minmax(220px, 1fr))", gap: "2rem" }}>
          {[
            { icon: "truck", title: "Livraison nationale", desc: "Livraison dans toutes les villes du Maroc" },
            { icon: "shield", title: "Produit authentique", desc: "100% original, satisfaction garantie" },
            { icon: "tag", title: "Codes promo", desc: "Offres exclusives pour nos clients fidèles" },
            { icon: "sparkle", title: "Éditions limitées", desc: "Des créations exclusives et numérotées" },
          ].map((p, i) => (
            <div key={i} style={{ textAlign: "center", padding: "2rem 1.5rem" }}>
              <div style={{ color: "var(--gold)", marginBottom: "1rem", display: "flex", justifyContent: "center" }}><Icon name={p.icon} size={28} /></div>
              <div style={{ fontFamily: "var(--serif)", fontSize: "1.1rem", marginBottom: "0.5rem" }}>{p.title}</div>
              <div style={{ fontSize: "0.78rem", color: "var(--muted)" }}>{p.desc}</div>
            </div>
          ))}
        </div>
      </section>
    </>
  );
}

// ─── SHOP PAGE ────────────────────────────────────────────────────────────────
function ShopPage({ products, filter, setFilter, addToCart, setSelectedProduct }) {
  return (
    <section className="section" style={{ paddingTop: "8rem" }}>
      <div className="section-label">Toutes nos créations</div>
      <h2 className="section-title">La <em>Collection</em></h2>
      <div style={{ display: "flex", gap: "1rem", alignItems: "center", flexWrap: "wrap", marginTop: "1rem" }}>
        <div className="filters">
          {[["all", "Tous"], ["men", "Homme"], ["women", "Femme"], ["unisex", "Unisexe"]].map(([v, l]) => (
            <button key={v} className={`filter-btn ${filter.type === v ? "active" : ""}`} onClick={() => setFilter(f => ({ ...f, type: v }))}>{l}</button>
          ))}
        </div>
        <div className="price-range" style={{ marginLeft: "auto", display: "flex", alignItems: "center", gap: "0.75rem" }}>
          <span style={{ fontSize: "0.65rem", letterSpacing: "0.15em", textTransform: "uppercase", color: "var(--muted)" }}>Max:</span>
          <input type="range" min="500" max="1000" step="50" value={filter.maxPrice} onChange={e => setFilter(f => ({ ...f, maxPrice: +e.target.value }))} />
          <span style={{ fontSize: "0.82rem", color: "var(--gold)", minWidth: "60px" }}>{filter.maxPrice} MAD</span>
        </div>
      </div>
      <div className="products-grid">
        {products.map(p => <ProductCard key={p.id} product={p} onAdd={addToCart} onOpen={setSelectedProduct} />)}
      </div>
      {products.length === 0 && (
        <div style={{ textAlign: "center", padding: "4rem", color: "var(--muted)" }}>
          <p>Aucun produit ne correspond à vos filtres.</p>
        </div>
      )}
    </section>
  );
}

// ─── PRODUCT CARD ─────────────────────────────────────────────────────────────
function ProductCard({ product, onAdd, onOpen }) {
  return (
    <div className="product-card" onClick={() => onOpen(product)}>
      <div className="product-img">
        <img src={product.img} alt={product.name} />
        <div className="product-overlay" />
        {product.badge && <div className="product-badge">{product.badge}</div>}
      </div>
      <div className="product-info">
        <div className="product-name">{product.name}</div>
        <div className="product-notes">{product.notes}</div>
        <div className="product-meta">
          <div className="product-price">{product.price} <span>MAD · {product.ml}</span></div>
          <div className="product-rating">
            <Icon name="star" size={12} />
            <span>{product.rating}</span>
            <span style={{ color: "var(--muted)" }}>({product.reviews})</span>
          </div>
        </div>
        <button className="product-add-btn" onClick={e => { e.stopPropagation(); onAdd(product); }}>
          Ajouter au panier
        </button>
      </div>
    </div>
  );
}

// ─── ABOUT PAGE ───────────────────────────────────────────────────────────────
function AboutPage() {
  return (
    <>
      <section style={{ paddingTop: "8rem", padding: "8rem 4rem 5rem", background: "var(--black)" }}>
        <div className="section-label">Notre univers</div>
        <h1 className="section-title" style={{ maxWidth: 600 }}>Née de la passion<br />pour <em>l'excellence</em></h1>
        <p style={{ color: "var(--muted)", fontSize: "0.9rem", lineHeight: "1.9", maxWidth: 560, marginTop: "1.5rem" }}>
          Maison Dorée est une maison de parfums marocaine fondée sur la conviction que le luxe authentique est accessible. Chaque fragrance est le fruit d'une recherche méticuleuse des plus belles matières premières du monde.
        </p>
      </section>
      <div style={{ height: "400px", overflow: "hidden", position: "relative" }}>
        <img src="https://images.unsplash.com/photo-1547887538-e3a2f32cb1cc?w=1400&q=80" alt="Atelier" style={{ width: "100%", height: "100%", objectFit: "cover", opacity: 0.5 }} />
        <div style={{ position: "absolute", inset: 0, background: "linear-gradient(to bottom, var(--black), transparent, var(--black))" }} />
      </div>
      <div className="about-section" style={{ background: "var(--black)" }}>
        <div className="section-label">Nos valeurs</div>
        <h2 className="section-title">Ce qui nous <em>définit</em></h2>
        <div className="values-grid">
          {[
            { icon: "sparkle", title: "Excellence", text: "Chaque flacon représente des heures de travail artisanal et un soin apporté aux moindres détails." },
            { icon: "shield", title: "Authenticité", text: "Nos formules sont originales, jamais copiées. Chaque fragrance porte une signature unique." },
            { icon: "star", title: "Héritage", text: "Nous puisons notre inspiration dans la riche tradition parfumière arabe et orientale." },
          ].map((v, i) => (
            <div key={i} className="value-card">
              <div className="value-icon"><Icon name={v.icon} size={28} /></div>
              <div className="value-title">{v.title}</div>
              <p className="value-text">{v.text}</p>
            </div>
          ))}
        </div>
      </div>
    </>
  );
}

// ─── CONTACT PAGE ─────────────────────────────────────────────────────────────
function ContactPage() {
  const [sent, setSent] = useState(false);
  return (
    <div style={{ paddingTop: "72px" }}>
      <section style={{ padding: "4rem 4rem 2rem" }}>
        <div className="section-label">Nous contacter</div>
        <h1 className="section-title">Parlons de <em>Parfums</em></h1>
      </section>
      <div className="contact-section">
        <div>
          {[
            { icon: "phone", label: "Téléphone", value: "+212 6 00 00 00 00" },
            { icon: "whatsapp", label: "WhatsApp", value: "+212 6 00 00 00 00" },
            { icon: "mail", label: "Email", value: "contact@maisondoree.ma" },
            { icon: "truck", label: "Livraison", value: "Toutes les villes du Maroc" },
          ].map((c, i) => (
            <div key={i} className="contact-info-item">
              <div className="contact-icon"><Icon name={c.icon} size={20} /></div>
              <div>
                <div className="contact-info-label">{c.label}</div>
                <div className="contact-info-value">{c.value}</div>
              </div>
            </div>
          ))}
          <div style={{ marginTop: "2rem", padding: "2rem", border: "1px solid var(--border)", background: "var(--charcoal)" }}>
            <div style={{ fontSize: "0.65rem", letterSpacing: "0.2em", textTransform: "uppercase", color: "var(--gold)", marginBottom: "1rem" }}>Horaires</div>
            <div style={{ display: "flex", flexDirection: "column", gap: "0.5rem", fontSize: "0.82rem", color: "var(--muted)" }}>
              <div style={{ display: "flex", justifyContent: "space-between" }}><span>Lun – Ven</span><span style={{ color: "var(--white)" }}>9h – 19h</span></div>
              <div style={{ display: "flex", justifyContent: "space-between" }}><span>Samedi</span><span style={{ color: "var(--white)" }}>10h – 17h</span></div>
              <div style={{ display: "flex", justifyContent: "space-between" }}><span>Dimanche</span><span>Fermé</span></div>
            </div>
          </div>
        </div>
        <div>
          {sent ? (
            <div style={{ padding: "3rem", border: "1px solid var(--gold)", textAlign: "center" }}>
              <div style={{ color: "var(--gold)", marginBottom: "1rem", display: "flex", justifyContent: "center" }}><Icon name="check" size={32} /></div>
              <p style={{ fontFamily: "var(--serif)", fontSize: "1.2rem" }}>Message envoyé</p>
              <p style={{ fontSize: "0.8rem", color: "var(--muted)", marginTop: "0.5rem" }}>Nous vous répondrons dans les 24h.</p>
            </div>
          ) : (
            <div style={{ display: "flex", flexDirection: "column", gap: "1rem" }}>
              {[["Nom complet", "text"], ["Email", "email"], ["Sujet", "text"]].map(([label, type]) => (
                <div key={label} className="form-group">
                  <label className="form-label">{label}</label>
                  <input className="form-input" type={type} placeholder={label} />
                </div>
              ))}
              <div className="form-group">
                <label className="form-label">Message</label>
                <textarea className="form-input" rows={5} placeholder="Votre message..." style={{ resize: "vertical", fontFamily: "var(--sans)" }} />
              </div>
              <button className="btn-gold" style={{ alignSelf: "flex-start", padding: "14px 40px" }} onClick={() => setSent(true)}>Envoyer le message</button>
            </div>
          )}
        </div>
      </div>
    </div>
  );
}

// ─── ADMIN PANEL ──────────────────────────────────────────────────────────────
function AdminPanel({ orders, setOrders, adminTab, setAdminTab }) {
  const [products, setProducts] = useState(PRODUCTS);
  const [newProduct, setNewProduct] = useState({ name: "", price: "", type: "unisex", notes: "" });

  const updateOrderStatus = (id, status) => setOrders(prev => prev.map(o => o.id === id ? { ...o, status } : o));

  const stats = [
    { label: "Commandes totales", val: orders.length },
    { label: "En attente", val: orders.filter(o => o.status === "pending").length },
    { label: "CA total (MAD)", val: orders.reduce((s, o) => s + o.total, 0) },
    { label: "Produits actifs", val: products.length },
  ];

  return (
    <div className="admin-panel" style={{ paddingTop: "90px" }}>
      <div className="admin-header">
        <h1 className="admin-title">Dashboard Admin</h1>
        <div style={{ fontSize: "0.72rem", color: "var(--muted)" }}>Maison Dorée · Panel de gestion</div>
      </div>

      {/* STATS */}
      <div style={{ display: "grid", gridTemplateColumns: "repeat(auto-fit, minmax(160px, 1fr))", gap: "1rem", marginBottom: "2rem" }}>
        {stats.map((s, i) => (
          <div key={i} style={{ background: "var(--mid)", padding: "1.25rem", border: "1px solid var(--border)" }}>
            <div style={{ fontFamily: "var(--serif)", fontSize: "1.8rem", color: "var(--gold)" }}>{s.val}</div>
            <div style={{ fontSize: "0.65rem", letterSpacing: "0.12em", textTransform: "uppercase", color: "var(--muted)", marginTop: "4px" }}>{s.label}</div>
          </div>
        ))}
      </div>

      <div className="admin-tabs">
        {[["products", "Produits"], ["orders", "Commandes"], ["promo", "Codes promo"], ["shipping", "Livraison"]].map(([v, l]) => (
          <button key={v} className={`admin-tab ${adminTab === v ? "active" : ""}`} onClick={() => setAdminTab(v)}>{l}</button>
        ))}
      </div>

      {adminTab === "products" && (
        <div>
          <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr 1fr 1fr auto", gap: "0.75rem", marginBottom: "1.5rem", background: "var(--mid)", padding: "1.25rem", border: "1px solid var(--border)" }}>
            <input className="form-input" placeholder="Nom du parfum" value={newProduct.name} onChange={e => setNewProduct(f => ({ ...f, name: e.target.value }))} />
            <input className="form-input" type="number" placeholder="Prix (MAD)" value={newProduct.price} onChange={e => setNewProduct(f => ({ ...f, price: e.target.value }))} />
            <input className="form-input" placeholder="Notes olfactives" value={newProduct.notes} onChange={e => setNewProduct(f => ({ ...f, notes: e.target.value }))} />
            <select className="form-select" value={newProduct.type} onChange={e => setNewProduct(f => ({ ...f, type: e.target.value }))}>
              <option value="unisex">Unisexe</option>
              <option value="men">Homme</option>
              <option value="women">Femme</option>
            </select>
            <button className="btn-gold" style={{ padding: "10px 20px", fontSize: "0.65rem" }} onClick={() => {
              if (!newProduct.name || !newProduct.price) return;
              setProducts(p => [...p, { ...newProduct, id: Date.now(), price: +newProduct.price, rating: 5.0, reviews: 0, badge: "New", ml: "100ml", img: PRODUCTS[0].img }]);
              setNewProduct({ name: "", price: "", type: "unisex", notes: "" });
            }}>+ Ajouter</button>
          </div>
          <table className="admin-table">
            <thead><tr><th>Produit</th><th>Type</th><th>Prix</th><th>Notes</th><th>Action</th></tr></thead>
            <tbody>
              {products.map(p => (
                <tr key={p.id}>
                  <td style={{ fontFamily: "var(--serif)" }}>{p.name}</td>
                  <td><span style={{ fontSize: "0.7rem", textTransform: "capitalize" }}>{p.type}</span></td>
                  <td style={{ color: "var(--gold)" }}>{p.price} MAD</td>
                  <td style={{ color: "var(--muted)", fontSize: "0.75rem" }}>{p.notes}</td>
                  <td><button style={{ background: "none", border: "none", color: "var(--muted)", cursor: "pointer" }} onClick={() => setProducts(prev => prev.filter(x => x.id !== p.id))}><Icon name="trash" size={14} /></button></td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      )}

      {adminTab === "orders" && (
        <table className="admin-table">
          <thead><tr><th>ID</th><th>Client</th><th>Ville</th><th>Articles</th><th>Total</th><th>Statut</th><th>Action</th></tr></thead>
          <tbody>
            {orders.map(o => (
              <tr key={o.id}>
                <td style={{ color: "var(--gold)", fontSize: "0.75rem" }}>{o.id}</td>
                <td>{o.customer}</td>
                <td style={{ color: "var(--muted)", fontSize: "0.78rem" }}>{o.city}</td>
                <td style={{ color: "var(--muted)", fontSize: "0.75rem" }}>{o.items.join(", ")}</td>
                <td style={{ color: "var(--gold)" }}>{o.total} MAD</td>
                <td><span className={`status-badge status-${o.status}`}>{o.status === "pending" ? "En attente" : o.status === "confirmed" ? "Confirmé" : "Livré"}</span></td>
                <td>
                  <select className="form-select" style={{ padding: "4px 8px", fontSize: "0.72rem", width: "auto" }} value={o.status} onChange={e => updateOrderStatus(o.id, e.target.value)}>
                    <option value="pending">En attente</option>
                    <option value="confirmed">Confirmé</option>
                    <option value="delivered">Livré</option>
                  </select>
                </td>
              </tr>
            ))}
          </tbody>
        </table>
      )}

      {adminTab === "promo" && (
        <div>
          <div style={{ marginBottom: "1.5rem", padding: "1rem", background: "rgba(201,168,76,0.08)", border: "1px solid rgba(201,168,76,0.2)", fontSize: "0.8rem", color: "var(--muted)" }}>
            Codes promo actifs : <strong style={{ color: "var(--gold)" }}>LUXURY20</strong> (20%), <strong style={{ color: "var(--gold)" }}>WELCOME10</strong> (10%), <strong style={{ color: "var(--gold)" }}>VIP30</strong> (30%)
          </div>
          <table className="admin-table">
            <thead><tr><th>Code</th><th>Remise</th><th>Statut</th></tr></thead>
            <tbody>
              {Object.entries(PROMO_CODES).map(([code, rate]) => (
                <tr key={code}>
                  <td style={{ fontFamily: "var(--serif)", fontSize: "1rem", letterSpacing: "0.1em" }}>{code}</td>
                  <td style={{ color: "var(--gold)" }}>{rate * 100}%</td>
                  <td><span className="status-badge status-confirmed">Actif</span></td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      )}

      {adminTab === "shipping" && (
        <table className="admin-table">
          <thead><tr><th>Ville</th><th>Frais de livraison</th></tr></thead>
          <tbody>
            {CITIES.map(c => (
              <tr key={c.name}>
                <td>{c.name}</td>
                <td style={{ color: "var(--gold)" }}>{c.fee} MAD</td>
              </tr>
            ))}
          </tbody>
        </table>
      )}
    </div>
  );
}

// ─── FOOTER ───────────────────────────────────────────────────────────────────
function Footer({ navigate }) {
  return (
    <footer className="footer">
      <div className="footer-grid">
        <div>
          <div className="footer-brand">MAISON DORÉE</div>
          <p className="footer-desc">Des fragrances d'exception pour les âmes exigeantes. Livraison dans tout le Maroc.</p>
        </div>
        <div>
          <div className="footer-col-title">Navigation</div>
          <ul className="footer-links">
            {[["Accueil", "home"], ["Boutique", "shop"], ["À propos", "about"], ["Contact", "contact"]].map(([l, p]) => (
              <li key={p}><a onClick={() => navigate(p)}>{l}</a></li>
            ))}
          </ul>
        </div>
        <div>
          <div className="footer-col-title">Informations</div>
          <ul className="footer-links">
            {["Livraison & Retours", "Politique de confidentialité", "Conditions d'utilisation", "FAQ"].map(l => (
              <li key={l}><a>{l}</a></li>
            ))}
          </ul>
        </div>
        <div>
          <div className="footer-col-title">Contact</div>
          <ul className="footer-links">
            <li><a>+212 6 00 00 00 00</a></li>
            <li><a>contact@maisondoree.ma</a></li>
            <li style={{ marginTop: "0.5rem", fontSize: "0.72rem", color: "var(--muted)" }}>Lun–Ven · 9h–19h</li>
          </ul>
          <div style={{ marginTop: "1.25rem" }}>
            <div className="footer-col-title" style={{ marginBottom: "0.75rem" }}>Paiement accepté</div>
            <div style={{ fontSize: "0.78rem", color: "var(--muted)" }}>💵 Cash à la livraison</div>
          </div>
        </div>
      </div>
      <div className="footer-bottom">
        <span>© 2026 <span>Maison Dorée</span>. Tous droits réservés.</span>
        <span>Fait avec passion au 🇲🇦 Maroc</span>
      </div>
    </footer>
  );
}
