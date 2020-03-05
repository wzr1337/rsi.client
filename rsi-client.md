# RSI Client API proposal

# rsi-clinet.ts

```typescript
export RSI = {
  getServices : function (subscribe=false, options?:RSIQueryParams):Promise<Subject|Object> {
    return new Promise((resolve, reject) => {
      if(subscribe) {
        const ret = new Observable()
        ...
        ws.send(...)
        ws.on('data', (data) => {
            ret.next(JSON.parse(data).data);
        });
        return ret;
      }
      else {
        return http.GET(.....)
      }
    }
  }

  getResource: (resourceUrl:String, options?:RSIQueryParams) {
    const ret = new Observable()
    ...
    ret.next(data);
    return ret;
  }


  export async createElement(resourceUrl:String, payload?:RSIObject) {
    // promise for successful creation or error
    return new Promise((resolve, reject) => {
      const newElement = new Observable()
      ...
      // establis a RSI subscription to the newly created element when .subscribve() is called on the the Obersvable
      resolve(newElement);
    });
  }
}


```

# any client 

```typescript
import RSI from 'rsi-client';

const rsiClient = RSI.connect('https://my-server.com/api/v1');

const services = await rsiClient.getServices();
// or subscribe
const serviceSubscription = rsiClient.getServices().subscribe(...);
serviceSubscription.unsubscribe();

const remoteaccess = services['remoteaccess'];
// or
const remoteaccess = await rsiClient.getService('remoteaccess');

// resource level access
const vehicles = await remoteaccess['vehicles?$expand=users&vin=ABDC123456'];
// or subscribe
const vehiclesSubscription = remoteaccess['vehicles?$expand=users&vin=ABDC123456'].subscribe(...);
vehiclesSubscription.unsubscribe();
// or
const vehicles = await rsiClient.getResource('remoteaccess/vehicles', {$expand:"users", vin:"ABDC123456"});

// or subscribe
const vehiclesSubscription = rsiClient.getResource('remoteaccess/vehicles?$expand=users&vin=ABDC123456').subscribe(...);
vehiclesSubscription.unsubscribe();

// create a new vehicle
const newVehicle = await vehicles.create({vin: "SOME123456"});
// or get an element to subscribe to back
vehicles.create({vin: "SOME123456"}).subscribe(...);

// element level access

const myCar = await vehicles['55191944-2187-4fa9-9d3c-77e5f8a8b765?$expand=users&vin=ABDC123456'];
// or subscribe
const vehicleSubscription = vehicles['55191944-2187-4fa9-9d3c-77e5f8a8b765?$expand=users&vin=ABDC123456'].subscribe(...);
vehicleSubscription.unsubscribe();
// or
const myCar = await rsiClient.getElement('remoteaccess/vehicles/55191944-2187-4fa9-9d3c-77e5f8a8b765?$expand=users&vin=ABDC123456');
// or subscribe
const vehicleSubscription = rsiClient.getElement('remoteaccess/vehicles/55191944-2187-4fa9-9d3c-77e5f8a8b765?$expand=users&vin=ABDC123456').subscribe(...);
vehicleSubscription.unsubscribe();

// remove a vehicle
const newVehicle = await vehicles.delete('remoteaccess/vehicles/55191944-2187-4fa9-9d3c-77e5f8a8b765');

// update a vehicle
const newVehicle = await vehicles.delete('remoteaccess/vehicles/55191944-2187-4fa9-9d3c-77e5f8a8b765');

```
