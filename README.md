# KEF Speaker Controller
An electron-based controller for KEF wireless speakers. Only tested with KEF LSX, but the LS50 should work as well.

<img src="https://i.imgur.com/6OPbLZ1.jpg" width="500">

## Getting sttarted
Before you get started, make sure to change the speaker IP address to your speaker's address. You can do so in src/utils/kefControl.ts on line 39.

```
yarn
yarn electron:serve
```

## Features & Todos
- [x] Turn on/off
- [x] Input source switching
- [x] Volume slider
- [x] Playback controls
- [ ] Playback controls design
- [ ] Speaker IP settings
- [ ] Include electron-builder
- [ ] App Icon
- [ ] Sound signature controls
