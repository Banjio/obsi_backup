Amazing slicer in my eyes but you need to know which settings to use

### Minimal time for a layer
This is often the reason why the print speed is not decreasing although you increase the speed massively its under filament > cooling

### Volumetric Flow 
Under the filaments settings its usually 15 mm/s² and can be fine tuned with this orca slicer test https://www.obico.io/blog/maximum-volumetric-speed-test-in-orcaslicer-a-comprehensive-guide/

### Fuzzy Skin
Amazing for decorative stuff but problematic when you have parts that are fragile because fizzy skin can reduce the stability of prints

## Adhesion Improvement
### First Layer
Adhesion can be improved by setting the first layer heigh to the noozle diameter (e.g. 0.4mm if you have a 0.4mm noozle) and the width of the first layer to noozle diameter + 0.2mm (e.g. 0.6mm for forementioned case)

### Infill Patterns:

* Gyriod: Strength from all sides 
* Adaptive Cubic: All Purpose (Speed and some stength)
* Honyecomb: Long time best all purpose and still pretty solid
* Cubic: Strength (And recommended by makersmuse)
* Linear and Grid: Speed
* Cross Hatch: Default

## Muster unterer/oberer Fläche 

Kann einen schönen finishing touch geben wenn man für die unter Fläche z.B Oktagramm Spirale nutzt.

## Overhang improvement

If you have prints with overhanging parts this settings can help 

* *Wall printing order*: Inner/Outer &Rightarrow; Outer layer better adheres to the print 
* *Ensure vertical thickness*: Filing vertical gaps and area with insufficient material
* *Slow down for overhang* (Under layer speed)
* Reverse odds on overhangs or improve overhangs 

## Support Settings

* **Raft**: Es wird eine Hiflskonstruktion (eng. raft = Floß) auf den boden gedruckt als erste Schicht auf diese Schicht wird dann das eigentliche Werkstück gedruckt. Sollte vor allem bei Stücken genutzt werden die wenig haftung mit dem Boden hätten. Der Nachteil ist, dass das Stück nachgearbeitet werden muss da es stark an dem raft haftet
* **Brim**: Ähnlich wie beim Rafting zus. Layer die um das Werkstück gedruckt wird um Haftung zu erhöhen bei ABS sehr empfohlen. Es wird meist auf 3-5 Loops beschränkt
* **Skirt**: Testkonstruktion die vor dem tatsächlichen Druck auf die vermutliche Testfläche aufgedruckt wird. Sie soll helfen zu erkennen ob die erste Schicht perfekt funktionieren würde. Danach kann der Skirt entfernt werden und der richtige Druck beginnen es empfiehlt sich tatsächlich immer ein Skirt (vor allem bei komplexeren Drucken) zu legen um sich zu versichern, dass die Einstellungen stimmen.

## Adaptive Bed Leveling
Bed is leveled before each print but only for the print area. 
https://github.com/SoftFever/OrcaSlicer/wiki/adaptive-bed-mesh