# Tarixdan-savollar
Java tilida yozilgan ilk Android ilovam
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Map;
import java.util.Scanner;
import java.util.prefs.Preferences;

public class Main {

    private static final int QUESTION_TIME = 15; // Soniya
    private static final String BEST_SCORE_PREFIX = "historyQuizBest_";

    private static final Map<String, String> TOPIC_LABELS = Map.of(
            "uzbek", "O‘zbekiston tarixi",
            "world", "Jahon tarixi",
            "all", "Barchasi (Aralash)"
    );

    // Modern Java 21 Record for Questions
    public record Question(
            String topic,
            String questionText,
            List<String> options,
            int correctIndex,
            String explanation
    ) {}

    private static final List<Question> ALL_QUESTIONS = List.of(
            // O‘zbekiston tarixi (10 ta savol)
            new Question("uzbek", "Amir Temur qaysi yilda tug‘ilgan?", 
                    List.of("1326-yil", "1336-yil", "1346-yil", "1356-yil"), 1, 
                    "Amir Temur 1336-yil 9-aprelda Kesh (hozirgi Shahrisabz) yaqinidagi Xo‘ja Ilg‘or qishlog‘ida tavallud topgan."),
            new Question("uzbek", "Buyuk Ipak yo‘lining eng muhim markazlaridan biri bo‘lgan shahar qaysi?", 
                    List.of("Samarqand", "Rim", "Afina", "London"), 0, 
                    "Samarqand Markaziy Osiyoda sharq va g‘arb savdo yo‘llari kesishgan eng yirik madaniy va tijorat markazlaridan biri bo‘lgan."),
            new Question("uzbek", "Mirzo Ulug‘bekning asosiy ilmiy asari qanday nomlanadi?", 
                    List.of("Devonu lug‘otit turk", "Ziji jadidi Ko‘ragoniy", "Boburnoma", "Qutadg‘u bilig"), 1, 
                    "«Ziji jadidi Ko‘ragoniy» — Samarqand rasadxonasida olib borilgan kuzatuvlar asosida yaratilgan mashhur astronomik jadval va asar."),
            new Question("uzbek", "«Boburnoma» asarining muallifi kim?", 
                    List.of("Alisher Navoiy", "Zahiriddin Muhammad Bobur", "Abulg‘ozi Bahodirxon", "Munis"), 1, 
                    "«Boburnoma» Zahiriddin Muhammad Boburning hayoti, yurishlari, tabiat va xalqlar haqidagi qimmatli tarixiy-memuar asaridir."),
            new Question("uzbek", "Mahmud Qoshg‘ariyning XI asrda yaratilgan mashhur tilshunoslik va ensiklopedik asari qaysi?", 
                    List.of("Devonu lug‘otit turk", "Hibatul haqoyiq", "Muhokamatul lug‘atayn", "Tarixi Rashidiy"), 0, 
                    "«Devonu lug‘otit turk» turkiy xalqlar tili, etnografiyasi, geografiyasi va adabiy merosi bo‘yicha eng muhim manbalardan biridir."),
            new Question("uzbek", "Xorazm Ma’mun akademiyasi («Donishmandlar uyi») qaysi hukmdor davrida tashkil etilgan?", 
                    List.of("Ma’mun ibn Muhammad", "Jaloliddin Manguberdi", "Muhammad Xorazmshoh", "Sulton Sanjar"), 0, 
                    "Xorazmshoh Ma’mun ibn Muhammad (995–1017) davrida Gurganjda (Urganchda) tashkil etilgan akademiyada Abu Rayhon Beruniy va Ibn Sino kabi olimlar ijod qilgan."),
            new Question("uzbek", "Jaloliddin Manguberdi mo‘g‘ul qo‘shinlariga qarshi mashhur jangni qaysi daryo bo‘yida olib borgan?", 
                    List.of("Sirdaryo", "Amudaryo", "Hind daryosi", "Zarafshon"), 2, 
                    "Jaloliddin Manguberdi 1221-yilda Hind daryosi bo‘yida Chingizxon boshchiligidagi mo‘g‘ul qo‘shiniga qarshi qahramonona jang qilgan."),
            new Question("uzbek", "Amir Temur saltanatining poytaxti qaysi shahar bo‘lgan?", 
                    List.of("Buxoro", "Samarqand", "Xiva", "Toshkent"), 1, 
                    "Samarqand Sohibqiron Amir Temur saltanatining poytaxti sifatida obodonlashtirilib, ilm-fan, me’morchilik va hunarmandchilik markaziga aylangan."),
            new Question("uzbek", "«O‘tkan kunlar» romanining muallifi kim?", 
                    List.of("Abdulla Qodiriy", "Cho‘lpon", "Fitrat", "Oybek"), 0, 
                    "«O‘tkan kunlar» o‘zbek romanchiligining ilk yirik namunasi bo‘lib, yozuvchi Abdulla Qodiriy qalamiga mansub."),
            new Question("uzbek", "O‘zbekiston Respublikasining Davlat mustaqilligi qachon e’lon qilingan?", 
                    List.of("1990-yil 20-iyun", "1991-yil 31-avgust", "1992-yil 2-mart", "1992-yil 8-dekabr"), 1, 
                    "O‘zbekiston Respublikasining Davlat mustaqilligi 1991-yil 31-avgustda e’lon qilingan."),

            // Jahon tarixi (10 ta savol)
            new Question("world", "«Tarix otasi» deb kim ataladi?", 
                    List.of("Miki Maus", "Penovays", "Gerodot", "Putin"), 2, 
                    "Qadimgi yunon muallifi Gerodot «Tarix» asarida voqealarni manbalar va sabab-oqibatlar asosida bayon etgani uchun an’anaviy ravishda «tarix otasi» sanaladi."),
            new Question("world", "Qadimgi Misrda yozuv tizimi qanday atalgan?", 
                    List.of("Mixxat", "Iyeroglif", "Runik yozuv", "Alfabet"), 1, 
                    "Qadimgi Misr yozuv belgilari yunonchada «muqaddas o‘yma belgilar» ma’nosini beruvchi iyerogliflar deb atalgan."),
            new Question("world", "Birinchi jahon urushi qaysi yillarda bo‘lib o‘tgan?", 
                    List.of("1912–1916-yillar", "1914–1918-yillar", "1918–1922-yillar", "1939–1945-yillar"), 1, 
                    "Birinchi jahon urushi 1914-yil 28-iyulda boshlanib, 1918-yil 11-noyabrda Kompyen sulhi bilan yakunlangan."),
            new Question("world", "Qadimgi Yunonistonda Olimpiya o‘yinlari qaysi shaharda o‘tkazilgan?", 
                    List.of("Sparta", "Olimpiya", "Korinf", "Fiva"), 1, 
                    "Olimpiya o‘yinlari miloddan avvalgi 776-yilda (an’anaviy sana) Elida viloyatidagi Olimpiya shahri yaqinida boshlangan."),
            new Question("world", "Ikkinchi jahon urushi qaysi yilda boshlangan?", 
                    List.of("1914-yil", "1918-yil", "1939-yil", "1945-yil"), 2, 
                    "Ikkinchi jahon urushi 1939-yil 1-sentyabrda Germaniyaning Polshaga bostirib kirishi bilan boshlangan."),
            new Question("world", "Qadimgi Mesopotamiyada keng qo‘llanilgan qadimiy yozuv turi qaysi?", 
                    List.of("Mixxat", "Iyeroglif", "Kirill yozuvi", "Piktografiya"), 0, 
                    "Mesopotamiyada (xususan, Shumer va Bobil madaniyatlarida) loy lavhalarga ponasimon tayoqcha bilan yoziladigan mixxat ishlatilgan."),
            new Question("world", "Xristofor Kolumb Amerikaga ilk safarini qaysi yilda amalga oshirgan?", 
                    List.of("1453-yil", "1492-yil", "1519-yil", "1607-yil"), 1, 
                    "Xristofor Kolumb 1492-yilda Atlantika okeanini kesib o‘tib, Bagama arxipelagi va Karib havzasi orollariga yetib borgan."),
            new Question("world", "Buyuk Fransiya inqilobi qaysi yilda boshlangan?", 
                    List.of("1776-yil", "1789-yil", "1815-yil", "1848-yil"), 1, 
                    "Buyuk Fransiya inqilobi 1789-yilda — xususan, 14-iyuldagi Bastiliya qal’asining olinishi bilan bog‘liq muhim tarixiy bosqichda boshlangan."),
            new Question("world", "Birlashgan Millatlar Tashkiloti (BMT) qaysi yilda tashkil topgan?", 
                    List.of("1919-yil", "1945-yil", "1950-yil", "1961-yil"), 1, 
                    "BMT Nizomi 1945-yil 26-iyunda San-Fransiskoda imzolangan va o‘sha yilning 24-oktyabrida rasman kuchga kirgan."),
            new Question("world", "Qadimgi Rimda gladiatorlik janglari o‘tkazilgan eng mashhur ulkan amfiteatr qaysi?", 
                    List.of("Panteon", "Kolizey", "Akropol", "Partenon"), 1, 
                    "Kolizey (Flaviy amfiteatri) milodiy I asrda Rimda qurilgan bo‘lib, gladiatorlik janglari va tomoshalar o‘tkazilgan eng mashhur inshootdir.")
    );

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        Preferences prefs = Preferences.userNodeForPackage(Main.class);

        System.out.println("=========================================");
        System.out.println("      TARIX VIKTORINASIGA XUSH KELIBSIZ!    ");
        System.out.println("=========================================");

        boolean playAgain = true;
        while (playAgain) {
            System.out.println("\nMavzuni tanlang:");
            System.out.println("1. O‘zbekiston tarixi");
            System.out.println("2. Jahon tarixi");
            System.out.println("3. Barchasi (Aralash)");
            System.out.print("Tanlovingiz (1-3): ");

            String choiceInput = scanner.nextLine().trim();
            String selectedTopic = switch (choiceInput) {
                case "1" -> "uzbek";
                case "2" -> "world";
                case "3" -> "all";
                default -> null;
            };

            if (selectedTopic == null) {
                System.out.println("Noto‘g‘ri tanlov! Iltimos, 1 dan 3 gacha bo‘lgan raqamlardan birini kiriting.");
                continue;
            }

            System.out.println("\nFaol mavzu: " + TOPIC_LABELS.get(selectedTopic));

            // Savollarni tanlash va aralashtirish
            List<Question> activeQuestions = new ArrayList<>(
                    selectedTopic.equals("all") 
                            ? ALL_QUESTIONS 
                            : ALL_QUESTIONS.stream().filter(q -> q.topic().equals(selectedTopic)).toList()
            );
            Collections.shuffle(activeQuestions);

            int score = 0;
            int correctCount = 0;

            for (int i = 0; i < activeQuestions.size(); i++) {
                Question q = activeQuestions.get(i);
                System.out.println("\n-----------------------------------------");
                System.out.println("Savol " + (i + 1) + " / " + activeQuestions.size());
                System.out.println(q.questionText());
                System.out.println("(Har bir savol uchun taxminiy vaqt: " + QUESTION_TIME + " soniya)");

                for (int j = 0; j < q.options().size(); j++) {
                    System.out.println((j + 1) + ". " + q.options().get(j));
                }

                System.out.print("Javobingizni tanlang (1-4): ");
                String answerInput = scanner.nextLine().trim();

                int selectedIndex = -1;
                try {
                    selectedIndex = Integer.parseInt(answerInput) - 1;
                } catch (NumberFormatException ignored) {
                }

                if (selectedIndex == q.correctIndex()) {
                    int earnedPoints = 10 + QUESTION_TIME; // Soddalashtirilgan vaqt bonus hisobi
                    score += earnedPoints;
                    correctCount++;
                    System.out.println("✅ To‘g‘ri! +" + earnedPoints + " ball.");
                } else {
                    System.out.println("❌ Noto‘g‘ri javob.");
                    if (selectedIndex >= 0 && selectedIndex < q.options().size()) {
                        System.out.println("To‘g‘ri javob edi: " + q.options().get(q.correctIndex()));
                    }
                }
                
                if (q.explanation() != null && !q.explanation().isBlank()) {
                    System.out.println("💡 Izoh: " + q.explanation());
                }
            }

            // Eng yuqori natijani saqlash (High Score)
            String bestScoreKey = BEST_SCORE_PREFIX + selectedTopic;
            int prevBest = prefs.getInt(bestScoreKey, 0);
            boolean isNewRecord = false;

            if (score > prevBest) {
                prefs.putInt(bestScoreKey, score);
                isNewRecord = true;
            }
            int bestScore = Math.max(score, prevBest);

            System.out.println("\n=========================================");
            System.out.println("              O‘YIN TUGADI               ");
            System.out.println("=========================================");
            System.out.println("Umumiy ball: " + score);
            System.out.println(activeQuestions.size() + " ta savoldan " + correctCount + " tasiga to‘g‘ri javob berdingiz.");
            
            if (isNewRecord) {
                System.out.println("🏆 TABRIKLAYMIZ! Yangi rekord: " + bestScore + " ball!");
            } else {
                System.out.println("⭐ Shu mavzudagi rekord natijangiz: " + bestScore + " ball.");
            }

            System.out.print("\nQaytadan o‘ynaysizmi? (ha/yo‘q): ");
            String again = scanner.nextLine().trim().toLowerCase();
            if (!again.equals("ha") && !again.equals("h") && !again.equals("yes")) {
                playAgain = false;
                System.out.println("O‘ynaganingiz uchun rahmat! Xayr!");
            }
        }
        scanner.close();
    }
}
