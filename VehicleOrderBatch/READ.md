    global class VehicleOrderBatch implements Database.Batchable<sObject> {

    global Database.QueryLocator start (Database.BatchableContext bc) {

    return Database.getQueryLocator([

    SELECT Id, Status_c, Vehicle___c

    FROM Vehicle_Order___c

    WHERE Status__c

    'Pending'

    ]);

    }

    global void execute(Database.BatchableContext bc, List<Vehicle_Order_c> orderList) {

    Set<Id> vehicleIds = new Set<Id>();

    for (Vehicle_Order_c order orderList) {

    if (order.Vehicle_c != null) {

    vehicleIds.add(order.Vehicle__c);

    }

    }

    if (!vehicleIds.isEmpty()) {

    Map<Id, Vehicle___c> vehicleStockMap = new Map<Id, Vehicle___c>();

    for (Vehicle_c vehicle: [SELECT Id, Stock Quantity_c FROM Vehicle_c WHERE Id IN vehicleIds]) {

    vehicleStockMap.put(vehicle. Id, vehicle);

    }

    List<Vehicle_Order_c> ordersToUpdate = new List<Vehicle_Order_c>(); List<Vehicle__c> vehiclesToUpdate = new List<Vehicle__c>();

    for (Vehicle_Order_c order orderList) {

    if (vehicleStockMap.containsKey(order. Vehicle__c)) {

    Vehicle_c vehicle = vehicleStockMap.get(order. Vehicle__c);

    if (vehicle.Stock_Quantity___c > 0) {

    order.Status___c = 'Confirmed';

    vehicle.Stock_Quantity___c -= 1;

    ordersToUpdate.add(order);

    vehiclesToUpdate.add(vehicle);

    }

    }

    }

    if (!ordersToUpdate.isEmpty()) {

    update ordersToUpdate;

    }

    if (!vehicles ToUpdate.isEmpty()) {

    update vehiclesToUpdate;

    }

    }

    global void finish(Database.BatchableContext bc) {

    System.debug('Vehicle order batch job completed.');

    }

    }
