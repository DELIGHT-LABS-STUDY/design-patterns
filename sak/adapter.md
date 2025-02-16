### 어댑터는 호환되지 않는 인터페이스를 가진 객체들이 협업할 수 있도록 하는 구조적 디자인
패턴

- 호환성이 없는 인터페이스 때문에 함께 동작할 수 없는 클래스들을 함께 작동해주도록 변환

## 객체 어댑터
```
// 클라이언트 인터페이스
interface MediaPlayer {
    fun play(audioType: String, fileName: String)
}

// 서비스 (기존 클래스)
class AdvancedMediaPlayer {
    fun playMp4(fileName: String) {
        println("Playing mp4 file: $fileName")
    }
    
    fun playVlc(fileName: String) {
        println("Playing vlc file: $fileName")
    }
}

// 객체 어댑터
class MediaAdapter(
// 서비스 객체 주입  (합성)
// 단일 상속
private val advancedPlayer: AdvancedMediaPlayer) : MediaPlayer {
    override fun play(audioType: String, fileName: String) {
        when (audioType.lowercase()) {
            "vlc" -> advancedPlayer.playVlc(fileName)
            "mp4" -> advancedPlayer.playMp4(fileName)
            else -> println("Invalid media type")
        }
    }
}

// 클라이언트
class AudioPlayer : MediaPlayer {
    private val mediaAdapter = MediaAdapter(AdvancedMediaPlayer())
    
    override fun play(audioType: String, fileName: String) {
        when (audioType.lowercase()) {
            "mp3" -> println("Playing mp3 file: $fileName")
            "vlc", "mp4" -> mediaAdapter.play(audioType, fileName)
            else -> println("Invalid media type")
        }
    }
}
```

### 장점 
- 단일 책임 원칙
- open/close 원칙