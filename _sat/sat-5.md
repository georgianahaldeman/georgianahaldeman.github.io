---
title: "How to address things going wrong"
date: 2024-09-17
excerpt: ""
collection: sat
---

1. Check for the type before casting, for example:

```
class Network {

    ObjectInputStream inputStream;
    InterCom interCom;

    void listen() throws IOException, ClassNotFoundException {
        while (true) {
            Object signal = inputStream.readObject();
            CrewMessage crewMessage = (CrewMessage) signal;
            interCom.broadcast(crewMessage);
        }
    }
}
```

With this improvement becomes:

```
class Network {

    ObjectInputStream inputStream;
    InterCom interCom;

    void listen() throws IOException, ClassNotFoundException {
        while (true) {
            Object signal = inputStream.readObject();
            if (signal instanceof CrewMessage) {
                CrewMessage crewMessage = (CrewMessage) signal;
                interCom.broadcast(crewMessage);
            }
        }
    }
}
```

### Exceptions

2. **Fail Fast**: Separate normal paths from validation paths, for example:

```
class CruiseControl {
    static final double SPEED_OF_LIGHT_KMH = 1079252850;
    static final double SPEED_LIMIT = SPEED_OF_LIGHT_KMH;

    private double targetSpeedKmh;

    void setTargetSpeedKmh(double speedKmh) {
        if (speedKmh < 0) {
            throw new IllegalArgumentException();
        } else if (speedKmh <= SPEED_LIMIT) {
            targetSpeedKmh = speedKmh;
        } else {
            throw new IllegalArgumentException();
        }
    }
}
```

Becomes:

```
class CruiseControl {
    static final double SPEED_OF_LIGHT_KMH = 1079252850;
    static final double SPEED_LIMIT = SPEED_OF_LIGHT_KMH;

    private double targetSpeedKmh;

    void setTargetSpeedKmh(double speedKmh) {
        if (speedKmh < 0 || speedKmh > SPEED_LIMIT) {
            throw new IllegalArgumentException();
        }

        targetSpeedKmh = speedKmh;
    }
}
```

3. Use the type of the exception to learn more about what went wrong and communicate that in the exception message, for example:

```
class TransmissionParser {
    static Transmission parse(String rawMessage) {
        if (rawMessage != null
                && rawMessage.length() != Transmission.MESSAGE_LENGTH) {
            throw new IllegalArgumentException("Bad message received!");
        }

        String rawId = rawMessage.substring(0, Transmission.ID_LENGTH);
        String rawContent = rawMessage.substring(Transmission.ID_LENGTH);
        try {
            int id = Integer.parseInt(rawId);
            String content = rawContent.trim();
            return new Transmission(id, content);
        } catch (Exception e) {
            throw new IllegalArgumentException("Bad message received!");
        }
    }
}
```
The exception type in `catch` is too general, we can first make it more specific:

```
class TransmissionParser {
    static Transmission parse(String rawMessage) {
        if (rawMessage != null &&
                rawMessage.length() != Transmission.MESSAGE_LENGTH) {
            throw new IllegalArgumentException("Bad message received!");
        }

        String rawId = rawMessage.substring(0, Transmission.ID_LENGTH);
        String rawContent = rawMessage.substring(Transmission.ID_LENGTH);
        try {
            int id = Integer.parseInt(rawId);
            String content = rawContent.trim();
            return new Transmission(id, content);
        } catch (NumberFormatException e) {
            throw new IllegalArgumentException("Bad message received!");
        }
    }
}
```

We can communicate this information further using the message in the exception like so:

```
class TransmissionParser {
    static Transmission parse(String rawMessage) {
        if (rawMessage != null
                && rawMessage.length() != Transmission.MESSAGE_LENGTH) {
            throw new IllegalArgumentException(
                String.format("Expected %d, but got %d characters in '%s'",
                    Transmission.MESSAGE_LENGTH, rawMessage.length(),
                    rawMessage));
        }

        String rawId = rawMessage.substring(0, Transmission.ID_LENGTH);
        String rawContent = rawMessage.substring(Transmission.ID_LENGTH);
        try {
            int id = Integer.parseInt(rawId);
            String content = rawContent.trim();
            return new Transmission(id, content);
        } catch (NumberFormatException e) {
            throw new IllegalArgumentException(
                String.format("Expected number, but got '%s' in '%s'",
                        rawId, rawMessage));
        }
    }
}
```

You may also pass the cause chain to the exception like so:

```
class TransmissionParser {
    static Transmission parse(String rawMessage) {
        if (rawMessage != null
                && rawMessage.length() != Transmission.MESSAGE_LENGTH) {
            throw new IllegalArgumentException(
                String.format("Expected %d, but got %d characters in '%s'",
                        Transmission.MESSAGE_LENGTH, rawMessage.length(),
                        rawMessage));
        }

        String rawId = rawMessage.substring(0, Transmission.ID_LENGTH);
        String rawContent = rawMessage.substring(Transmission.ID_LENGTH);
        try {
            int id = Integer.parseInt(rawId);
            String content = rawContent.trim();
            return new Transmission(id, content);
        } catch (NumberFormatException e) {
            throw new IllegalArgumentException(
                String.format("Expected number, but got '%s' in '%s'",
                    rawId, rawMessage), e);
        }
    }
}
```

[[Worksheet]](/_sat/files/9_17.pdf)