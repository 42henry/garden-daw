# garden-daw
Gardening + Music Making!

For making plants:
https://www.youtube.com/watch?v=feNVBEPXAcE

Wiki on classic waves:
https://en.wikipedia.org/wiki/Sawtooth_wave

FFT:
https://www.youtube.com/watch?v=h7apO7q16V0

Some notes on raylib audio:

LoadAudioStream(sampleRate, sampleSize, channels)
 - sampleRate should be 44100Hz as that covers human hearing range (pitches)
 - sampleSize can just be 32 (bits per sample, we'll use f32)
 - channels is 1 for mono, 2 for stereo

UpdateAudioStream(stream, &data, frameCount)
 - data should be a pointer to an array of f32s?
 - frameCount total number of frames
     - one frame contains one sample per channel
     - thus if we're on stereo, for the same number of frames we need frameCount * 2
 - only call this when IsAudioProcessed(stream) returns true
 - also need SetAudioStreamBufferSizeDefault(BUFFER_FRAMES), usually 4096
 - also remember to call PlayAudioStream(stream) first

Sine wave example:
```odin
BUFFER_FRAMES :: 4096
buffer: [BUFFER_FRAMES]f32

sampleRate: f32 = 44100

amplitude: f32 = 0.5
frequency: f32 = 440

phase: f32 = 0
phaseIncrement := 2 * math.PI * frequency / sampleRate // divide by sampleRate as under the hood we only take a sample every sample rate

if rl.IsAudioStreamProcessed(stream)
{
    for i in 0..<BUFFER_FRAMES
    {
        buffer[i] = amplitude * math.sin(phase)
        phase += phaseIncrement
        if phase > 2 * math.PI do phase -= 2 * math.PI
    }
    rl.UpdateAudioStream(stream, buffer, BUFFER_FRAMES)
}
```
NOTE: if we want stereo, we need buffer[2 * i] = blah and buffer[2 * i + 1] = blah where they both store the same blah for each channel, and the for loop only goes for half the total BUFFER_FRAMES in this instance, where BUFFER_FRAMES would be 4096 * 2
