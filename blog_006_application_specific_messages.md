---
title: "AIS Application-Specific Messages: Beyond Standard Position Reports"
date: 2025-01-20
categories: AIS
---

{% include meta.html %}
{% include clarity.html %}
{% include font.html %}

# AIS Application-Specific Messages: Beyond Standard Position Reports
_(Posted {{page.date}})_

Most people familiar with AIS (Automatic Identification System) know about the standard position reports (message types 1-3) and static vessel data (message type 5). However, the AIS standard defines a powerful mechanism for transmitting specialized information through **Application-Specific Messages** (ASM). These messages enable vessels, shore stations, and navigation aids to exchange domain-specific data that goes far beyond basic position and identification.

## What Are Application-Specific Messages?

Application-Specific Messages are binary data payloads transmitted via two specific AIS message types:

* **Message Type 6** - Addressed Binary Message: Point-to-point communication between two specific AIS devices
* **Message Type 8** - Binary Broadcast Message: Broadcasting information to all AIS receivers in range

Think of standard AIS messages as SMS texts with a fixed format, while application-specific messages are like emails with attachments - they can carry structured, specialized data for specific purposes.

## The Structure: DAC and FI

Every application-specific message is identified by two key components:

1. **DAC (Designated Area Code)**: Identifies the geographic region or organization that defined the message format
2. **FI (Function Identifier)**: Specifies the exact message type within that designated area

For example:
* DAC=001, FI=11 → International standard for "UTC and Date Response"
* DAC=200, FI=10 → European Inland Waterways "Ship Static and Voyage Data"
* DAC=316, FI=1 → United States "PAWSS (Portable AIS Wireless Safety System)"

## IMO SN.1/Circ.289: International Standards

The International Maritime Organization published circular SN.1/Circ.289 titled "Guidance on the use of AIS Application-Specific Messages" which standardizes 24 different message types under DAC=001 for international use. These cover critical maritime operations:

### Weather and Environmental Data

**Meteorological and Hydrological Data (FI=31)**: Ships and shore stations can broadcast comprehensive weather observations including:
* Wind speed and direction
* Air temperature and pressure
* Wave height and period
* Water temperature and salinity
* Ice conditions

**Weather Observation from Ship (FI=21)**: Simplified weather reports specifically formatted for vessel transmission.

**Environmental Messages (FI=26)**: Environmental sensor data including pollutant levels, water quality measurements, and other ecological parameters.

### Safety and Navigation

**Area Notice (FI=22, 23)**: Warnings about navigational hazards, restricted areas, or temporary obstructions. These can include:
* Drifting hazards
* Cable or pipeline areas
* Restricted areas
* Meteorological/oceanographic data collection zones
* Traffic separation schemes

**Marine Traffic Signal (FI=18, 19)**: Status of traffic control signals at locks, bridges, or harbor entrances - essentially maritime "traffic lights".

**Dangerous Cargo Indication (FI=25)**: Information about hazardous cargo aboard vessels, critical for emergency response planning.

**Tidal Window (FI=14)**: Notification of tidal conditions affecting safe passage through specific areas.

### Port and Vessel Operations

**Berthing Data (FI=20)**: Information about berth availability, assignment, and status at ports and terminals.

**Number of Persons on Board (FI=40)**: Critical for search and rescue operations, providing exact headcount.

**Route Information (FI=27, 28)**: Planned routes shared between vessels or from shore stations, enabling better traffic coordination.

### Vessel Information

**Extended Ship Static and Voyage Related Data (FI=24)**: Additional vessel details beyond the standard type 5 message, such as air draught (height above water).

**Text Description (FI=0, 1)**: Free-form text messages for communication between vessels or shore stations.

## Why Application-Specific Messages Matter

These specialized messages enable several important use cases:

**1. Enhanced Maritime Safety**: Real-time weather data from ships at sea creates a distributed network of meteorological sensors, improving forecasts and warnings for vessels in the area.

**2. Port Optimization**: Berthing data and vessel route information help ports manage traffic flow, reduce congestion, and optimize berth assignments.

**3. Environmental Monitoring**: Ships equipped with sensors can report environmental data, creating a global monitoring network for ocean health, pollution tracking, and climate research.

**4. Regulatory Compliance**: Dangerous cargo indications and persons-on-board messages support safety regulations and enable better emergency response.

**5. Inland Waterway Management**: Specialized messages for river and canal navigation help manage locks, bridges, and narrow passages efficiently.

## Decoding Application-Specific Messages with AISmessages

The [AISmessages](https://github.com/tbsalling/aismessages) Java library provides comprehensive support for decoding application-specific messages. Let's look at some practical examples.

### Example 1: Meteorological Data (DAC=1, FI=31)

Meteorological and hydrological data messages contain detailed weather observations. Here's how to decode them:

{% highlight java %}
import dk.tbsalling.aismessages.ais.messages.*;
import dk.tbsalling.aismessages.ais.messages.asm.*;
import dk.tbsalling.aismessages.nmea.messages.NMEAMessage;

public class WeatherDataExample {
    public static void main(String[] args) {
        // Real AIS message containing meteorological data
        String nmea = "!AIVDM,1,1,,A,85MwpKiKf:MPiQa:ofV@v2mQTfB26oEtbEVqh4j1QDQPHjhpkNJ3,0*11";
        
        NMEAMessage nmeaMessage = new NMEAMessage(nmea);
        AISMessage aisMessage = AISMessageFactory.create(nmeaMessage);
        
        if (aisMessage instanceof BinaryBroadcastMessage) {
            BinaryBroadcastMessage binaryMsg = (BinaryBroadcastMessage) aisMessage;
            ApplicationSpecificMessage asm = binaryMsg.getApplicationSpecificMessage();
            
            if (asm instanceof MeteorologicalAndHydrographicalData) {
                MeteorologicalAndHydrographicalData weather = 
                    (MeteorologicalAndHydrographicalData) asm;
                
                System.out.println("Weather Report from MMSI: " + binaryMsg.getSourceMmsi());
                System.out.println("Position: " + weather.getLatitude() + 
                                   ", " + weather.getLongitude());
                System.out.println("Wind Speed: " + weather.getWindSpeed() + " knots");
                System.out.println("Wind Direction: " + weather.getWindDirection() + "°");
                System.out.println("Air Temperature: " + weather.getAirTemperature() + "°C");
                System.out.println("Air Pressure: " + weather.getAirPressure() + " hPa");
                System.out.println("Wave Height: " + weather.getSignificantWaveHeight() + " m");
            }
        }
    }
}
{% endhighlight %}

This enables ships to become mobile weather stations, contributing to maritime meteorological services.

### Example 2: Inland Waterway Data (DAC=200, FI=10)

For vessels operating on rivers and canals in Europe, specialized messages provide critical information:

{% highlight java %}
public class InlandShipExample {
    public static void main(String[] args) {
        // AIS message from inland waterway vessel
        String nmea = "!AIVDM,1,1,,B,839udkPj2d<dteLMt1T0a?bP01L0,0*79";
        
        NMEAMessage nmeaMessage = new NMEAMessage(nmea);
        AISMessage aisMessage = AISMessageFactory.create(nmeaMessage);
        
        if (aisMessage instanceof BinaryBroadcastMessage) {
            BinaryBroadcastMessage binaryMsg = (BinaryBroadcastMessage) aisMessage;
            ApplicationSpecificMessage asm = binaryMsg.getApplicationSpecificMessage();
            
            if (asm instanceof InlandShipStaticAndVoyageRelatedData) {
                InlandShipStaticAndVoyageRelatedData inland = 
                    (InlandShipStaticAndVoyageRelatedData) asm;
                
                System.out.println("European Vessel ID: " + 
                                   inland.getUniqueEuropeanVesselIdentificationNumber());
                System.out.println("Length: " + inland.getLengthOfShip() + " m");
                System.out.println("Beam: " + inland.getBeamOfShip() + " m");
                System.out.println("Ship Type Code: " + inland.getShipOrCombinationType());
                System.out.println("Hazardous Cargo: " + inland.getHazardousCargo());
                System.out.println("Draught: " + inland.getDraught() + " m");
                System.out.println("Loaded Status: " + 
                                   (inland.getLoaded() == 1 ? "Loaded" : 
                                    inland.getLoaded() == 2 ? "Unloaded" : "N/A"));
            }
        }
    }
}
{% endhighlight %}

This data is essential for managing lock operations, bridge clearances, and traffic flow in constrained waterways.

### Example 3: Area Notices (DAC=1, FI=22)

Area notices warn about navigational hazards or define special areas:

{% highlight java %}
public class AreaNoticeExample {
    public static void main(String[] args) {
        String nmea = "!AIVDM,1,1,,B,8@30oni?1j020@00,0*23";
        
        NMEAMessage nmeaMessage = new NMEAMessage(nmea);
        AISMessage aisMessage = AISMessageFactory.create(nmeaMessage);
        
        if (aisMessage instanceof BinaryBroadcastMessage) {
            BinaryBroadcastMessage binaryMsg = (BinaryBroadcastMessage) aisMessage;
            ApplicationSpecificMessage asm = binaryMsg.getApplicationSpecificMessage();
            
            if (asm instanceof AreaNotice) {
                AreaNotice notice = (AreaNotice) asm;
                
                System.out.println("Notice Type: " + notice.getNoticeType());
                System.out.println("Start Time: " + notice.getStartTime());
                System.out.println("Duration: " + notice.getDuration() + " minutes");
                System.out.println("Area Shape: " + notice.getSubAreaType());
                // Area coordinates and geometry...
            }
        }
    }
}
{% endhighlight %}

### Example 4: Handling Unknown Messages

Not all application-specific messages are standardized. The library gracefully handles unknown types:

{% highlight java %}
public class UnknownMessageExample {
    public static void main(String[] args) {
        // Message with DAC=316 (US), FI=7 (custom application)
        String nmea = "!AIVDM,1,1,,A,8@30oni?1j020@00,0*23";
        
        NMEAMessage nmeaMessage = new NMEAMessage(nmea);
        AISMessage aisMessage = AISMessageFactory.create(nmeaMessage);
        
        if (aisMessage instanceof BinaryBroadcastMessage) {
            BinaryBroadcastMessage binaryMsg = (BinaryBroadcastMessage) aisMessage;
            
            System.out.println("DAC: " + binaryMsg.getDesignatedAreaCode());
            System.out.println("FI: " + binaryMsg.getFunctionalId());
            
            ApplicationSpecificMessage asm = binaryMsg.getApplicationSpecificMessage();
            
            if (asm instanceof UnknownApplicationSpecificMessage) {
                // Access raw binary data for custom parsing
                UnknownApplicationSpecificMessage unknown = 
                    (UnknownApplicationSpecificMessage) asm;
                System.out.println("Binary Data: " + binaryMsg.getBinaryData());
                // Custom parsing logic for regional/proprietary formats...
            }
        }
    }
}
{% endhighlight %}

### Example 5: Processing a Stream of Mixed Messages

In real-world applications, you'll receive a mix of standard and application-specific messages:

{% highlight java %}
import dk.tbsalling.aismessages.AISInputStreamReader;
import java.io.InputStream;

public class StreamProcessingExample {
    public static void main(String[] args) throws Exception {
        InputStream inputStream = // ... your AIS data source
        
        AISInputStreamReader reader = new AISInputStreamReader(
            inputStream,
            aisMessage -> {
                // Handle all message types
                switch (aisMessage.getMessageType()) {
                    case PositionReportClassAScheduled:
                        // Standard position report
                        break;
                        
                    case BinaryBroadcastMessage:
                    case AddressedBinaryMessage:
                        // Application-specific message
                        processApplicationSpecificMessage(aisMessage);
                        break;
                        
                    default:
                        // Other message types
                        break;
                }
            }
        );
        
        reader.run();
    }
    
    private static void processApplicationSpecificMessage(AISMessage msg) {
        BinaryBroadcastMessage binaryMsg = (BinaryBroadcastMessage) msg;
        ApplicationSpecificMessage asm = binaryMsg.getApplicationSpecificMessage();
        
        // Route to specific handlers based on message type
        if (asm instanceof MeteorologicalAndHydrographicalData) {
            handleWeatherData((MeteorologicalAndHydrographicalData) asm);
        } else if (asm instanceof AreaNotice) {
            handleAreaNotice((AreaNotice) asm);
        } else if (asm instanceof BerthingData) {
            handleBerthingData((BerthingData) asm);
        }
        // ... etc.
    }
    
    private static void handleWeatherData(MeteorologicalAndHydrographicalData weather) {
        // Store in database, update forecasts, etc.
    }
    
    private static void handleAreaNotice(AreaNotice notice) {
        // Update navigation warnings, alert nearby vessels, etc.
    }
    
    private static void handleBerthingData(BerthingData berthing) {
        // Update port management system, optimize berth allocation, etc.
    }
}
{% endhighlight %}

## Real-World Applications

Application-specific messages power several important maritime systems:

**Port Community Systems**: Ports like Hamburg use berthing data and route information messages to optimize vessel traffic management and berth allocation through systems like [PRISE](https://www.dakosy.de/en/solutions/port-community-system/prise/).

**Weather Services**: National meteorological agencies collect weather observations from vessels equipped to transmit meteorological data messages, improving forecast accuracy for remote ocean areas.

**Vessel Traffic Services (VTS)**: Shore-based traffic management centers use area notices and marine traffic signals to guide vessels through congested or hazardous areas.

**Search and Rescue**: The "Number of Persons on Board" message provides rescue coordinators with accurate passenger counts during emergency responses.

**Environmental Monitoring**: Research vessels and commercial ships contribute to ocean health monitoring by transmitting environmental data.

## Technical Specifications

For those implementing AIS systems or working with the specifications directly:

**ITU-R M.1371-5**: The definitive technical specification for AIS, defining message formats, bit layouts, and encoding rules. Available from [ITU](http://www.itu.int/rec/R-REC-M.1371-5-201402-I).

**IMO SN.1/Circ.289**: "Guidance on the use of AIS Application-Specific Messages" - defines the standardized international messages (DAC=001). This circular is essential reading for understanding the full scope of application-specific message types.

**Regional Standards**: Various regions define their own DAC codes:
* DAC=200: European inland waterways
* DAC=316: United States
* DAC=235: United Kingdom
* DAC=244: Netherlands
* And many others...

## Supported Messages in AISmessages

The AISmessages library currently supports decoding these application-specific message types:

**International (DAC=001)**:
* Text Description (FI=0, 1)
* UTC/Date Inquiry (FI=10)
* UTC/Date Response (FI=11)
* Tidal Window (FI=14)
* VTS Generated/Synthetic Targets (FI=17)
* Marine Traffic Signal (FI=18, 19)
* Berthing Data (FI=20)
* Weather Observation from Ship (FI=21)
* Area Notice - broadcast and addressed (FI=22, 23)
* Extended Ship Static and Voyage Related Data (FI=24)
* Dangerous Cargo Indication (FI=25)
* Environmental (FI=26)
* Route Information - broadcast and addressed (FI=27, 28)
* Meteorological and Hydrographical Data (FI=31)
* Number of Persons on Board (FI=40)

**Regional (DAC=200)**:
* Inland Ship Static and Voyage Related Data (FI=10)

The library is actively maintained and new message types are added as standards evolve and requirements emerge.

## Getting Started

To start working with application-specific messages in your Java applications, add AISmessages to your project:

{% highlight xml %}
<dependency>
    <groupId>dk.tbsalling</groupId>
    <artifactId>aismessages</artifactId>
    <version>4.1.0</version>
</dependency>
{% endhighlight %}

Then explore the `dk.tbsalling.aismessages.ais.messages.asm` package for all available application-specific message types.

## Conclusion

Application-specific messages extend AIS far beyond simple position tracking, enabling a rich ecosystem of maritime data exchange. From weather reporting to port operations, from environmental monitoring to safety warnings, these messages make AIS a truly multi-purpose maritime communications system.

The standardization efforts through IMO and ITU ensure interoperability, while regional extensions allow for local needs and innovations. Libraries like AISmessages make it straightforward to decode and utilize this wealth of maritime data in your applications.

Whether you're building port management systems, contributing to weather services, developing navigation aids, or simply exploring maritime data, application-specific messages provide the specialized information needed for modern maritime operations.

## Further Reading

* [AISmessages on GitHub](https://github.com/tbsalling/aismessages) - Full source code and documentation
* [ITU-R M.1371-5 Specification](http://www.itu.int/rec/R-REC-M.1371-5-201402-I) - The definitive AIS technical standard
* [IALA AIS Service](https://www.iala-aism.org/technical/ais/) - International Association of Marine Aids to Navigation and Lighthouse Authorities
* [What is AIS?](https://tbsalling.dk/blog_000_ais.html) - Introduction to AIS basics
* [AISmessages v4 - Immutable Value Objects](https://tbsalling.dk/blog_005_aismessages_major_v4.html) - Latest architecture improvements
