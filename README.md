import React, { useState, useEffect } from "react";
export default function App() {
 const [books, setBooks] = useState(() => {
   const saved = localStorage.getItem("my-library-books");
   return saved ? JSON.parse(saved) : [];
 });
 const [search, setSearch] = useState("");
 const [form, setForm] = useState({
   title: "",
   author: "",
   genre: "",
   cover: "",
   status: "To Read",
   rating: 1,
   favorite: false,
 });
 useEffect(() => {
   localStorage.setItem(
     "my-library-books",
     JSON.stringify(books)
   );
 }, [books]);
 const addBook = () => {
   if (!form.title.trim() || !form.author.trim())
     return;
   setBooks([
     ...books,
     {
       id: Date.now(),
       ...form,
       dateAdded: new Date().toISOString(),
     },
   ]);
   setForm({
     title: "",
     author: "",
     genre: "",
     cover: "",
     status: "To Read",
     rating: 1,
     favorite: false,
   });
 };
 const deleteBook = (id) => {
   setBooks(books.filter((book) => book.id !== id));
 };
 const toggleFavorite = (id) => {
   setBooks(
     books.map((book) =>
       book.id === id
         ? {
             ...book,
             favorite: !book.favorite,
           }
         : book
     )
   );
 };
 const clearLibrary = () => {
   if (window.confirm("Clear entire library?")) {
     localStorage.removeItem("my-library-books");
     setBooks([]);
   }
 };
 const exportLibrary = () => {
   const blob = new Blob(
     [JSON.stringify(books, null, 2)],
     {
       type: "application/json",
     }
   );
   const url = URL.createObjectURL(blob);
   const a = document.createElement("a");
   a.href = url;
   a.download = "library-backup.json";
   a.click();
   URL.revokeObjectURL(url);
 };
 const importLibrary = (event) => {
   const file = event.target.files[0];
   if (!file) return;
   const reader = new FileReader();
   reader.onload = (e) => {
     try {
       const imported = JSON.parse(
         e.target.result
       );
       if (Array.isArray(imported)) {
         setBooks(imported);
       } else {
         alert(
           "Invalid library backup file."
         );
       }
     } catch {
       alert("Import failed.");
     }
   };
   reader.readAsText(file);
 };
 const filteredBooks = books.filter((book) =>
   `${book.title} ${book.author} ${book.genre} ${book.status}`
     .toLowerCase()
     .includes(search.toLowerCase())
 );
 const completed = books.filter(
   (b) => b.status === "Completed"
 ).length;
 const reading = books.filter(
   (b) => b.status === "Reading"
 ).length;
 const favorites = books.filter(
   (b) => b.favorite
 ).length;
 return (
   <div className="min-h-screen bg-gradient-to-br from-pink-100 via-purple-100 to-sky-100 p-8">
     <div className="max-w-7xl mx-auto">
       <div className="text-center mb-8">
         <h1 className="text-5xl font-bold text-slate-700">
           :books: My Personal Library
         </h1>
         <p className="text-slate-500 mt-2">
           Organize your reading journey
         </p>
       </div>
       {/* Stats */}
       <div className="grid md:grid-cols-4 gap-4 mb-8">
         <div className="bg-white/80 rounded-3xl p-6 shadow-lg border border-pink-200">
           <h3 className="text-slate-500">
             Total Books
           </h3>
           <p className="text-4xl font-bold text-slate-700">
             {books.length}
           </p>
         </div>
         <div className="bg-green-100 rounded-3xl p-6 shadow-lg border border-green-200">
           <h3 className="text-slate-600">
             Completed
           </h3>
           <p className="text-4xl font-bold text-green-700">
             {completed}
           </p>
         </div>
         <div className="bg-blue-100 rounded-3xl p-6 shadow-lg border border-blue-200">
           <h3 className="text-slate-600">
             Reading
           </h3>
           <p className="text-4xl font-bold text-blue-700">
             {reading}
           </p>
         </div>
         <div className="bg-rose-100 rounded-3xl p-6 shadow-lg border border-rose-200">
           <h3 className="text-slate-600">
             Favorites
           </h3>
           <p className="text-4xl font-bold text-rose-700">
             {favorites}
           </p>
         </div>
       </div>
       {/* Add Book Form */}
       <div className="bg-white/80 rounded-3xl p-6 shadow-lg border border-pink-200 mb-8">
         <h2 className="text-2xl font-semibold mb-4 text-slate-700">
           Add Book
         </h2>
         <div className="grid md:grid-cols-3 gap-4">
           <input
             type="text"
             placeholder="Book Title"
             value={form.title}
             onChange={(e) =>
               setForm({
                 ...form,
                 title: e.target.value,
               })
             }
             className="p-3 rounded-xl border border-pink-200 bg-pink-50"
           />
           <input
             type="text"
             placeholder="Author"
             value={form.author}
             onChange={(e) =>
               setForm({
                 ...form,
                 author: e.target.value,
               })
             }
             className="p-3 rounded-xl border border-pink-200 bg-pink-50"
           />
           <input
             type="text"
             placeholder="Genre"
             value={form.genre}
             onChange={(e) =>
               setForm({
                 ...form,
                 genre: e.target.value,
               })
             }
             className="p-3 rounded-xl border border-pink-200 bg-pink-50"
           />
           <input
             type="text"
             placeholder="Cover Image URL"
             value={form.cover}
             onChange={(e) =>
               setForm({
                 ...form,
                 cover: e.target.value,
               })
             }
             className="p-3 rounded-xl border border-pink-200 bg-pink-50 md:col-span-2"
           />
           <select
             value={form.status}
             onChange={(e) =>
               setForm({
                 ...form,
                 status: e.target.value,
               })
             }
             className="p-3 rounded-xl border border-pink-200 bg-pink-50"
           >
             <option>To Read</option>
             <option>Reading</option>
             <option>Completed</option>
           </select>
           <select
             value={form.rating}
             onChange={(e) =>
               setForm({
                 ...form,
                 rating: Number(
                   e.target.value
                 ),
               })
             }
             className="p-3 rounded-xl border border-pink-200 bg-pink-50"
           >
             {[1,2,3,4,5].map((n) => (
               <option
                 key={n}
                 value={n}
               >
                 {":star:".repeat(n)}
               </option>
             ))}
           </select>
           <button
             onClick={addBook}
             className="bg-pink-400 hover:bg-pink-500 text-white rounded-xl p-3 shadow-md"
           >
             Add Book
           </button>
         </div>
       </div>
       {/* Controls */}
       <div className="flex flex-wrap gap-3 mb-8">
         <input
           type="text"
           placeholder="Search books..."
           value={search}
           onChange={(e) =>
             setSearch(e.target.value)
           }
           className="flex-1 min-w-[250px] p-3 rounded-xl bg-white border border-pink-200 shadow-sm"
         />
         <button
           onClick={exportLibrary}
           className="bg-emerald-300 hover:bg-emerald-400 text-white px-4 py-2 rounded-xl shadow-md"
         >
           Export JSON
         </button>
         <label className="bg-sky-300 hover:bg-sky-400 text-white px-4 py-2 rounded-xl shadow-md cursor-pointer">
           Import JSON
           <input
             type="file"
             accept=".json"
             hidden
             onChange={importLibrary}
           />
         </label>
         <button
           onClick={clearLibrary}
           className="bg-rose-300 hover:bg-rose-400 text-white px-4 py-2 rounded-xl shadow-md"
         >
           Clear Library
         </button>
       </div>
       {/* Books Grid */}
       <div className="grid lg:grid-cols-2 gap-6">
         {filteredBooks.map((book) => (
           <div
             key={book.id}
             className="bg-white/80 rounded-3xl overflow-hidden shadow-lg border border-pink-100 hover:-translate-y-1 transition-all duration-300"
           >
             <div className="flex">
               {
               <div className="p-5 flex-1">
                 <div className="flex justify-between">
                   <h2 className="text-xl font-bold text-slate-700">
                     {book.title}
                   </h2>
                   <button
                     onClick={() =>
                       toggleFavorite(
                         book.id
                       )
                     }
                     className="text-2xl"
                   >
                     {book.favorite
                       ? ":heart:"
                       : ":white_heart:"}
                   </button>
                 </div>
                 <p className="text-slate-500">
                   {book.author}
                 </p>
                 <div className="flex flex-wrap gap-2 mt-3">
                   <span className="bg-pink-100 text-pink-700 px-3 py-1 rounded-full text-sm">
                     {book.genre}
                   </span>
                   <span className="bg-purple-100 text-purple-700 px-3 py-1 rounded-full text-sm">
                     {book.status}
                   </span>
                 </div>
                 <div className="mt-3 text-yellow-500 text-lg">
                   {":star:".repeat(book.rating)}
                 </div>
                 <button
                   onClick={() =>
                     deleteBook(book.id)
                   }
                   className="mt-4 bg-rose-300 hover:bg-rose-400 text-white px-4 py-2 rounded-xl"
                 >
                   Delete
                 </button>
               </div>
             </div>
           </div>
         ))}
       </div>
       {filteredBooks.length === 0 && (
         <div className="text-center py-20 text-slate-500">
           No books found :books:
         </div>
       )}
     </div>
   </div>
 );
}
