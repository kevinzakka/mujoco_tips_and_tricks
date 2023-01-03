# MuJoCo Weekly: Tips and Tricks for MuJoCo Users, Every Week

A weekly "newsletter" introducing [MuJoCo](https://github.com/deepmind/mujoco) users to new features, tips, and tricks.

- [MuJoCo Weekly: Tips and Tricks for MuJoCo Users, Every Week](#mujoco-weekly-tips-and-tricks-for-mujoco-users-every-week)
  - [Week 1](#week-1)

## Week 1

Here are two ways to implement gravity compensation in MuJoCo:

- Inject fake upwards forces at the center-of-mass (CoM) of each body in a kinematic chain such that each force exactly counteracts the body’s weight (weight = gravity x mass). Write these forces to `xfrc_applied` which are user-defined Cartesian wrenches that MuJoCo will apply to body CoMs.

```python
def compensate_gravity(self, model: mjcf.RootElement, physics: mjcf.Physics) -> None:
    gravity = np.hstack([physics.model.opt.gravity, [0, 0, 0]])
    physics_bodies = physics.bind(model.find_all("body"))
    physics_bodies.xfrc_applied[:] = -gravity * physics_bodies.mass[..., None]
```

- Use the body’s `gravcomp` attribute, which is a new feature added as of version 2.3.1. `gravcomp=1.0` will exactly counteract gravity, values greater than 1 will create a buoyancy effect. The default value for gravcomp is 0.

```python
def compensate_gravity(model: mjcf.RootElement) -> None:
    for body in model.find_all("body"):
        body.gravcomp = 1.0
```
