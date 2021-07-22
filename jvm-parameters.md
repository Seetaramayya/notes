# JVM Parameters

- `InitialRAMPercentage` allows to configure the initial heap size of app, 
  percentage of total physical memory. This will be picked only when `-Xms` 
  is __not__ configured   
- `MinRAMPercentage` allows to configure the Max heap size of the app, 
  this will be picked only when physical memory is low (for example 100MB) 
  otherwise `MaxRAMPercentage`. Both parameters will be ignored if `-Xmx` 
  is set
- `MaxRAMPercentage` allows to configure the Max heap size of the app.

# Resources

- [Dzone article](https://dzone.com/articles/difference-between-initialrampercentage-minramperc)