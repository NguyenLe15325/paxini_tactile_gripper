# paxini tactile gripper v2
## gripper v1 's design flaws:
- bulky, heavy, waste of material
- plenty of internal unused empty spaces 
- redundant, overcomplicated mechanical designs, add up deadweight
- electronics are mounted outside of the shell which take up more space outward
### fatal design: 
- gripper v1 (specifically v1.0.6) experience skipping gear teeth due to the spur rack undergoes flexure under heavy load
- The longer the spur rack is, the more it is susceptible to flexure under load without proper support to push the spur rack against the spur gear
- Design v1.0.6 use long 200mm guide rail, flexible 3d printing material PETG, large clearance between shell's inner face to spur rack (2mm), this create almost no support to push spur rack inward against the spur gear unless the spur rack flex more than 2mm, even the support shell itself flex!
- All of these plus the material crack and creep overtime making gear teeth skipping occur at lighter load
## gripper v2:
- Use [reBot Arm B601](https://github.com/Seeed-Projects/reBot-DevArm/tree/main) 's gripper as design reference
- Compatible to [reBot Arm B601 RS](https://github.com/Seeed-Projects/reBot-DevArm/tree/main/hardware/reBot_B601_RS), reuse the same [gripper connector](https://github.com/Seeed-Projects/reBot-DevArm/blob/main/hardware/reBot_B601_RS/Metal_Parts/2-M7-STATOR.step) CNC part of the arm
- Solve all v1 's design flaw: highly compact (use lower gear module 1.25 as compared to 1.5 in v1), structurally optimized, low part count, electronics mounted internally, add DC022B female DC jack to power FE-URT-1 board, strong spur rack flexure support, low clearance (0.3mm) while minimizing contact area between support and spur rack to avoid unwanted friction
### v2.0.0
- 200mm guide rail, gripping range 110.2mm. View [Dimensions](assets/v2.0.0/dimensions.JPG)
- Viewing v1.0.6 and v2.0.0 [side by side](assets/v2.0.0/v2.0.0_vs_v1.0.6/isometric.png)
- [View](assets/v2.0.0/rebot_b601_rs_arm/isometric.png) gripper when attached to reBot B601 RS arm