// server/index.js - THE TRUTH (SATYAM) Backend with Authentication, Admin-Only Editing & Search const express = require('express'); const mongoose = require('mongoose'); const cors = require('cors'); const bcrypt = require('bcryptjs'); const jwt = require('jsonwebtoken'); const Article = require('./models/Article'); const User = require('./models/User');

const app = express(); app.use(express.json()); app.use(cors());

mongoose.connect('mongodb://localhost:27017/the_truth_satyam', { useNewUrlParser: true, useUnifiedTopology: true, });

const SECRET_KEY = 'your_secret_key';

// User Login app.post('/api/login', async (req, res) => { const { username, password } = req.body; const user = await User.findOne({ username }); if (!user) return res.status(400).json({ message: 'User not found' });

const isMatch = await bcrypt.compare(password, user.password); if (!isMatch) return res.status(400).json({ message: 'Invalid credentials' });

const token = jwt.sign({ id: user._id, role: user.role }, SECRET_KEY, { expiresIn: '1h' }); res.json({ token, role: user.role, message: 'Login successful' }); });

// Middleware for authentication const authMiddleware = (req, res, next) => { const token = req.headers['authorization']; if (!token) return res.status(401).json({ message: 'Unauthorized' }); try { const decoded = jwt.verify(token.split(' ')[1], SECRET_KEY); req.user = decoded; next(); } catch (err) { res.status(401).json({ message: 'Invalid token' }); } };

// Admin Middleware const adminMiddleware = (req, res, next) => { if (req.user.role !== 'admin') return res.status(403).json({ message: 'Access denied' }); next(); };

// Get all articles (Public access - View only) app.get('/api/articles', async (req, res) => { const articles = await Article.find(); res.json(articles); });

// Search Articles (Public access - View only) app.get('/api/articles/search', async (req, res) => { const { query } = req.query; const articles = await Article.find({ title: new RegExp(query, 'i') }); res.json(articles); });

// Add new article (Admin only) app.post('/api/articles', authMiddleware, adminMiddleware, async (req, res) => { const newArticle = new Article(req.body); await newArticle.save(); res.json(newArticle); });

// Update an article (Admin only) app.put('/api/articles/:id', authMiddleware, adminMiddleware, async (req, res) => { const updatedArticle = await Article.findByIdAndUpdate(req.params.id, req.body, { new: true }); res.json(updatedArticle); });

// Delete an article (Admin only) app.delete('/api/articles/:id', authMiddleware, adminMiddleware, async (req, res) => { await Article.findByIdAndDelete(req.params.id); res.json({ message: 'Article deleted' }); });

app.listen(5000, () => console.log('THE TRUTH (SATYAM) running on port 5000'));

// pages/index.js - THE TRUTH (SATYAM) Home Page (View Only) import { useEffect, useState } from 'react'; import Link from 'next/link'; import Image from 'next/image';

export default function Home() { const [articles, setArticles] = useState([]); const [searchQuery, setSearchQuery] = useState('');

useEffect(() => { fetch('http://localhost:5000/api/articles') .then(res => res.json()) .then(data => setArticles(data)); }, []);

const handleSearch = async () => { const res = await fetch(http://localhost:5000/api/articles/search?query=${searchQuery}); const data = await res.json(); setArticles(data); };

return ( <div className="container mx-auto p-4"> <div className="flex justify-between items-center mb-4"> <Image src="/logo.png" alt="THE TRUTH (SATYAM) Logo" width={200} height={80} /> <Image src="/satyam-publication-logo.png" alt="Satyam Publication Logo" width={100} height={50} /> </div> <h1 className="text-3xl font-bold mb-4 text-center">THE TRUTH (SATYAM) - Latest News</h1> <input type="text" className="border p-2 w-full mb-2" placeholder="Search articles..." value={searchQuery} onChange={(e) => setSearchQuery(e.target.value)} /> <button className="bg-blue-500 text-white p-2 mb-4" onClick={handleSearch}>Search</button> {articles.map(article => ( <div key={article._id} className="border p-4 mb-4"> <h2 className="text-2xl font-semibold">{article.title}</h2> <p>{article.content.substring(0, 100)}...</p> <Link href={/article/${article._id}} className="text-blue-500">Read More</Link> </div> ))} </div> ); }
