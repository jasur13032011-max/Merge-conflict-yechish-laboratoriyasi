# Merge-conflict-yechish-laboratoriyasi
Git bilan ishlashda eng ko'p qo'rqinch va tushunmovchiliklarga sabab bo'ladigan mavzu — bu Merge Conflict (To'qnashuv). Aslida esa bu odatiy holat.

Keling, konfliktlar bilan professional darajada ishlashni barcha ssenariylar misolida ko'rib chiqamiz.

1. Senariy 1: Oddiy 2 ta Branch To'qnashuvi
Ikkala branchda ham bitta faylning aynan bir xil qatori o'zgartirilganda konflikt chiqadi.

Bash
# Tayyorgarlik
git switch main
echo "Asosiy matn" > loyiha.txt
git add loyiha.txt && git commit -m "docs: loyiha fayli yaratildi"

# 1-branch yaratamiz
git switch -c branch-A
echo "Branch A o'zgarishi" > loyiha.txt
git add loyiha.txt && git commit -m "docs: branch A o'zgarishi"

# main'ga qaytib, 2-branch yaratamiz
git switch main
git switch -c branch-B
echo "Branch B o'zgarishi" > loyiha.txt
git add loyiha.txt && git commit -m "docs: branch B o'zgarishi"

# Konflikt keltirib chiqarish
git switch main
git merge branch-A # Muammosiz o'tadi (Fast-forward)
git merge branch-B # KONFLIKT!
Yechish: loyiha.txt faylini ochganimizda Git markerlarni ko'rsatadi:

Plaintext
<<<<<<< HEAD
Branch A o'zgarishi
=======
Branch B o'zgarishi
>>>>>>> branch-B
Markerlarni (<<<<<<<, =======, >>>>>>>) o'chirib, o'rniga yakuniy to'g'ri kodni qoldiramiz (masalan, ikkala yozuvni ham saqlaymiz) va saqlab commit qilamiz:

Bash
git add loyiha.txt
git commit -m "fix: branch-B birlashtirildi va konflikt yechildi"
2. git merge --abort Bilan Bekor Qilish
Agar konfliktlar juda ko'payib ketgandek tuyulsa va siz hozircha ularni yechishga tayyor bo'lmasangiz, merj jarayonini butunlay bekor qilib, hammasini dastlabki holatiga qaytarishingiz mumkin:

Bash
git merge --abort
Bu buyruq sizni xuddi merj boshlanishidan oldingi top-toza holatga qaytaradi.

3. git checkout --ours va --theirs Ishlatish
Agar siz konflikt chiqganda fayllarni bittalab tahrirlab o'tirmasdan, shunchaki o'zingizning (ours) yoki narigi branchning (theirs) faylini butunlay qabul qilmoqchi bo'lsangiz, ushbu buyruqlar vaqtni tejaydi:

Bash
# Biz turgan branch kodi to'g'ri deb hisoblab, uni qabul qilish:
git checkout --ours rasm.png

# Kelayotgan branch kodi to'g'ri deb hisoblab, uni qabul qilish:
git checkout --theirs rasm.png

# Keyin odatdagidek add va commit qilinadi
git add rasm.png
git commit -m "merge: ziddiyat ours/theirs orqali hal qilindi"
4. Senariy 2: 3 ta Faylda Konflikt Chiqganda
Agar to'qnashuv 3 ta faylda (index.html, style.css, app.js) birdan sodir bo'lsa, vahimaga tushish kerak emas. Ularni bittalab, ketma-ket hal qilinadi.

git status buyrug'i orqali aynan qaysi fayllarda konflikt borligini aniqlab olasiz (Ular Both modified bo'lib turadi).

index.html faylini ochib, markerlarni tozalaysiz va saqlaysiz ➡️ git add index.html qilasiz.

style.css faylida ham xuddi shunday qilasiz ➡️ git add style.css.

app.js faylini ham to'g'rilab ➡️ git add app.js qilasiz.

Hamma fayl indekslangach (yashil bo'lgach), bitta umumiy commit qilinadi: git commit -m "fix: 3 ta fayldagi konfliktlar hal qilindi".

5. Ataylab Xato: Markerlarni O'chirmasdan Commit Qilish
Dasturchilar yo'l qo'yadigan eng xunuk xatolardan biri — konflikt markerlarini (<<<<<<< HEAD) fayl ichidan o'chirishni unutilib, kodni commit qilib yuborishdir.

Nima uchun bu ataylab xato hisoblanadi va sababi nimada?
Git buni texnik jihatdan taqiqlamaydi (chunki Git uchun bu shunchaki oddiy matn). Ammo bu kod dasturning ishdan chiqishiga (crash) olib keladi. Masalan, JavaScript yoki Python bu belgilarni ko'rganda SyntaxError beradi va loyiha ishlamay qoladi. Shuning uchun har doim commit qilishdan oldin kodni tekshirish shart.

6. GitHub PR'da Konflikt Chiqganda Lokal Yechish
Agar GitHub sahifangizda "This branch has conflicts that must be resolved" degan qizil yozuv paydo bo'lsa, uni lokal kompyuterda yechish eng xavfsiz yo'ldir:

Bash
# 1. Mahalliy kompyuterda o'z branchingizga o'ting
git switch feature/vazifa

# 2. Main branchdagi eng so'nggi kodlarni o'zingizga torting
git pull origin main

# 3. Terminalda konfliktlar chiqadi. Ularni yuqoridagi usullarda yechasiz.
# 4. Konfliktlar yechilgach, add va commit qilasiz:
git add .
git commit -m "fix: main branch bilan konfliktlar yechildi"

# 5. Kodni qaytadan GitHub'ga push qilasiz
git push
GitHub'dagi PR avtomatik ravishda yashil rangga kiradi va merge qilishga tayyor bo'ladi.

7. Visual Mergetool Sozlash (VS Code)
Konfliktlarni matn ko'rinishida emas, vizual chiroyli oynada yechish uchun VS Code'ni Git'ga bosh muharrir qilib sozlash mumkin:

Bash
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'
Endi konflikt yuzaga kelganda terminalda shunchaki git mergetool buyrug'ini yozsangiz, VS Code sizga har bir o'zgarishni alohida oynada (Accept Current, Accept Incoming) qulay tugmalar bilan ko'rsatib beradi.

Report: Konfliktlarni Oldini Olish Strategiyalari
Jamoada ishlayotganda konfliktlar sonini minimal darajaga tushirish uchun quyidagi qoidalarga amal qilish tavsiya etiladi:

Kichik va Tez-Tez Commit'lar: Kuniga 1 marta katta kod push qilgandan ko'ra, har bir kichik mantiqiy yakunlangan vazifani alohida commit va push qilish lozim.

Tez-Tez git pull qilish: Ishni boshlashdan oldin va kun davomida bir necha bor main branchdagi yangiliklarni o'z branchingizga olib turing (git pull origin main). Bu sizni katta to'qnashuvlardan asraydi.

Vazifalarni To'g'ri Taqsimlash: Jamoada ikki dasturchi bir vaqtning o'zida aynan bitta faylning bitta qatoriga tegadigan vazifani bajarmasligi kerak. Arxitekturani oldindan kelishib olish konfliktlarni 80% ga kamaytiradi.
