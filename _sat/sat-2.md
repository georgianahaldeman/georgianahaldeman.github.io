---
title: "Appropriate constructs"
date: 2024-09-03
excerpt: ""
collection: sat
---

One of the examples from last class used an ```enum``` structure:

```
private Result analyzeOrganic(Sample sample) {
    if (microscope.isHumanoid(sample) == false) {
        return Result.ALIEN;
    } else {
        return Result.HUMANOID;
    }
}
```

An enumeration is a pre-defined set of values, for example:

```
public enum Result {
    HUMANOID, INORGANIC, ALIEN
}
```

The advantage to using an enumeration is that when you declare a variable, a parameter or return type of an enumeration type then all the values (stored in the variable, passed to the parameter or returned) must be in the predefined set of the enumeration. For example, ```analyzeOrganic``` can only return three values.

Additionally, you can add attributes and constructor to enumerations just like with classes likes so:

```
public enum Result {
    HUMANOID(75), 
    INORGANIC(40), 
    ALIEN(100);

    final int coolnessIndex;

    Result(int newCI){
        this.coolnessIndex = newCI;
    }
}
```

**Warm-up Problem**: How can we improve the quality of this code snippet:

```
class CruiseControl {

    private double targetSpeedKmh;

    void setPreset(int speedPreset) {
        if (speedPreset == 2) {
            setTargetSpeedKmh(16944);
        } else if (speedPreset == 1) {
            setTargetSpeedKmh(7667);
        } else if (speedPreset == 0) {
            setTargetSpeedKmh(0);
        }
    }

    void setTargetSpeedKmh(double speed) {
        targetSpeedKmh = speed;
    }
}
```

Numbers without an apparent meaning that steer the program are called **magic numbers**. We can dispel their magic and thus improve the quality of the code by making them constants:

```
class CruiseControl {

    static final int STOP_PRESET = 0;
    static final int PLANETARY_SPEED_PRESET = 1;
    static final int CRUISE_SPEED_PRESET = 2;

    static final double CRUISE_SPEED_KMH = 16944;
    static final double PLANETARY_SPEED_KMH = 7667;
    static final double STOP_SPEED_KMH = 0;

   private double targetSpeedKmh;

    void setPreset(int speedPreset) {
        if (speedPreset == CRUISE_SPEED_PRESET) {
            setTargetSpeedKmh(CRUISE_SPEED_KMH);
        } else if (speedPreset == PLANETARY_SPEED_PRESET) {
            setTargetSpeedKmh(PLANETARY_SPEED_KMH);
        } else if (speedPreset == STOP_PRESET) {
            setTargetSpeedKmh(STOP_SPEED_KMH);
        }
    }

    void setTargetSpeedKmh(double speed) {
        targetSpeedKmh = speed;
    }
}
```

Can we do better?

Note that nothing restricts the user to call ```setPreset``` with values other than the three values it checks for and the function terminates without error. We can make the semantics of this function more transperant for outside usage by using an ```enum``` construct like so:

```
class CruiseControl {

    private double targetSpeedKmh;

    void setPreset(SpeedPreset speedPreset) {
        Objects.requireNonNull(speedPreset);

        setTargetSpeedKmh(speedPreset.speedKmh);
    }

    void setTargetSpeedKmh(double speedKmh) {
        targetSpeedKmh = speedKmh;
    }
}
enum SpeedPreset {
    STOP(0), PLANETARY_SPEED(7667), CRUISE_SPEED(16944);

    final double speedKmh;

    SpeedPreset(double speedKmh) {
        this.speedKmh = speedKmh;
    }
}
```

[[Appropriate Construct Worksheet]](/sat/files/9_3.pdf)
[[Answers]](/sat/files/9_3_ans.pdf)