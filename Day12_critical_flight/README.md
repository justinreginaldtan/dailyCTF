# Day12 - critical_flight

- **Platform**: HackTheBox  
- **Category**: Hardware  
- **Difficulty**: Easy  
- **Date**: 2025-05-12  
- **Tools Used**: Zip, Online Gerber Viewer (PCBWay)  

---

## Summary
The challenge provided Gerber files for a PCB. By loading the layers into an online Gerber viewer, I identified hidden flag text: one half printed on the silkscreen layer and the other etched into the copper layer. Combining both halves revealed the full flag.

---

## Enumeration
- Challenge provided multiple Gerber (`.gbr`) files.  
- Uploaded the folder into an online Gerber viewer.  
- Reviewed layers:  
  - Copper  
  - Soldermask  
  - Silkscreen  
  - Solderpaste  
  - Drill  
  - Board outline  

---

## Exploitation
- Analyzed the silkscreen layer and discovered the first half of the flag (mirrored).  
- Disabled non-essential layers (soldermask, silkscreen, solderpaste).  
- Focused on copper layers (`*_F.gbr`, `*_B.gbr`) and revealed the second half of the flag etched into traces.  
- Combined both halves to reconstruct the full flag.  

---

## Lessons Learned
- Gerber files describe PCB structure layer by layer, and analyzing them reveals hidden details.  
- Silkscreen layers often contain visible text, while copper layers may conceal hidden etchings.  
- Disabling non-essential layers in a viewer helps isolate the relevant artifacts.  
- Online Gerber viewers provide a fast, accessible way to analyze PCB designs in CTFs.  
