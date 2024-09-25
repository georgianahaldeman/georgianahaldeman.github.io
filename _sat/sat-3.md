---
title: "Conventions"
date: 2024-09-05
excerpt: ""
collection: sat
---

Conventions are rules that are widely agreed upon by professionals. Used approapriately and consistently, they can help with the readability of the code.


**Rule 1**: Remove excessive comments for example:

```
class Inventory {
    // Fields (we only have one)
    List<Supply> supplies = new ArrayList<>(); // The list of supplies.

    // Methods
    int countContaminatedSupplies() {
        // TODO: check if field is already initialized (not null)

        int contaminatedCounter = 0; // the counter
        // No supplies => no contamination
        for (Supply supply : supplies) { // begin FOR
            if (supply.isContaminated()) {
                contaminatedCounter++; // increment counter!
            } // End IF supply is contaminated
        }// End FOR

        // Returns the number of contaminated supplies.
        return contaminatedCounter; // Handle with care!
    }
} // End of Inventory class
```

Becomes:

```
class Inventory {

    List<Supply> supplies = new ArrayList<>();

    int countContaminatedSupplies() {
        if (supplies == null || supplies.isEmpty()) {
            // No supplies => no contamination
            return 0;
        }

        int contaminatedCounter = 0;
        for (Supply supply : supplies) {
            if (supply.isContaminated()) {
                contaminatedCounter++;
            }
        }

        return contaminatedCounter;
    }
}
```

**Rule 2**: Remove code in comments for example:

```
class LaunchChecklist {

    List<String> checks = Arrays.asList(
            "Cabin Leak",
            // "Communication", // Do we actually want to talk to Houston?
            "Engine",
            "Hull",
            // "Rover", // We won't need it, I think...
            "OxygenTank"
            //"Supplies"
    );

    Status prepareLaunch(Commander commander) {
        for (String check : checks) {
            boolean shouldAbortTakeoff = commander.isFailing(check);
            if (shouldAbortTakeoff) {
                //System.out.println("REASON FOR ABORT: " + item);
                return Status.ABORT_TAKE_OFF;
            }
        }
        return Status.READY_FOR_TAKE_OFF;
    }
}
```

Becomes:

```
class LaunchChecklist {

    List<String> checks = Arrays.asList(
            "Cabin Leak",
            "Engine",
            "Hull",
            "OxygenTank"
    );

    Status prepareLaunch(Commander commander) {
        for (String check : checks) {
            boolean shouldAbortTakeoff = commander.isFailing(check);
            if (shouldAbortTakeoff) {
                return Status.ABORT_TAKE_OFF;
            }
        }
        return Status.READY_FOR_TAKE_OFF;
    }
}
```

**Rule 3**: Replace comments with constants for example:

```
enum SmallDistanceUnit {

    CENTIMETER,
    INCH;

    double getConversionRate(SmallDistanceUnit unit) {
        if (this == unit) {
            return 1; // identity conversion rate
        }

        if (this == CENTIMETER && unit == INCH) {
            return 0.393701; // one centimeter in inch
        } else {
            return 2.54; // one inch in centimeters
        }
    }
}
```

Becomes:

```
enum SmallDistanceUnit {

    CENTIMETER,
    INCH;

    static final double INCH_IN_CENTIMETERS = 2.54;
    static final double CENTIMETER_IN_INCHES = 1 / INCH_IN_CENTIMETERS;
    static final int IDENTITY = 1;


    double getConversionRate(SmallDistanceUnit unit) {
        if (this == unit) {
            return IDENTITY;
        }

        if (this == CENTIMETER && unit == INCH) {
            return CENTIMETER_IN_INCHES;
        } else {
            return INCH_IN_CENTIMETERS;
        }
    }
}
```

**Rule 4**: Replace comments with utility methods for example:

```
class FuelSystem {

    List<Double> tanks = new ArrayList<>();

    int getAverageTankFillingPercent() {
        double sum = 0;
        for (double tankFilling : tanks) {
            sum += tankFilling;
        }
        double averageFuel = sum / tanks.size();
        // round to integer percent
        return Math.toIntExact(Math.round(averageFuel * 100));
    }
}
```

Becomes:

```
class FuelSystem {

    List<Double> tanks = new ArrayList<>();

    int getAverageTankFillingPercent() {
        double sum = 0;
        for (double tankFilling : tanks) {
            sum += tankFilling;
        }
        double averageFuel = sum / tanks.size();
        return roundToIntegerPercent(averageFuel);
    }

    static int roundToIntegerPercent(double value) {
        return Math.toIntExact(Math.round(value * 100));
    }
}
```
**Rule 5**: Do document implementation decisions using a pattern like this:

```
/*
* In the context of [USE CASE],
* facing [CONCERN]
* we decided for [OPTION]
* to achieve [QUALITY],
* accepting [DOWNSIDE].
*/
```

For example:

```
class Inventory {
    
    private List<Supply> list = new ArrayList<>();

    void add(Supply supply) {
        list.add(supply);
        Collections.sort(list);
    }

    boolean isInStock(String name) {
        // fast implementation
        return Collections.binarySearch(list, new Supply(name)) != -1;
    }
}
```

Becomes:

```
    boolean isInStock(String name) {
        /*
         * In the context of checking availability of supplies by name,
         * facing severe performance issues with >1000 supplies
         * we decided to use the binary search algorithm
         * to achieve item retrieval within 1 second,
         * accepting that we must keep the supplies sorted.
         */
        return Collections.binarySearch(list, new Supply(name)) != -1;
    }
```

**Rule 6**: Use examples in your documentation for instance:

```
class Supply {

    /**
     * The code universally identifies a supply.
     *
     * It follows a strict format, beginning with an S (for supply), followed
     * by a five digit inventory number. Next comes a backslash that
     * separates the country code from the preceding inventory number. This
     * country code must be exactly two capital letters standing for one of
     * the participating nations (US, EU, RU, CN). After that follows a dot
     * and the actual name of the supply in lowercase letters.
     */
    static final Pattern CODE =
            Pattern.compile("^S\\d{5}\\\\(US|EU|RU|CN)\\.[a-z]+$");
}
```

Becomes:

```
class Supply {

    /**
     * The expression universally identifies a supply code.
     *
     * Format: "S<inventory-number>\<COUNTRY-CODE>.<name>"
     *
     * Valid examples: "S12345\US.pasta", "S08342\CN.wrench",
     * "S88888\EU.laptop", "S12233\RU.brush"
     *
     * Invalid examples:
     * "R12345\RU.fuel"      (Resource, not supply)
     * "S1234\US.light"      (Need five digits)
     * "S01234\AI.coconut"   (Wrong country code. Use US, EU, RU, or CN)
     * " S88888\EU.laptop "  (Trailing whitespaces)
    */
    static final Pattern SUPPLY_CODE =
            Pattern.compile("^S\\d{5}\\\\(US|EU|RU|CN)\\.[a-z]+$");
}
```


[[Naming Conventions Worksheet]](/_sat/files/9_5.pdf)
