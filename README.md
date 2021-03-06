Firstly if you use this code or are doing anything with ADS-B broadcast, I would be interested in hearing what you are up to. Get in touch on @nzkarit on twitter or adsb (AT) karit [dot] nz

# "ADS-B Out" add-on for SoftRF-Emu, Stratux, etc...

This repository contains "ADS-B Out" encoder for Tx-capable SDR hardware.

It is currently written in architecture independent Python language and can be used as an add-on for existing
open source "ADS-B In" solutions. One known good example is [Stratux](https://github.com/cyoung/stratux).

# Disclaimer
The source code is published for academic purpose only.

# Instructions
1. Execute *ADSB_Encoder.py* all the options have defaults so none are needed to generate with defaults. Running help will show you the optiosn you can change:
```
$ ./ADSB_Encoder.py

$ ./ADSB_Encoder.py -h
usage: ADSB_Encoder.py [-h] [-i ICAO] [--lat LATITUDE] [--lon LONGITUDE]
                       [-a ALTITUDE] [--ca CAPABILITY] [--tc TYPECODE]
                       [--ss SURVEILLANCESTATUS] [--nicsb NICSUPPLEMENTB]
                       [--time TIME] [-s SURFACE] [-o OUTPUTFILENAME]
                       [-r REPEATS] [--csv CSVFILE]

This tool will generate ADS-B data in a form that a hackRF can broadcast. In
addition to providing the information at the command the defaults can be
changed in the config.cfg file and the the loggin config changed in
logging.cfg.

optional arguments:
  -h, --help            show this help message and exit
  -i ICAO, --icao ICAO  The ICAO number for the plane in hex. Ensure the ICAO
                        is prefixed with '0x' to ensure this is parsed as a
                        hex number. Default: 0xABCDEF
  --lat LATITUDE, --latitude LATITUDE
                        Latitude for the plane in decminal degrees. Default:
                        12.34
  --lon LONGITUDE, --long LONGITUDE, --longitude LONGITUDE
                        Longitude for the place in decminal degrees. Default:
                        56.78
  -a ALTITUDE, --alt ALTITUDE, --altitude ALTITUDE
                        Altitude in decminal feet. Default: 9876.5
  --ca CAPABILITY, --capability CAPABILITY
                        The capability. (Think this is always 5 from ADSB
                        messages. More info would be appreciate). Default: 5
  --tc TYPECODE, --typecode TYPECODE
                        The type for the ADSB messsage. See https://adsb-
                        decode-guide.readthedocs.io/en/latest/content/introduc
                        tion.html#ads-b-message-types for more information.
                        Default: 11
  --ss SURVEILLANCESTATUS, --surveillancestatus SURVEILLANCESTATUS
                        The surveillance status. (Think this is always 0 from
                        ADSB messages. More info would be appreciate).
                        Default: 0
  --nicsb NICSUPPLEMENTB, --nicsupplementb NICSUPPLEMENTB
                        The NIC supplement-B.(Think this is always 0 from ADSB
                        messages. More info would be appreciate). Default: 0
  --time TIME           The time. (Think this is always 0 from ADSB messages.
                        More info would be appreciate). Default: 0
  -s SURFACE, --surface SURFACE
                        If the plane is on the ground or not. Default: False
  -o OUTPUTFILENAME, --out OUTPUTFILENAME, --output OUTPUTFILENAME
                        The iq8s output filename. This is the file which you
                        will feed into the hackRF. Default: Samples_256K.iq8s
  -r REPEATS, --repeats REPEATS
                        How many repeats of the data to perform. Default: 1
  --csv CSVFILE, --csvfile CSVFILE, --in CSVFILE, --input CSVFILE
                        Import a CSV file with the plane data in it. Default:

```
2. Transmit the signal into air:
```
$ hackrf_transfer -t Samples_256K.iq8s -f 915000000 -s 2000000 -x 10
call hackrf_sample_rate_set(2000000 Hz/2.000 MHz)
call hackrf_baseband_filter_bandwidth_set(1750000 Hz/1.750 MHz)
call hackrf_set_freq(915000000 Hz/915.000 MHz)
Stop with Ctrl-C
 3.9 MiB / 1.000 sec =  3.9 MiB/second
 0.5 MiB / 1.000 sec =  0.5 MiB/second

User cancel, exiting...
Total time: 2.00039 s
hackrf_stop_tx() done
hackrf_close() done
hackrf_exit() done
fclose(fd) done
exit
$
```
 * -t is the input file to transmit
 * -f is the frequency in hertz. In the real world this would be 1090000000 but do not use that
 * -s is the sample rate in hertz
 * -x is the gain
3. Receive the Signal
```
$ sudo ./dump1090 --net --freq 915000000
...
```
![](https://github.com/lyusupov/ADSB-Out/raw/master/documents/images/dump1090.JPG)

# Generate CSV files
These CSV files can be used for input into the application
## generateAllICAO.py
This script will generate a CSV with all the different ICAO numbers in it.

# Import FlightRadar24 CSV files
This script will take a FR24 CSV files and convert it ready for import into ADSB_Encoder.py. It outputs a file called fr24.csv.
```
$ ./FR24csv.py --csv <exportFromFR24.csv>
$ ./ADSB_Encoder.py --csv fr24.csv

$ ./FR24csv.py --help
usage: FR24csv.py [-h] [-i ICAO] --csv CSVFILE

This script will take a FR24 CSV file and convert it into a format for
FR24csv.py

optional arguments:
  -h, --help            show this help message and exit
  -i ICAO, --icao ICAO  The ICAO number for the plane in hex. Ensure the ICAO
                        is prefixed with '0x' to ensure this is parsed as a
                        hex number. This is 24 bits long. Default: 0x75008F
  --csv CSVFILE, --csvfile CSVFILE, --in CSVFILE, --input CSVFILE
                        The name of the FR24 CSV file
```
# References
1. "*Gr-Air-Modes*", **Nick Foster**, 2012
1. "*EXPLOITING THE AUTOMATIC DEPENDENT SURVEILLANCE BROADCAST SYSTEM VIA FALSE TARGET INJECTION*", **Domenic Magazu III**, 2012
1. "*ADS-B out by HACKRF and received over the air by rtl-sdr dongle and dump1090*", **Jiao Xianjun**, 2014
1. "*Ghost in the Air(Trafﬁc): On insecurity of ADS-B protocol and practical attacks on ADS-B devices*", **Andrei Costin and Aurelien Francillon**, 2015
1. "*ADS-B Decoding Guide*", **Junzi Sun**, 2017
1. "*Inside Radio: An Attack and Defense Guide*", **Qing Yang & Lin Huang**, 2018

# History
This is a fork orginally from https://github.com/lyusupov/ADSB-Out in September 2017. 
