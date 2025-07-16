    global class VehicleOrderBatchScheduler implements Schedulable {

    global void execute(SchedulableContext sc) {

        VehicleOrderBatch batchJob = new VehicleOrderBatch();

        Database.executeBatch(batchJob, 50); // 50 is the batch size

    }

    }


Open Execute Anonymous window


    String cronExp = '0 0 12 * * ?'; // Runs daily at 12:00 PM

    System.schedule('Daily Vehicle Order Processing', cronExp, new VehicleOrderBatchScheduler());
