## Script Lifecycle

[Official Reference](https://docs.unity3d.com/Manual/ExecutionOrder.html)

#### Take a look the video below

<video width="800" height="480" controls>
  <source src="../imgs/dot-net/script-lifecycle.mp4" type="video/mp4">
</video>

#### Some important methods need to clear

Script S that is Behaviour and attached to a gameObject GO.

**1. Awake**
  - once
  - only need *GO* active

**2. OnEnable**
  - in Disable
  - both *GO* and *S* active

**3. OnDisable**
  - in Enable
  - one of 2 *GO* or *S* inactive

**4. Start**
  - once
  - before the 1st frame
  - and after the 1st call of OnEnable

**5. Update**
  - in Enable
  - each frame

|No.| GO       | S       | Awake   | OnEnable | Start | Update | OnDisable |
|--  | -------- | ------- |-------- | -------  | ----- | ------ | --------- | 
|1  | *Inactive* | **Active**  | x       | x        | x     | x      | x         |
|2  | **Active**   | *Inactive*| v       | x        | x     | x      | x         |
|3  | **Active**   | **Active**  | v     | v      | v  | v      | x         |
