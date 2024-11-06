[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/4ZAJL3PP)
# Laporan Akhir Final Project OOP D

## 1. Informasi Umum
* **Nama Game**: Cooking Duck
* **Anggota Kelompok**:
    1. Nixon Castroman - 5025231024
    2. Dustin Felix - 5025231046
    3. Daffano Abyan Zakyanto Azmy- 5025231249
* **Tech Stack**: [C#, Unity]

## 2. Deskripsi Game

### 2.1 Konsep Game
* **Genre**: Cooking
* **Gameplay/Rule**: <br>

  The game offers **Single Player** and **Multiplayer modes**.
 <br> In **Single Player**, players choose a difficulty level (Easy, Medium, or Hard), which determines recipe complexity and time limits. <br>
**Multiplayer** Mode enables players to work together, with the chance to assign tasks to each player. Collaborative gameplay can yield higher scores through efficient teamwork. <br>
<br>**Scoring:** Points are awarded based on the number of dishes completed. <br>
<br>**Recipe System:** <br>
    Each dish has a recipe that includes specific ingredients and steps (e.g., chopped vegetables, fried meat, boiled pasta).
Different difficulty levels could introduce more complex recipes <br>
<br>**Difficulty Levels:** <br>
    **Easy:** More time, simpler recipes. <br>
    **Medium:** Moderate time, recipes with more steps. <br>
    **Hard:** Less time, more complex recipes. <br>

* **Objective**: Cook as much dishes as possible based on the given recipe until the time is up. <br>

* **Single/Multi Player**: Both

### 2.2 Fitur Utama
1. Choose difficulty
2. Choose character
3. Co-op multiplayer

## 3. Implementasi Fitur Wajib

### 3.1 Save/Load System
* **Implementasi**:
  Save highest score on each difficulty
* **Konsep OOP**:
  **Encapsulation**: The high scores for each difficulty level are stored within the **GameData** class, keeping this data private and only accessible through specific methods.
* **Penerapan SOLID**:
  **Single Responsibility Principle (SRP)** : SaveSystem only handles saving and loading data without handling game logic.
  **Open/Closed Principle (OCP)** : SaveSystem can be extended for different storage formats (like cloud or database storage) without modifying its core methods.
* **Design Pattern yang Digunakan**:
* **Code Snippet**:
```
import json


highscore_file = 'highscores.json'


class HighscoreLoader:
    def load(self):
        try:
            with open(highscore_file, 'r') as file:
                return json.load(file)
        except FileNotFoundError:
            return {'easy': 0, 'medium': 0, 'hard': 0}


class HighscoreSaver:
    def save(self, highscores):
        with open(highscore_file, 'w') as file:
            json.dump(highscores, file)


class HighscoreUpdater:
    def __init__(self, loader, saver):
        self.loader = loader
        self.saver = saver


    def update_highscore(self, difficulty, score):
        highscores = self.loader.load()
        highscores[difficulty] = max(highscores[difficulty], score)
        self.saver.save(highscores)


def main():
    loader = HighscoreLoader()
    saver = HighscoreSaver()
    updater = HighscoreUpdater(loader, saver)
```

### 3.2 Achievement System
* **Jenis Achievement**:
    1. High Score Hard Level: Awarded when the player scores at least 10 points in Hard level.
    2. Multiplayer Score Goal: Awarded when the player scores at least 20 points in Multiplayer mode.
* **Konsep OOP**:
  **Encapsulation**: Each achievement is responsible for managing its own criteria and tracking whether it is unlocked, encapsulating behavior within specific classes.
  **Inheritance**: Different achievements inherit from the Achievement base class, sharing properties and methods while allowing specific criteria implementations.
  **Polymorphism**: By overriding CheckCriteria, each achievement implements custom behavior for checking score-based conditions.
* **Penerapan SOLID**:
  **Open/Closed Principle (OCP)**: New achievements can be added as subclasses without modifying the base class, making the system extensible.
  **Liskov Substitution Principle (LSP)**: Any specific achievement class (e.g., HighScoreHardLevel) can replace the Achievement base class without affecting the AchievementManager.c
  **Interface Segregation Principle (ISP)**: If you were to have separate interfaces for achievements based on score, multiplayer, etc., you would implement only relevant interfaces.
  **Dependency Inversion Principle (DIP)**: The AchievementManager depends on abstractions (the Achievement class) rather than concrete implementations, allowing flexibility.
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

### 4.1 Fitur 1
* **Implementasi**:
* **Konsep OOP**:
* **Penerapan SOLID (Optional)**:
* **Design Pattern yang Digunakan (Optional)**:
* **Code Snippet**:
```
[Code snippet here]
```

### 4.2 Fitur 2
* **Implementasi**:
* **Konsep OOP**:
* **Penerapan SOLID (Optional)**:
* **Design Pattern yang Digunakan (Optional)**:
* **Code Snippet**:
```
[Code snippet here]
```

## 5. Screenshot dan Demo
* **Screenshot 1**: [Deskripsi]
* **Screenshot 2**: [Deskripsi]
* **Link Demo Video**: [URL]

## 6. Panduan Instalasi dan Menjalankan Game
1. [Langkah 1]
2. [Langkah 2]
3. [Langkah n]

## 7. Kendala dan Solusi
1. **Kendala 1**:
    * Solusi:
2. **Kendala 2**:
    * Solusi:

## 8. Kesimpulan dan Pembelajaran
* **Kesimpulan**:
* **Pembelajaran**:
