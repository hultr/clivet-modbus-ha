# Clivet Sphera EVO 2.0 Tower — Complete Modbus Register Reference

## Control Registers (0–12)

These are read/write control registers for commanding the heat pump. Use with caution.

| Reg | Name | Manual Description | Explanation | Notes |
| --- | --- | --- | --- | --- |
| 0 | ON/OFF | Bit0: 0=OFF(TS), 1=ON(TS) (ROOM TEMP thermostat control) / Bit1: 0=OFF(T1S), 1=ON(T1S) (WATER FLOW TEMP control Zone 1) / Bit2: 0=DHW(T5S) OFF, 1=DHW(T5S) ON / Bit3: 0=OFF(T2S), 1=ON(T2S) (WATER FLOW TEMP control Zone 2) / Bit4–15: Reserved | Master ON/OFF bitmask. Controls which subsystems are active. / TS = room thermostat based control / T1S = flow temp control zone 1 / T5S = DHW tank heating / T2S = flow temp control zone 2 | R/W |
| 1 | Mode setting | 1: auto mode; 2: Cooling; 3: heating; other value: Invalid | Sets the operating mode. Must match what the unit supports. | R/W |
| 2 | Set the water temperature T1s | bit8–bit15: Setting water temp T1s corresponding to ZONE 2 / bit0–bit7: Setting water temp T1s corresponding to ZONE 1 | Water flow temperature setpoint. Low byte = Zone 1, High byte = Zone 2. Both packed into one 16-bit register. | R/W |
| 3 | Setting air temperature Ts | Room temp. Setting, 17°C–30°C if Ta is enabled; Read=real*2; for example: set=17.5°C, read=35 | Room air temperature setpoint. Value is actual temp × 2 (so 35 = 17.5°C). Only effective if room sensor Ta is enabled. | R/W, scale ×2 |
| 4 | T5s | Börvärde VV, 20°C–60/75°C (Sphera A with AHS can be set to 75, other unit for 60) default = 50 | DHW tank temperature setpoint. Max 75°C with AHS (Auxiliary Heat Source), otherwise 60°C. (Swedish manual: 'Börvärde VV' = hot water setpoint) | R/W |
| 5 | Function setting | Bit0–3: Reserved / Bit4: Sterilization (disinfect) / Bit5: Holiday away (read only, can not write) / Bit6: Silent mode / Bit7: 0=silent mode level1, 1=silent mode level2 / Bit8: Holiday home (read only, can not write) / Bit9: Reserved / Bit10: ECO mode / Bit11: DHW pump return pump / Bit12: 1=Zone 1 curve enable, 0=disable / Bit13: 1=Zone 2 curve enable, 0=disable / Bit14–15: Reserved | Function control bitmask. Controls silent mode, ECO, sterilization (legionella cycle), weather curves, and DHW recirc pump. Note: Holiday away/home bits are read-only. | R/W (partial) |
| 6 | Curve selection | bit8–bit15: ZONE 2 Curve 1–9 / bit0–bit7: ZONE 1 Curve 1–9 | Selects which weather compensation curve (1–9) to use for each zone. Low byte = Zone 1, High byte = Zone 2. | R/W |
| 7 | Forced hot water | 0: disable; 1: Forced ON; 2: Forced OFF / TBH is for tank heater. | Force DHW heating on or off. TBH = Tank Backup Heater (electric element in the DHW tank). | R/W |
| 8 | Force TBH | 0: disable; 1: Forced ON; 2: Forced OFF / IBH is for system | Force the Tank Backup Heater on or off. | R/W |
| 9 | Force IBH | 0: disable; 1: Forced ON; 2: Forced OFF / TBH and IBH cannot be forced together | Force the Internal Backup Heater (system electric heater) on or off. Cannot be active simultaneously with TBH forced on. | R/W |
| 10 | (unnamed) | 0–24 hrs | Likely a timer setting. Possibly DHW schedule or delay timer. Description is minimal. | R/W |
| 11 | T1s setpoint zone 1 | Setting water temperature T1s corresponding to ZONE 1 | Dedicated zone 1 water temperature setpoint. May duplicate the low byte of register 2. | R/W |
| 12 | T1s setpoint zone 2 | Setting water temperature T1s corresponding to ZONE 2 | Dedicated zone 2 water temperature setpoint. May duplicate the high byte of register 2. | R/W |


## STATUS (Registers 100–147)

Read-only status registers for ODU, compressor, refrigerant circuit, and general system state.

| Reg | Name | Manual Description (verbatim) | Explanation | Notes |
| --- | --- | --- | --- | --- |
| 100 | Operating frequency | Compressor operating frequency in Hz. Send value = actual value | Compressor speed in Hz. 0 = off. Higher = more capacity. This is the actual speed, not the target (see reg 132). | Confirmed |
| 101 | Operating mode | The actual operating mode of the whole machine. / 0 = OFF / 3 = cooling / 0 = heating / [Swedish manual has printing error] | Current operating mode. The Swedish translation contains a printing error (lists 0 for both OFF and heating, and 3 for cooling). / CORRECTED from Italian original ('Modalità operativa dell’unità'): / 0 = OFF (spegnimento) / 2 = COOL (raffreddamento) / 3 = HEAT (riscaldamento) / This matches register 1000 exactly. | CORRECTED |
| 102 | Fan speed | Fan speed, unit r/min. Send value = actual speed | ODU fan speed in RPM. |  |
| 103 | PMV opening | Outside the unit electronic expansion valve opening degree, the unit: P. Send value = actual value (show only 8 multiples Only Multiples of 8 Will BE Shown) | PMV = Pulse Motor Valve = Electronic Expansion Valve (EXV). Shows the valve opening position in pulses. Only reports in steps of 8. |  |
| 104 | Inlet water temperature | TW_in, unit: °C. Send value = actual value | Water return temperature (water coming back from the heating system to the heat pump). |  |
| 105 | Outlet water temperature | TW_out, unit: °C. Send value = actual value | Water supply temperature (water leaving the heat pump toward the heating system). |  |
| 106 | T3 temperature | Condenser temperature in °C. Send value = actual value | Confirmed from Italian original: 'Temperatura condensatore' = condenser temperature. T3 is the condenser/coil temperature sensor, NOT the outdoor ambient sensor. Note: some Midea community sources (e.g., Mosibi ESPHome YAML) use T3 for outdoor ambient — this appears to be a different convention or a different sensor assignment on other Midea variants. For the Clivet Sphera EVO 2.0 specifically, T3 = condenser. | Confirmed IT |
| 107 | T4 temperature | Outside temperature in °C. Send value = actual value | Confirmed from Italian original: 'Temperatura esterna' = outside/ambient temperature. T4 is the outdoor ambient sensor on the ODU. | Confirmed IT |
| 108 | Exhaust gas temperature | Compressor exhaust temperature Tp, in °C. Send value = actual value | Compressor discharge temperature. Critical for health monitoring. Values >120°C indicate problems. |  |
| 109 | Return gas temperature | Back to the compressor temperature Th, unit: °C. Send value = actual value | Suction gas temperature returning to compressor. Used for superheat calculation. |  |
| 110 | T1 | Water outlet temperature of the backup heater or additional heating source (IBH and/or AHS) | Water temperature after the backup/auxiliary heater. Shows combined output of heat pump + backup. |  |
| 111 | T1B | Flow water temperature for low-temperature zone (only in case of 2-zone kit) | Water flow temperature for zone 2 (low-temp zone, e.g., underfloor). Only relevant with 2-zone kit installed. | 2-zone only |
| 112 | T2 | The temperature of the refrigerant liquid side, unit: °C. Send value = actual value | Refrigerant liquid line temperature. Used for subcooling calculations. |  |
| 113 | T2B | Refrigerant gas side temperature, unit: °C. Send value = actual value | Refrigerant gas side temperature. |  |
| 114 | Ta | Indoor temperature, unit: °C / Send value = actual value | Indoor/room air temperature from the wired controller or connected room sensor. |  |
| 115 | T5 | Water tank temperature | DHW tank temperature. Key for DHW control and your guard automation. |  |
| 116 | Pressure value 1 | ODU high pressure value, unit: kPa. Send value = true value | Refrigerant high-side pressure in kPa. Critical safety parameter. |  |
| 117 | Pressure value 2 | ODU low pressure value, unit: kPa. Send value = true value (reserved) | Refrigerant low-side (suction) pressure in kPa. Marked 'reserved' may not report on all models. | May be empty |
| 118 | ODU current | ODU operation current, unit A, sending value = actual value | Outdoor unit current draw in Amps. |  |
| 119 | ODU voltage | ODU voltage unit: V. Send value = actual value (reserved) | ODU supply voltage. Marked reserved. | May be empty |
| 120 | Tbt1 | Tbt1, unit: °C. Send value = actual value | Buffer/inertial tank temperature sensor 1. Only relevant with buffer tank installed. | Config dep. |
| 121 | Tbt2 | Tbt2, unit: °C. Send value = actual value | Buffer/inertial tank temperature sensor 2 or secondary tank. | Config dep. |
| 122 | Compressor running time | Compressor operation time, unit: hour, send value = actual value | Compressor total runtime in hours. Useful for maintenance scheduling. |  |
| 123 | Unit | The 200 register is reserved for type 0702, and the value for type 071X represents type capacity 4-30 represents 4-30KW | Unit type/capacity identifier. Value encodes the capacity range (4–30 kW). The 200 register reference is model-specific. |  |
| 124 | Current fault |  | Current active fault code. Cross-reference with error code table. |  |
| 125 | Fault 1 | Specific fault code, refer to the code table. | Historical fault code 1. |  |
| 126 | Fault 2 | Specific fault code, refer to the code table. | Historical fault code 2. |  |
| 127 | Fault 3 | Specific fault code, refer to the code table. | Historical fault code 3. |  |

### Register 128: Status bit 1 (Bitmask)

| Reg | Bit | Name | Manual Description | Explanation |
| --- | --- | --- | --- | --- |
| 128 | BIT0 | Recirculation pump | Recirculation pump | Water circulation pump is running. |
| 128 | BIT1 | Defrost | Defrost | Defrost cycle active. System reverses to melt ice from outdoor coil. |
| 128 | BIT2 | Anti-freeze | Anti-freeze | Anti-freeze protection. Runs pump/compressor to prevent water freezing. |
| 128 | BIT3 | Oil return | Oil return | Compressor oil return cycle active. |
| 128 | BIT4 | Remote ON/OFF (1 : d8) | Remote ON/OFF (1 : d8) | Remote on/off status. 'd8' likely refers to a dip switch or digital input. |
| 128 | BIT5 | ODU test mode | ODU test mode | Outdoor unit is in test/commissioning mode. |
| 128 | BIT6 | Room thermostat heat | Room thermostat heat | Room thermostat is calling for heating. |
| 128 | BIT7 | Room thermostat cool | Room thermostat cool | Room thermostat is calling for cooling. |
| 128 | BIT8 | Solar input | Solar input | Solar thermal input is active/available. Solar kit only. |
| 128 | BIT9 | Water tank anti-freezing | Water tank anti-freezing | DHW tank anti-freeze protection is active. |
| 128 | BIT10 | SG signal | SG 1: normal electrical price 0: high electrical price | Smart Grid pricing signal. 1 = normal price, 0 = high price. |
| 128 | BIT11 | EVU signal | EVU 1: free electrical 0: based on SG signal | EVU (utility) load-shedding signal. European smart grid feature. |
| 128 | BIT12 | Reserved | Reserved |  |
| 128 | BIT13 | Upload SN | Upload SN, 1: ask for 0: not ask for | Request to upload serial number. |
| 128 | BIT14 | Upload software version | Upload software version, 1: ask for; 0: not ask for | Request to upload firmware version. |
| 128 | BIT15 | Installation parameter | Ask for installation parameter, 1: ask for; 0: not ask for | Request for installation parameters. |

### Register 129: Load output (Bitmask)

| Reg | Bit | Name | Manual Description | Explanation |
| --- | --- | --- | --- | --- |
| 129 | BIT0 | IBH | IBH | Internal Backup Heater stage 1 is active (electric resistance heater). |
| 129 | BIT1 | IBH2 | IBH2 | Internal Backup Heater stage 2 is active. |
| 129 | BIT2 | TBH | TBH | Tank Backup Heater is active (electric element in DHW tank). |
| 129 | BIT3 | Water pump PUMP_I | Water pump PUMP_I | IDU water circulation pump is running. |
| 129 | BIT4 | SV1 | SV1 | Solenoid Valve 1 — main DHW/heating diverter valve energized. |
| 129 | BIT5 | SV2 | SV2 | Solenoid Valve 2 — secondary circuit/zone diverter valve. |
| 129 | BIT6 | External pump P_o | External pump P_o | External pump is running. |
| 129 | BIT7 | Recirculation pump P_D | Recirculation pump P_D | DHW recirculation pump is running. |
| 129 | BIT8 | Mix pump P_C | Mix pump P_C | Mixing circuit pump is running. |
| 129 | BIT9 | SV3 | SV3 | Solenoid Valve 3 — third circuit valve (likely solar or third zone). |
| 129 | BIT10 | HEAT4 | HEAT4 | Fourth heating output or crankcase heater. |
| 129 | BIT11 | Solar pump Pump_S | Solar pump Pump_S | Solar circuit pump is running. Solar kit only. |
| 129 | BIT12 | ALARM | ALARM | System alarm is active. Check fault registers. |
| 129 | BIT13 | ON Compressor | ON Compressor | Compressor is running. |
| 129 | BIT14 | External heat source | External heat source | External/auxiliary heat source (e.g., gas boiler) is being called. |
| 129 | BIT15 | DEFROST | DEFROST | Defrost active on load output side. |

### Registers 130–147: Software, Capacity, ODU Details

| Reg | Name | Manual Description (verbatim) | Explanation | Notes |
| --- | --- | --- | --- | --- |
| 130 | Software version of internal unit | 0–99 The software version of the internal unit | IDU controller firmware version number. |  |
| 131 | Software version of user interface | 0–99 The software version of the user interface | Wired controller/display firmware version. |  |
| 132 | Unit target frequency | Frequency | Target compressor frequency the controller is aiming for. Compare with reg 100 (actual). |  |
| 133 | DC bus current | Unit: Ampere | DC bus current inside the inverter. |  |
| 134 | DC bus voltage | Return value = actual value / 10 (Unit: Volt) | DC bus voltage. Divide raw value by 10 to get Volts. | Scale /10 |
| 135 | TF module temperature | Units (°C) - Outside unit feedback | Power module (IGBT) temperature in the inverter. High values = thermal stress. |  |
| 136 | Curve 1 T1S | Actual value - Registration result corresponding to area 1 | Weather compensation curve 1 calculated output for zone 1. |  |
| 137 | Curve 2 T1S | Actual value --- Calculation result corresponding to area 2 | Weather compensation curve 2 calculated output for zone 2. |  |
| 138 | Water flow | Actual value *100 units: M3/H | Water flow rate. Manual says 'Actual value *100 units: M3/H'. GUESSWORK: This most likely means raw_value / 100 = m³/h (i.e., the raw value is the actual flow multiplied by 100 to avoid decimals). Reasoning: typical residential heat pump flow rates are 0.5–2.0 m³/h, so raw values of 50–200 would be plausible. If it meant raw × 100, you'd get absurd flow rates. Register 1019 uses identical phrasing. Verify against a physical flow meter or by checking if raw values fall in the 50–200 range during operation. | GUESSWORK |
| 139 | ODU current limit scheme | Scheme value ----- Outside machine feedback 174 | Current limiting scheme. References internal ODU register 174. |  |
| 140 | IDU capacity | Actual value *100 units: kW | IDU capacity. GUESSWORK: Same ambiguous '*100' notation as water flow. Most likely raw_value / 100 = kW. For a 10 kW unit, raw value would be 1000. If it meant raw × 100, a value of 10 would give 1000 kW which is absurd for residential. | GUESSWORK |
| 141 | T solar |  | Solar collector temperature. Only with solar kit. | Solar kit |
| 142 | Number of cascade units | BIT1-BIT15 represents 1-15 unit online/offline status / BIT0 Reserved | Cascade system: each bit (BIT1-BIT15) = one cascade unit (1-15) online/offline. Single-unit: ignore. | Cascade only |
| 143 | High bit of electrical | Power consumption | Upper 16 bits of total electrical consumption. Combine with reg 144. |  |
| 144 | Low bit of electrical | Power consumption | Lower 16 bits. Total = (reg143 << 16) + reg144. |  |
| 145 | High bit of heat | System heating capacity | Upper 16 bits of total heat energy produced. |  |
| 146 | Low bit of heat | System heating capacity | Lower 16 bits. Total = (reg145 << 16) + reg146. |  |
| 147 | A series-Sphera AHS voltage output | Actual value *10 units: kW | Auxiliary Heat Source output. Raw × 10 = kW. A-series/Sphera models. | A-series |


## Unit Status Registers (1000–1042)

IDU-specific status registers.

| Reg | Name | Manual Description (verbatim) | Explanation | Notes |
| --- | --- | --- | --- | --- |
| 1000 | Operation mode | MODE Operation, 2: COOL, 3: HEAT, 0: OFF | IDU operation mode. 0=OFF, 2=COOL, 3=HEAT. |  |
| 1001 | Com. Rps | Com. rps, unit: Hz, read = real | Compressor rotations per second. Should correlate with reg 100. |  |
| 1002 | Twi | TW_in, unit: °C; inlet water temperature; read = real | Water inlet temperature from IDU sensor. Compare with reg 104. |  |
| 1003 | Two | TW_out, unit: °C; outlet water temperature; read = real | Water outlet temperature from IDU sensor. Compare with reg 105. |  |
| 1004 | Tsolar | Tsolar, unit: °C; solar temperature; read = real | Solar collector temperature. Solar kit only. | Solar kit |
| 1005 | Salve unit error code | Refer to error code table | Slave unit error code (cascade) or possibly 'valve unit error code' (translation). |  |
| 1006 | P6 error | Reserved | Reserved. P6 is a specific error code (module fault). |  |

### Register 1007: IDU status 1 (Bitmask)

| Reg | Bit | Name | Manual Description | Explanation |
| --- | --- | --- | --- | --- |
| 1007 | BIT0 | Deforst | Deforst | Defrost active (note: 'Deforst' is a typo in manual for 'Defrost'). |
| 1007 | BIT1 | Anti-freeze | Anti-freeze | Anti-freeze protection mode active. |
| 1007 | BIT2 | Oil return | Oil return | Compressor oil return operation active. |
| 1007 | BIT3–7 | Reserved | Reserved | Unused bits. |

### Register 1008: IDU status 2 (Bitmask)

| Reg | Bit | Name | Manual Description | Explanation |
| --- | --- | --- | --- | --- |
| 1008 | BIT0 | Cool | Cool | Cooling mode is active. |
| 1008 | BIT1 | Heat | Heat | Heating mode is active. |
| 1008 | BIT2 | DHW | DHW | Domestic Hot Water production is active. |
| 1008 | BIT3 | IBH backup | IBH backup system electrical resistance; 1- with 0- without | Indicates IBH is installed (not necessarily running). 1=present, 0=absent. |
| 1008 | BIT4 | T1 water outlet temp | T1 water outlet temperature; 1- with 0- without | Indicates T1 outlet temp sensor is installed. 1=present, 0=absent. |
| 1008 | BIT5–7 | Reserved | Reserved | Unused. The manual shows 4 'Reserved' rows above bit4. |

### Register 1009: IDU load (Bitmask)

| Reg | Bit | Name | Manual Description | Explanation |
| --- | --- | --- | --- | --- |
| 1009 | BIT0 | IBH1 | IBH1 = 1- active; 0- off | Internal Backup Heater stage 1 is running. |
| 1009 | BIT1 | IBH2 | IBH2 = 1- active; 0- off | Internal Backup Heater stage 2 is running. |
| 1009 | BIT2 | Reserved | Reserved |  |
| 1009 | BIT3 | Water pump PUMP_i | Water pump PUMP_i 1- active; 0- off | IDU water circulation pump is running. |
| 1009 | BIT4 | RUN | RUN 1- active; 0- off | Compressor RUN command is active. |
| 1009 | BIT5 | DEFROST | DEFROST 1- active; 0- off | Defrost cycle is active. |
| 1009 | BIT6 | Reserved | Reserved |  |
| 1009 | BIT7 | HEAT 4 compressor heater | HEAT 4 compressor heater 1- active; 0- off | Compressor crankcase heater is active. Prevents refrigerant pooling during standby. |

Register 1010: IDU load output — All bits Reserved.

### Registers 1011–1021: IDU Temperatures and System Data

| Reg | Name | Manual Description (verbatim) | Explanation | Notes |
| --- | --- | --- | --- | --- |
| 1011 | T1 | Total water outlet, unit:°C, read = real invalid: 0x7F | Primary water outlet temperature. 0x7F (127) = sensor error/disconnected. |  |
| 1012 | T1B | Total water outlet (after Auxiliary heat source), unit:°C. R & It read= real invalid: 0x7F | Water temp after auxiliary/backup heater. |  |
| 1013 | T2 | Liquid refrigerant temperature, unit:°C. read = real invalid: 0x7F | Refrigerant liquid line temperature. |  |
| 1014 | T2B | Liquid gas temperature, unit:°C. read = real invalid: 0x7F | Refrigerant liquid/gas temperature. |  |
| 1015 | T5 | Tank tem. unit:°C. read = real invalid: 0x7F | DHW tank temperature. |  |
| 1016 | Ta | Indoor air tem. unit:°C. read = real invalid: 0x7F | Indoor/room air temperature. |  |
| 1017 | Tbt1 | Unit inertial storage tank temperature:°C. read = real invalid: 0x7F | Buffer/inertial tank temperature. | Config dep. |
| 1018 | Tbt2 | Unit additional storage tank temperature:°C. read = real invalid: 0x7F | Secondary storage tank temperature. | Config dep. |
| 1019 | Water flow | Read=real* 100 unit: M3/H | Water flow rate. Same scaling as reg 138. GUESSWORK: raw_value / 100 = m³/h. See reg 138 explanation for reasoning. | GUESSWORK |
| 1020 | Unit type | 10-18: means 10-18KW (second generation machines active) | Unit capacity identifier. Value = kW rating. |  |
| 1021 | Unit target frequency |  | IDU-side target frequency. Compare with reg 132. |  |

### Registers 1022–1042: IDU Software, ODU Telemetry

| Reg | Name | Manual Description (verbatim) | Explanation | Notes |
| --- | --- | --- | --- | --- |
| 1022 | Software version | 1-99 means IDU software version | IDU firmware version. |  |
| 1023 | High bit of capacity |  | Upper 16 bits of capacity value. |  |
| 1024 | Low bit of capacity |  | Lower 16 bits of capacity value. |  |
| 1025 | IDU capacity | Read = real *100 unit: Kw | IDU capacity. Verify scaling. |  |
| 1026 | Fan rpm | Rpm, read = real | ODU fan RPM from IDU telemetry. |  |
| 1027 | PMV | ODU EXV opening, unit: Pulse. R & It READ = REAL (show only 8 multiples Only Multiples of 8 Will BE Shown) | ODU Electronic Expansion Valve opening in pulses. Steps of 8. Same as reg 103 but from IDU perspective. |  |
| 1028 | T3 | Coil temp., unit:°C. | Coil (heat exchanger) temperature. |  |
| 1029 | T4 | Ambient temperature, unit:°C. | Outdoor ambient temperature from IDU telemetry. |  |
| 1030 | Tp | Discharge Tp, unit:°C. | Compressor discharge temperature. |  |
| 1031 | Th | Suction tem. unit:°C. | Suction gas temperature. |  |
| 1032 | TF | Unit (°C) ---- Outside machine feedback invalid value 0x7F | Temperature from ODU feedback. 0x7F = invalid. |  |
| 1033 | Pressure 1 | ODU high pressure, unit: kPA. read = real | High-side pressure in kPa. Compare with reg 116. |  |
| 1034 | Pressure 2 | ODU low pressure, unit: kPA. read = real (Reserved) | Low-side pressure in kPa. |  |
| 1035 | DC bus current | Unit: ampere | DC bus current. |  |
| 1036 | DC bus voltage | Read = real (unit: V) | DC bus voltage. |  |
| 1037 | ODU current | Operation current, unit A, read = real | ODU mains current draw. |  |
| 1038 | ODU voltage | Voltage, unit: V, read = real | ODU mains voltage. |  |
| 1039 | ODU frequency limitation solution | Solution ----- read from ODU 174 | What is currently limiting compressor frequency. References ODU internal reg 174. |  |
| 1040 | High bit of electrical computation |  | Upper 16 bits of electrical energy counter. |  |
| 1041 | Low bit of electrical computation |  | Lower 16 bits of electrical energy counter. |  |
| 1042 | ODU software version |  | ODU firmware version. |  |


## Password-Protected Technician Registers (200–272)

PASSWORD PROTECTED installer parameters. Writing incorrect values can cause malfunction or damage. Warranty void if modified without authorization.

| Reg | Name | Manual Description (verbatim) | Explanation | Notes |
| --- | --- | --- | --- | --- |
| 200 | Type | High 8 bits: type of home appliance (Central heating: 0x07) / Bit 4: product algebra (0x0*=Serie E, 0x1*=2nd gen R32 A series, 0X2*=2nd gen R32 upgrade A series, 0x3*=A-Sphera custom upgrade) / Lower 4 bits: subtypes / R32 refrigerant freq conversion water pump model: 0X*2; Complete as 0x0732 / R32 refrigerant conversion pump model: 0x02 | Unit type identifier. Encodes appliance type, product series, refrigerant type, and pump model in a single 16-bit value. | Read |
| 201 | T1S cooling set temperature upper limit | Low 8 bits = zone 1, high 8 bits = zone 2. Lower 8 bits are area 1, upper 8 bits are area 2 | Cooling mode water temp setpoint upper limit, packed per zone. | R/W |
| 202 | T1S cooling set temperature lower limit | Low 8 bits = zone 1, high 8 bits = zone 2 | Cooling mode water temp setpoint lower limit. | R/W |
| 203 | T1S heating set temperature upper limit | Low 8 bits = zone 1, high 8 bits = zone 2 | Heating mode water temp setpoint upper limit. | R/W |
| 204 | T1S heating set temperature lower limit | Low 8 bits = zone 1, high 8 bits = zone 2 | Heating mode water temp setpoint lower limit. | R/W |
| 205 | T5 set temperature upper limit | Read = real*2 actual value *2 | DHW tank temp upper limit. Scaling ×2. | R/W, ×2 |
| 206 | T5 set temperature lower limit | Read = real*2 actual value *2 | DHW tank temp lower limit. Scaling ×2. | R/W, ×2 |
| 207 | Hot water setting temperature upper limit |  | DHW setting upper limit. | R/W |
| 208 | Hot limit water set temperature lower limit |  | DHW setting lower limit. | R/W |
| 209 | The operating time of the recirculation pump | Recirculation pump, default operating time 5 minutes, adjustment interval 5–120 min, with steps of 1 min | DHW recirculation pump run time per cycle. | R/W |

### Register 210: Parameter setting 1 (Bitmask)

| Reg | Bit | Name | Manual Description | Explanation |
| --- | --- | --- | --- | --- |
| 210 | BIT0 | Priority | 0= space cooling and heating priority, 1= Hot water priority | Sets whether space heating/cooling or DHW takes priority. |
| 210 | BIT1 | Dual Room Thermostat | Dual Room Thermostat, 0= not supported; 1= support | Enables dual room thermostat support. |
| 210 | BIT2 | Room thermostat MODE SETTING | Room thermostat MODE SETTING | Room thermostat mode configuration. |
| 210 | BIT3 | Support room thermostat | Support room thermostat (Room thermostat) | Enables room thermostat feature. |
| 210 | BIT4 | Support room temp sensor Ta | Support room temperature sensor Ta | Enables the Ta room temperature sensor input. |
| 210 | BIT5 | Support PUMPI pump mute | Support PUMPI pump mute function, 1:support | Enables pump quiet/mute operation mode. |
| 210 | BIT6 | T1S heating high/low temp | T1S heating high/low temperature setting (read only) | Read-only: shows heating temp range setting. |
| 210 | BIT7 | Heating enable | Heating enable | Enables heating function. |
| 210 | BIT8 | T1S cooling high/low temp | T1S cooling high/low temperature setting (read only) zone 1 | Read-only: shows cooling temp range setting. |
| 210 | BIT9 | Cooling enable | Cooling enable | Enables cooling function. |
| 210 | BIT10 | DHW pump Pipe Disinfect | DHW pump supports Pipe Disinfect | Enables pipe disinfection via DHW pump. |
| 210 | BIT11 | Reserved | Reserved |  |
| 210 | BIT12 | DHW pump support | DHW pump; 1: support; 0: not supported | Enables DHW circulation pump feature. |
| 210 | BIT13 | Support sterilization | Support sterilization function | Enables legionella sterilization cycle. |
| 210 | BIT14 | Support water tank TBH | Support water tank electric heating TBH (read only) | Read-only: indicates TBH is installed. |
| 210 | BIT15 | DHW enable/disable | DHW enable/disable | Enables or disables DHW function entirely. |

### Register 211: Parameter setting 2 (Bitmask)

| Reg | Bit | Name | Manual Description | Explanation |
| --- | --- | --- | --- | --- |
| 211 | BIT0 | IBH/AHS installation position | IBH/AHS installation position 1: buffer tank 0: pipeline | Where the backup heater is installed. Buffer tank or inline. |
| 211 | BIT1 | Tbt sensor enable | Tbt sensor enable 1: Yes 0: No | Enables buffer tank temperature sensor. |
| 211 | BIT2 | Ta sensor position | Ta sensor position 1: IDU 0: HMI | Room temp sensor location. IDU = inside unit, HMI = on wired controller. |
| 211 | BIT3 | Dual zone setting | Dual zone setting is effective | Activates dual-zone operation. |
| 211 | BIT4 | T1S heating high/low zone 2 | T1S heating high/low temperature setting zone 2 | Zone 2 heating temp range. |
| 211 | BIT5 | T1S cooling high/low zone 2 | T1S cooling high/low temperature setting zone 2 | Zone 2 cooling temp range. |
| 211 | BIT6 | T1B sensor enable | T1B sensor enable 0: None 1: Yes | Enables the T1B (zone 2 flow) sensor. |
| 211 | BIT7 | Smart grid | Smart grid: 0: None 1: Yes | Enables Smart Grid functionality. |
| 211 | BIT8 | Input port definition | Input port definition: 0: remote switch 1: DHW Heater | Configures input port function. |
| 211 | BIT9 | Support solar module | Support solar module 1: Yes 0: No | Enables solar thermal integration. |
| 211 | BIT10 | Piping length | Piping length selection 1:>10m 0:<10m | Refrigerant piping length setting. GUESSWORK: BIT10 and BIT11 have identical descriptions. They may encode a 2-bit value for multiple length ranges (e.g., 00=<10m, 01=10-20m, 10=20-30m, 11=>30m), or one may be for liquid line and the other for gas line. Could also be a manual copy-paste error where only one bit is actually used. |
| 211 | BIT11 | Piping length | Piping length selection 1:>10m 0:<10m | See BIT10 note above. |
| 211 | BIT12 | RT_Ta_PCNEn | RT_Ta_PCNEn (Enable temperature small board) | Enables additional temperature sensing board. |
| 211 | BIT13 | RT_Ta_PCNEn | RT_Ta_PCNEn (Enable temperature small board) | Second enable bit for temp board. |
| 211 | BIT14 | Dry contact M1M2 | Dry contact M1M2 control AHS 1: Yes 0: No | Enables dry contact control for Auxiliary Heat Source. |
| 211 | BIT15 | ACS dual water tank | ACS (DHW dual water tank enable) 1:Yes 0:No | Enables dual DHW tank configuration. |

### Registers 212–272: Setpoints, Timers, and Curves

| Reg | Name | Manual Description (verbatim) | Explanation | Notes |
| --- | --- | --- | --- | --- |
| 212 | dT5_On | A series: Default: 10°C, range: 1–30°C / E series: Default: 5°C, range: 2–10°C, adjustment 1°C | DHW hysteresis ON threshold. How far below setpoint before DHW starts. |  |
| 213 | dT1S5 | Default: 10°C, range: 5-40°C, adjustment interval 1°C | Temperature differential between T1S and T5. |  |
| 214 | T_Interval_DHW | Default: 5min, range: 5~5min, adjustment interval 1min | DHW operating interval timer. Italian original also says 'intervallo 5~5 min', confirming this is NOT a translation error but a printing error in the source document. GUESSWORK: The range is likely 5-50 min or 5-60 min (dropped trailing zero). Reasoning: (1) a fixed 5-min register with 1-min adjustment makes no sense, (2) reg 209 (recirc pump) has range 5-120 min for a similar timer, (3) reg 242 (t_DHWHP_restrict) has range 10-600 min. Verify by writing values >5 and checking acceptance. | Print error IT |
| 215 | T4DHWmax | Default: 43°C, range 35-43°C, adjustment interval 1°C | Maximum outdoor temp for DHW heat pump operation. Above this, DHW may use electric only. |  |
| 216 | T4DHWmin | A series: Default: -10°C, range: -25–30°C; / E series: Default: -10°C, range: -25–-5°C; adjustment 1°C | Minimum outdoor temp for DHW heat pump operation. CORRECTED: A series range is -25 to 30°C (not -25 to -30°C). E series range unclear from manual: possibly -25 to -5°C or -25 to 5°C. |  |
| 217 | t_TBH_delay | Default: 30min range: 0–240min; adjustment interval 5min | Delay before TBH (tank electric heater) is activated. |  |
| 218 | dT5S_TBH_off | Default: 5°C, range: 0–10°C, adjustment interval 1°C | Temperature differential for TBH shutoff. |  |
| 219 | T4_TBH_on | A series: Default: 5°C, range: -5–50°C; / E series: Default: 5°C, range: -5–-20°C; adjustment interval 1°C | Outdoor temp threshold that triggers TBH activation. |  |
| 220 | T5s_DI | Sterilization function water tank set temperature, setting range 60–70°C, default 65°C | Legionella disinfection target tank temperature. |  |
| 222 | t_DI_hightemp | Sterilization high temperature time, setting range: 5–60 min, default 15min | Duration to hold at sterilization temperature. |  |
| 223 | t_interval_C | Cooling mode compressor start time interval; range: 5–5min, default value is 5min | Minimum compressor restart delay in cooling mode. |  |
| 224 | dT1SC | Default: 5°C, range: 2-10°C, adjustment interval 1°C | Cooling mode T1S hysteresis. |  |
| 225 | dT5C | Default: 2°C, range: 1-10°C, adjustment interval 1°C | Cooling mode T5 hysteresis. |  |
| 226 | T4cmax | Default: 52°C, range: 35-52°C; adjustment interval 1°C | Maximum outdoor temp for cooling operation. |  |
| 227 | T4cmin | Default: 10°C, range: -5-25°C; adjustment interval 1°C | Minimum outdoor temp for cooling operation. |  |
| 228 | t_interval_H | Heating mode compressor start time interval; range: 5–60min, default is 5 min | Minimum compressor restart delay in heating mode. |  |
| 229 | dT1SH | A series: Default: 5°C, range: 2-20°C; / E series: Default: 5°C, range: 2-10°C; adjustment interval 1°C | Heating mode T1S hysteresis. |  |
| 230 | dTSH | Default: 2°C, range: 1-10°C, adjustment interval 1°C | Heating mode TS (room temp) hysteresis. |  |
| 231 | T4hmax | Default: 25°C, range: 20-35°C, adjustment interval 1°C | Maximum outdoor temp for heating operation. |  |
| 232 | T4hmin | A series: Default: -1.5°C, range: -25-30°C, adjusted spacing 1°C / E Series: Default: -1.5°C, range: -25-15°C, adjusted spacing 1°C | Minimum outdoor temp for heating operation. |  |
| 233 | T4_IBH_on | Ambient temperature of the electric auxiliary heating IBH of the hydraulic module is turned setting range: -15–10°C, the default value: -5°C | Outdoor temp threshold that triggers IBH activation. CORRECTED: range is -15 to +10°C (not -15 to -10°C). |  |
| 234 | dT1_IBH_on | Hydraulic module electric auxiliary heating IBH opening temperature hysteresis, setting range: default value is 5°C | IBH activation temperature hysteresis. |  |
| 235 | t_IBH_delay | Hydraulic module electric auxiliary heating IBH delay opening time, setting range: 15–120min, default value 30min | Delay before IBH activates after conditions are met. |  |
| 236 | t_IBH12_delay | RESERVED | Reserved. |  |
| 237 | T4_AHS_on | External heat source AHS turns on ambient temp. / A series: Range -15–30°C; / E series: setting range -15–10°C; / Clivet model default is 10°C, Midea model -5°C | Outdoor temp that triggers AHS. NOTE: Clivet default differs from Midea default. CORRECTED: A series range is -15 to 30°C, E series is -15 to 10°C. | Vendor diff |
| 238 | dT1_AHS_on | External heat source AHS turns on temperature difference. / A series: Range: 2–20°C, default 5°C / E series: Setting range: 2–10°C, default 5°C | AHS activation temperature differential. |  |
| 239 | dT1_AHS_off | RESERVED | Reserved. |  |
| 240 | t_AHS_delay | Compressor run time before starting additional heating source, setting range 5–120min, default value 30min | Delay before AHS is started after compressor. |  |
| 241 | t_DHWHP_max | Maximum time for heat pump to run hot water, setting range: 10–600min; default value: 120min; setting value is minute | Max DHW heat pump cycle duration before timeout. |  |
| 242 | t_DHWHP_restrict | Maximum operating time of the heat pump in heating/cooling mode. Setting range is: 10–600min; default is 30min; setting value is minute. | Max continuous heating/cooling run time. |  |
| 243 | T4autocmin | Default value is: 25°C, range is: 20–29°C, adjustment interval is 1°C. | Auto mode: minimum outdoor temp for cooling. |  |
| 244 | T4autohmax | Default value is: 17°C, range is: 10–17°C, adjustment interval is 1°C. | Auto mode: maximum outdoor temp for heating. |  |
| 245 | T1S_H.A_H | T1 set value in heating mode during vacation, range: 20–25°C, default 25°C | Vacation/holiday heating flow temp setpoint. |  |
| 246 | T5S_H.A_DHW | T5 set value in hot water mode during vacation, range: 20–25°C, default 25°C | Vacation/holiday DHW tank temp setpoint. |  |
| 247 | Start percentage | Range is 10-100, default is 10. Adjustment interval is 10 | Compressor start percentage/power. |  |
| 248 | Adjustment time | Range 1-60 default 5 | Some adjustment time parameter. |  |
| 249 | dTbt2 | Range 0-50 default 15 | Buffer tank 2 temperature differential. |  |
| 250 | IBH1 power | Range 0-200, default 0, unit 100W | IBH1 rated power. Value × 100 = Watts. Default 0 = not configured. |  |
| 251 | IBH2 power | Range 0-200, default 0, unit 100W | IBH2 rated power. |  |
| 252 | TBH power | Range 0-200, default 0, unit 100W | TBH rated power. |  |
| 253 | Comfort parameter | Reserved, query the register and report address error | Reserved. Querying may return error. |  |
| 254 | Comfort parameter | Reserved, query the register and report address error | Reserved. Querying may return error. |  |
| 255 | t_DRYUP | Heating days, setting range: 4–15 days, default 8 days | Floor drying/screed drying: heating phase duration. | Floor dry |
| 256 | t_HIGHPEAK | Drying days, setting range: 3–7 days, default 5 days | Floor drying: peak temperature hold duration. | Floor dry |
| 257 | t_DRYD | Cooling days, setting range: 4–15 days, default 5 days | Floor drying: cooling phase duration. | Floor dry |
| 258 | T_DRYPEAK | Maximum drying temperature, setting range: 30–55°C, default 45°C | Floor drying: peak temperature. | Floor dry |
| 259 | t_firstFH | Floor heating first run time, default value 72hrs, setting range: 48-96hrs | Floor heating initial commissioning run time. | Floor heat |
| 260 | T1S(First warm) | Target outlet water temp in preheating for floor mode: setting range: 25–35°C, default:25°C | Floor heating: initial warm-up flow temperature. | Floor heat |
| 261 | T1SetC1 | Cooling temperature curve 9 parameters, setting range 5-25°C, default 10°C | Weather curve cooling parameter point 1. |  |
| 262 | T1SetC2 | Cooling temperature curve 9 parameters, setting range 5-25°C, default 16°C | Weather curve cooling parameter point 2. |  |
| 263 | T4C1 | Cooling temperature curve 9 parameters, setting range (-5)-46°C, default 35°C | Weather curve cooling outdoor temp point 1. |  |
| 264 | T4C2 | Cooling temperature curve 9 parameters, setting range (-5)-46°C, default 25°C | Weather curve cooling outdoor temp point 2. |  |
| 265 | T1SetH1 | Heating temperature curve 9 parameters, setting range 25-60°C, default 35°C | Weather curve heating flow temp point 1. |  |
| 266 | T1SetH1 | Heating temperature curve 9 parameters, setting range 25-60°C, default 28°C | Weather curve heating flow temp point 2. GUESSWORK: Manual repeats name 'T1SetH1' from reg 265. This should almost certainly be 'T1SetH2'. Reasoning: regs 261/262 follow the pattern T1SetC1/T1SetC2 for cooling, and 265/266 should mirror as T1SetH1/T1SetH2 for heating. The different default values (35°C vs 28°C) confirm these are two distinct parameters. | Typo in manual |
| 267 | T4H1 | eating temperature curve 9 parameters, setting range (-25)-35°C, default -5°C | Weather curve heating outdoor temp point 1. Manual says 'eating temperature' — this is a trivial typo for 'Heating temperature'. The pattern T4H1/T4H2 mirrors T4C1/T4C2 (regs 263/264) for cooling. | Typo in manual |
| 268 | T4H2 | Heating temperature curve 9 parameters, setting range (-25)-35°C, default 7°C | Weather curve heating outdoor temp point 2. |  |
| 269 | HB:t_T4_FRESH_C | Current limiting scheme, 0= No setting; 1–8= Scheme 1–8, default 0 | Frequency/current limiting scheme for cooling. 0 = no limiting. |  |
| 270 | LB:t_T4_FRESH_H | Range 0.5-t, adjustment interval 0.5, sending value = actual value *2 / Range 0.5-6hour, adjustment interval 0.5, sending value = actual value *2 | Fresh air heating time settings. Two sub-parameters. Values scaled ×2. | Scale ×2 |
| 271 | T_PUMPI_DELAY | Range 2-20, adjustment interval 0.5, sending value = actual value *2 | Pump delay timer. Value scaled ×2. | Scale ×2 |
| 272 | EMISSION TYPE | Bit12-15: Zone 2 cooling terminal type / Bit8-11: Zone 1 cooling terminal type / Bit4-7: Zone 2 heating terminal type / Bit0-3: Zone 1 heating terminal type | Emission/terminal type configuration. Encodes what type of emitters (radiators, underfloor, fan coils) are connected to each zone for heating and cooling. |  |


## Error Code Table

Error codes as reported in registers 124–127 (current fault, fault 1–3) and register 1005. The Modbus column is the numeric value that appears in the register. IDU = Indoor Unit fault, ODU = Outdoor Unit fault.NOTE: The table now includes both Swedish (SE) and Italian (IT) manual versions. Rows marked with ⚠️ indicate where the Modbus code or Unit assignment differs between the Swedish and Italian manuals. There is a systematic discrepancy in H-codes (H1 through HE) where the Swedish manual appears to have shifted Modbus codes by one position relative to the Italian manual. The Italian version is believed to be the original source.

| Code | Description (English) | Modbus SE | Modbus IT | Unit SE | Unit IT | Swedish manual text | Italian manual text |
| --- | --- | --- | --- | --- | --- | --- | --- |
| E0 | Water flow error (3 occurrences). Check pump, filter, air in system. | 1 | 1 | IDU | IDU | Vattenflödesfel (3 gånger vattenflödesfel) | Interruzione flusso d'acqua (interruzione del flusso d'acqua 3 volte) |
| E1 | Phase error (3-phase models). Phase sequence or missing phase. | 33 | 33 | ODU | ODU | Fasfel (3-fasmodeller) | Errore di fase linea-linea o fase a zero (i modelli trifase hanno questo codice errore) |
| E2 | Communication error between line controller and hydraulic module. | 2 | 2 | IDU | IDU | Kommunikationsfel linjekontroll och hydraulisk modul | Errore di comunicazione tra interfaccia utente e modulo idraulico |
| E3 | T1 outlet water temperature sensor fault. | 4 | 4 | IDU | IDU | Sensorfel T1 utgående vattentemperatur | Guasto sensore temperatura uscita acqua T1 |
| E4 | T5 water tank temperature sensor fault. | 5 | 5 | IDU | IDU | Sensorfel T5 vattentanktemperatur | Guasto sensore temperatura acqua dell'accumulo T5 |
| E5 | T3 outdoor temperature sensor fault. | 39 | 39 | ODU | ODU | Sensorfel utomhusenhet T3 temperatur | Guasto sensore temperatura T3 unità esterna |
| E6 | T4 outdoor ambient temperature sensor fault. | 40 | 40 | ODU | ODU | Sensorfel utomhusenhet T4 utomhustemperatur | Guasto sensore temperatura ambiente T4 unità esterna |
| E7 | Italian manual specifies this as Tbt1 inertial tank sensor fault. Swedish manual says only "Sensorfel" (sensor fault) without specifying which sensor. | 6 | 6 | IDU | IDU | Sensorfel | Guasto sensore Tbt1 accumulo inerziale |
| E8 | Flow alarm (paddle switch) after 3 occurrences. Swedish says manual reset required; Italian says can be restored after minutes. Different reset behavior described. | 9 | 9 | IDU | IDU | Flödeslarm (padelvakt) efter 3 gånger = manuell återställning | Guasto flusso d'acqua (viene visualizzato tre volte e può essere ripristinato dopo minuti) |
| E9 | Th return air temperature sensor fault (ODU). | 41 | 41 | ODU | ODU | Sensorfel utomhusenhet Th returlufttemperatur | Guasto sensore temperatura Th |
| EA | Tp discharge temperature sensor fault (ODU). | 42 | 42 | ODU | ODU | Sensorfel utomhusenhet Tp utsugstemperatur | Guasto sensore temperatura dell'aria Tp unità esterna |
| Eb | Solar temperature sensor fault. | 7 | 7 | IDU | IDU | Sensorfel Tsolar | Guasto sensore Tsolar |
| EC | Buffer tank 2 temperature sensor fault. | 8 | 8 | IDU | IDU | Sensorfel Tbt2 | Guasto sensore Tbt2 accumulo aggiuntivo ACS |
| Ed | Twin plate heat exchanger water temp sensor fault. | 10 | 10 | IDU | IDU | Tvillingplåt utbytesvatten temperatursensorfel | Guasto sensore temperatura acqua sostituzione scheda Twin |
| EE | Hydraulic module EEPROM error. Board memory fault. | 11 | 11 | IDU | IDU | Hydraulisk modul EEprom fel | Modulo idraulico EEprom guasto |
| P0 | Low pressure protection. Check refrigerant charge. | 50 | 50 | ODU | ODU | Lågspänning brytarskydd | Protezione bassa pressione |
| P1 | High pressure / discharge temp protection. | 52 | 52 | ODU | ODU | Högtryck / utsugstemperatur kontrollbrytarskydd | Protezione interruttore di regolazione della temperatura di scarico/alta pressione |
| P3 | Compressor overvoltage protection. | 53 | 53 | ODU | ODU | Kompressorns överspänningsskydd | Protezione da sovracorrente del compressore |
| P4 | Tp discharge temperature too high protection. | 54 | 54 | ODU | ODU | Tp skydd för hög utsugstemperatur | Protezione surriscaldamento temperatura aria espulsa Tp |
| P5 | Water inlet/outlet temperature difference protection. | 26 | 26 | IDU | IDU | Twin-Twout eller Twout-Twin inkommande och utgående vattentemperatur differensskydd | Protezione Twin-Twout, Twout-Twin o temperatura di mandata acqua troppo elevata |
| P6 | Module protection. After 3x L0/L1 or H4 permanent fault. Power cycle to recover. | 55 | 55 | ODU | ODU | Modulskydd (tre gånger L0 eller L1 feldisplay, H4 permanentfel, behöver slå ifrån strömmen för att återhämta) | Protezione modulo (IPDU e IR341, controllare il contenuto specifico) |
| Pb | Frost protection active (not an error, informational). | 25 | 25 | IDU | IDU | Frostskydd (ej skyddad, larmlampan blinkar inte), linjekontrollen visar inte Pb och visar frostskyddsikonen | Antigelo (non è una protezione, la spia di allarme non lampeggia), il comando remoto non visualizza Pb, ma visualizza l'icona antigelo |
| Pd | T3 outdoor unit overtemperature protection. | 57 | 57 | ODU | ODU | Utomhusdelen T3:s övertemperaturskydd | Protezione da sovratemperatura T3 unità esterna |
| PP | Abnormal water temperature differential. Check flow/air locks. | 31 | 31 | IDU | IDU | Avvikande temperaturdifferens mellan inkommande och utgående vatten | Differenza di temperatura anomala tra ingresso e uscita acqua |
| H0 | Communication error between indoor and outdoor unit. | 3 | 3 | IDU | IDU | Kommunikationsfel mellan innedel och utedel | Errore di comunicazione tra unità interna e unità esterna (anomalia continua nella comunicazione per 10 secondi) |
| H0 / (ODU) | ⚠️ Italian manual lists H0 twice: once for IDU (Modbus 3) and once for ODU (Modbus 38). Swedish manual has only one H0 entry (IDU/3) and assigns Modbus 38 to H1. | — | 38 | — | ODU | (not in Swedish manual as separate entry) | Errore di comunicazione tra unità esterna e unità interna (nessuna comunicazione nel giro di 10 s) |
| H1 | ⚠️ Communication error ODU and IR341 (inverter module). | 38 | 39 | ODU | ODU | Kommunikationsfel utomhusenhet och IR341 (externt fel och växlarmodul) | Errore di comunicazione unità esterna e IR341 (unità esterna e modulo inverter) |
| H2 | ⚠️ T2 refrigerant gas side temperature sensor fault. | 39 | 12 | ODU | IDU | Sensorfel T2 köldmediegassida temperatur | Guasto sensore temperatura refrigerante lato gas T2 |
| H3 | ⚠️ T2B refrigerant liquid side temperature sensor fault. | 12 | 13 | IDU | IDU | Sensorfel T2B köldmedie vätskesida temperatur | Guasto sensore temperatura refrigerante lato liquido T2B |
| H4 | ⚠️ 3x L-alarm (L0/L1). Inverter module persistent fault. | 13 | 44 | IDU | ODU | 3 gånger L-larm (L0/L1) | Dopo 3 segnalazioni di L (L0/L1) in 1 ora appare H4, che non è resettabile. |
| H5 | ⚠️ Ta (room) temperature sensor fault. | 44 | 15 | ODU | IDU | Ta temperatur sensorfel | Guasto sensore temperatura Ta |
| H6 | ⚠️ DC fan motor fault. | 15 | 45 | IDU | ODU | DC fläktfel | Guasto ventilatore DC |
| H7 | ⚠️ Voltage protection fault. | 45 | 46 | ODU | ODU | Spänningsskyddsfel | Tensione di alimentazione anormale |
| H8 | ⚠️ High pressure sensor fault. Replace with resistor if ODU not installed. | 46 | 47 | ODU | ODU | Fel högtryckssensor (utbyte med resistor när externa enhet inte är installe-rad) | Guasto sensore alta pressione (sostituzione con resistenza quando l'unità esterna non è installata) |
| H9 | ⚠️ Tw2 sensor fault. | 47 | 20 | ODU | IDU | Tw2 sensorfel | Sensore guasto Tw2 |
| HA | ⚠️ Twout panel water temperature sensor fault. | 20 | 14 | IDU | IDU | Sensorfel Twout panelersatt vattentemperatur | Guasto sensore di temperatura uscita scambiatore a piastre |
| Hb | ⚠️ 3x PP pre-alarm with Twout<7°C. Manual reset required. Freeze risk. | 14 | 21 | IDU | IDU | 3 gånger PP-förlarm och Twout<7°C (manuell återställning) | Tre guasti consecutivi protezione PP e Twout<7°C; ripristino per mancanza di tensione |
| Hd | ⚠️ Cascade communication error between slave and master. | 21 | 24 | IDU | IDU | Kommunikationsfel mellan slav och master (kaskad) | Errore di comunicazione slave e master (questo errore si verifica quando diverse unità sono collegate in parallelo) |
| HE | ⚠️ IDU adapter board communication error. | 24 | 23 | IDU | IDU | Kommunikationsfel adapterkort innedel | Errore di comunicazione modulo idraulico e scheda adattatore modulo idraulico |
| HF | ODU EEPROM error. | 43 | 43 | ODU | ODU | EEpromfel extern maskin | Guasto EEPROM unità esterna |
| HH | DESCRIPTION DIFFERS: Swedish says "H6 fault occurs twice in 10 min". Italian says "H6 fault 10 consecutive times in 120 minutes". Significant difference in threshold (2x/10min vs 10x/120min). | 48 | 48 | ODU | ODU | H6 fel uppträder två gånger på 10 min (kan återställas efter avstängning) | Guasto H6 10 volte consecutive in 120 minuti (ripristino dopo spegnimento) |
| HP | 3x low pressure protection (<0.6MPa) within 1 hour. | 49 | 49 | ODU | ODU | Tre gånger lågtrycksskydd (mindre än 0.6MPa), inträffade kontinuerligt inom 1h | Protezione bassa pressione modalità raffreddamento (entro 1 ora, la bassa pressione è inferiore a 0,6 MPa per tre volte consecutive, può essere azzerata automaticamente) |
| C7 | Inverter board heatsink high temperature. | 65 | 65 | ODU | ODU | Högtemperatur kylfläns inverterkort | Protezione da sovratemperatura dissipatore di calore |
| bH | PED board fault. | 143 | 143 | ODU | ODU | PED-kort fel | Guasto scheda PED piccola |
| F1 | Low DC bus voltage. | 142 | 142 | ODU | ODU | Låg DC-busspänning | Protezione bassa tensione bus CC |
| L0 | DC compressor module fault. | 112 | 112 | ODU | ODU | Modulfel DC-kompressor | Errore modulo compressore CC |
| L1 | DC bus low voltage protection. | 116 | 116 | ODU | ODU | DC-bus lågspänningsskydd | Protezione bassa tensione bus DC |
| L2 | DC bus high voltage protection. | 134 | 134 | ODU | ODU | DC-bus högspänningsskydd | Protezione alta tensione del bus DC |
| L4 | MCE error / sync / closed loop fault. | 135 | 135 | ODU | ODU | MCE-fel / synkronisering /sluten loop | Errore MC/sincronizzazione/circuito chiuso |
| L5 | Zero speed protection. | 136 | 136 | ODU | ODU | Nollvarvsskydd | Protezione a velocità zero |
| L7 | Phase sequence error protection. | 138 | 138 | ODU | ODU | Skydd mot fassekvensfel | Protezione errore sequenza fasi |
| L8 | Speed change >15Hz protection between consecutive moments. | 139 | 139 | ODU | ODU | Hastighetsväxling >15Hz skydd vid föregående och senaste moment | Protezione per quando la variazione di velocità precedente e successiva è >15 Hz |
| L9 | Protection: difference between set speed and actual speed >15Hz. | 141 | 141 | ODU | ODU | Skydd differens mellan inställd hastighet och drifthastighet >15Hz | Protezione per quando la differenza tra la velocità impostata e la velocità di funzionamento è >15 Hz |

## Appendix: SV1, SV2, SV3 Explained

SV1 (Reg 129, BIT4): Main DHW/heating diverter valve. Switches water flow between the space heating circuit and the DHW tank coil. When energized, typically routes flow to DHW.

SV2 (Reg 129, BIT5): Secondary circuit diverter valve. In dual-zone configurations, routes water to a second heating zone (e.g., underfloor heating vs radiators) or to a buffer/inertial tank.

SV3 (Reg 129, BIT9): Third circuit valve. Typically associated with solar thermal input or a third zone. Given the Sphera EVO 2.0 supports solar kit integration, SV3 likely controls the solar circuit valve.

These are motorized ball valves or rotary actuator valves. A stuck SV1 is the most impactful failure, as it can route DHW-temperature water (45–55°C) into the heating circuit or vice versa.
