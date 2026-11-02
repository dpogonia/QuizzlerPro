[Русский](README.md) | **English**

# Quizzler Pro

An iOS quiz in SwiftUI: you guess a character's name, tap **Yes** or **No** while the timer runs, and set records.

Four modes: Rick and Morty, South Park, Big Mouth, and Human Resources. The task is to recognize the character. A round is 20 questions.

<p align="center">
  <img src="Docs/start.png" width="230" hspace="8" alt="Home screen">
  <img src="Docs/settings-records.png" width="230" hspace="8" alt="Records and difficulty">
  <img src="Docs/settings-preferences.png" width="230" hspace="8" alt="Theme, sound, haptics, language">
</p>
<p align="center">
  <img src="Docs/game-south-park-tolkien.png" width="230" hspace="8" alt="Game, South Park">
  <img src="Docs/game-south-park-ok.png" width="230" hspace="8" alt="Game, South Park">
  <img src="Docs/game-rick-and-morty.png" width="230" hspace="8" alt="Game, Rick and Morty">
</p>

## What it does

A splash comes first: the icon spins while the show banks download. Then a menu with four modes and settings.

Pick a mode and the game starts: a picture, a question, **Yes** / **No**, and a timer. Character photos come from an API. A round is 20 questions. Music plays, and on the last three seconds the phone gives a light haptic tick.

If you leave a game unfinished, **Continue** appears on the menu. Opening that same mode again asks whether to return to the saved round or start over.

Settings hold records per mode, timer speed (that is the difficulty: 1 to 10 seconds per question), theme, sound, haptics, and language (Russian / English).

## Modes

- **Rick and Morty** — characters and photos from [rickandmortyapi.com](https://rickandmortyapi.com)
- **South Park** — characters from [spapi.dev](https://spapi.dev), pictures downloaded through the API
- **Big Mouth** and **Human Resources** — characters and photos from [Fandom](https://bigmouth.fandom.com/api.php)

## Offline

During the splash the show banks are downloaded ahead of time: character lists and their pictures. A successful response is written to disk as JSON. The next launch checks that cache first. With no network, the last successful copy is used. Pictures also stay in memory and `URLCache`, so the same poster is not downloaded twice.

If no bank downloaded and there is no cache yet, the splash stays up with a Retry button. If only one mode failed and the others are already in memory, you can retry the download or go on and play what is there.

## Continue a game

A round is not lost if you close the screen or leave the app.

A snapshot is written to disk: mode, question number, score, seconds left, text, the correct answer, and the current picture.

The **Continue** card on the home screen shows the mode and progress such as `4/20`. A tap returns to the same question and the same timer.

If you open that mode again instead of tapping Continue, the app asks whether to return to the saved round or start from zero. Another mode can be started right away. The saved game stays until it is overwritten.

The snapshot is erased when the round is finished. A new start in the same mode also replaces the old file.

## Run

iOS 17.6 or later is required.

```bash
git clone https://github.com/dpogonia/Quizzler-Pro.git
cd Quizzler-Pro
open Quizzler.xcodeproj
```

## Code

If you edit strings in `Quizzler/Resources`, [SwiftGen](https://github.com/SwiftGen/SwiftGen) will refresh `L10n`. The project still builds without it: the generated file is already in the repository.

Your own round music can go in `Quizzler/Resources/Audio/` under the name `game_music`.

Built with Swift and SwiftUI. Packages come through Swift Package Manager. Networking uses `URLSession`, models use `Codable`, the disk uses `FileManager`, and settings and records use `UserDefaults`. Screens update through Combine. Sound uses AVFoundation, haptics use Core Haptics.

Content comes from public APIs: [rickandmortyapi.com](https://rickandmortyapi.com), [spapi.dev](https://spapi.dev), and [Fandom](https://bigmouth.fandom.com) / [southpark.fandom.com](https://southpark.fandom.com). Names and photos are downloaded from there. The bundle has no character pictures.

The app target is mostly screens and dependency setup. Networking, files, and UserDefaults live in `Packages/CoreServices`. Quiz logic lives in `QuizServices`. Buttons and cards live in `QuizUI`.

```text
Quizzler/
├── App/            dependency setup
├── UI/             splash, menu, game, settings
├── Flux/           round state
├── Resources/      strings and sounds
└── Assets.xcassets icon and accent color

Packages/
├── CoreServices
├── QuizServices
└── QuizUI
```

Taps, a correct answer, and a wrong answer are short WAV files through `AVAudioPlayer`. Music loops only on the game screen and pauses when the app goes to the background. The session is `.ambient`, so it does not take over other audio.

Russian and English live in `Localizable.xcstrings`. SwiftGen builds `L10n` from them, so the code has no raw keys. Theme, language, sound, haptics, and timer length are separate services on top of `UserDefaults`.

## License

For study only: look, run, and take apart. Commercial use and presenting the work as your own are not allowed. The text is in [`LICENSE`](LICENSE).
