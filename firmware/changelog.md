## Firmware Versions

- **v1.0.0**  
Initial release—supports hardware v0.4

- **v1.1.0** [REMOVED]
Supports hardware v0.4 only. Change to hardware means that RF module now directly triggers Gate Controller, rather than through the CIM. Code to receive the RF_OUT signal to GPIO3 was removed.

- **v2.0.0**  
Supports hardware v0.4 and v0.5. High-level logic sequence was changed. Hardware change to v0.5 means that RF_OUT was merged with CIM_OUT, and so GPIO3 was monitoring all gate triggers regardless of which module was pulling the line. The CIM's virtual gate status was now calculated upon seeing a gate trigger event, rather than as part of the CIM's own trigger output function.