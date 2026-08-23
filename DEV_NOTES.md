# DEV NOTES — 24 August 2026

## Turbo Spectrum / ESPectrum — sesiunea de lucru

### 1. ROM custom 48K
- Am verificat `include/roms/rom48Kcustom.h`.
- Fișierul conține un ROM custom de 16 KB reprezentat ca array C:
  `gb_rom_0_48k_custom[]`.
- `ESPConfig.cpp` îl selectează pentru arhitectura `48Kcs`:
  `MemESP::rom[0] = (uint8_t *) gb_rom_0_48k_custom;`
  și apoi ROM-ul este deplasat cu 8 bytes (`+= 8`).

### 2. Turbo Spectrum boot
- Am lucrat cu `ts_boot.bin`.
- Au fost pregătite și:
  - `include/roms/ts_boot.h`
  - `ts_boot.bin`
  - `ts_boot_ESPectrum.bin`
  - directorul `ts_bin/`
- Ideea de lucru: conținutul boot ROM-ului Turbo Spectrum este folosit pentru a construi/testa ROM-ul custom în ESPectrum.
- Testarea pe placa fizică rămâne pentru moment neefectuată, deoarece placa nu este disponibilă.

### 3. Verificarea arhitecturii în ESPectrum
Am urmărit unde este verificată `Config::arch`, pentru a înțelege fluxul:
- `src/ESPConfig.cpp` — selectarea ROM-urilor și inițializarea arhitecturii.
- `src/MemESP.cpp` — `ramCurrent`, `bankLatch`, `pagingLock`, `pagingmode2A3`.
- `src/Ports.cpp` — paging, porturi și comportamentul specific modelelor.
- `src/OSDMain.cpp` — meniul și selecția arhitecturii.
- `src/CPU.cpp` — configurarea CPU în funcție de arhitectură.
- `src/Video.cpp` — comportamentul video în funcție de arhitectură.
- `src/ESPectrum.cpp` — inițializare și timing specific arhitecturii.
- `src/Snapshot.cpp` — detectarea/menținerea arhitecturii la snapshot-uri.

### 4. Observație importantă pentru continuare
Modelul de lucru este:
1. se alege arhitectura la pornire;
2. codul verifică `Config::arch` în punctele unde comportamentul diferă;
3. pentru ROM custom se selectează ROM-ul corespunzător;
4. pentru Turbo Spectrum trebuie urmărit separat ce comportament hardware trebuie emulat în RAM/paging/porturi/CPU/video, fără a modifica inutil codul existent.

### 5. Git — salvare proiect
A fost creat remote-ul personal:
`turbo -> https://github.com/nicolaebizi/turbo-spectrum-espectrum.git`

Commit deja făcut:
`a9f4736 Add Turbo Spectrum custom 16K ROM`

Commitul conține modificarea:
`include/roms/rom48Kcustom.h`

A fost făcut push cu:
`git push -u turbo master`

Rezultat:
- branch-ul local `master` urmărește acum `turbo/master`;
- proiectul a fost publicat în repository-ul personal Turbo Spectrum.

### 6. Fișiere încă necomise la finalul sesiunii
Conform ultimului `git status`, acestea erau untracked:
- `.DS_Store`
- `.vscode/settings.json`
- `drive/`
- `include/roms/ts_boot.h`
- `ts_bin/`
- `ts_boot.bin`
- `ts_boot_ESPectrum.bin`

Nu le adăugăm automat până nu decidem exact ce trebuie păstrat în repository.

## Următorul pas
Continuăm de aici, fără să schimbăm ce funcționează deja:
1. verificăm exact conținutul `rom48Kcustom.h`;
2. stabilim cum trebuie introdus `ts_boot.bin` în ROM-ul custom;
3. verificăm pornirea ca Spectrum 48K fără modificări suplimentare;
4. abia apoi modificăm emularea hardware Turbo Spectrum, dacă este necesar.
