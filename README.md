# Parking-system
#include <iostream>
using namespace std;

class Vehicle {
public:
    string licensePlate;
    int entryYear;

    Vehicle(string a, int b) : licensePlate(a), entryYear(b) {}
    virtual void displayInfo() = 0;
    virtual double calculateParkingFee() = 0;
    virtual ~Vehicle() {}
};

class Car : public Vehicle {
private:
    int doorCount;
public:
    Car(string x, int y, int z) : Vehicle(x, y), doorCount(z) {}

    void displayInfo() override {
        cout << "Car Plate: " << licensePlate << "\n";
        cout << "Year: " << entryYear << "\n";
        cout << "Door Count: " << doorCount << "\n";
    }

    double calculateParkingFee() override {
        return 2000.0 * doorCount;
    }
};

class Van : public Vehicle {
private:
    double loadCapacity;
public:
    Van(string x, int y, double z) : Vehicle(x, y), loadCapacity(z) {}

    void displayInfo() override {
        cout << "Van Plate: " << licensePlate << "\n";
        cout << "Year: " << entryYear << "\n";
        cout << "Load Capacity: " << loadCapacity << "\n";
    }

    double calculateParkingFee() override {
        return 5000.0 + (100.0 * loadCapacity);
    }
};

class Motorcycle : public Vehicle {
private:
    int engineCC;
public:
    Motorcycle(string x, int y, int z) : Vehicle(x, y), engineCC(z) {}

    void displayInfo() override {
        cout << "Motorcycle Plate: " << licensePlate << "\n";
        cout << "Engine: " << engineCC << " cc\n";
        cout << "Year: " << entryYear << "\n";
    }

    double calculateParkingFee() override {
        return 1000.0 + 0.5 * engineCC;
    }
};

class ParkingSlot {
public:
    Vehicle* vehicle;
    int slotNumber;

    ParkingSlot(int x) : slotNumber(x), vehicle(nullptr) {}

    ~ParkingSlot() {
        if (vehicle) {
            delete vehicle;
        }
    }

    bool isEmpty() {
        return vehicle == nullptr;
    }

    void parkVehicle(Vehicle* v) {
        if (isEmpty()) {
            vehicle = v;
        } else {
            cout << "Slot " << slotNumber << " is already occupied.\n";
            delete v;
        }
    }

    void removeVehicle() {
        if (!isEmpty()) {
            cout << "Removing vehicle with plate " << vehicle->licensePlate << " from slot " << slotNumber << "\n";
            delete vehicle;
            vehicle = nullptr;
        } else {
            cout << "Slot " << slotNumber << " is already empty.\n";
        }
    }

    void displaySlotInfo() {
        if (isEmpty()) {
            cout << "Slot " << slotNumber << " is empty.\n";
        } else {
            cout << "Information for Slot " << slotNumber << ":\n";
            vehicle->displayInfo();
            cout << "Parking Fee: " << vehicle->calculateParkingFee() << "\n";
        }
    }

    double getParkingFee() {
        return isEmpty() ? 0.0 : vehicle->calculateParkingFee();
    }
};

class ParkingManager {
private:
    ParkingSlot* slots[5];

public:
    ParkingManager() {
        for (int i = 0; i < 5; i++) {
            slots[i] = new ParkingSlot(i + 1);
        }
    }

    ~ParkingManager() {
        for (int i = 0; i < 5; i++) {
            delete slots[i];
        }
    }

    bool parkVehicle(Vehicle* v) {
        for (int i = 0; i < 5; i++) {
            if (slots[i]->isEmpty()) {
                slots[i]->parkVehicle(v);
                cout << "Vehicle parked successfully in slot " << (i + 1) << "\n";
                cout << "Parking Fee: " << slots[i]->getParkingFee() << "\n";
                return true;
            }
        }
        cout << "Sorry, parking is full.\n";
        delete v;
        return false;
    }

    void removeVehicle(int slotNumber) {
        if (slotNumber < 1 || slotNumber > 5) {
            cout << "Invalid slot number.\n";
            return;
        }
        slots[slotNumber - 1]->removeVehicle();
    }

    void displayAllSlots() const {
        for (int i = 0; i < 5; i++) {
            slots[i]->displaySlotInfo();
        }
    }
};

int main() {
    ParkingManager manager;
    int input, type;

    while (true) {
        cout << "\nEnter choice (1: Park, 2: Remove, 3: Display, 4: Exit): ";
        cin >> input;

        if (input == 4) break;

        Vehicle* vehicle = nullptr;

        if (input == 1) {
            cout << "Select vehicle type (1: Car, 2: Van, 3: Motorcycle): ";
            cin >> type;
            if (type == 1) {
                vehicle = new Car("A2", 1990, 4);
            } else if (type == 2) {
                vehicle = new Van("B3", 2006, 3000);
            } else if (type == 3) {
                vehicle = new Motorcycle("C6", 2018, 150);
            } else {
                cout << "Invalid vehicle type.\n";
                continue;
            }
            manager.parkVehicle(vehicle);
        } else if (input == 2) {
            cout << "Enter slot number to remove vehicle: ";
            cin >> type;
            manager.removeVehicle(type);
        } else if (input == 3) {
            manager.displayAllSlots();
        } else {
            cout << "Invalid choice.\n";
        }
    }

    return 0;
}
