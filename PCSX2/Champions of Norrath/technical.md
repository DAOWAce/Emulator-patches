// The game's streaming worker handles multiple stream slots:
//   s5 = 0  background streamed audio
//   s5 = 1  boot/logo and level-transition streaming
//   s5 = 2  dialogue voice
//
// A global bypass at 0024A914 fixes dialogue latency but breaks
// background-stream fades/transitions. This patch instead redirects
// the branch through unused executable padding at 002DFF9C and bypasses
// the wait only for dialogue stream 2. Other stream slots retain the
// original v1 == 0 behavior.
//
// Hook:
//   0024A914  080B7FE7  j 002DFF9C
//   0024A918 remains the original delay-slot instruction.
//
// Injected logic at 002DFF9C:
//   if (s5 == 2) goto 0024A980
//   if (v1 == 0) goto 0024A980
//   goto 0024A91C
//
// Tested:
// - Multiple NPC conversations
// - Multi-page voiced dialogue
// - Correct background-audio ducking during speech
// - Normal background music/ambient stream transitions
// - Opening/new-game and other tested cutscenes
// - Normal environmental audio

// Hook original stream-status branch into unused executable padding.
patch=1,EE,0024A914,word,080B7FE7

// Code cave: 002DFF9C-002DFFBC
patch=1,EE,002DFF9C,word,26A1FFFE
patch=1,EE,002DFFA0,word,10200005
patch=1,EE,002DFFA4,word,00000000
patch=1,EE,002DFFA8,word,10600003
patch=1,EE,002DFFAC,word,00000000
patch=1,EE,002DFFB0,word,08092A47
patch=1,EE,002DFFB4,word,00000000
patch=1,EE,002DFFB8,word,08092A60
patch=1,EE,002DFFBC,word,00000000
