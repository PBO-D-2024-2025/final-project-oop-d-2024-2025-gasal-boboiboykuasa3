[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/4ZAJL3PP)
# Laporan Akhir Final Project OOP D

## 1. Informasi Umum
* **Nama Game**: Gosong Cooking
* **Anggota Kelompok**:
  
    1. Nixon Castroman - 5025231024
    2. Dustin Felix - 5025231046
    3. Daffano Abyan Zakyanto Azmy- 5025231249
       
* **Tech Stack**: [C#, Unity]

## 2. Deskripsi Game

### 2.1 Konsep Game
* **Genre**: Cooking
* **Gameplay/Rule**: <br>

Game ini menawarkan **Single Player** Mode.
Pemain bertugas untuk memasak hidangan berdasarkan resep yang telah ditentukan. Pemain harus memotong bahan, memasak di alat masak tertentu (seperti kompor), dan menyajikan makanan dengan urutan yang benar untuk mendapatkan poin.
<br>**Scoring:** Poin diberikan berdasarkan jumlah hidangan yang berhasil dimasak dan disajikan dengan benar. <br>
<br>**Recipe System:** <br>
    Setiap hidangan memiliki resep unik yang terdiri dari bahan-bahan tertentu dan langkah-langkah spesifik.

* **Objective**: Tujuan utama dalam game ini adalah untuk memasak dan menyajikan sebanyak mungkin hidangan berdasarkan resep yang diberikan sebelum waktu habis. <br>

* **Single/Multi Player**: Single Player

### 2.2 Fitur Utama
1. Cooking (Cutting, Frying and Assembling the Ingredients)
2. Delivery

## 3. Implementasi Fitur Wajib

### 3.1 Save/Load System
* **Implementasi**:
  Memberikan Highest Score
* **Konsep OOP**:<br>
  - Encapsulation <br>
    Detail implementasi seperti pengaturan teks untuk tampilan skor akhir dan skor tertinggi `(recipesDeliveredText, highScoreText)` hanya dapat diakses di dalam kelas. Metode seperti `Show` dan `Hide` juga 
    digunakan untuk mengatur visibilitas UI tanpa mengekspos detail implementasi ke luar.
    ```
    [SerializeField] private TextMeshProUGUI recipesDeliveredText;
    [SerializeField] private TextMeshProUGUI highScoreText;
    
    private void Show()
    {
        gameObject.SetActive(true);
    }
    
    private void Hide()
    {
        gameObject.SetActive(false);
    }
    ```
  - Abstraction <br>
   Kelas ini menyembunyikan logika bagaimana data skor saat ini `(currentScore)` diperoleh dari `DeliveryManager` atau bagaimana skor tertinggi dikelola menggunakan `PlayerPrefs`. Pengembang hanya berinteraksi 
   dengan kelas ini melalui event `KitchenGameManager_OnStateChanged`.
   ```
    int currentScore = DeliveryManager.Instance.GetSuccessfulRecipesAmount();
    int highScore = PlayerPrefs.GetInt("HighScore", 0);
    
    if (currentScore > highScore)
    {
        highScore = currentScore;
        PlayerPrefs.SetInt("HighScore", highScore);
        PlayerPrefs.Save();
    }

   ```
   
* **Penerapan SOLID**:<br>
  - Single Responsibility Principle (SRP)
    Kelas ini hanya bertanggung jawab untuk mengatur tampilan layar akhir permainan. Hal ini mencakup menampilkan skor saat ini dan skor tertinggi, serta mengatur visibilitas layar.
    Logika penghitungan skor dan manajemen state game ditangani oleh `DeliveryManager` dan `KitchenGameManager`.
  
* **Code Snippet**:
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using TMPro;

public class GameOverUI : MonoBehaviour
{
    [SerializeField] private TextMeshProUGUI recipesDeliveredText;
    [SerializeField] private TextMeshProUGUI highScoreText;

    private void Start()
    {
        KitchenGameManager.Instance.OnStateChanged += KitchenGameManager_OnStateChanged;

        Hide();

    }

    private void KitchenGameManager_OnStateChanged(object sender, System.EventArgs e)
    {
        if (KitchenGameManager.Instance.IsGameOver())
        {
            Show();
            int currentScore = DeliveryManager.Instance.GetSuccessfulRecipesAmount();
            recipesDeliveredText.text = currentScore.ToString();

            
            int highScore = PlayerPrefs.GetInt("HighScore",0);

           
            if (currentScore > highScore)
            {
                highScore = currentScore;
                PlayerPrefs.SetInt("HighScore", highScore);
                PlayerPrefs.Save();
            }

            
            highScoreText.text = highScore.ToString();

        }
        else
        {
            Hide();
        }
    }

    private void Show()
    {
        gameObject.SetActive(true);
    }

    private void Hide()
    {
        gameObject.SetActive(false);
    }
}

```

### 3.2 Achievement System
* **Jenis Achievement**:
    1. `CUPU` : Berhasil menyelesaikan 2 resep sebelum waktu habis
    2. `B AJA` : Berhasil menyelesaikan 4 resep sebelum waktu habis
    3. `DEWA` : Berhasil menyelesaikan 6 resep sebelum waktu habis
* **Konsep OOP**:<br>
  - Encapsulation
    Data dan perilaku terkait pencapaian `(achievement)` disatukan dalam satu kelas `AchievementUI`.
    Variabel seperti `achievementPanel, achievementText, dan unlockedAchievements` bersifat privat atau hanya diakses melalui metode tertentu, menjaga integritas data.
    ```
    [SerializeField] private GameObject achievementPanel; 
    [SerializeField] private TextMeshProUGUI achievementText; 
    private HashSet<string> unlockedAchievements = new HashSet<string>();
    ```

* **Penerapan SOLID**:
  - Single Responsibility Principle (SRP):
  Kelas `AchievementUI` hanya bertanggung jawab untuk menangani pencapaian (mengecek, menampilkan, menyimpan, dan memuat).
  ```
  public void CheckAchievements(int currentDeliveries) {
    // Mengecek dan menambah achievement berdasarkan jumlah delivery
  }
  
  private void SaveAchievements() {
      // Menyimpan achievement ke PlayerPrefs
  }
  
  private void LoadAchievements() {
      // Memuat achievement dari PlayerPrefs
  }
  ```
  - Open/Closed Principle (OCP):
    Kelas ini terbuka untuk ekstensifikasi tetapi tertutup untuk modifikasi. Misalnya, jika ingin menambahkan jenis `achievement` baru, kita hanya perlu menambah logika di `CheckAchievements` tanpa mengubah          mekanisme lainnya.
  ```
  if (currentDeliveries >= 6 && !unlockedAchievements.Contains("DEWA")) {
    unlockedAchievements.Add("DEWA");
    newUnlockedAchievements.Add("DEWA");
    }
  ```

* **Code Snippet**:
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using TMPro;

public class AchievementUI : MonoBehaviour
{
    [SerializeField] private GameObject achievementPanel;  
    [SerializeField] private TextMeshProUGUI achievementText;  
    [SerializeField] private float displayDuration = 3f; 

    private HashSet<string> unlockedAchievements = new HashSet<string>();  
    private int currentDeliveries = 0;  

    private const string AchievementKey = "UnlockedAchievements";  

    private void Start()
    {
        achievementPanel.SetActive(false); 

        
        LoadAchievements();
        DeliveryManager.Instance.OnRecipeSucces += OnRecipeSucces;
    }

    private void OnRecipeSucces(object sender, System.EventArgs e)
    {
        currentDeliveries = DeliveryManager.Instance.GetSuccessfulRecipesAmount();  
        CheckAchievements(currentDeliveries); 
    }

    
    public void CheckAchievements(int currentDeliveries)
    {
        List<string> newUnlockedAchievements = new List<string>();


        if (currentDeliveries >= 2 && !unlockedAchievements.Contains("CUPU"))
        {
            unlockedAchievements.Add("CUPU");
            newUnlockedAchievements.Add("CUPU");
        }


        if (currentDeliveries >= 4 && !unlockedAchievements.Contains("B AJA"))
        {
            unlockedAchievements.Add("B AJA");
            newUnlockedAchievements.Add("B AJA");
        }


        if (currentDeliveries >= 6 && !unlockedAchievements.Contains("DEWA"))
        {
            unlockedAchievements.Add("DEWA");
            newUnlockedAchievements.Add("DEWA");
        }


        if (newUnlockedAchievements.Count > 0)
        {
            ShowAchievements(newUnlockedAchievements);
            SaveAchievements(); 
        }
    }


    private void ShowAchievements(List<string> newUnlockedAchievements)
    {
        achievementPanel.SetActive(true);
        achievementText.text = string.Join("\n", newUnlockedAchievements);  
        StartCoroutine(HideAchievementPanelAfterDelay());
    }


    private IEnumerator HideAchievementPanelAfterDelay()
    {
        yield return new WaitForSeconds(displayDuration);
        achievementPanel.SetActive(false); 
    }

    // Menyimpan pencapaian yang telah dibuka ke PlayerPrefs
    private void SaveAchievements()
    {
        string achievementsString = string.Join(",", unlockedAchievements);  
        PlayerPrefs.SetString(AchievementKey, achievementsString);  
        PlayerPrefs.Save(); 
    }

    
    private void LoadAchievements()
    {
        if (PlayerPrefs.HasKey(AchievementKey))
        {
            string savedAchievements = PlayerPrefs.GetString(AchievementKey);  
            string[] achievementsArray = savedAchievements.Split(',');  
            foreach (string achievement in achievementsArray)
            {
                unlockedAchievements.Add(achievement);  
            }
        }
    }
}

```

## 4. Implementasi Fitur Lain

### 4.1 Fitur
* **Implementasi**: Cutting 
* **Konsep OOP**:
   - Inheritance<br>
    Kelas `CuttingCounter` mewarisi dari `BaseCounter`. Pewarisan memungkinkan kelas anak `(CuttingCounter)` untuk menggunakan atau menimpa properti dan metode dari kelas induk `(BaseCounter)`.
    ```
    public class CuttingCounter : BaseCounter, IHasProgress
    ```
   - Encapsulation<br>
    Kode ini menggunakan access modifiers seperti `private` dan `public` untuk mengontrol akses terhadap data atau metode. Misalnya:
    Variabel `cuttingRecipeSOArray` dienkapsulasi dengan private untuk mencegah akses langsung dari luar kelas.
    Fungsi `HasRecipeWithInput` membungkus logika pengecekan resep, sehingga detailnya tidak terlihat di luar kelas.
    ```
    [SerializeField] private CuttingRecipeSO[] cuttingRecipeSOArray;
    private int cuttingProgress;
    ```

  - Polymorphism<br>
   Kelas ini menimpa metode `Interact` dan `InteractAlternate` dari kelas induk `BaseCounter`, menunjukkan `overriding`. Ini memungkinkan perilaku yang berbeda untuk metode yang sama di kelas anak.
   ```
    public override void Interact(Player player)
    {
    
    }
    
    public override void InteractAlternate(Player player)
    {
    
    }

   ```

 - Abstraction<br>
  Kelas ini menyembunyikan detail implementasi dari logika kompleks, seperti logika untuk memeriksa resep `(HasRecipeWithInput)` dan mendapatkan output resep `(GetOutputForInput)`.
  ```
  private bool HasRecipeWithInput(KitchenObjectSO inputKitchenObjectSO)
    {
    CuttingRecipeSO cuttingRecipeSO = GetCuttingRecipeSOWithInput(inputKitchenObjectSO);
    return cuttingRecipeSO != null;
    }

    private KitchenObjectSO GetOutputForInput(KitchenObjectSO inputKitchenObjectSO)
    {
    CuttingRecipeSO cuttingRecipeSO = GetCuttingRecipeSOWithInput(inputKitchenObjectSO);
    return cuttingRecipeSO != null ? cuttingRecipeSO.output : null;
    }
  ```
* **Penerapan SOLID**:
  - **Single Responsibility Principle (SRP):**
  Kelas `CuttingCounter` hanya bertanggung jawab untuk menangani logika pemotongan objek dapur `(kitchen object)`. Tugasnya terbatas pada proses interaksi terkait pemotongan dan progresnya, tanpa       
  mencampuradukkan tanggung jawab lain seperti memasak atau penyimpanan.
  - **Open/Closed Principle (OCP):**
  `CuttingCounter` mendukung ekspansi melalui penggunaan `ScriptableObject (CuttingRecipeSO)`. Menambahkan resep baru tidak memerlukan perubahan pada kode kelas ini, cukup dengan menambahkan objek baru ke daftar 
  resep.

### 4.2 Fitur 2
* **Implementasi**: Throwing Trash
* **Konsep OOP**:
  - Inheritance<br>
    Kelas `TrashCounter` mewarisi dari `BaseCounter`, yang memungkinkan `TrashCounter` untuk menggunakan metode atau properti dari kelas induknya, termasuk metode `Interact`.
    ```
    public class TrashCounter : BaseCounter
    ```
  - Abstraction<br>
    Kelas ini menyembunyikan detail implementasi terkait dengan cara objek dihancurkan `(DestroySelf)` atau cara event `OnAnyObjectTrashed` dipanggil. Pemain hanya perlu memanggil `Interact`, tanpa mengetahui 
    detail teknis.
    ```
    player.GetKitchenObject().DestroySelf();
    OnAnyObjectTrashed?.Invoke(this, EventArgs.Empty);
    ```
  - Encapsulation<br>
    Data atau logika seperti event `OnAnyObjectTrashed` dienkapsulasi, sehingga hanya bisa diakses melalui kelas ini. Selain itu, metode `ResetStaticData` digunakan untuk mengatur ulang event secara aman.
    ```
    public static event EventHandler OnAnyObjectTrashed;

    new public static void ResetStaticData()
    {
        OnAnyObjectTrashed = null;
    }
    ```
  - Polymorphism<br>
    Metode `Interact` dari `BaseCounter` ditimpa (overridden) untuk memberikan perilaku spesifik bagi `TrashCounter`, yaitu menghancurkan objek yang dipegang oleh pemain.
    ```
    public override void Interact(Player player)
    {
    if (player.HasKitchenObject())
    {
        player.GetKitchenObject().DestroySelf();

        OnAnyObjectTrashed?.Invoke(this, EventArgs.Empty);
    }
    }
    ```
  * **Penerapan SOLID**:
    - **Dependency Inversion Principle (DIP):**
      `TrashCounter` bergantung pada abstraksi seperti `KitchenObject` tanpa mengikat diri pada detail implementasi.
    - **Single Responsibility Principle (SRP):**
      `TrashCounter` hanya menangani logika pembuangan objek dapur, memastikan kelas ini tetap fokus dan tidak memiliki tanggung jawab lain seperti memasak atau penyajian.
      
### 4.3 Fitur 3
* **Implementasi**: Delivery
* **Konsep OOP**:
  - Encapsulation<br>
    Properti `Instance` dienkapsulasi sebagai properti public dengan pengaturan hanya dapat dilakukan secara `privat`. Hal ini memastikan bahwa hanya satu instance `DeliveryCounter` yang ada pada waktu tertentu.
    ```
    public static DeliveryCounter Instance { get; private set; }
    ```
  - Polymorphism<br>
    Metode `Interact` dari `BaseCounter` ditimpa untuk memberikan logika khusus pada `DeliveryCounter`. Saat pemain berinteraksi, game memeriksa apakah pemain membawa sesuatu, dan jika iya, memproses pengiriman resep.
    ```
    public override void Interact(Player player)
    {
        if (player.HasKitchenObject())
        {
            if (player.GetKitchenObject().TryGetPlate(out PlateKitchenObject plateKitchenObject))
            {
                DeliveryManager.Instance.DeliverRecipe(plateKitchenObject);
                player.GetKitchenObject().DestroySelf();
            }
        }
    }
    ```
  - Inheritance<br>
    Kelas `DeliveryCounter` mewarisi `BaseCounter`, memungkinkan untuk menggunakan metode dan properti bawaan seperti `Interact`.
    ```
    public class DeliveryCounter : BaseCounter
    ```
  - Abstraction<br>
    Kode ini menyembunyikan detail teknis proses pengiriman resep. Pemain hanya perlu memanggil `Interact` tanpa mengetahui bagaimana `DeliveryManager` bekerja.
    ```
    DeliveryManager.Instance.DeliverRecipe(plateKitchenObject);
    ```
* **Penerapan SOLID**:
  - **Single Responsibility Principle (SRP):**
    `DeliveryCounter` hanya bertugas menangani logika penyampaian `(delivery)` makanan ke sistem manajer pengantaran `(DeliveryManager)`. Ini memastikan kelas tetap fokus pada satu tanggung jawab.
  - **Interface Segregation Principle (ISP):**
    Tidak ada antarmuka tambahan yang diterapkan di luar kebutuhan pengantaran. Semua fungsi relevan dengan tujuan kelas.
  - **Dependency Inversion Principle (DIP):**
    `DeliveryCounter` berinteraksi dengan `DeliveryManager` melalui abstraksi, memudahkan penggantian atau perubahan dalam sistem manajemen pengantaran.
    
### 4.4 Fitur 4
* **Implementasi**: Frying
* **Konsep OOP**:
  - Inheritance<br>
    Kelas `StoveCounter` mewarisi dari `BaseCounter`, memungkinkan `StoveCounter` untuk menggunakan metode atau properti dari kelas induknya, seperti `Interact`.
    ```
    public class StoveCounter : BaseCounter, IHasProgress
    ```
  - Encapsulation<br>
    Kode ini menjaga detail implementasi dengan menggunakan variabel privat `(private)` dan properti serialized `([SerializeField])` untuk pengelolaan data resep dan state internal.
    ```
    [SerializeField] private FryingRecipeSO[] fryingRecipeSOArray;
    [SerializeField] private BurningRecipeSO[] burningRecipeSOArray;
    
    private State state;
    private float fryingTimer;
    private float burningTimer;
    private FryingRecipeSO fryingRecipeSO;
    private BurningRecipeSO burningRecipeSO;
    ```
  - Polymorphism<br>
    Kelas ini menimpa metode `Interact` dari `BaseCounter` untuk memberikan perilaku spesifik saat pemain berinteraksi dengan `StoveCounter`.
    ```
    public override void Interact(Player player)
    {
        if (!HasKitchenObject())
        {
        }
        else
        {
        }
    }
    ```
  - Abstraction<br>
   Fungsi seperti `HasRecipeWithInput, GetFryingRecipeSOWithInput, dan GetBurningRecipeSOWithInput` menyembunyikan detail implementasi terkait pemeriksaan atau pencarian resep, sehingga membuat kode lebih bersih.
   ```
    private bool HasRecipeWithInput(KitchenObjectSO inputKitchenObjectSO)
    {
        FryingRecipeSO fryingRecipeSO = GetFryingRecipeSOWithInput(inputKitchenObjectSO);
        return fryingRecipeSO != null;
    }
    
    private FryingRecipeSO GetFryingRecipeSOWithInput(KitchenObjectSO inputKitchenObjectSO)
    {
        foreach (FryingRecipeSO fryingRecipeSO in fryingRecipeSOArray)
        {
            if (fryingRecipeSO.input == inputKitchenObjectSO)
            {
                return fryingRecipeSO;
            }
        }
        return null;
    }
   ```
* **Penerapan SOLID**:
  - **Single Responsibility Principle (SRP):**
    `StoveCounter` bertanggung jawab atas logika memasak objek dapur. Semua tanggung jawab terkait penggorengan dipisahkan dari fitur lain seperti pemotongan atau penyajian.

  - **Open/Closed Principle (OCP):**
    Sistem mendukung penambahan resep memasak atau pembakaran baru dengan memperluas `ScriptableObject (FryingRecipeSO dan BurningRecipeSO)`, tanpa mengubah kode inti.
### 4.5 Fitur 5
* **Implementasi**: Plate Spawning
* **Konsep OOP**:
  - Inheritance<br>
    `PlatesCounter` mewarisi kelas `BaseCounter`. Dengan pewarisan ini, `PlatesCounter` mendapatkan metode `Interact` dari `BaseCounter`, yang kemudian ditimpa dengan implementasi khusus untuk spawn dan pengambilan piring.
    ```
    public class PlatesCounter : BaseCounter
    ```
  - Encapsulation<br>
    Variabel seperti `platesSpawnedAmount, spawnPlateTimer, dan spawnPlateTimerMax` dienkapsulasi di dalam kelas dan tidak diakses langsung dari luar. Mereka dikelola secara internal untuk mengontrol logika spawn dan jumlah maksimum piring yang dihasilkan.
    ```
    private float spawnPlateTimer;
    private float spawnPlateTimerMax = 4f;
    private int platesSpawnedAmount;
    private int platesSpawnedAmountMax = 4;
    ```
  - Polymorphism<br>
    Metode `Interact` dari kelas induk `BaseCounter` ditimpa (overridden) untuk menangani interaksi khusus, yaitu pengambilan piring oleh pemain. Implementasi ini memungkinkan PlatesCounter memiliki perilaku unik dibandingkan kelas lain yang juga mewarisi `BaseCounter`.
    ```
    public override void Interact(Player player)
    {
        if (!player.HasKitchenObject())
        {
            if (platesSpawnedAmount > 0)
            {
                platesSpawnedAmount--;
                KitchenObject.SpawnKitchenObject(plateKitchenObjectSO, player);
    
                OnPlateRemoved?.Invoke(this, EventArgs.Empty);
            }
        }
    }

    ```
  - Abstraction<br>

    Detail seperti timer untuk spawn piring dan event handling disembunyikan dari pengguna luar. Pemain hanya berinteraksi dengan Interact, tanpa mengetahui detail bagaimana piring di-spawn atau dihapus.
    ```
    spawnPlateTimer += Time.deltaTime;
    if (spawnPlateTimer > spawnPlateTimerMax)
    {
        spawnPlateTimer = 0f;
        if (platesSpawnedAmount < platesSpawnedAmountMax)
        {
            platesSpawnedAmount++;
            OnPlateSpawned?.Invoke(this, EventArgs.Empty);
        }
    }
    ```
* **Penerapan SOLID**:
  - **Single Responsibility Principle (SRP):**
    `PlatesCounter` bertanggung jawab mengelola logika pemunculan dan pengambilan piring, tanpa mencampurkan fungsionalitas lain seperti memasak atau penyajian.
  - **Open/Closed Principle (OCP):**
    Sistem ini dapat diperluas, misalnya dengan menambahkan variasi jenis piring yang dihasilkan melalui pengaturan `KitchenObjectSO`, tanpa memodifikasi kode inti.


### 4.5 Fitur 5
* **Implementasi**: Plate Spawning
* **Konsep OOP**:
  - Inheritance <br>
    Kelas `ContainerCounter` mewarisi `BaseCounter`, memungkinkan untuk menggunakan metode dan properti dari kelas induk seperti `Interact`.
    ```
    public class ContainerCounter : BaseCounter
    ```
  - Polymorphism <br>
    Metode `Interact` dari `BaseCounter` ditimpa `(overridden)` untuk memberikan logika spesifik bagi `ContainerCounter`. Ketika pemain berinteraksi, objek akan "diberikan" ke pemain jika        pemain tidak memegang objek apapun.
    ```
    public override void Interact(Player player)
    {
        if (!player.HasKitchenObject())
        {
            KitchenObject.SpawnKitchenObject(kitchenObjectSO, player);
            OnPlayerGrabbedObject?.Invoke(this, EventArgs.Empty);
        }
    }
    ```

## 5. Screenshot dan Demo
* **Screenshot 1**: Main Menu <br>
![WhatsApp Image 2024-12-10 at 23 16 04_5f9a0434](https://github.com/user-attachments/assets/335370b4-fe10-4b3e-9435-eb4aaf98a680)

* **Screenshot 2**: Main Game <br>
![WhatsApp Image 2024-12-10 at 23 16 22_b7ec0948](https://github.com/user-attachments/assets/d49c7d0a-e586-4c57-9deb-5d44ab0f160b)

* **Screenshot 3**: Frying <br>
![WhatsApp Image 2024-12-10 at 23 17 53_6b730652](https://github.com/user-attachments/assets/c5224fdd-b380-4173-99ef-6b7f3aedea60)

* **Screenshot 4**: Cutting <br>
![WhatsApp Image 2024-12-10 at 23 17 33_75ea2dd5](https://github.com/user-attachments/assets/ede7f463-3774-45f5-b13d-34e7797ac269)

* **Screenshot 5**: Delivery <br>
![WhatsApp Image 2024-12-10 at 23 18 42_57c14e10](https://github.com/user-attachments/assets/4a51ee2a-5f5e-4611-a30c-08671fa56659)

* **Screenshot 6**: Trash <br>
![WhatsApp Image 2024-12-10 at 23 18 59_bd9c6295](https://github.com/user-attachments/assets/3ed54de4-9031-48c2-9227-c229640e1973)


* **Screenshot 7**: Pause <br>
![WhatsApp Image 2024-12-10 at 23 19 10_85145c8d](https://github.com/user-attachments/assets/e5dba7f4-0d0f-44e0-b89e-c2619b1c5a8c)

* **Screenshot 8**: Achievements <br>
![WhatsApp Image 2024-12-10 at 23 20 08_de11cf10](https://github.com/user-attachments/assets/81f5ee43-079f-4d74-ba59-22fbddaf9cde)

  ![WhatsApp Image 2024-12-10 at 22 53 49_85fc91b6](https://github.com/user-attachments/assets/faafcf27-39e6-4bd9-a6a5-742fe5ff14ae)

* **Link Demo Video**: https://youtu.be/k9vcOy5YI9Q
* **Link Download Game**: https://drive.google.com/file/d/14Q_knJ8NxM0oo7zDZgKD6cikKGHk8jpy/view?usp=sharing

## 6. Panduan Instalasi dan Menjalankan Game
1. Download File Zip melalui link diatas
2. Setelah itu extract filenya
3. Masuk kedalam folder `GosongCooking` dan cari file `GosongCooking.exe`
4. Kemudian Double Click
5. Game akan berjalan

## 7. Kendala dan Solusi
1. **Kendala 1**: Waktu pengerjaan Final Project bertabrakan dengan berbagai tugas akhir lainnya serta Ujian Akhir Semester (EAS) beberapa mata kuliah.
    * Solusi: Mengatur waktu sebaik mungkin <br>
2. **Kendala 2**: Kurangnya pengalaman dalam pembuatan game dan penggunaan game engine.
   * Solusi: Untuk mengatasi kendala ini, kami memanfaatkan berbagai sumber yang tersedia di internet.

## 8. Kesimpulan dan Pembelajaran
* **Kesimpulan**:<br>
  Kami berhasil menyelesaikan proyek Game bertema memasak yang kami beri nama Gosong Cooking. Dalam pengembangannya, kami telah menerapkan berbagai konsep pemrograman berbasis objek (OOP) dan prinsip-prinsip 
  desain perangkat lunak SOLID. Proyek ini memberikan sebuah tantangan teknis, dan juga sebuah perjalanan yang meningkatkan pemahaman kami dalam bidang Pemrograman Berbasis Objek khususnya.
  
  Meskipun tidak lepas dari berbagai kendala dan tantangan, seperti pengelolaan waktu yang kurang optimal serta beberapa hambatan teknis yang harus dihadapi, kami merasa bangga dengan hasil yang telah dicapai 
  (walaupun dirasa belum maksimal). Kendala-kendala tersebut justru menjadi bagian penting dari proses belajar, mendorong kami untuk berpikir kritis dan mencari solusi yang kreatif.

* **Pembelajaran**:<br>
  Salah satu pelajaran berharga yang kami peroleh dari pengalaman ini adalah pentingnya manajemen waktu yang baik. Kami menyadari bahwa mengerjakan proyek dengan jadwal yang mepet dapat memengaruhi kualitas 
  akhir yang dihasilkan. Oleh karena itu, ke depannya kami akan belajar untuk merencanakan waktu dengan lebih matang agar setiap tugas dapat diselesaikan dengan lebih baik dan tanpa tekanan yang berlebihan. 
  Dengan persiapan yang lebih baik, kami yakin hasil yang akan kami capai di masa depan dapat jauh lebih maksimal.

  Selain itu, kami juga belajar bagaimana menerapkan prinsip-prinsip desain perangkat lunak seperti SOLID. Penerapan OOP dalam game ini juga mengajarkan kami bagaimana membuat kode yang bersih, terorganisir, dan 
  terstruktur, sehingga lebih mudah dipahami dan dikelola.

  Kami berharap, dengan semua pengalaman dan pembelajaran yang telah kami peroleh, kami dapat terus berkembang di masa mendatang.
