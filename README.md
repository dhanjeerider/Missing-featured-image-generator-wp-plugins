# Auto Missing Featured Image — Plugin Guide (Hinglish)

> **v1.1.0 me naya kya hai:** Unsplash ke **3 alag fields** (Application ID / Access Key / Secret Key — bilkul Unsplash dashboard jaisa), har key par **show/hide (eye) button** aur **Test Connection button** (key sahi hai ya nahi + rate-limit kitni bachi hai turant pata chalega), Groq model suggestions + docs links, aur naya sundar admin design.

## Ye plugin kya karta hai?

WordPress ke un sab posts/pages/products ko dhoondhta hai jinki **featured image missing** hai, unke **title ko dekh ke Unsplash ya Pexels se relevant photo search** karta hai, usko tumhari **Media Library me download** karke properly **featured image set** kar deta hai (sirf URL hotlink nahi karta — isliye plugin delete karne ke baad bhi images safe rahengi).

## ⚠️ Sabse pehle — tumhari Groq key ke baare me

1. Tumne jo `gsk_...` key chat me bheji thi, wo ab **public ho chuki hai samjho**. Use **console.groq.com → API Keys** me jaa ke **delete kar do** aur ek **nayi key banao**. Warna koi bhi tumhara quota use kar sakta hai.
2. Maine key ko plugin code me **hardcode nahi kiya** hai — jaanboojh ke. Wajah:
   - WordPress.org SVN repo aise plugin ko **reject** kar deta hai (shared/hardcoded keys allowed nahi).
   - Plugin install karne wale har bande ko tumhari key dikh jayegi, quota khatam/account ban risk.
   - Settings me key **ek baar paste** karna hai, bas 10 second. Kaam bilkul same hoga.
3. **Groq vs Grok confusion clear:** `gsk_` wali key **Groq** ki hai. Groq image *generate nahi* karta (wo fast LLM hai). Plugin me Groq AI har post ke title/excerpt ko padh ke **best English search keywords** deta hai (Hindi title ke liye ye bahut kaam ka hai) → phir un keywords se Unsplash/Pexels search hota hai. Agar tumhe sach me **AI se image generate** karwani hai to wo **xAI Grok** (`xai-` wali paid key, console.x.ai) hai — iska optional option bhi plugin me dal diya hai (Section 4 settings me).

## Install kaise karein

1. `auto-missing-featured-image.zip` download karo.
2. WordPress Admin → **Plugins → Add New → Upload Plugin** → zip select → Install → Activate.
3. Left menu me **Auto Featured Image** aa jayega.

## API Keys kahan se lein (sab free, 2-2 minute)

| Service | Kahan se | Kaunsi key | Approx free limit |
|---|---|---|---|
| Unsplash | unsplash.com/oauth/applications → apni app kholo | Wahan 3 values dikhengi — **Application ID, Access Key, Secret Key** — plugin me ab teeno ke alag fields hain, waise hi bhar do. Search ke liye sirf **Access Key** use hoti hai (`Authorization: Client-ID`) | ~50 req/hour (demo) |
| Pexels | pexels.com/api → Your API Key | API Key | ~200 req/hour |
| Groq AI (optional) | console.groq.com → API Keys → Create | `gsk_...` wali | Free tier limits model pe depend |
| xAI Grok (optional, paid) | console.x.ai | `xai-...` wali | Credits based |

**Tip:** Unsplash + Pexels **dono** keys dal do. Ek ki hourly limit khatam hogi to plugin dusri se apne aap continue karega.

**Unsplash 401 error aye to?** Matlab Access Key wale field me galti se **Secret Key ya Application ID** paste ho gayi hai. Sahi Access Key dalo (eye button se dikha ke confirm kar sakte ho), **Test Connection** dabao — green aye to Save kar do. Yahi sabse common wajah hai "Pexels chal raha par Unsplash nahi" wali problem ki. **Secret Key kabhi kisi ke saath share mat karo** (chat/screenshot me bhi nahi) — agar bhej di hai to Unsplash app page se keys regenerate kar do.

## Recommended Settings (Settings & API Keys tab)

- **Image source:** Unsplash first, then Pexels (jo pasand ho)
- **Quality:** **Medium (~1000px)** — tumne jo kaha tha, wahi default hai 👍
- **Orientation:** Landscape (blog ke liye best)
- **Build search query from:** Title only
- **Extra keywords:** Apni niche ke 1-2 words (jaise `technology`, `cooking`) — optional
- **Fallback query:** Jaise `blogging laptop writing` — jab title se kuch na mile to ye chalega
- **Groq AI mode:**
  - English titles → **Fallback only** (quota bachega, sirf fail hone pe AI request jayegi, one-by-one batch me)
  - Hindi/mixed titles → **Always** (AI pehle English keywords banayega, results bahut better ayenge)
- **Post types:** Jo chahiye tick karo — post, page, WooCommerce product, koi bhi custom post type chalega
- **Post status:** Publish + Draft rakho
- **Only missing:** ✅ ON rakho (existing featured image kabhi overwrite nahi hogi)

## Use kaise karein

### Batch mode (sabse fast)
1. **Auto Featured Image → Scan & Process** — missing wale posts ki list dikhegi (filter: post type / title search).
2. Posts tick karo → **"▶ Auto-set for Selected"** dabao. (Ya **"Auto-set All on This Page"**)
3. Plugin **one-by-one** process karega — progress bar + live log chalega. Stop button bhi hai.
4. Har row me lagi hui image ki thumbnail result me dikhegi.

Ek post ke liye plugin ye try karta hai (order me): (AI mode Always ho to) AI keywords → pura title → title ke pehle 5 words → (AI fallback) Groq keywords se dobara search → tumhara fallback query → (on ho to) Grok se AI image generate.

### Manual mode (khud photo choose karna ho to)
- Har row pe **Find Images** button → popup me photos ki grid ayegi → keywords badal ke dobara search kar sakte ho, ya **AI Keywords (Groq)** button se AI se keyword banwa sakte ho → jo photo pasand aye, **Set Featured** dabao.
- Ya kisi single post ki edit screen pe right side me **"Find Featured Image"** box milega — wahin se search/set ho jayega.
- **Auto Set** button ek hi post ko turant auto-process karta hai.

## Dhyan dene wali baatein

- **Rate limit:** Batch me agar API error dikhne lage to samjho hourly limit khatam. 1 ghanta ruko ya dusri source ko first kar do. Jo posts ho gaye hain wo list se apne aap hat jayenge, dobara run karne pe bache hue hi process honge.
- **Backup:** Badi batch (500+ posts) se pehle site ka backup le lo — good practice hai, waise plugin existing images ko chhuta nahi hai.
- **Images ki license:** Unsplash/Pexels photos free-to-use hain. Plugin photographer ka credit image caption me apne aap dal deta hai.
- Har image media library me post ke title wale naam se save hoti hai, **alt text auto** set hota hai (SEO ke liye mast).

## WordPress.org SVN Repo pe submit kerne se pehle ki checklist ✅

Plugin ko SVN-friendly banaya hai (prefix `amfi_`, text domain, nonce + capability checks, sanitization/escaping, `wp_remote_*` ka use, koi hardcoded key/CDN nahi, readme.txt me **External Services** section, uninstall.php, GPL license). Submit se pehle tumhe ye karna hai:

1. **Naam badlo (placeholders):**
   - `auto-missing-featured-image.php` me `Author: Your Name` aur `Author URI` apna karo.
   - `readme.txt` me `Contributors: yourusername` ki jagah apna **wordpress.org username** dalo.
   - `Tested up to:` ko apne WordPress ke latest tested version se match karo.
2. **Plugin Check plugin** (wordpress.org ka official "Plugin Check" plugin) apni site pe chala ke sare errors fix karo — SVN review me yahi chalta hai.
3. Ek live/test site pe plugin ko **khud test** karo: 2-3 posts pe batch, manual picker, settings save.
4. Screenshots banao (Scan page, picker, settings) — ye SVN ke `/assets` folder me jate hain (plugin zip ke andar nahi).
5. wordpress.org → **Plugins → Developers → Add your plugin** me zip submit karo. Review team email karegi, phir SVN access milega.
6. Agar reviewer plugin display name me "Unsplash/Pexels" (trademarks) pe objection kare to name sirf **"Auto Missing Featured Image – Stock Photos & AI"** kar dena. Slug me koi trademark nahi rakha hai, isliye slug safe hai.
7. **Note:** readme me jo External Services wala section hai use mat hatana — AI/stock API wale plugins ke liye ye wordpress.org pe mandatory hai.

## Files jo mile hain

- `auto-missing-featured-image.zip` — WordPress me upload ke liye ready plugin
- `auto-missing-featured-image/` — unzipped source code (SVN `/trunk` me isi ke andar ki files dalengi)
- `PLUGIN-GUIDE-HINGLISH.md` — ye wali guide
