#ai-generated
* Create 3 Sketches  with 3 different Circles each with Diameters and change the Z Value so that it is moved along the z axis
* Create a loft for the 3 Sketcheds
* Create a Thickness for the loft : Use pipe and select the top and bottom if you want to hollow out the body entirely
* Move the thickness on the body if you want to perform further operations
## Copilot Prompts
Got it, we need to ensure that our loft creates a solid body that we can work with. Let's take another approach to create a tapered, hollow cylinder using the `Loft` and `Thickness` tools:

1. **Open FreeCAD**: Launch FreeCAD on your device.
    
2. **Create a new document**: Click on `File` > `New` to create a new document.
    
3. **Switch to the Part Design workbench**: Click on the `View` menu, go to `Workbench`, and select `Part Design`.
    
4. **Create a new Body**: Click on the `Create a new body` button (blue box icon) in the toolbar.
    
5. **Create a new Sketch**: Click on the `Create a new sketch` button (blue square with a pencil icon) and choose the XY plane.
    
6. **Draw the bottom circle**:
    
    - Select the `Create a circle` tool from the toolbar.
        
    - Click on the origin (center of the plane) to place the center of the circle.
        
    - Drag outwards to draw the circle and set the radius to 25 mm (this will give you a diameter of 50 mm).
        
    - Close the sketch by clicking on the `Close` button.
        
7. **Create the first Datum Plane**:
    
    - Click on the `Create a new datum plane` button (a plane with arrows icon) in the toolbar.
        
    - Select the XY plane as the reference.
        
    - Set the offset to 40 mm (1/3 of the total height).
        
    - Click `OK` to create the datum plane.
        
8. **Create the second Sketch**:
    
    - Select the datum plane you just created.
        
    - Click on the `Create a new sketch` button and choose the datum plane.
        
    - Draw a circle with a slightly larger radius than the bottom circle (e.g., 30 mm radius, 60 mm diameter).
        
    - Close the sketch by clicking on the `Close` button.
        
9. **Create the second Datum Plane**:
    
    - Click on the `Create a new datum plane` button again.
        
    - Select the XY plane as the reference.
        
    - Set the offset to 80 mm (2/3 of the total height).
        
    - Click `OK` to create the second datum plane.
        
10. **Create the third Sketch**:
    
    - Select the second datum plane you just created.
        
    - Click on the `Create a new sketch` button and choose the datum plane.
        
    - Draw a circle with a radius of 35 mm (this will give you a diameter of 70 mm).
        
    - Close the sketch by clicking on the `Close` button.
        
11. **Create the loft**:
    
    - Switch to the `Part` workbench.
        
    - Click on the `Loft` tool (two connected shapes icon) in the toolbar.
        
    - Select the sketches in the correct order: the bottom circle, the middle circle, and the top circle.
        
    - Ensure that the `Create solid` option is checked.
        
    - Click `OK` to create the tapered cylinder.
        
12. **Hollow out the cylinder**:
    
    - Switch back to the `Part Design` workbench.
        
    - Select the tapered cylinder in the model tree.
        
    - Click on the `Thickness` tool (a shape with a hole icon) in the toolbar.
        
    - In the Thickness dialog, set the thickness to 6 mm.
        
    - Select the top face of the cylinder to hollow out.
        
    - Click `OK` to create the hollow cylinder with the specified wall thickness.
        

This should give you a solid tapered cylinder that you can hollow out using the Thickness tool. If you encounter any issues or need further assistance, feel free to let me know!