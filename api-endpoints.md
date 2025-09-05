# API Endpoints Documentation

Base URL: `http://3.235.179.11`

All APIs require an Authorization header with an API key. Replace `YOUR_API_KEY` with your actual API key.

## 1. StyleTTS2 API (Port 8000)

### Generate Speech
Converts text to speech using StyleTTS2.

```bash
curl -X POST "${BASE_URL}:8000/generate" \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer YOUR_API_KEY" \
    -d '{
        "text": "Hello, this is a test of StyleTTS2 speech synthesis.",
        "target_voice": "andreas"
    }'
```

### List Available Voices
Returns the list of available voice options.

```bash
curl -X GET "${BASE_URL}:8000/voices" \
    -H "Authorization: Bearer YOUR_API_KEY"
```

### Health Check
Checks if the StyleTTS2 model is loaded and healthy.

```bash
curl -X GET "${BASE_URL}:8000/health" \
    -H "Authorization: Bearer YOUR_API_KEY"
```

**Available Voices:** `andreas`, `woman`

---

## 2. Seed-VC API (Port 8001)

### Voice Conversion
Converts voice from source audio to target voice style.

```bash
curl -X POST "http://localhost:8000/convert" \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer 12345" \
    -d '{
        "source_audio_key": "examples/reference/woman.wav",
        "target_voice": "huberman"
    }'
```

### List Available Target Voices
Returns the list of available target voices for conversion.

```bash
curl -X GET "${BASE_URL}:8001/voices" \
    -H "Authorization: Bearer YOUR_API_KEY"
```

### Health Check
Checks if the Seed-VC model is loaded and healthy.

```bash
curl -X GET "${BASE_URL}:8001/health" \
    -H "Authorization: Bearer YOUR_API_KEY"
```

**Available Target Voices:** `andreas`, `woman`, `trump`

---

## 3. Make-An-Audio API (Port 8002)

### Generate Audio
Generates audio from text prompt using Make-An-Audio.

```bash
curl -X POST "${BASE_URL}:8002/generate" \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer YOUR_API_KEY" \
    -d '{
        "prompt": "A dog barking in the park"
    }'
```

### Health Check
Checks if the Make-An-Audio model is loaded and healthy.

```bash
curl -X GET "${BASE_URL}:8002/health" \
    -H "Authorization: Bearer YOUR_API_KEY"
```

---

## Response Formats

### Successful Audio Generation Response
```json
{
    "audio_url": "https://s3-presigned-url-to-audio-file",
    "s3_key": "path/to/audio/file/in/s3"
}
```

### Health Check Response
```json
{
    "status": "healthy",
    "model": "loaded"
}
```

### Voice List Response
```json
{
    "voices": ["andreas", "woman", "trump"]
}
```

### Error Response
```json
{
    "detail": "Error message describing what went wrong"
}
```

---

## Authentication

All endpoints require authentication using an API key in the Authorization header:

```bash
-H "Authorization: Bearer YOUR_API_KEY"
```

or

```bash
-H "Authorization: YOUR_API_KEY"
```

---

## Notes

1. **StyleTTS2 API**: Supports text-to-speech with character limit of 5000 characters
2. **Seed-VC API**: Requires source audio file to be uploaded to S3 first, then reference the S3 key
3. **Make-An-Audio API**: Generates audio from text descriptions of sounds/music
4. All APIs return presigned S3 URLs that expire in 1 hour
5. Generated audio files are automatically cleaned up from local storage after upload to S3

## Example Usage Flow

### 1. Generate speech with StyleTTS2:
```bash
curl -X POST "${BASE_URL}:8000/generate" \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer YOUR_API_KEY" \
    -d '{"text": "Hello world", "target_voice": "andreas"}'
```

### 2. Convert voice with Seed-VC:
```bash
# First upload your source audio to S3, then:
curl -X POST "${BASE_URL}:8001/convert" \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer YOUR_API_KEY" \
    -d '{"source_audio_key": "uploads/my-audio.wav", "target_voice": "trump"}'
```

### 3. Generate sound effects with Make-An-Audio:
```bash
curl -X POST "${BASE_URL}:8002/generate" \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer YOUR_API_KEY" \
    -d '{"prompt": "Rain falling on leaves"}'
```

