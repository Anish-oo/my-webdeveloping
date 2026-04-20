import { useState, useEffect, useRef } from "react";
import {
  Home, Search, Library, Play, Pause, SkipBack, SkipForward,
  Shuffle, Repeat, Heart, ChevronDown, ChevronLeft, Plus, X,
  Volume2, Music2, MoreHorizontal, Disc3, Radio, Clock, ListMusic
} from "lucide-react";

// ──────────────────────────────────────────────
// DESIGN TOKENS
// ──────────────────────────────────────────────
const C = {
  bg:       "#060610",
  surf:     "#0E0E1C",
  surf2:    "#16162A",
  surf3:    "#1F1F38",
  border:   "rgba(255,255,255,0.07)",
  accent:   "#FF4D00",
  accentSoft:"rgba(255,77,0,0.22)",
  gold:     "#FFB400",
  text:     "#F0F0F8",
  text2:    "#8888AA",
  text3:    "#55556A",
  success:  "#30D158",
};

const G = [
  ["#FF416C","#FF4B2B"], ["#4776E6","#8E54E9"], ["#11998e","#38ef7d"],
  ["#F7971E","#FFD200"], ["#c94b4b","#4b134f"], ["#8360c3","#2ebf91"],
  ["#FC4A1A","#F7B733"], ["#f953c6","#b91d73"], ["#0F2027","#4286f4"],
  ["#ee0979","#ff6a00"], ["#7F00FF","#E100FF"], ["#373B44","#4286f4"],
  ["#30cfd0","#330867"], ["#a18cd1","#fbc2eb"], ["#fd7543","#f97316"],
  ["#16213e","#e94560"],
];

// ──────────────────────────────────────────────
// MOCK DATA
// ──────────────────────────────────────────────
const SONGS = [
  // 1980s — Ilaiyaraaja
  {id:1,  title:"Ninaithale Inikkum",       artist:"Ilaiyaraaja",         album:"Ninaithale Inikkum",    year:1980,dur:258,mood:"love",   g:0},
  {id:2,  title:"Poove Sempoove",            artist:"Ilaiyaraaja",         album:"Ninaithale Inikkum",    year:1980,dur:243,mood:"love",   g:0},
  {id:3,  title:"Roja Kaadhal",              artist:"Ilaiyaraaja",         album:"Nool Veli",             year:1981,dur:267,mood:"love",   g:3},
  {id:4,  title:"Thenral Vanthu Theendum",   artist:"Ilaiyaraaja",         album:"Alaigal Oivathillai",   year:1981,dur:276,mood:"melody", g:2},
  {id:5,  title:"Ilayavana Chombu",          artist:"Ilaiyaraaja",         album:"Uyarntha Ullam",        year:1983,dur:231,mood:"melody", g:1},
  {id:6,  title:"Mundhanai Mudichu",         artist:"Ilaiyaraaja",         album:"Mundhanai Mudichu",     year:1983,dur:252,mood:"kuthu",  g:9},
  {id:7,  title:"Ennavale Adi Ennavale",     artist:"Ilaiyaraaja",         album:"Muthal Mariyathai",     year:1985,dur:285,mood:"love",   g:6},
  {id:8,  title:"Vellai Pookal",             artist:"Ilaiyaraaja",         album:"Ilamai Oonjal Aadukirathu",year:1986,dur:234,mood:"melody",g:2},
  {id:9,  title:"Kaadhal Oviyam",            artist:"Ilaiyaraaja",         album:"Ninaithale Inikkum",    year:1980,dur:258,mood:"love",   g:3},
  {id:10, title:"Nenje Ezhu",                artist:"Ilaiyaraaja",         album:"Ilamai Oonjal Aadukirathu",year:1986,dur:210,mood:"sad",  g:11},
  // 1990s — AR Rahman & Ilaiyaraaja
  {id:11, title:"Keladi Kanmani",            artist:"Ilaiyaraaja",         album:"Keladi Kanmani",        year:1990,dur:243,mood:"love",   g:5},
  {id:12, title:"Chinna Chinna Aasai",       artist:"A.R. Rahman",         album:"Roja",                  year:1992,dur:267,mood:"melody", g:1},
  {id:13, title:"Kaadhal Rojave",            artist:"A.R. Rahman",         album:"Roja",                  year:1992,dur:285,mood:"love",   g:6},
  {id:14, title:"Chikku Bukku Rayile",       artist:"A.R. Rahman",         album:"Gentleman",             year:1993,dur:231,mood:"kuthu",  g:9},
  {id:15, title:"Mukkala Mukkabla",          artist:"A.R. Rahman",         album:"Kadhalan",              year:1994,dur:285,mood:"kuthu",  g:10},
  {id:16, title:"Urvasi Urvasi",             artist:"A.R. Rahman",         album:"Kadhalan",              year:1994,dur:267,mood:"kuthu",  g:4},
  {id:17, title:"Uyire Uyire",               artist:"A.R. Rahman",         album:"Bombay",                year:1995,dur:297,mood:"love",   g:5},
  {id:18, title:"Kannalanae",                artist:"A.R. Rahman",         album:"Bombay",                year:1995,dur:264,mood:"melody", g:2},
  {id:19, title:"Humma Humma",               artist:"A.R. Rahman",         album:"Bombay",                year:1995,dur:315,mood:"kuthu",  g:0},
  {id:20, title:"Thillana Thillana",         artist:"A.R. Rahman",         album:"Muthu",                 year:1995,dur:252,mood:"kuthu",  g:3},
  {id:21, title:"Poovukku Poovukkum",        artist:"A.R. Rahman",         album:"Jeans",                 year:1998,dur:279,mood:"love",   g:7},
  {id:22, title:"Snehithane Snehithane",     artist:"A.R. Rahman",         album:"Alaipayuthey",          year:2000,dur:312,mood:"love",   g:8},
  {id:23, title:"Ennai Konjam Maattu",       artist:"A.R. Rahman",         album:"Alaipayuthey",          year:2000,dur:285,mood:"melody", g:1},
  {id:24, title:"Kanne Kanne",               artist:"A.R. Rahman",         album:"Alaipayuthey",          year:2000,dur:261,mood:"love",   g:6},
  // 2000s — Harris, Yuvan, Vidyasagar
  {id:25, title:"Oru Maalai",                artist:"Harris Jayaraj",      album:"Ghajini",               year:2005,dur:297,mood:"sad",    g:11},
  {id:26, title:"Mundhinam Parthene",        artist:"Harris Jayaraj",      album:"Vaaranam Aayiram",      year:2008,dur:312,mood:"love",   g:2},
  {id:27, title:"Ennamo Nadakkudhu",         artist:"Harris Jayaraj",      album:"Ghajini",               year:2005,dur:258,mood:"melody", g:5},
  {id:28, title:"Kannamoochi Yenada",        artist:"Yuvan Shankar Raja",  album:"7G Rainbow Colony",     year:2004,dur:285,mood:"melody", g:13},
  {id:29, title:"Venmathi Venmathi",         artist:"Yuvan Shankar Raja",  album:"Vallavan",              year:2006,dur:279,mood:"love",   g:7},
  {id:30, title:"Appadi Podu",               artist:"Vidyasagar",          album:"Ghilli",                year:2004,dur:264,mood:"kuthu",  g:9},
  {id:31, title:"Unakkum Enakkum",           artist:"Bharadwaj",           album:"Boys",                  year:2003,dur:315,mood:"love",   g:3},
  {id:32, title:"Sindhanai Sei",             artist:"Yuvan Shankar Raja",  album:"Paruthiveeran",         year:2007,dur:285,mood:"melody", g:4},
  {id:33, title:"Nee Partha Vizhigal",       artist:"Harris Jayaraj",      album:"Ayan",                  year:2009,dur:276,mood:"love",   g:1},
  {id:34, title:"Kannamoochi",               artist:"Yuvan Shankar Raja",  album:"Ayan",                  year:2009,dur:252,mood:"love",   g:12},
  // 2010s — Anirudh, GV Prakash, D. Imman
  {id:35, title:"Why This Kolaveri Di",      artist:"Anirudh Ravichander", album:"3",                     year:2012,dur:234,mood:"kuthu",  g:0},
  {id:36, title:"Kannazhaga",                artist:"Anirudh Ravichander", album:"3",                     year:2012,dur:258,mood:"love",   g:8},
  {id:37, title:"Nenjukulle",                artist:"A.R. Rahman",         album:"Kadal",                 year:2013,dur:312,mood:"love",   g:6},
  {id:38, title:"Selfie Pulla",              artist:"Anirudh Ravichander", album:"Kaththi",               year:2014,dur:243,mood:"kuthu",  g:10},
  {id:39, title:"Aaluma Doluma",             artist:"Anirudh Ravichander", album:"Vedalam",               year:2015,dur:258,mood:"kuthu",  g:9},
  {id:40, title:"Vera Maari",                artist:"Yuvan Shankar Raja",  album:"Masss",                 year:2015,dur:231,mood:"kuthu",  g:4},
  {id:41, title:"Maruvaarthai Pesathe",      artist:"D. Imman",            album:"Enakku Innoru Per Irukku",year:2016,dur:261,mood:"sad",  g:11},
  {id:42, title:"Merkku Thodarchi Malai",    artist:"GV Prakash Kumar",    album:"Merku Thodarchi Malai", year:2018,dur:276,mood:"melody", g:2},
  {id:43, title:"Kaathalae Kaathalae",       artist:"Govind Vasantha",     album:"96",                    year:2018,dur:285,mood:"sad",    g:5},
  {id:44, title:"Poo Vilangiyadhu",          artist:"Govind Vasantha",     album:"96",                    year:2018,dur:297,mood:"melody", g:13},
  {id:45, title:"Singapenne",                artist:"A.R. Rahman",         album:"Bigil",                 year:2019,dur:243,mood:"kuthu",  g:0},
  {id:46, title:"Theemai Dhaan Vellum",      artist:"Anirudh Ravichander", album:"Kaappaan",              year:2019,dur:243,mood:"melody", g:7},
  // 2020s
  {id:47, title:"Vaathi Coming",             artist:"Anirudh Ravichander", album:"Master",                year:2021,dur:231,mood:"kuthu",  g:3},
  {id:48, title:"Master The Blaster",        artist:"Anirudh Ravichander", album:"Master",                year:2021,dur:258,mood:"kuthu",  g:9},
  {id:49, title:"Arabic Kuthu",              artist:"Anirudh Ravichander", album:"Beast",                 year:2022,dur:243,mood:"kuthu",  g:10},
  {id:50, title:"Ponni Nadhi",               artist:"A.R. Rahman",         album:"Ponniyin Selvan",       year:2022,dur:285,mood:"melody", g:1},
  {id:51, title:"Kannaara Kannaara",         artist:"A.R. Rahman",         album:"Ponniyin Selvan",       year:2022,dur:297,mood:"melody", g:6},
  {id:52, title:"Naa Ready",                 artist:"Anirudh Ravichander", album:"Leo",                   year:2023,dur:231,mood:"kuthu",  g:0},
  {id:53, title:"Anbe Anbe",                 artist:"Anirudh Ravichander", album:"Leo",                   year:2023,dur:258,mood:"love",   g:8},
  {id:54, title:"Jigidi Killaadi",           artist:"Anirudh Ravichander", album:"Leo",                   year:2023,dur:243,mood:"kuthu",  g:4},
  {id:55, title:"Whistle Podu",              artist:"Anirudh Ravichander", album:"GOAT",                  year:2024,dur:255,mood:"kuthu",  g:14},
  {id:56, title:"Manasantha Nuvve",          artist:"A.R. Rahman",         album:"PS-2",                  year:2024,dur:270,mood:"melody", g:5},
  {id:57, title:"Kanave Kanave",             artist:"GV Prakash Kumar",    album:"David",                 year:2013,dur:279,mood:"sad",    g:11},
  {id:58, title:"Iravukku Aayiram Kangal",   artist:"D. Imman",            album:"Iravukku Aayiram Kangal",year:2018,dur:261,mood:"melody",g:15},
  {id:59, title:"Nakka Mukka",               artist:"Yuvan Shankar Raja",  album:"Kadhalil Sodhappuvadhu",year:2010,dur:252,mood:"kuthu",  g:9},
  {id:60, title:"Saahasam",                  artist:"Anirudh Ravichander", album:"Manithan",              year:2016,dur:258,mood:"melody", g:7},
];

const ARTISTS = [
  {id:1, name:"Ilaiyaraaja",        bio:"The Maestro — Pioneer of Tamil film music since 1976",  color:"#F7971E"},
  {id:2, name:"A.R. Rahman",        bio:"Mozart of Madras — Global icon & Oscar winner",          color:"#4776E6"},
  {id:3, name:"Harris Jayaraj",     bio:"King of intense BGM and soulful melodies",               color:"#11998e"},
  {id:4, name:"Yuvan Shankar Raja", bio:"Youthful energy meets deep melody",                       color:"#ee0979"},
  {id:5, name:"Anirudh Ravichander",bio:"The millennial maestro redefining Tamil pop",            color:"#8360c3"},
  {id:6, name:"GV Prakash Kumar",   bio:"Versatile composer with folk & modern touch",             color:"#FC4A1A"},
  {id:7, name:"D. Imman",           bio:"Master of devotional and emotional melodies",             color:"#373B44"},
  {id:8, name:"Govind Vasantha",    bio:"Indie sensation — the genius behind 96",                 color:"#11998e"},
  {id:9, name:"Vidyasagar",         bio:"Commercial blockbuster specialist",                       color:"#c94b4b"},
];

const PLAYLISTS = [
  {id:1,  name:"Ilaiyaraaja Classics",  desc:"Timeless melodies from The Maestro",        songs:[1,2,3,4,5,6,7,8,9,10,11], g:3},
  {id:2,  name:"AR Rahman Gold",        desc:"Greatest hits from Mozart of Madras",       songs:[12,13,14,15,16,17,18,19,20,21,22,23,24,37,45,50,51,56], g:1},
  {id:3,  name:"80s Melodies",          desc:"Evergreen songs from the golden decade",    songs:[1,2,3,4,5,6,7,8,9,10], g:0},
  {id:4,  name:"90s Hits",              desc:"The unforgettable 90s Tamil music",         songs:[11,12,13,14,15,16,17,18,19,20,21,22,23,24], g:6},
  {id:5,  name:"2000s Favourites",      desc:"Best of 2000s Tamil cinema",                songs:[25,26,27,28,29,30,31,32,33,34], g:5},
  {id:6,  name:"Anirudh Hits",          desc:"The new-age sensation's best tracks",       songs:[35,36,38,39,46,47,48,49,52,53,54,55,60], g:10},
  {id:7,  name:"🔥 Kuthu Vibes",        desc:"Dance-floor ready Tamil bangers",           songs:[6,14,15,16,19,20,30,35,38,39,40,45,47,48,49,52,54,55,59], g:9},
  {id:8,  name:"❤️ Love Melodies",      desc:"Romantic songs for every mood",             songs:[1,2,3,7,9,11,13,17,18,21,22,24,26,29,31,33,36,43,53], g:7},
  {id:9,  name:"💙 Sad Songs",          desc:"For bittersweet, nostalgic moments",        songs:[10,25,41,43,57], g:11},
  {id:10, name:"2020s Fresh",           desc:"Latest Tamil hits 2020–2026",               songs:[47,48,49,50,51,52,53,54,55,56], g:14},
];

// ──────────────────────────────────────────────
// HELPERS
// ──────────────────────────────────────────────
const fmt = (s) => `${Math.floor(s/60)}:${String(Math.floor(s%60)).padStart(2,"0")}`;

function Art({ g, size, r }) {
  const [c1, c2] = G[g % G.length];
  return (
    <div style={{
      width: size, height: size, borderRadius: r || 12, flexShrink: 0,
      background: `linear-gradient(145deg, ${c1}, ${c2})`,
      display:"flex", alignItems:"center", justifyContent:"center",
      boxShadow: `0 4px 20px ${c1}44`,
    }}>
      <Music2 size={size * 0.34} color="rgba(255,255,255,0.65)" />
    </div>
  );
}

// ──────────────────────────────────────────────
// MAIN APP
// ──────────────────────────────────────────────
export default function App() {
  const [tab,         setTab]         = useState("home");
  const [song,        setSong]        = useState(SONGS[0]);
  const [playing,     setPlaying]     = useState(false);
  const [progress,    setProgress]    = useState(0);
  const [shuffle,     setShuffle]     = useState(false);
  const [repeat,      setRepeat]      = useState(0);     // 0 off, 1 all, 2 one
  const [liked,       setLiked]       = useState(new Set());
  const [fullPlayer,  setFullPlayer]  = useState(false);
  const [searchQ,     setSearchQ]     = useState("");
  const [viewArtist,  setViewArtist]  = useState(null);
  const [viewPL,      setViewPL]      = useState(null);
  const [myPLs,       setMyPLs]       = useState([]);
  const [createPL,    setCreatePL]    = useState(false);
  const [plName,      setPlName]      = useState("");
  const [vol,         setVol]         = useState(80);
  const advancingRef  = useRef(false);

  // Inject Google Fonts
  useEffect(() => {
    const el = document.createElement("link");
    el.rel = "stylesheet";
    el.href = "https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;500;600;700;800;900&family=DM+Serif+Display:ital@0;1&display=swap";
    document.head.appendChild(el);
    return () => el.remove();
  }, []);

  // Progress timer
  useEffect(() => {
    if (!playing) return;
    const id = setInterval(() => {
      setProgress(p => Math.min(p + (100 / song.dur) * 0.5, 100));
    }, 500);
    return () => clearInterval(id);
  }, [playing, song.id]);

  // Auto-advance
  useEffect(() => {
    if (progress >= 100 && !advancingRef.current) {
      advancingRef.current = true;
      setTimeout(() => {
        if (repeat === 2) {
          setProgress(0);
        } else {
          nextSong(song, shuffle, repeat);
        }
        advancingRef.current = false;
      }, 400);
    }
  }, [progress]);

  function nextSong(cur, sh, rep) {
    const idx = SONGS.findIndex(s => s.id === cur.id);
    let next;
    if (sh) next = SONGS[Math.floor(Math.random() * SONGS.length)];
    else     next = SONGS[(idx + 1) % SONGS.length];
    setSong(next); setProgress(0);
  }

  function prevSong(cur) {
    if (progress > 15) { setProgress(0); return; }
    const idx = SONGS.findIndex(s => s.id === cur.id);
    setSong(SONGS[(idx - 1 + SONGS.length) % SONGS.length]);
    setProgress(0);
  }

  const play  = (s)  => { setSong(s); setProgress(0); setPlaying(true); };
  const heart = (id) => setLiked(p => { const n=new Set(p); n.has(id)?n.delete(id):n.add(id); return n; });

  // ── SHARED SONG ROW ─────────────────────────
  function SongRow({ s, idx, showNum }) {
    const active = song.id === s.id;
    return (
      <div onClick={() => play(s)} style={{
        display:"flex", alignItems:"center", gap:12, padding:"9px 0",
        borderBottom:`1px solid ${C.border}`, cursor:"pointer",
        background: active ? `${C.accentSoft}` : "transparent",
        borderRadius: active ? 10 : 0, paddingLeft: active ? 8 : 0, paddingRight: active ? 8 : 0,
        transition:"all .2s",
      }}>
        {showNum && (
          <span style={{color: active ? C.accent : C.text3, width:22, textAlign:"center", fontSize:12, flexShrink:0}}>
            {active && playing ? "♬" : idx+1}
          </span>
        )}
        <Art g={s.g} size={46} r={8} />
        <div style={{flex:1, minWidth:0}}>
          <div style={{color: active ? C.accent : C.text, fontWeight:600, fontSize:14,
            overflow:"hidden", textOverflow:"ellipsis", whiteSpace:"nowrap"}}>{s.title}</div>
          <div style={{color:C.text2, fontSize:11.5, marginTop:2}}>{s.artist} · {s.year}</div>
        </div>
        <div style={{display:"flex", alignItems:"center", gap:6}}>
          <span style={{color:C.text3, fontSize:11}}>{fmt(s.dur)}</span>
          <div onClick={e=>{e.stopPropagation();heart(s.id)}} style={{cursor:"pointer",padding:4}}>
            <Heart size={15} color={liked.has(s.id)?C.accent:C.text3}
              fill={liked.has(s.id)?C.accent:"none"} />
          </div>
        </div>
      </div>
    );
  }

  // ── HOME ────────────────────────────────────
  function HomeScreen() {
    const eras = [
      {label:"80s Classics",      color:"#F7971E", songs:SONGS.filter(s=>s.year<1990)},
      {label:"90s Hits",          color:"#4776E6", songs:SONGS.filter(s=>s.year>=1990&&s.year<2000)},
      {label:"2000s Favourites",  color:"#11998e", songs:SONGS.filter(s=>s.year>=2000&&s.year<2010)},
      {label:"2010–2026 Trending",color:"#ee0979", songs:SONGS.filter(s=>s.year>=2010)},
    ];
    return (
      <div style={{padding:"0 16px 24px"}}>
        {/* HEADER */}
        <div style={{paddingTop:24, marginBottom:28}}>
          <p style={{color:C.text2, fontSize:13, margin:0, letterSpacing:1}}>🎵 வணக்கம் (Vanakkam)</p>
          <h1 style={{margin:"6px 0 0", fontSize:30, fontWeight:900, fontFamily:"'Outfit',sans-serif", lineHeight:1}}>
            <span style={{color:C.text}}>Tamil </span>
            <span style={{background:`linear-gradient(90deg,${C.accent},${C.gold})`,
              WebkitBackgroundClip:"text", WebkitTextFillColor:"transparent"}}>Tunes</span>
          </h1>
          <p style={{color:C.text2, fontSize:12, margin:"6px 0 0"}}>1980 – April 2026 · Ad-free · {SONGS.length} songs</p>
        </div>

        {/* FEATURED PLAYLISTS */}
        <Section label="Featured Playlists" right={<SmBtn onClick={()=>setTab("library")}>See all</SmBtn>}>
          <ScrollRow>
            {PLAYLISTS.slice(0,6).map(pl=>(
              <div key={pl.id} onClick={()=>{setViewPL(pl);setTab("playlist")}}
                style={{flexShrink:0, width:148, cursor:"pointer"}}>
                <div style={{
                  width:148, height:148, borderRadius:18,
                  background:`linear-gradient(145deg,${G[pl.g][0]},${G[pl.g][1]})`,
                  display:"flex", flexDirection:"column", justifyContent:"flex-end",
                  padding:12, boxShadow:`0 10px 30px ${G[pl.g][0]}55`, marginBottom:8,
                }}>
                  <div style={{color:"rgba(255,255,255,0.95)", fontSize:13, fontWeight:700, lineHeight:1.3,
                    textShadow:"0 1px 6px rgba(0,0,0,0.6)"}}>{pl.name}</div>
                </div>
                <div style={{color:C.text2, fontSize:11}}>{pl.songs.length} songs</div>
              </div>
            ))}
          </ScrollRow>
        </Section>

        {/* ERA SECTIONS */}
        {eras.map(era=>(
          <Section key={era.label} label={era.label} labelColor={era.color}>
            <ScrollRow>
              {era.songs.slice(0,10).map(s=>(
                <div key={s.id} onClick={()=>play(s)} style={{flexShrink:0, width:108, cursor:"pointer"}}>
                  <div style={{
                    width:108, height:108, borderRadius:14,
                    background:`linear-gradient(145deg,${G[s.g][0]},${G[s.g][1]})`,
                    display:"flex", alignItems:"center", justifyContent:"center",
                    marginBottom:7,
                    border: song.id===s.id ? `2.5px solid ${era.color}` : "2.5px solid transparent",
                    boxShadow: song.id===s.id ? `0 0 16px ${era.color}88` : "none",
                    transition:"all .2s",
                  }}>
                    <Music2 size={34} color="rgba(255,255,255,0.72)" />
                  </div>
                  <div style={{color:C.text, fontSize:11.5, fontWeight:600,
                    overflow:"hidden", textOverflow:"ellipsis", whiteSpace:"nowrap"}}>{s.title}</div>
                  <div style={{color:C.text2, fontSize:10.5}}>{s.artist.split(" ")[0]}</div>
                </div>
              ))}
            </ScrollRow>
          </Section>
        ))}

        {/* ARTISTS */}
        <Section label="Artists">
          <ScrollRow gap={20}>
            {ARTISTS.map(a=>(
              <div key={a.id} onClick={()=>{setViewArtist(a);setTab("artist")}}
                style={{flexShrink:0, width:82, cursor:"pointer", textAlign:"center"}}>
                <div style={{
                  width:76, height:76, borderRadius:"50%", margin:"0 auto 8px",
                  background:`linear-gradient(135deg,${a.color},${a.color}99)`,
                  display:"flex", alignItems:"center", justifyContent:"center",
                  fontSize:22, fontWeight:800, color:"rgba(255,255,255,0.95)",
                  boxShadow:`0 8px 24px ${a.color}55`,
                }}>
                  {a.name.split(" ").map(n=>n[0]).join("").slice(0,2)}
                </div>
                <div style={{color:C.text, fontSize:10.5, fontWeight:600,
                  overflow:"hidden", textOverflow:"ellipsis", whiteSpace:"nowrap"}}>{a.name.split(" ")[0]}</div>
              </div>
            ))}
          </ScrollRow>
        </Section>
      </div>
    );
  }

  // ── SEARCH ──────────────────────────────────
  function SearchScreen() {
    const filtered = SONGS.filter(s=>
      !searchQ ||
      s.title.toLowerCase().includes(searchQ.toLowerCase()) ||
      s.artist.toLowerCase().includes(searchQ.toLowerCase()) ||
      s.year.toString().includes(searchQ) ||
      s.mood.toLowerCase().includes(searchQ.toLowerCase())
    );
    const moods = [
      {k:"love",  emoji:"❤️",  color:"#FF416C"},
      {k:"melody",emoji:"🎵",  color:"#4776E6"},
      {k:"kuthu", emoji:"🔥",  color:"#F7971E"},
      {k:"sad",   emoji:"💙",  color:"#8360c3"},
    ];
    return (
      <div style={{padding:"0 16px"}}>
        <h2 style={$title({marginTop:24, marginBottom:16})}>Search</h2>
        <div style={{display:"flex", alignItems:"center", gap:10,
          background:C.surf2, borderRadius:16, padding:"13px 16px",
          marginBottom:20, border:`1px solid ${C.border}`}}>
          <Search size={18} color={C.text2} />
          <input value={searchQ} onChange={e=>setSearchQ(e.target.value)}
            placeholder="Songs, artists, years, moods…"
            autoFocus
            style={{flex:1, background:"none", border:"none", outline:"none",
              color:C.text, fontSize:15, fontFamily:"'Outfit',sans-serif"}} />
          {searchQ && <X size={16} color={C.text2} onClick={()=>setSearchQ("")} style={{cursor:"pointer"}} />}
        </div>
        {!searchQ && (
          <div style={{marginBottom:24}}>
            <div style={{color:C.text2, fontSize:12, marginBottom:10, fontWeight:600, textTransform:"uppercase", letterSpacing:1}}>Browse by Mood</div>
            <div style={{display:"flex", gap:10, flexWrap:"wrap"}}>
              {moods.map(m=>(
                <button key={m.k} onClick={()=>setSearchQ(m.k)} style={{
                  background:`${m.color}18`, border:`1px solid ${m.color}44`,
                  color:m.color, borderRadius:24, padding:"9px 18px",
                  fontSize:13, cursor:"pointer", fontFamily:"'Outfit',sans-serif", fontWeight:600,
                }}>
                  {m.emoji} {m.k.charAt(0).toUpperCase()+m.k.slice(1)}
                </button>
              ))}
            </div>
          </div>
        )}
        <div style={{color:C.text2, fontSize:12, marginBottom:10}}>
          {filtered.length} songs found
        </div>
        {filtered.map((s,i)=><SongRow key={s.id} s={s} idx={i} showNum />)}
      </div>
    );
  }

  // ── LIBRARY ─────────────────────────────────
  function LibraryScreen() {
    const likedSongs = SONGS.filter(s=>liked.has(s.id));
    return (
      <div style={{padding:"0 16px"}}>
        <div style={{display:"flex", justifyContent:"space-between", alignItems:"center", paddingTop:24, marginBottom:20}}>
          <h2 style={$title()}>Your Library</h2>
          <button onClick={()=>setCreatePL(true)} style={{
            background:C.accent, border:"none", borderRadius:22,
            color:"white", fontSize:13, fontWeight:600,
            padding:"7px 16px", cursor:"pointer", display:"flex",
            alignItems:"center", gap:5, fontFamily:"'Outfit',sans-serif",
          }}>
            <Plus size={14}/> New Playlist
          </button>
        </div>

        {/* Liked Songs Card */}
        <PLCard
          g={-1} /* special */
          customBg="linear-gradient(145deg,#FF416C,#c94b4b)"
          icon={<Heart size={22} color="white" fill="white"/>}
          name="Liked Songs"
          sub={`${likedSongs.length} songs`}
          onClick={()=>{setViewPL({id:"liked",name:"Liked Songs",songs:liked,desc:"Your hearted songs",g:0,isLiked:true});setTab("playlist");}}
        />

        {/* User Playlists */}
        {myPLs.map(pl=>(
          <PLCard key={pl.id} g={pl.g} name={pl.name} sub={`${pl.songs.length} songs`}
            onClick={()=>{setViewPL(pl);setTab("playlist");}} />
        ))}

        {/* Curated */}
        <div style={{color:C.text2, fontSize:12, fontWeight:600, textTransform:"uppercase",
          letterSpacing:1, marginBottom:12, marginTop:8}}>Curated Playlists</div>
        {PLAYLISTS.map(pl=>(
          <div key={pl.id} onClick={()=>{setViewPL(pl);setTab("playlist")}}
            style={{display:"flex", alignItems:"center", gap:12, padding:"10px 0",
              borderBottom:`1px solid ${C.border}`, cursor:"pointer"}}>
            <div style={{width:54, height:54, borderRadius:14,
              background:`linear-gradient(145deg,${G[pl.g][0]},${G[pl.g][1]})`,
              display:"flex", alignItems:"center", justifyContent:"center", flexShrink:0}}>
              <ListMusic size={22} color="rgba(255,255,255,0.85)"/>
            </div>
            <div style={{flex:1}}>
              <div style={{color:C.text, fontWeight:600, fontSize:14}}>{pl.name}</div>
              <div style={{color:C.text2, fontSize:12}}>{pl.songs.length} songs</div>
            </div>
            <ChevronLeft size={18} color={C.text3} style={{transform:"rotate(180deg)"}}/>
          </div>
        ))}
      </div>
    );
  }

  // ── ARTIST ──────────────────────────────────
  function ArtistScreen() {
    if (!viewArtist) return null;
    const artistSongs = SONGS.filter(s=>s.artist===viewArtist.name);
    return (
      <div>
        <div style={{
          minHeight:220, padding:"0 16px 24px",
          background:`linear-gradient(160deg,${viewArtist.color}44,${viewArtist.color}11,${C.bg})`,
          display:"flex", flexDirection:"column", justifyContent:"flex-end", position:"relative",
        }}>
          <BackBtn onClick={()=>setTab("home")} />
          <div style={{width:86, height:86, borderRadius:"50%", marginBottom:14,
            background:`linear-gradient(135deg,${viewArtist.color},${viewArtist.color}88)`,
            display:"flex", alignItems:"center", justifyContent:"center",
            fontSize:30, fontWeight:900, color:"rgba(255,255,255,0.95)",
            boxShadow:`0 12px 40px ${viewArtist.color}66`}}>
            {viewArtist.name.split(" ").map(n=>n[0]).join("").slice(0,2)}
          </div>
          <h1 style={{color:C.text, margin:0, fontSize:26, fontWeight:900, lineHeight:1}}>{viewArtist.name}</h1>
          <p style={{color:C.text2, margin:"6px 0 0", fontSize:13}}>{viewArtist.bio}</p>
          <p style={{color:C.text3, margin:"4px 0 0", fontSize:12}}>Since {viewArtist.since||"1980s"} · {artistSongs.length} songs</p>
        </div>
        <div style={{padding:"16px 16px 0"}}>
          <button onClick={()=>artistSongs[0]&&play(artistSongs[0])} style={{
            background:C.accent, color:"white", border:"none", borderRadius:28,
            padding:"13px 40px", fontSize:15, fontWeight:700, cursor:"pointer",
            fontFamily:"'Outfit',sans-serif", marginBottom:20,
            boxShadow:`0 8px 30px ${C.accent}55`,
          }}>▶ Play All</button>
          {artistSongs.map((s,i)=><SongRow key={s.id} s={s} idx={i} showNum/>)}
        </div>
      </div>
    );
  }

  // ── PLAYLIST ────────────────────────────────
  function PlaylistScreen() {
    if (!viewPL) return null;
    const isLiked = viewPL.isLiked;
    const plSongs = isLiked
      ? SONGS.filter(s=>liked.has(s.id))
      : SONGS.filter(s=>(Array.isArray(viewPL.songs)?viewPL.songs:Array.from(viewPL.songs)).includes(s.id));
    const [c1,c2] = G[viewPL.g%G.length];
    return (
      <div>
        <div style={{
          textAlign:"center", padding:"60px 24px 28px",
          background:`linear-gradient(180deg,${c1}55,${c1}11,transparent)`,
          position:"relative",
        }}>
          <BackBtn onClick={()=>setTab("library")} />
          <div style={{width:168, height:168, borderRadius:22, margin:"0 auto 18px",
            background: isLiked ? "linear-gradient(145deg,#FF416C,#c94b4b)" : `linear-gradient(145deg,${c1},${c2})`,
            display:"flex", alignItems:"center", justifyContent:"center",
            boxShadow:`0 24px 70px ${isLiked?"#FF416C":c1}66`}}>
            {isLiked ? <Heart size={64} color="white" fill="white"/> : <Music2 size={64} color="rgba(255,255,255,0.8)"/>}
          </div>
          <h1 style={{color:C.text, fontWeight:900, margin:"0 0 6px", fontSize:22}}>{viewPL.name}</h1>
          <p style={{color:C.text2, fontSize:13, margin:"0 0 22px"}}>{viewPL.desc || `${plSongs.length} songs`}</p>
          {plSongs.length>0 && (
            <button onClick={()=>play(plSongs[0])} style={{
              background:C.accent, color:"white", border:"none",
              borderRadius:30, padding:"14px 52px", fontSize:15, fontWeight:700,
              cursor:"pointer", fontFamily:"'Outfit',sans-serif",
              boxShadow:`0 8px 30px ${C.accent}55`,
            }}>▶ Play All</button>
          )}
        </div>
        <div style={{padding:"4px 16px 0"}}>
          {plSongs.length===0
            ? <div style={{color:C.text2, textAlign:"center", padding:"40px 0", fontSize:14}}>No songs yet. Like songs to see them here ❤️</div>
            : plSongs.map((s,i)=><SongRow key={s.id} s={s} idx={i} showNum/>)
          }
        </div>
      </div>
    );
  }

  // ── MINI PLAYER ─────────────────────────────
  function MiniPlayer() {
    return (
      <div onClick={()=>setFullPlayer(true)} style={{
        position:"fixed", bottom:64, left:0, right:0, maxWidth:430, margin:"0 auto",
        background:"rgba(14,14,28,0.96)", backdropFilter:"blur(24px)",
        borderTop:`1px solid ${C.border}`, padding:"10px 16px",
        display:"flex", alignItems:"center", gap:12, cursor:"pointer", zIndex:150,
      }}>
        <Art g={song.g} size={44} r={10}/>
        <div style={{flex:1, minWidth:0}}>
          <div style={{color:C.text, fontWeight:700, fontSize:14,
            overflow:"hidden", textOverflow:"ellipsis", whiteSpace:"nowrap"}}>{song.title}</div>
          <div style={{color:C.text2, fontSize:12}}>{song.artist}</div>
        </div>
        <div onClick={e=>{e.stopPropagation();heart(song.id)}} style={{cursor:"pointer",padding:4}}>
          <Heart size={18} color={liked.has(song.id)?C.accent:C.text3}
            fill={liked.has(song.id)?C.accent:"none"} />
        </div>
        <div onClick={e=>{e.stopPropagation();setPlaying(p=>!p)}} style={{cursor:"pointer",padding:4}}>
          {playing ? <Pause size={24} color={C.text}/> : <Play size={24} color={C.text}/>}
        </div>
        <div onClick={e=>{e.stopPropagation();nextSong(song,shuffle,repeat)}} style={{cursor:"pointer",padding:4}}>
          <SkipForward size={22} color={C.text}/>
        </div>
        {/* progress strip */}
        <div style={{position:"absolute", bottom:0, left:0, right:0, height:2, background:C.border}}>
          <div style={{width:`${progress}%`, height:"100%", background:C.accent, transition:"width .5s linear"}}/>
        </div>
      </div>
    );
  }

  // ── FULL PLAYER ─────────────────────────────
  function FullPlayer() {
    const [c1,c2] = G[song.g%G.length];
    const elapsed = song.dur * progress / 100;
    return (
      <div style={{
        position:"fixed", inset:0, zIndex:300, maxWidth:430, margin:"0 auto",
        background:`linear-gradient(180deg,${c1}55 0%,${C.bg} 45%)`,
        backdropFilter:"blur(60px)",
        display:"flex", flexDirection:"column", overflowY:"auto",
      }}>
        {/* Top bar */}
        <div style={{display:"flex", alignItems:"center", justifyContent:"space-between",
          padding:"52px 20px 0"}}>
          <button onClick={()=>setFullPlayer(false)} style={$iconBtn}>
            <ChevronDown size={28} color={C.text}/>
          </button>
          <div style={{textAlign:"center"}}>
            <div style={{color:C.text2, fontSize:11, textTransform:"uppercase", letterSpacing:2}}>Now Playing</div>
            <div style={{color:C.text3, fontSize:11}}>{song.year}</div>
          </div>
          <button style={$iconBtn}>
            <MoreHorizontal size={24} color={C.text}/>
          </button>
        </div>

        {/* Album Art */}
        <div style={{display:"flex", justifyContent:"center", margin:"32px 0 28px"}}>
          <div style={{
            width:268, height:268, borderRadius:24,
            background:`linear-gradient(145deg,${c1},${c2})`,
            display:"flex", alignItems:"center", justifyContent:"center",
            boxShadow:`0 32px 90px ${c1}77`,
            animation: playing ? "artPulse 3s ease-in-out infinite" : "none",
          }}>
            <Music2 size={96} color="rgba(255,255,255,0.68)"/>
          </div>
        </div>

        {/* Song info + heart */}
        <div style={{display:"flex", alignItems:"flex-start", justifyContent:"space-between",
          padding:"0 24px", marginBottom:22}}>
          <div style={{flex:1}}>
            <h2 style={{color:C.text, margin:0, fontSize:24, fontWeight:900, lineHeight:1.2}}>{song.title}</h2>
            <p style={{color:C.text2, margin:"7px 0 0", fontSize:15, fontWeight:500}}>{song.artist}</p>
            <p style={{color:C.text3, margin:"3px 0 0", fontSize:12}}>{song.album}</p>
          </div>
          <div onClick={()=>heart(song.id)} style={{cursor:"pointer", padding:8}}>
            <Heart size={28} color={liked.has(song.id)?C.accent:C.text2}
              fill={liked.has(song.id)?C.accent:"none"}/>
          </div>
        </div>

        {/* Seek bar */}
        <div style={{padding:"0 24px", marginBottom:10}}>
          <div style={{height:4, background:C.surf3, borderRadius:4, cursor:"pointer", position:"relative"}}
            onClick={e=>{
              const r=e.currentTarget.getBoundingClientRect();
              setProgress(Math.max(0,Math.min(100,((e.clientX-r.left)/r.width)*100)));
            }}>
            <div style={{width:`${progress}%`, height:"100%", background:`linear-gradient(90deg,${c1},${C.accent})`,
              borderRadius:4, transition:"width .5s linear", position:"relative"}}>
              <div style={{position:"absolute", right:-6, top:"50%", transform:"translateY(-50%)",
                width:14, height:14, borderRadius:"50%", background:C.accent,
                boxShadow:`0 0 8px ${C.accent}`}}/>
            </div>
          </div>
          <div style={{display:"flex", justifyContent:"space-between", marginTop:8}}>
            <span style={{color:C.text3, fontSize:11}}>{fmt(elapsed)}</span>
            <span style={{color:C.text3, fontSize:11}}>{fmt(song.dur)}</span>
          </div>
        </div>

        {/* Controls */}
        <div style={{display:"flex", alignItems:"center", justifyContent:"space-between",
          padding:"0 24px", marginBottom:28}}>
          <button onClick={()=>setShuffle(s=>!s)} style={{...($iconBtn), opacity:shuffle?1:.38}}>
            <Shuffle size={22} color={shuffle?C.accent:C.text}/>
          </button>
          <button onClick={()=>prevSong(song)} style={$iconBtn}>
            <SkipBack size={34} color={C.text} fill={C.text}/>
          </button>
          <button onClick={()=>setPlaying(p=>!p)} style={{
            width:70, height:70, borderRadius:"50%",
            background:`linear-gradient(135deg,${C.accent},#FF8C00)`,
            border:"none", cursor:"pointer",
            display:"flex", alignItems:"center", justifyContent:"center",
            boxShadow:`0 10px 40px ${C.accent}88`, transition:"transform .15s",
          }}>
            {playing
              ? <Pause size={32} color="white" fill="white"/>
              : <Play  size={32} color="white" fill="white"/>}
          </button>
          <button onClick={()=>nextSong(song,shuffle,repeat)} style={$iconBtn}>
            <SkipForward size={34} color={C.text} fill={C.text}/>
          </button>
          <button onClick={()=>setRepeat(r=>(r+1)%3)} style={{...($iconBtn), opacity:repeat>0?1:.38}}>
            <Repeat size={22} color={repeat>0?C.accent:C.text}/>
          </button>
        </div>

        {/* Volume */}
        <div style={{display:"flex", alignItems:"center", gap:12, padding:"0 28px", marginBottom:28}}>
          <Volume2 size={16} color={C.text3}/>
          <input type="range" min={0} max={100} value={vol}
            onChange={e=>setVol(+e.target.value)}
            style={{flex:1, accentColor:C.accent, height:4, cursor:"pointer"}}/>
          <Volume2 size={22} color={C.text3}/>
        </div>

        {/* Mood + album badge */}
        <div style={{display:"flex", gap:10, padding:"0 24px", marginBottom:24}}>
          <div style={{background:C.surf2, borderRadius:14, padding:"10px 16px", flex:1}}>
            <div style={{color:C.text3, fontSize:11, marginBottom:2}}>MOOD</div>
            <div style={{color:C.text, fontWeight:600, fontSize:13, textTransform:"capitalize"}}>{song.mood}</div>
          </div>
          <div style={{background:C.surf2, borderRadius:14, padding:"10px 16px", flex:2}}>
            <div style={{color:C.text3, fontSize:11, marginBottom:2}}>ALBUM</div>
            <div style={{color:C.text, fontWeight:600, fontSize:13,
              overflow:"hidden", textOverflow:"ellipsis", whiteSpace:"nowrap"}}>{song.album}</div>
          </div>
        </div>

        {/* Repeat label */}
        {repeat===2 && (
          <div style={{textAlign:"center", color:C.accent, fontSize:12, marginBottom:10}}>🔂 Repeat One</div>
        )}
        {repeat===1 && (
          <div style={{textAlign:"center", color:C.accent, fontSize:12, marginBottom:10}}>🔁 Repeat All</div>
        )}
      </div>
    );
  }

  // ── BOTTOM NAV ──────────────────────────────
  const navItems = [
    {id:"home",    Icon:Home,    label:"Home"},
    {id:"search",  Icon:Search,  label:"Search"},
    {id:"library", Icon:Library, label:"Library"},
  ];

  const activeTab = ["artist","playlist"].includes(tab)
    ? (tab==="artist"?"home":"library") : tab;

  // ── RENDER ──────────────────────────────────
  return (
    <div style={{
      maxWidth:430, margin:"0 auto", height:"100dvh", minHeight:"100vh",
      background:C.bg, fontFamily:"'Outfit',system-ui,sans-serif",
      position:"relative", overflow:"hidden", display:"flex", flexDirection:"column",
    }}>
      <style>{`
        *{box-sizing:border-box;scrollbar-width:none;}
        *::-webkit-scrollbar{display:none;}
        @keyframes artPulse{0%,100%{transform:scale(1) rotate(0deg);box-shadow:0 32px 90px ${G[song.g][0]}77;}
          50%{transform:scale(1.025) rotate(1deg);box-shadow:0 40px 110px ${G[song.g][0]}99;}}
        input[type=range]{-webkit-appearance:none;background:${C.surf3};border-radius:99px;outline:none;}
        input[type=range]::-webkit-slider-thumb{-webkit-appearance:none;width:16px;height:16px;border-radius:50%;background:${C.accent};cursor:pointer;}
      `}</style>

      {/* Create Playlist Modal */}
      {createPL && (
        <div style={{position:"fixed",inset:0,background:"rgba(0,0,0,0.75)",zIndex:400,
          display:"flex",alignItems:"center",justifyContent:"center"}}>
          <div style={{background:C.surf2, borderRadius:22, padding:26, width:310, boxShadow:"0 30px 80px rgba(0,0,0,0.6)"}}>
            <h3 style={{color:C.text,margin:"0 0 18px",fontWeight:800,fontSize:18}}>New Playlist</h3>
            <input value={plName} onChange={e=>setPlName(e.target.value)}
              placeholder="Give it a name…"
              autoFocus
              style={{width:"100%",background:C.surf3,border:`1px solid ${C.border}`,borderRadius:12,
                padding:"12px 16px",color:C.text,fontSize:14,fontFamily:"'Outfit',sans-serif",
                outline:"none",marginBottom:16}}/>
            <div style={{display:"flex",gap:10}}>
              <button onClick={()=>{setCreatePL(false);setPlName("");}}
                style={{flex:1,background:C.surf3,border:"none",borderRadius:12,padding:13,
                  color:C.text2,cursor:"pointer",fontFamily:"'Outfit',sans-serif"}}>Cancel</button>
              <button onClick={()=>{
                if(plName.trim()){
                  setMyPLs(p=>[...p,{id:Date.now(),name:plName.trim(),songs:[],g:Math.floor(Math.random()*G.length)}]);
                  setPlName(""); setCreatePL(false);
                }
              }} style={{flex:1,background:C.accent,border:"none",borderRadius:12,padding:13,
                color:"white",cursor:"pointer",fontWeight:700,fontFamily:"'Outfit',sans-serif"}}>Create</button>
            </div>
          </div>
        </div>
      )}

      {/* Scrollable main area */}
      <div style={{flex:1, overflowY:"auto", paddingBottom:130}}>
        {tab==="home"     && <HomeScreen/>}
        {tab==="search"   && <SearchScreen/>}
        {tab==="library"  && <LibraryScreen/>}
        {tab==="artist"   && <ArtistScreen/>}
        {tab==="playlist" && <PlaylistScreen/>}
      </div>

      {/* Mini Player (always visible unless full player is open) */}
      {!fullPlayer && <MiniPlayer/>}

      {/* Full Player overlay */}
      {fullPlayer && <FullPlayer/>}

      {/* Bottom Navigation */}
      <div style={{
        position:"fixed", bottom:0, left:0, right:0, maxWidth:430, margin:"0 auto",
        background:"rgba(6,6,16,0.96)", backdropFilter:"blur(28px)",
        borderTop:`1px solid ${C.border}`,
        display:"flex", zIndex:200, paddingBottom:"env(safe-area-inset-bottom)",
      }}>
        {navItems.map(({id,Icon,label})=>{
          const active = activeTab===id;
          return (
            <button key={id} onClick={()=>setTab(id)} style={{
              flex:1, display:"flex", flexDirection:"column", alignItems:"center",
              padding:"11px 0 8px", background:"none", border:"none", cursor:"pointer",gap:4,
            }}>
              <Icon size={22} color={active?C.accent:C.text3} strokeWidth={active?2.2:1.8}/>
              <span style={{fontSize:10, color:active?C.accent:C.text3,
                fontWeight:active?700:500, fontFamily:"'Outfit',sans-serif"}}>{label}</span>
              {active && <div style={{width:4,height:4,borderRadius:"50%",background:C.accent}}/>}
            </button>
          );
        })}
      </div>
    </div>
  );
}

// ──────────────────────────────────────────────
// TINY UI PRIMITIVES
// ──────────────────────────────────────────────
function Section({label, labelColor, right, children}) {
  return (
    <div style={{marginBottom:28}}>
      <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:14}}>
        <h2 style={{margin:0,color:labelColor||"#F0F0F8",fontSize:17,fontWeight:800,
          fontFamily:"'Outfit',sans-serif"}}>{label}</h2>
        {right}
      </div>
      {children}
    </div>
  );
}

function ScrollRow({children, gap=12}) {
  return (
    <div style={{display:"flex",gap,overflowX:"auto",paddingBottom:6}}>
      {children}
    </div>
  );
}

function PLCard({g, customBg, icon, name, sub, onClick}) {
  const C_surf2 = "#16162A";
  const C_text  = "#F0F0F8";
  const C_text2 = "#8888AA";
  const bg = customBg || `linear-gradient(145deg,${G[g%G.length][0]},${G[g%G.length][1]})`;
  return (
    <div onClick={onClick} style={{
      background:C_surf2, borderRadius:18, padding:16, marginBottom:14,
      cursor:"pointer", display:"flex", alignItems:"center", gap:14, border:"1px solid rgba(255,255,255,0.05)",
    }}>
      <div style={{width:56,height:56,borderRadius:14,background:bg,
        display:"flex",alignItems:"center",justifyContent:"center",flexShrink:0}}>
        {icon || <Music2 size={24} color="rgba(255,255,255,0.85)"/>}
      </div>
      <div style={{flex:1}}>
        <div style={{color:C_text,fontWeight:700,fontSize:15}}>{name}</div>
        <div style={{color:C_text2,fontSize:12,marginTop:2}}>{sub}</div>
      </div>
    </div>
  );
}

function BackBtn({onClick}) {
  return (
    <button onClick={onClick} style={{
      position:"absolute",top:20,left:16,background:"rgba(0,0,0,0.4)",
      border:"none",borderRadius:"50%",width:38,height:38,
      display:"flex",alignItems:"center",justifyContent:"center",cursor:"pointer",zIndex:10,
    }}>
      <ChevronLeft size={22} color="white"/>
    </button>
  );
}

function SmBtn({onClick, children}) {
  return (
    <button onClick={onClick} style={{
      background:"none",border:"none",color:"#FF4D00",fontSize:12,
      cursor:"pointer",padding:0,fontWeight:600,fontFamily:"'Outfit',sans-serif",
    }}>{children}</button>
  );
}

const $title = (extra={}) => ({
  color:"#F0F0F8", fontWeight:900, fontSize:22, margin:0,
  fontFamily:"'Outfit',sans-serif", ...extra,
});

const $iconBtn = {
  background:"none", border:"none", cursor:"pointer", padding:8,
  display:"flex", alignItems:"center", justifyContent:"center",
};
