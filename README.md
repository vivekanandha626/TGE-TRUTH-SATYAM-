# TGE-TRUTH-SATYAM-/ pages/_app.js - Include THE TRUTH (SATYAM) and Satyam Publication Logos import '../styles/globals.css'; import Image from 'next/image'; import Link from 'next/link';

function MyApp({ Component, pageProps }) { return ( <div> <header className="bg-gray-900 text-white p-4 flex items-center justify-between"> <Link href="/"> <Image src="/logo.png" alt="THE TRUTH (SATYAM) Logo" width={150} height={50} /> </Link> <h1 className="text-xl font-bold">THE TRUTH (SATYAM)</h1> <Image src="/satyam-publication-logo.png" alt="Satyam Publication Logo" width={80} height={40} className="ml-auto" /> </header> <Component {...pageProps} /> </div> ); }

export default MyApp;

// Place the logo images in the public directory as /public/logo.png and /public/satyam-publication-logo.png

// pages/index.js - THE TRUTH (SATYAM) Home Page with Logos import { useEffect, useState } from 'react'; import Link from 'next/link'; import Image from 'next/image';

export default function Home() { const [articles, setArticles] = useState([]); const [searchQuery, setSearchQuery] = useState('');

useEffect(() => { fetch('http://localhost:5000/api/articles') .then(res => res.json()) .then(data => setArticles(data)); }, []);

const handleSearch = async () => { const res = await fetch(http://localhost:5000/api/articles/search?query=${searchQuery}); const data = await res.json(); setArticles(data); };

return ( <div className="container mx-auto p-4"> <div className="flex justify-between items-center mb-4"> <Image src="/logo.png" alt="THE TRUTH (SATYAM) Logo" width={200} height={80} /> <Image src="/satyam-publication-logo.png" alt="Satyam Publication Logo" width={100} height={50} /> </div> <h1 className="text-3xl font-bold mb-4 text-center">THE TRUTH (SATYAM) - Latest News</h1> <input type="text" className="border p-2 w-full mb-2" placeholder="Search articles..." value={searchQuery} onChange={(e) => setSearchQuery(e.target.value)} /> <button className="bg-blue-500 text-white p-2 mb-4" onClick={handleSearch}>Search</button> {articles.map(article => ( <div key={article._id} className="border p-4 mb-4"> <h2 className="text-2xl font-semibold">{article.title}</h2> <p>{article.content.substring(0, 100)}...</p> <Link href={/article/${article._id}} className="text-blue-500">Read More</Link> </div> ))} </div> ); }
