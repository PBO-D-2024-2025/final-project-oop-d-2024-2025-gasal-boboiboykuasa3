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

  The game offers **Single Player** mode.
<br>**Scoring:** Points are awarded based on the number of dishes completed. <br>
<br>**Recipe System:** <br>
    Each dish has a recipe that includes specific ingredients and steps (e.g., chopped vegetables, fried meat, boiled pasta).

* **Objective**: Cook as much dishes as possible based on the given recipe until the time is up. <br>

* **Single/Multi Player**: Single Player

### 2.2 Fitur Utama
1. Cooking (Cutting, Frying)
2. Recipe Succeed

## 3. Implementasi Fitur Wajib

### 3.1 Save/Load System
* **Implementasi**:
  Memberikan Highest Score
* **Konsep OOP**:<br>
  - Encapsulation <br>
    Detail implementasi seperti pengaturan teks untuk tampilan skor akhir dan skor tertinggi (recipesDeliveredText, highScoreText) hanya dapat diakses di dalam kelas. Metode seperti Show dan Hide juga digunakan     untuk mengatur visibilitas UI tanpa mengekspos detail implementasi ke luar.
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
   Kelas ini menyembunyikan logika bagaimana data skor saat ini (currentScore) diperoleh dari DeliveryManager atau bagaimana skor tertinggi dikelola menggunakan PlayerPrefs. Pengembang hanya berinteraksi dengan     kelas ini melalui event KitchenGameManager_OnStateChanged.
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
    Logika penghitungan skor dan manajemen state game ditangani oleh DeliveryManager dan KitchenGameManager.
  
* **Design Pattern yang Digunakan**:
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
    1. High Score Hard Level: Awarded when the player scores at least 10 points in Hard level.
    2. Multiplayer Score Goal: Awarded when the player scores at least 20 points in Multiplayer mode.
* **Konsep OOP**:<br>
  **Encapsulation**: Each achievement is responsible for managing its own criteria and tracking whether it is unlocked, encapsulating behavior within specific classes.
  <br>**Inheritance**: Different achievements inherit from the Achievement base class, sharing properties and methods while allowing specific criteria implementations.
  <br>**Polymorphism**: By overriding CheckCriteria, each achievement implements custom behavior for checking score-based conditions.
* **Penerapan SOLID**:
 <br> **Open/Closed Principle (OCP)**: New achievements can be added as subclasses without modifying the base class, making the system extensible.
  <br>**Liskov Substitution Principle (LSP)**: Any specific achievement class (e.g., HighScoreHardLevel) can replace the Achievement base class without affecting the AchievementManager.c
  <br>**Interface Segregation Principle (ISP)**: If you were to have separate interfaces for achievements based on score, multiplayer, etc., you would implement only relevant interfaces.
  <br>**Dependency Inversion Principle (DIP)**: The AchievementManager depends on abstractions (the Achievement class) rather than concrete implementations, allowing flexibility.
* **Design Pattern yang Digunakan**:
* **Code Snippet**:
```
public class HighScoreHardLevel : Achievement
{
    public HighScoreHardLevel()
    {
        Name = "High Score in Hard Level";
        Description = "Score at least 10 points in Hard difficulty.";
        ScoreThreshold = 10;
        IsUnlocked = false;
    }

    public override bool CheckCriteria(GameSession session)
    {
        if (session.Difficulty == "Hard" && session.Score >= ScoreThreshold)
        {
            IsUnlocked = true;
        }
        return IsUnlocked;
    }
}

AchievementManager achievementManager = new AchievementManager();
GameSession currentSession = new GameSession("Hard", "Single Player", 10); // Example session with score
achievementManager.EvaluateAchievements(currentSession);

```

## 4. Implementasi Fitur Lain

### 4.1 Fitur
* **Implementasi**: Cutting 
* **Konsep OOP**:
   - Inheritance<br>
    Kelas CuttingCounter mewarisi dari BaseCounter. Pewarisan memungkinkan kelas anak (CuttingCounter) untuk menggunakan atau menimpa properti dan metode dari kelas induk (BaseCounter).
    ```
    public class CuttingCounter : BaseCounter, IHasProgress
    ```
   - Encapsulation<br>
    Kode ini menggunakan access modifiers seperti private dan public untuk mengontrol akses terhadap data atau metode. Misalnya:
    Variabel cuttingRecipeSOArray dienkapsulasi dengan private untuk mencegah akses langsung dari luar kelas.
    Fungsi HasRecipeWithInput membungkus logika pengecekan resep, sehingga detailnya tidak terlihat di luar kelas.
    ```
    [SerializeField] private CuttingRecipeSO[] cuttingRecipeSOArray;
    private int cuttingProgress;
    ```

  - Polymorphism<br>
   Kelas ini menimpa metode Interact dan InteractAlternate dari kelas induk BaseCounter, menunjukkan overriding. Ini memungkinkan perilaku yang berbeda untuk metode yang sama di kelas anak.
   ```
    public override void Interact(Player player)
    {
    
    }
    
    public override void InteractAlternate(Player player)
    {
    
    }

   ```

 - Abstraction<br>
  Kelas ini menyembunyikan detail implementasi dari logika kompleks, seperti logika untuk memeriksa resep (HasRecipeWithInput) dan mendapatkan output resep (GetOutputForInput).
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

### 4.2 Fitur 2
* **Implementasi**: Throwing Trash
* **Konsep OOP**:
  - Inheritance<br>
    Kelas TrashCounter mewarisi dari BaseCounter, yang memungkinkan TrashCounter untuk menggunakan metode atau properti dari kelas induknya, termasuk metode Interact.
    ```
    public class TrashCounter : BaseCounter
    ```
  - Abstraction<br>
    Kelas ini menyembunyikan detail implementasi terkait dengan cara objek dihancurkan (DestroySelf) atau cara event OnAnyObjectTrashed dipanggil. Pemain hanya perlu memanggil Interact, tanpa mengetahui detail       teknis.
    ```
    player.GetKitchenObject().DestroySelf();
    OnAnyObjectTrashed?.Invoke(this, EventArgs.Empty);
    ```
  - Encapsulation<br>
    Data atau logika seperti event OnAnyObjectTrashed dienkapsulasi, sehingga hanya bisa diakses melalui kelas ini. Selain itu, metode ResetStaticData digunakan untuk mengatur ulang event secara aman.
    ```
    public static event EventHandler OnAnyObjectTrashed;

    new public static void ResetStaticData()
    {
        OnAnyObjectTrashed = null;
    }
    ```
  - Polymorphism<br>
    Metode Interact dari BaseCounter ditimpa (overridden) untuk memberikan perilaku spesifik bagi TrashCounter, yaitu menghancurkan objek yang dipegang oleh pemain.
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
### 4.3 Fitur 3
* **Implementasi**: Delivery
* **Konsep OOP**:
  - Encapsulation<br>
    Properti Instance dienkapsulasi sebagai properti public dengan pengaturan hanya dapat dilakukan secara privat. Hal ini memastikan bahwa hanya satu instance DeliveryCounter yang ada pada waktu tertentu.
    ```
    public static DeliveryCounter Instance { get; private set; }
    ```
  - Polymorphism<br>
    Metode Interact dari BaseCounter ditimpa untuk memberikan logika khusus pada DeliveryCounter. Saat pemain berinteraksi, game memeriksa apakah pemain membawa sesuatu, dan jika iya, memproses pengiriman resep.
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
    Kelas DeliveryCounter mewarisi BaseCounter, memungkinkan untuk menggunakan metode dan properti bawaan seperti Interact.
    ```
    public class DeliveryCounter : BaseCounter
    ```
  - Abstraction<br>
    Kode ini menyembunyikan detail teknis proses pengiriman resep. Pemain hanya perlu memanggil Interact tanpa mengetahui bagaimana DeliveryManager bekerja.
    ```
    DeliveryManager.Instance.DeliverRecipe(plateKitchenObject);
    ```

### 4.4 Fitur 4
* **Implementasi**: Frying
* **Konsep OOP**:
  - Inheritance<br>
    Kelas StoveCounter mewarisi dari BaseCounter, memungkinkan StoveCounter untuk menggunakan metode atau properti dari kelas induknya, seperti Interact.
    ```
    public class StoveCounter : BaseCounter, IHasProgress
    ```
  - Encapsulation<br>
    Kode ini menjaga detail implementasi dengan menggunakan variabel privat (private) dan properti serialized ([SerializeField]) untuk pengelolaan data resep dan state internal.
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
    Kelas ini menimpa metode Interact dari BaseCounter untuk memberikan perilaku spesifik saat pemain berinteraksi dengan StoveCounter.
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
   Fungsi seperti HasRecipeWithInput, GetFryingRecipeSOWithInput, dan GetBurningRecipeSOWithInput menyembunyikan detail implementasi terkait pemeriksaan atau pencarian resep, sehingga membuat kode lebih bersih.
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
### 4.5 Fitur 5
* **Implementasi**: Plate Spawning
* **Konsep OOP**:
  - Inheritance<br>
    PlatesCounter mewarisi kelas BaseCounter. Dengan pewarisan ini, PlatesCounter mendapatkan metode Interact dari BaseCounter, yang kemudian ditimpa dengan implementasi khusus untuk spawn dan pengambilan piring.
    ```
    public class PlatesCounter : BaseCounter
    ```
  - Encapsulation<br>
    Variabel seperti platesSpawnedAmount, spawnPlateTimer, dan spawnPlateTimerMax dienkapsulasi di dalam kelas dan tidak diakses langsung dari luar. Mereka dikelola secara internal untuk mengontrol logika spawn dan jumlah maksimum piring yang dihasilkan.
    ```
    private float spawnPlateTimer;
    private float spawnPlateTimerMax = 4f;
    private int platesSpawnedAmount;
    private int platesSpawnedAmountMax = 4;
    ```
  - Polymorphism<br>
    Metode Interact dari kelas induk BaseCounter ditimpa (overridden) untuk menangani interaksi khusus, yaitu pengambilan piring oleh pemain. Implementasi ini memungkinkan PlatesCounter memiliki perilaku unik dibandingkan kelas lain yang juga mewarisi BaseCounter.
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

## 5. Screenshot dan Demo
* **Screenshot 1**: [Deskripsi]
* **Screenshot 2**: [Deskripsi]
* **Link Demo Video**: [URL]

## 6. Panduan Instalasi dan Menjalankan Game
1. Download folder yang diberikan
2. [Langkah 2]
3. [Langkah n]

## 7. Kendala dan Solusi
1. **Kendala 1**: Waktu Final Project bertabrakan dengan berbagai tugas final dan juga EAS matakuliah
    * Solusi: Mengatur waktu sebaik mungkin
2. **Kendala 2**: Kurangnya pengalaman membuat game dan penggunaan game engine
   * Solusi: Mencari di internet.

## 8. Kesimpulan dan Pembelajaran
* **Kesimpulan**:
  Kami berhasil menyelesaikan proyek Game bertema memasak yang kami beri nama Gosong Cooking. Dalam pengembangannya, kami telah menerapkan berbagai konsep pemrograman berbasis objek (OOP) dan prinsip-prinsip desain perangkat lunak SOLID. Proyek ini memberikan sebuah tantangan teknis, dan juga sebuah perjalanan yang meningkatkan pemahaman kami dalam bidang Pemrograman Berbasis Objek khususnya.
  
Meskipun tidak lepas dari berbagai kendala dan tantangan, seperti pengelolaan waktu yang kurang optimal serta beberapa hambatan teknis yang harus dihadapi, kami merasa bangga dengan hasil yang telah dicapai (walaupun dirasa belum maksimal). Kendala-kendala tersebut justru menjadi bagian penting dari proses belajar, mendorong kami untuk berpikir kritis dan mencari solusi yang kreatif.

* **Pembelajaran**:
  Salah satu pelajaran berharga yang kami peroleh dari pengalaman ini adalah pentingnya manajemen waktu yang baik. Kami menyadari bahwa mengerjakan proyek dengan jadwal yang mepet dapat memengaruhi kualitas akhir yang dihasilkan. Oleh karena itu, ke depannya kami akan belajar untuk merencanakan waktu dengan lebih matang agar setiap tugas dapat diselesaikan dengan lebih baik dan tanpa tekanan yang berlebihan. Dengan persiapan yang lebih baik, kami yakin hasil yang akan kami capai di masa depan dapat jauh lebih maksimal.

Selain itu, kami juga belajar bagaimana menerapkan prinsip-prinsip desain perangkat lunak seperti SOLID. Penerapan OOP dalam game ini juga mengajarkan kami bagaimana membuat kode yang bersih, terorganisir, dan terstruktur, sehingga lebih mudah dipahami dan dikelola.

Kami berharap, dengan semua pengalaman dan pembelajaran yang telah kami peroleh, kami dapat terus berkembang di masa mendatang.
