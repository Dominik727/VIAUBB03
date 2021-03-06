# Labor 02 - Felhasználói felület készítése - Fragmentek, Chartok

## Bevezető

A labor során egy HR alkalmazást készítünk el, amelybe belépve a felhasználó meg tudja tekinteni személyes adatait, illetve szabadságot tud rögzíteni. Az alkalmazás nem használ perzisztens adattárolást és valós bejelentkeztetést, csak demo adatokkal dolgozik. A labor fő témája a Fragmentekkel való felületkészítés lesz.

<p align="center">
<img src="./assets/menu.png" width="160">
<img src="./assets/profile1.png" width="160">
<img src="./assets/profile2.png" width="160">
<img src="./assets/holiday1.png" width="160">
<img src="./assets/datepicker.png" width="160">
</p>

## Értékelés

Vezetett rész (1 pont)
- [Projekt létrehozása](#projekt-létrehozása)
- [Főmenü képernyő](#főmenü-képernyő)
- [Profil képernyő](#profil-képernyő)
- [Szabadság képernyő](#szabadság-képernyő)
- [Dátumválasztó, napok csökkentése](#dátumválasztó-napok-csökkentése)

Önálló feladat (1 pont)
- [Szabadság továbbfejlesztése](#szabadság-továbbfejlesztése)

Bónusz feladat
- [Fizetés menüpont megvalósítása](#Fizetés-menüpont-megvalósítása)

## Vezetett rész

### Projekt létrehozása

Első lépésként indítsuk el az Android Studio-t, majd:

1. Hozzunk létre egy új projektet, válasszuk az *Add No Activity* lehetőséget.
2. A projekt neve legyen `WorkplaceApp`, a kezdő package pedig `hu.bme.aut.workplaceapp`
3. Nyelvnek válasszuk a *Kotlin*-t.
4. A minimum API szint legyen API21: Android 5.0.
5. Az instant app támogatást, valamint a *Use legacy android.support libraries* pontot **ne** pipáljuk be.

Az első Activity-nk legyen egy Empty Activity, és nevezzük el `MenuActivity`-nek (app-on jobb gomb, New -> Activity -> Empty Activity). A hozzá tartozó layout fájl automatikusan megkapja az `activity_menu.xml` nevet.

Előzetesen töltsük le az alkalmazás képeit tartalmazó [tömörített fájlt](./downloads/res.zip) és bontsuk ki. A benne lévő drawable könyvtárat másoljuk be az app/src/main/res mappába (Studio-ban res mappán állva `Ctrl+V`).

### Főmenü képernyő

Az első Activity amit elkészítünk a navigációért lesz felelős. A labor során 2 funkciót fogunk megvalósítani, ezek a Profil és a Szabadság.

A projekt készítésekor már létrejött `activity_menu.xml` tartalmát cseréljük ki az alábbira:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_margin="16dp"
    android:gravity="center"
    android:orientation="vertical">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <FrameLayout
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1">

        </FrameLayout>

        <FrameLayout
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1">

        </FrameLayout>
    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <FrameLayout
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1">

        </FrameLayout>

        <FrameLayout
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1">

        </FrameLayout>

    </LinearLayout>

</LinearLayout>
```

Egy függőleges LinearLayout-ba tettünk bele 2 vízszintes LinearLayout-ot, mindkettő 2 gombot fog tartalmazni. Súlyozás segítségével 2 részre osztottuk a vízszintes LinearLayout-okat.
A gombon a háttér és a felirat elhelyezéséhez a korábbi laboron már látotthoz hasonlóan FrameLayout-ot fogunk használni.

Az első gombot például így készíthetjük el (a `FrameLayout` tagbe írjuk):
```xml
<ImageButton
    android:id="@+id/btnProfile"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:adjustViewBounds="true"
    android:scaleType="fitCenter"
    android:src="@drawable/profile" />

<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_gravity="center"
    android:text="Profile"
    android:textSize="34sp" />
```

A további 3 gombot ennek a mintájára készítsük el ezekkel az értékekkel:

| Szöveg | ID | Kép |
| -- | -- | -- |
| Holiday | `@+id/btnHoliday` | `@drawable/holiday` |
| Payment | `@+id/btnPayment` | `@drawable/payment` |
| Cafeteria | `@+id/btnCafeteria` | `@drawable/cafeteria` |

Ne felejtsük el a szövegeket kiszervezni erőforrásba! (a szövegen állva `Alt+Enter`)

Hozzunk létre a két új Empty Activity-t (`ProfileActivity` és `HolidayActivity`)

A MenuActivity fájljában (`MenuActivity.kt`) rendeljünk a gombok lenyomásához eseménykezelőt az onCreate metódusban:

```kotlin
btnProfile.setOnClickListener {
            val profileIntent = Intent(this, ProfileActivity::class.java)
            startActivity(profileIntent)
        }
        
btnHoliday.setOnClickListener {
            val holidayIntent = Intent(this, HolidayActivity::class.java)
            startActivity(holidayIntent)
        } });
```

Mivel az Activityt kézzel hoztuk létre, így az első futtatás előtt meg kell adnunk az `AndroidManifest.xml` file-ban, hogy mi legyen az alkalmazás belépési pontja.

```xml
…
<activity android:name=".MenuActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
</activity>
```

Próbáljuk ki az alkalmazást! 4 gombnak kell megjelennie és a felső kettőn működnie kell a navigációnak a (még) üres Activity-kbe.

### Profil képernyő

A Profil képernyő két lapozható oldalból fog állni, ezen a név, email, lakcím (első oldal), illetve a személyigazolvány szám, TAJ szám, adószám és törzsszám (második oldal) fognak megjelenni.

Hozzunk létre egy `data` package-et (jobb egérgomb hu.bme.aut.workplaceapp -> New -> Package), azon belül egy `Person` osztályt (New -> Kotlin File/Class -> Kind: Class), ebben fogjuk tárolni az oldalakon megjelenő adatokat:

```kotlin
class Person(
    val name: String,
    val email: String,
    val address: String,
    val id: String,
    val socialSecurityNumber: String,
    val taxId: String,
    val registrationId: String
)
```

A Person osztály példányának elérésére hozzunk létre egy `DataManager` osztályt (szintén a `data` package-en belül), ezzel fogjuk szimulálni a valós adatelérést. Ehhez a Singleton mintát használunk, hogy az alkalmazás minden részéből egyszerűen elérhető legyen. A Person példányt rögtön létre is hozzuk.

> Kotlinban nyelvi szintű támogatás van a singletonok létrehozására. Ahelyett, hogy nekünk kéne egyetlen statikus példányt felvennünk, elég csak a `class` kulcsszó helyett az [`object`](https://kotlinlang.org/docs/reference/object-declarations.html#object-declarations) kulcsszóval létrehoznunk az osztályt hogy egy singletont kapjunk.

```kotlin
object DataManager {
    val person: Person = Person(
        "Test User", "testuser@domain.com",
        "1234 Test, Random Street 1.",
        "123456AB",
        "123456789",
        "1234567890",
        "0123456789"
    )
}
```

Ezután elkészíthetjük a két oldalt, Fragmentekkel. Hozzuk létre egy új `fragments` package-ben a két Fragmentet (New -> Kotlin File/Class -> Kind: Class), ezek neve legyen `MainProfileFragment` és `DetailsProfileFragment`.

A két Fragmentben származzunk le a Fragment osztályból (androidx-es verziót válasszuk) és definiáljuk felül az onCreateView metódust. Ebben töltsük be a layout-ot, majd az onViewCreated metódusban a Person objektum adatait írjuk ki a TextView-kra.

`MainProfileFragment.kt`:
```kotlin
class MainProfileFragment: Fragment() {

    override fun onCreateView(inflater: LayoutInflater,
                              container: ViewGroup?,
                              savedInstanceState: Bundle?
    ): View? {
        return inflater.inflate(hu.bme.aut.workplaceapp.R.layout.profile_main, container, false)
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        val person: Person = DataManager.person

        tvName.text = person.name
        tvEmail.text = person.email
        tvAddress.text = person.address
    }
}
```

`DetailsProfileFragment.kt`:
```kotlin
class DetailsProfileFragment: Fragment() {

    override fun onCreateView(inflater: LayoutInflater,
                              container: ViewGroup?,
                              savedInstanceState: Bundle?
    ): View? {
        return inflater.inflate(R.layout.profile_detail, container, false)
    }
    
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        val person: Person = DataManager.person

        tvId.text = person.id
        tvSSN.text = person.socialSecurityNumber
        tvTaxId.text = person.taxId
        tvRegistrationId.text = person.registrationId
    }
}
```

Készítsük el a megfelelő layout-okat a Fragmentekhez (`R.layout.profile_main` és `R.layout.profile_detail`-en állva `Alt+Enter`).

`profile_main.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Name:"
        android:textAllCaps="true"
        android:textSize="20sp" />

    <TextView
        android:id="@+id/tvName"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="20dp"
        android:textColor="#000000"
        android:textSize="34sp" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Email:"
        android:textAllCaps="true"
        android:textSize="20sp" />

    <TextView
        android:id="@+id/tvEmail"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="20dp"
        android:textColor="#000000"
        android:textSize="34sp" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Address:"
        android:textAllCaps="true"
        android:textSize="20sp" />

    <TextView
        android:id="@+id/tvAddress"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="20dp"
        android:textColor="#000000"
        android:textSize="34sp" />

</LinearLayout>
```

`profile_detail.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="ID:"
        android:textAllCaps="true"
        android:textSize="20sp" />

    <TextView
        android:id="@+id/tvId"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="20dp"
        android:textColor="#000000"
        android:textSize="34sp" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Social Security ID:"
        android:textAllCaps="true"
        android:textSize="20sp" />

    <TextView
        android:id="@+id/tvSSN"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="20dp"
        android:textColor="#000000"
        android:textSize="34sp" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Tax ID:"
        android:textAllCaps="true"
        android:textSize="20sp" />

    <TextView
        android:id="@+id/tvTaxId"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="20dp"
        android:textColor="#000000"
        android:textSize="34sp" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Registration ID:"
        android:textAllCaps="true"
        android:textSize="20sp" />

    <TextView
        android:id="@+id/tvRegistrationId"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="20dp"
        android:textColor="#000000"
        android:textSize="34sp" />

</LinearLayout>
```

(Szervezzük ki a szövegeket erőforrásba)

Már csak a lapozás megvalósítása maradt hátra, ezt a ViewPager osztállyal fogjuk megvalósítani.

A korábban használt ViewPager elavult. Azóta ezt egy új ViewPager2-vel helyettesítették. Ennek a használatához szükségünk van a hozzá tartozó könyvtárakra. Ehhez az app szintű build.gradle-t módosítsuk:

```groovy
dependencies {
    ...
    implementation "com.google.android.material:material:1.1.0-beta01"
}
```

Ezután kattinsunk az Android Studioban jobb fent megjelenő `Sync Now` feliratra vagy a fejlécen szereplő mérges gradle elefánt gombra, hogy a library fájljai letöltődjenek.

Az `activity_profile.xml` fájlba hozzunk létre egy `ViewPager`-t:
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context="hu.bme.aut.workplaceapp.ProfileActivity">

    <androidx.viewpager2.widget.ViewPager2
        android:id="@+id/vpProfile"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
```

A ViewPager osztály egy FragmentStateAdapter osztály segítségével tudja az oldalakat létrehozni. Hozzunk létre egy új `adapter` package-be egy ProfilePagerAdaptert a két Fragmentünkhöz. 

`ProfilePagerAdapter.kt`:
```kotlin
class ProfilePagerAdapter(fa: FragmentActivity): FragmentStateAdapter(fa) {
    companion object {
        private const val NUM_PAGES = 2
    }

    override fun createFragment(position: Int): Fragment {
        return when(position){
            0 -> MainProfileFragment()
            1 -> DetailsProfileFragment()
            else -> MainProfileFragment ()
        }
    }

    override fun getItemCount(): Int = NUM_PAGES
}
```

A ProfileActivity-ben rendeljük hozzá a ViewPagerhez a most elkészített adaptert (onCreate metódus): 
```kotlin
vpProfile.adapter = ProfilePagerAdapter(this)
```

Próbáljuk ki az alkalmazást. A Profile gombra kattinva megjelennek a felhasználó adatai és lehet lapozni is.

### Szabadság képernyő

A Szabadság képernyőn egy kördiagramot fogunk megjeleníteni, ami mutatja, hogy mennyi szabadságot vettünk már ki és mennyi maradt. Ezen kívül egy gomb segítségével új szabadnap kivételét is megengedjük a felhasználónak.

Először egészítsük ki a DataManager osztályunkat, hogy kezelje a szabadsághoz kapcsolódó adatokat is:
```kotlin
private const val HOLIDAY_MAX_VALUE = 20
private const val HOLIDAY_DEFAULT_VALUE = 15

var holidays = HOLIDAY_DEFAULT_VALUE
…
fun getRemainingHolidays(): Int = HOLIDAY_MAX_VALUE - holidays
```

A PieChart kirajzolásához az [MPAndroidChart](https://github.com/PhilJay/MPAndroidChart) library-t fogjuk használni.

Projekt szintű build.gradle:
```groovy
allprojects {
    repositories {
        ...
        maven { url "https://jitpack.io" }
    }
}
```

App szintű build.gradle:
```groovy
dependencies {
    ...
    implementation 'com.github.PhilJay:MPAndroidChart:v3.0.3'
}
```

Ezután kattinsunk a `Sync Now` feliratra.

Ha a library fájljai letöltődtek, akkor írjuk meg az Activity layout-ját (`activity_holiday.xml`):
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context="hu.bme.aut.workplaceapp.HolidayActivity">

    <com.github.mikephil.charting.charts.PieChart
        android:id="@+id/chartHoliday"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1" />

    <Button
        android:id="@+id/btnTakeHoliday"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Take Holiday"
        android:layout_gravity="center" />

</LinearLayout>
```

Írjuk meg az Activity kódját (`HolidayActivity.kt`):
```kotlin
class HolidayActivity : AppCompatActivity() {

override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_holiday)

        btnTakeHoliday.setOnClickListener {
                // TODO: DatePickerDialogFragment megjelenítése
        }

        loadHolidays()
    }

private fun loadHolidays() {
        var entries: ArrayList<PieEntry> = ArrayList()

        entries.add(PieEntry(DataManager.holidays.toFloat(), "Taken"))
        entries.add(PieEntry(DataManager.getRemainingHolidays().toFloat(), "Remaining"))

        val dataSet = PieDataSet(entries, "Holidays")
        dataSet.setColors(*ColorTemplate.MATERIAL_COLORS)

        val data = PieData(dataSet)
        chartHoliday.data = data
        chartHoliday.invalidate()

    }
}
```

Próbáljuk ki az alkalmazást! A PieChart most már megjelenik, de a gomb még nem kell, hogy működjön.

### Dátumválasztó, napok csökkentése

A következő lépésben a Take Holiday gombra megjelenő dátumválasztó működését fogjuk megvalósítani. A gomb lenyomására megjelenik egy dátumválasztó és a dátum kiválasztása után a szabad napok eggyel csökkennek.

Hozzunk létre egy DatePickerDialogFragment osztályt:
```kotlin
class DatePickerDialogFragment: DialogFragment(), DatePickerDialog.OnDateSetListener {

    private lateinit var onDateSelectedListener: OnDateSelectedListener

    override fun onAttach(context: Context?) {
        super.onAttach(context)

        try{
            onDateSelectedListener = if (targetFragment != null){
                targetFragment as OnDateSelectedListener
            } else {
                activity as OnDateSelectedListener
            }
        } catch ( e: ClassCastException){
            throw RuntimeException(e)
        }
    }

    override fun onCreateDialog(savedInstanceState: Bundle?): Dialog {
        var c = Calendar.getInstance()
        var year = c.get(Calendar.YEAR)
        var month = c.get(Calendar.MONTH)
        var day = c.get(Calendar.DAY_OF_MONTH)

            return DatePickerDialog(
            activity!!, this,
            year, month, day
        )
    }

    override fun onDateSet(view: DatePicker?, year: Int, month: Int, day: Int) {
        onDateSelectedListener.onDateSelected(year, month, day)
    }

    interface OnDateSelectedListener {
        fun onDateSelected(year: Int, month: Int, day: Int)
    }
}
```

Az importoknál a `java.util`-t válasszuk a Calendarhoz, a Fragment-hez pedig az `androidx`-es verziót.

A laborvezetővel vizsgáljuk meg az `OnDateSelectedListener` interface működését. Az osztályt használóknak ezt az interface-t kell implementálnia és a megvalósított `onDateSelected` metódus kapja meg a dátumot.

Állítsuk be a gomb eseménykezelőjét a HolidayActivity-ben, hogy lenyomáskor jelenítse meg a dátumválasztót:
```kotlin
btnTakeHoliday.setOnClickListener {
            DatePickerDialogFragment().show(supportFragmentManager, "DATE_TAG")
        }
```

A kiválasztott dátum feldolgozásához implementáljuk az OnDateSelectedListener-t a HolidayActivity-ben:
```kotlin
class HolidayActivity: AppCompatActivity(), 
DatePickerDialogFragment.OnDateSelectedListener {
...

override fun onDateSelected(year: Int, month: Int, day: Int) {
        var numHolidays = DataManager.holidays

        if (DataManager.getRemainingHolidays() > 0) {
            DataManager.holidays = numHolidays + 1
        }
        
        // Update chart
        loadHolidays()
    }
```

Próbáljuk ki az alkalmazást! Most már a gomb is jól kell, hogy működjön, a napok számának is csökkennie kell a diagramon.


## Önálló feladat
### Szabadság továbbfejlesztése

- Csak akkor engedjünk új szabadságot kivenni, ha a kiválasztott nap a mai napnál későbbi. (Érdemes a DatePickerDialog osztálynak utánanézni)
- Ha elfogyott a szabadságkeretünk, akkor a Take Holiday gomb legyen letiltva.


## Bónusz feladat
### Fizetés menüpont megvalósítása

A Payment menüpontra kattintva jelenjen meg egy PaymentActivity rajta egy ViewPager-rel és 2 Fragment-tel (A Profile menüponthoz hasonlóan):
- `PaymentTaxesFragment`: kördiagram, aminek a közepébe van írva az aktuális fizetés és mutatja a nettó jövedelmet illetve a levont adókat (adónként külön)
- `MonthlyPaymentFragment`: egy oszlopdiagramot mutasson 12 oszloppal, a havi szinten lebontott fizetéseket mutatva - érdemes az adatokat itt is a DataManager osztályban tárolni

[Segítség](https://github.com/PhilJay/MPAndroidChart/wiki)
