# Project_Voxel-NURBS

A Next-Generation Open-Source CAD Engine written in Rust
> Vision: Ein hochperformantes, open-source CAD-System, das kompromisslose mathematische Flächenpräzision (Class-A), die Freiheit direkter Modellierung und die Geschwindigkeit moderner GPU-Rendering-Pipelines in einem speichersicheren Rust-Core vereint.
> 
1. Die Grundprinzipien (Core Dogmas)
 * Zero-Legacy, Zero-C++ UI Toolkits: Keine Abhängigkeiten zu GTK, Qt, OpenCASCADE oder veralteten C-Bibliotheken. Das UI rendert vollständig und nativ über die GPU.
 * Viewport-First (Direct Editing with Intent): Jede Geometrie-Änderung passiert direkt im 3D-Raum mit 120+ FPS. Kein Ausweichen auf verschachtelte Dialogfenster für einfache Operationen.
 * Graph-Driven Parametrics (Non-Linear): Kein starrer, linearer Feature-Baum, der bei kleinen Änderungen zusammenbricht. Stattdessen ein relationaler Constraint Dependency Graph (DAG) im Hintergrund.
 * Instant Startup & Low Resource Footprint: Startzeit unter 300 Millisekunden. Minimaler RAM-Verbrauch (unter 100 MB im Leerlauf).
 * Class-A by Design: Krümmungsstetigkeit (G2/G3) ist kein teures Zusatzmodul, sondern der Grundbaustein des Flächen-Kernels.
2. Die Architektur (Technical Stack)
 * Language: Rust (Edition 2024+) – striktes Memory Safety, mutabilitätskontrollierte Datenstrukturen und volle Multithreading-Skalierung.
 * Rendering & Viewport: wgpu (WebGPU-Standard für Vulkan, Metal, Direct3D 12) + benutzerdefinierte WGSL-Shader für Analyse-Tools (Zebra, Reflexion, Wandstärke).
 * UI-Framework: GPU-beschleunigtes UI via gpui oder iced (Immediate/Retained Hybrid), ausgelegt auf extrem schnelle Tastatur-Shortcuts und Radial-Menüs (Pie-Menus).
 * B-Rep & Geometry Engine: Modulares Rust-NURBS-Backend (z. B. aufbauend auf Truck / BrepRs mit eigener G2/G3-Solver-Erweiterung).
 * Windowing & Events: winit (Natives Handling von Multi-Touch, Stylus/S-Pen und Tastatur).
3. Schlüssel-Features & Workflow
A. The Surface Engine (Class-A & High-End Surfacing)
 * Direct Control Point Sculpting: Manuelle Manipulation von Bezier/NURBS-Kontrollpunkten direkt im Viewport mit Echtzeit-Messung.
 * Continuous Edge Matching: Dynamisches Einfrieren und Lösen von Randbedingungen (G0 Position, G1 Tangente, G2 Krümmung, G3 Torsion) zu Nachbarflächen.
 * Defined Angle & Sharp-Edge Continuity: Unterstützung für exakt definierte Winkelübergänge an Design- und Lichtkanten (Flanken folgen exakten Winkeln bei gleichzeitig krümmungs- oder torsionsstetigem Übergangsbereich).
 * Live Inspection Shaders: WGSL-GPU-Shader für Zebra-Streifen, Krümmungs-Kamm (Curvature Combs) und Entformungsschrägen-Analysen in Echtzeit ohne Framerate-Einbruch.
B. The Solid Engine (Direct Modeling)
 * Destructive & Non-Destructive Fillets/Chamfers: Verrundungen und Fasen werden direkt an B-Rep-Kanten gezogen. Schlägt eine Operation fehl, wird die Geometrie nicht zerstört, sondern visuell isoliert.
 * Push/Pull/Offset Surfaces: Beliebige B-Rep-Flächen lassen sich ziehen oder verschieben; das System berechnet angrenzende Topologien automatisch neu.
C. The Parametric Constraint Graph (Variational Core)
 * Smart Intent Recognition: Beim Ziehen von Flächen erkennt der Solver geometrische Bezüge (z. B. "Koaxial zu Zylinder X", "Parallel zu Ebene Y") und bietet an, diese als Parameter im Graph einzufrieren.
 * Table/Code-Driven Overrides: Alle Variablen lassen sich optional über eine Python/Rust-API oder eine Parameter-Tabelle ansteuern, ohne dass ein visueller Feature-Baum die direkte Modellierung blockiert.
D. Scan Data, Reverse Engineering & Strak Workflows
 * High-Performance Mesh / Point-Cloud Import: Direct-Memory-Mapping für extrem große STL/OBJ/PLY-Scandaten und Punktwolken (10+ Mio. Polygone) ohne UI-Ruckeln dank instanziiertem GPU-Vertex-Streaming in wgpu.
 * Digital Clay Overlay (Strak-Workflow): Direct Snapping von NURBS-Kontrollpunkten an importierte Scan-Meshes sowie Live-GPU-Distance-Heatmaps zur Anzeige der Abweichung zwischen Scan und NURBS-Fläche.
 * Blender Native Interop (.blend / Live-Link): Nativer Import/Export von Blender-Dateien und IPC-Live-Link (über Shared Memory), um Meshes, Kameras und Licht-Setups verlustfrei zwischen Blender und der CAD-Engine zu synchronisieren.
E. Advanced Tessellation Engine (3D Printing & FEM Interop)
 * High-Quality Adaptive Tessellation (B-Rep to Mesh): Adaptiv gesteuerte Vernetzung von NURBS-Flächen zu sauberen Triangle/Quad-Meshes ohne spitzwinklige Entartungen (Sliver-Triangles).
 * Strict Manifold Guarantee: Mathematisch garantierte, wasserdichte Topologie für den direkten Export nach 3MF/STL für den FDM/SLA/SLS-3D-Druck.
 * FEM-Ready Mesh Generation: Direkte Generierung strukturierter Oberflächen-Meshes mit hoher Elementgüte (Aspect Ratio, Jacobian Smoothness) für die direkte Übergabe an FEM-Solver (z. B. CalculiX, Elmer, Nastran).
 * Mesh-to-NURBS (Reverse Retopology): Werkzeuge zur automatischen oder halb-automatischen Umwandlung von sauberen Quad-Meshes zurück in getrimmte NURBS-Patches (G1/G2).
4. UI/UX Philosophie
 * Pie-Menus & Contextual Hotkeys: Häufige Befehle liegen auf schnellen Gesten (z. B. Tab, Space, S).
 * Non-Blocking Modal Tools: Während eine Phase gezogen wird, kann die Kamera stufenlos frei gedreht, gezoomt und analysiert werden.
 * Minimalist Canvas: Das 3D-Modell steht zu 95 % im Fokus. Menüleisten und Panels blendet das System Kontext-basiert ein oder aus.
5. Meilensteine & MVP Roadmap
 * Phase 0 (Proof of Concept): wgpu-Viewport mit winit. Import/Export von einfachen STEP- & IGES-Dateien. B-Rep-Darstellung auf der GPU mit 120 FPS.
 * Phase 1 (The Surface Sculptor): Erstellen von NURBS-Patches. Manuelle CV-Modifikation mit G1/G2/G3-Kopplung, Winkelkanten und Live-Zebra-Shader.
 * Phase 2 (Scan Data & Direct Solid Editing): GPU-Streaming für riesige Scan-STLs, Strak-Overlay sowie Extrusions-, Offset- und Verrundungs-Tools.
 * Phase 3 (Constraint Graph & Tessellation): Integration des variationalen Solvers für parametrische Maße sowie des High-End-Tessellators für 3D-Druck und FEM.
Das ist ein exzellent durchdachtes Fundament! Sollten wir dieses Dokument noch um konkrete Beispiele für Tastatur-Shortcuts oder eine Ordnerstruktur für das GitHub-Repository ergänzen?
