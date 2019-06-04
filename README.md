

# mjdemetra
Matlab function to perform seasonal adjustment with JDemetra+

MJDemetra is a Matlab interface to JDemetra+, the seasonal adjustment
software [officially
recommended](https://ec.europa.eu/eurostat/cros/system/files/Jdemetra_%20release.pdf)
to the members of the European Statistical System (ESS) and the European
System of Central Banks. JDemetra+ implements the two leading seasonal
adjustment methods
[TRAMO/SEATS+](http://www.bde.es/bde/en/secciones/servicios/Profesionales/Programas_estadi/Programas_estad_d9fa7f3710fd821.html)
and [X-12ARIMA/X-13ARIMA-SEATS](https://www.census.gov/srd/www/x13as/).

Besides seasonal adjustment, JDemetra+ bundles other time series models
that are useful in the production or analysis of economic statistics,
including for instance outlier detection, [nowcasting](https://github.com/nbbrd/jdemetra-nowcasting/wiki), temporal
disaggregation or benchmarking.

For more details on the JDemetra+ software see
<https://github.com/jdemetra/jdemetra-app>.

MJDemetra offers access to some of the the seasonal adjustment options and outputs of JDemetra+.

## Installation

- Make sure Matlab uses the appropiate Java version
(type ```version -java``` in Matlab to find out which version is used)
Matlab should use a Java SE version that is [compatible with
JDemetra+](https://github.com/jdemetra/jdemetra-app) 

- Edit the ```classpath.txt``` file (type ```which classpath.txt``` in Matlab to find its location)
and make  sure the paths containing your .jar libraries are listed. For example, 
my ```classpath.txt``` file includes the path where the java compiled sofware of JDemetra+ is included:
```L:\DSXNPAPER\Project 2017\R model\models\JDinMATLAB\demetra-tstoolkit-2.2.2.jar```
If you don't want to modify the ```classpath.txt``` file because you are using sofware that relies on older Java versions, then            add the line:
```javaclasspath('L:\DSXNPAPER\Project 2017\R model\models\JDinMATLAB\')``` 
at the beginning of the ```mjdemetra``` function so that the desired version of Java is used only within within the function.

## Example

The ```mjdemetra``` function can be used in many different ways. By default it plots the seasonally adjusted data (with confidence intervals only when the TramoSeats method is used) and highlights outliers. The seasonally adjusted series without removing calendar effects is also plotted.

```Matlab
        [sa, rslts]= mjdemetra2(data,'horizon',20,'Method','TramoSeats','CalendarOption','RSAfull')
        [sa, rslts]= mjdemetra2(data2,            'Method','X13'      );
        [sa, rslts]= mjdemetra2(data,'horizon',20,'Method','TramoSeats','CalendarOption','RSA5')
        [sa, rslts]= mjdemetra2(data,'horizon',20,'Method','X13'       ,'CalendarOption','RSA5c')
        [sa, rslts]= mjdemetra2(data)
        [sa, rslts]= mjdemetra2(data,                                  ,'CalendarOption','RSA0')
        [sa, rslts]= mjdemetra2(data,                                                          , 'grafico',false)
```

Note that the only compulsory input is 'data', which is a TsData object of JDemetra+. The following code performs the following tasks:

- Load the data from excel and create the TsData object (note the Java code runs in Matlab without any problem)

```Matlab
[x,dates]=xlsread('gdpQ','data');
fechasExcel=dates(2:end,1)
y=x(:,1)
 

% define the dates
datesVector = datevec(fechasExcel);
firstYear= datesVector(1,1);

% this is java code
firstPeriod = ec.tstoolkit.timeseries.simplets.TsPeriod(ec.tstoolkit.timeseries.simplets.TsFrequency.Quarterly, firstYear, 0);
data = ec.tstoolkit.timeseries.simplets.TsData(firstPeriod , y, false); 

```
- Perform the seasonal adjustment

```Matlab
[sa, rslts]= mjdemetra(data);  
```
