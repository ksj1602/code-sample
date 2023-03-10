# Code Sample

This is a code sample in C++ from one of my past projects [Goober Eats](https://github.com/ksj1602/goober-eats). The project is about a navigation program for a food delivery robot that takes in data for a street map and attempts to generate an optimal delivery route.

This particular code sample, contained in `PointToPointRouter.cpp`, deals with one of the key parts of the program: generating the optimal route from one point to another. It does so using Dijkstra's algorithm. In the overarching program, this method is repeatedly called to calculate the route between two points and then by chaining such routes, the program aims to output a route for all the deliveries.

In this particular code sample, the original code has been slightly modified to reflect improvements I thought of later on.

For context, here are the definitions of some of the structures/classes used in the code:

```c++
enum DeliveryResult
{
    DELIVERY_SUCCESS, NO_ROUTE, BAD_COORD
};

struct GeoCoord
{
    GeoCoord(std::string lat, std::string lon)
     : latitudeText(lat), longitudeText(lon), latitude(std::stod(lat)), longitude(std::stod(lon))
    {}

    GeoCoord()
     : latitudeText("0"), longitudeText("0"), latitude(0), longitude(0)
    {}

    std::string latitudeText;
    std::string longitudeText;
    double      latitude;
    double      longitude;
};

struct StreetSegment
{
    StreetSegment(const GeoCoord& s, const GeoCoord& e, std::string streetName)
     : start(s), end(e), name(streetName)
    {}

    StreetSegment()
    {}

    GeoCoord start;
    GeoCoord end;
    std::string name;
};

struct DeliveryRequest
{
    DeliveryRequest(std::string it, const GeoCoord& loc)
     : item(it), location(loc)
    {}
    std::string item;
    GeoCoord location;
};

struct processCoord {

    // the location of a processCoord is that of the GeoCoord it represents
    GeoCoord location;

    // variable required for Dijkstra's algorithm implementation
    double distanceFromSource = MAX_DOUBLE; // initializing all distances to infinity by default

    // < operator defined to use sets of processCoords, ordered by distance
    bool operator<(const processCoord &rhs) const {
        return (this->distanceFromSource < rhs.distanceFromSource);
    }
};
```

`StreetMap` is an expandable hash map which maps a `GeoCoord` to all the `StreetSegment`s that begin with it. This is used to retrieve all the possible routes we can take at any given point in our algorithm.
