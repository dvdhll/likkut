# HANDOFF — רשימת ליקוט (משנת יוסף)

אפליקציית לקט אישית מול mishnatyosef.org. לא רשמית, פרטית לדוד וחברים.

## מצב נוכחי (עדכני ל-2026-10-06)
- **חי:** https://likkut.pages.dev (Cloudflare Pages project `likkut`). גם `liktu.pages.dev` חי (כפילות ישנה, אפשר להתעלם).
- **קוד פתוח:** https://github.com/dvdhll/likkut (ענף main; push = מקור האמת, הפריסה לפרודקשן היא `wrangler pages deploy` ידני, לא git-connected).
- **APK:** `D:\Dropbox\קלוד פרויקטים\likkut-apk\` — עטיפת WebView (Capacitor) שטוענת את האתר מרחוק. `likkut.apk` (debug) + `likkut-release.apk` (חתום). keystore + סיסמה ב-`likkut-apk\KEYSTORE-סיסמה.txt`. הבנייה ב-`likkut-apk\BUILD.md` (חובה נתיב ASCII — העברית שוברת את AGP). ה-APK טוען מרחוק → מתעדכן לבד בכל פריסה.
- **100% client-side:** אין שרת. הדפדפן מדבר ישירות עם v2.mishnatyosef.org (CORS `*`). הסיסמה נשלחת ישירות, הטוקן ב-localStorage בלבד. אנליטיקס = Cloudflare Web Analytics (מצטבר, ללא עוגיות; token 95dbefb43b9e4b58b7a5db84ac29c212).

## כל האפליקציה בקובץ אחד
`public/index.html` — HTML+CSS+JS. + `manifest.json`, `sw.js`, אייקונים (`icon.svg`, `icon-192/512.png`, `apple-touch-icon.png`). פריסה: `cd mishnat-likut; npx wrangler pages deploy` (ואז `git add -A; git commit; git push`).

## נעשה בסשן 2026-10-06
- **תיקון ארגון מרובה-חשבונות (פרוס, 541eb39):** מיזוג 3 חשבונות היה מבולגן (מיון לפי מיקום-פנימי-בהזמנה → ערבוב). תוקן: מיון לפי `item_salesID` (מפתח חנות גלובלי, זהה בין חשבונות, עולה בסדר המדפים). מרובה → קיבוץ לקטגוריות (כל קטגוריה פעם אחת); בודד → סדר-חנות עם כותרות בלוקים. שמות קצרים (החלק אחרי הפסיק) קטנים-ומודגשים; בחשבון בודד אין שמות. כפתור "איפוס סימונים". סיווג משופר + תיקון התנגשויות substring (תות בפיתות, גביע ב"גביע יוגורט").

## בעבודה עכשיו (לא פרוס — ממתין לאישור דוד)
שני פיצ'רים שדוד ביקש 2026-10-06:
1. **מתג מאוחד/נפרד** — כשיש 2+ חשבונות: להציג רשימה מאוחדת (כמו היום) או רשימה נפרדת לכל חשבון. לזכור העדפה.
2. **חיפוש טקסטואלי חכם** — תיבת חיפוש שמצמצמת את הרשימה; fuzzy: חלק-משם + סובלנות לשגיאות כתיב + נרמול עברית (אותיות סופיות, ניקוד, הסרת {},(),[]).

## עובדות API (מ-mishnat-yosef-api.md בזיכרון)
- `POST /api/login` {email,password} → JWT. header `Authorization: Bearer`.
- `GET /api/orders` → paginated; `GET /api/orders/{id}` → `order.order.products[]` (`full_name`, `amount`=כמות, `billing_product`=1 → דלג, `units_type`=10 → לפי משקל, `item_salesID`=מפתח גלובלי).
- `/api/refresh` הורס את הטוקן הישן → לרענן רק ריאקטיבית על 401 (אחרת שורף טוקנים).
- **אזהרת PII:** `/api/profile` חושף ת.ז./CVV/טוקן-סליקה בטקסט גלוי — לקרוא רק את `p.site`, אף פעם לא ללוגים.
- חשבונות בדיקה: **משפחת הלל = manya.hillel@gmail.com**, **סבתא = shoshana.dory@gmail.com**.

## גוצ'ות
- **לא לפרוס בלי אישור מפורש של דוד** באותה בקשה.
- הטוקנים לבדיקה בדפדפן הבדיקה נוטים לפוג (מרוב בדיקות refresh) — זה ארטיפקט, לא באג; מתחברים מחדש.
- aapt/AGP נשברים על נתיב עברי — בניית APK רק מעותק ASCII (ראה BUILD.md).
- base64 מהדפדפן חסום להחזרה; הורדות אוטומטיות לא נוחתות → אייקוני PNG נוצרים ב-Node טהור (scratchpad/gen-icons.js).
