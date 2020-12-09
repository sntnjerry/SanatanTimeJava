# SanatanTime

Java module for converting the currently used Christian System Time to historic and vedic Sanatan System Time.

## Installation

Simply include the **SanatanTime.jar** file in your project.

### Special Guide For Android Projects:

- Create **libs** folder in your **app** folder where your **app level build.gradle** file is present.
- Download **SanatanTime.jar** from here, and place it in the **libs** folder created in previous step.
- In your **app level build.gradle** file, add the following line in **dependencies block**:

```gradle
implementation fileTree(dir: "libs", include: ["*.jar"])
```

- Done!

## Usage

Simply use this code (Modify according to your needs):

```java
import com.abhaytr.jbeauty.Beauty;
import com.abhaytr.jbeauty.ColorListener;
import java.util.Map;
import java.util.HashMap;

class YourClass
{
  
  public static void main(String args[])
  {
    Map parameters = new HashMap(); //For optional parameters (More about them below).
    Beauty.start(parameters, new ColorListener()
    {
      @Override
      public void on_new_color(int[] rgb_color)
      {
        int red_value = rgb_color[0];
        int green_value = rgb_color[1];
        int blue_value = rgb_color[2];
        //Now you can do anything with the RGB values (set as background, font color, etc..).
      }
    });
  }

}
```
Optional Parameters that are available are listed below in Parameters section and have to be passed as a Map (keys as name of parameters listed in Parameters section and value is your desired choice according to the options available for that parameter as specified in the Parameters section) which will be the first parameter for start() method of Beauty class.

## Parameters
- ### mode (Optional)
  
  Specifies colour set in which the colours have to vary.
  
  - **Parameter Value Type:** String or int[].
  
  - **Options for mode parameter**
    - **"dark":** Varies the colour in dark colours only (useful for dark mode projects).
    - **"light":** Varies the colour in light colours only (useful for light mode projects).
    - **[start_rgb, end_rgb]:** Varies the colour from start_rgb value (can be from 0 to 255) to end_rgb value (can be from 0 to 255).
  
  - **Default Value:** "" (i.e. varies from 0 to 255 RGB Values).

- ### start (Optional):
  
  Specefies colour in RGB format from which colours have to start varying.
  
  - **Input type for start parameter:** int[]{R_Value, G_Value, B_Value} (eg. int[]{0, 0, 0} for black).
  
  - **Default Value:** int[]{0, 0, 0} (for "dark", none or other mode parameter specified) and int[]{255, 255, 255} (for "light" mode parameter specified).

- ### time (Optional):
  
  Specifies the time in milliseconds after which the colour is changed according to its range.
  Useful for decreasing the time when using on slow hardware for maintaining the smoothness.
  
  - **Parameter Value Type:** int.
  
  - **Default Value:** 40 ms (Just perfect for majority hardware types).
  
So go ahead and enjoy the beauty of time varying RGB colour sets!

## Concept

In the Sanatan Time System, the day starts when the sun rises which happens around 3:30 AM everyday (It may differ by 1 to 2 Hours in different places and weather conditions). The units of the Sanatan Time System and its mathematical relation with the Current Time System Units are given below:

- **1 Vilipt (Sanatan Time Unit) = 1/150 Seconds OR 6.67 Milliseconds (approx.)(Christian Time Unit)**

- **1 Lipt (Sanatan Time Unit) = 60 Vilipt (Sanatan Time Unit) = 2/5 Seconds OR 400 Milliseconds (Christian Time Unit)**
    
    (60 Vilipt = 60 x 1 Vilipt = 60 x 6.67 Milliseconds = 400 Milliseconds (approx.) = 2/5 Seconds)

- **1 Pal (Sanatan Time Unit) = 60 Lipt (Sanatan Time Unit) = 24 Seconds (Christian Time Unit)**
    
    (60 Lipt = 60 x 1 Lipt = 60 x 400 Milliseconds = 24000 Milliseconds = 24 Seconds)

- **1 Ghadi (Sanatan Time Unit) = 60 Pal (Sanatan Time Unit) = 24 Minutes (Christian Time Unit)**
    
    (60 Pal = 60 x 1 Pal = 60 x 24 Seconds = 1440 Seconds = 24 Minutes)

- **1 Day = 60 Ghadi (Sanatan Time Unit) = 24 Hours (Christian Time Unit)**
    
    (60 Ghadi = 60 x 1 Ghadi = 60 x 24 Minutes = 1440 Minutes = 24 Hours)

Now, using the above relations, I have derived the mathematical formulas and mathematical functions which can be used to convert the Current System Time to Sanatan System Time and are listed below with explanation.

So, first of all, here is the list of information which we have with us:

- **sun_rise_hour** = The hour in which the sun rises (Most likely 3 in majority of the world).
- **sun_rise_minutes** = Number of minutes passed in sun_rise_hour after which the sun rises (Most likely 30 in majority of the world).
- **current_hour** = The hour in which the time is being calculated.
- **current_minutes** = Number of minutes passed in current_hour at the moment when time is being calculated.
- **current_seconds** = Number of seconds passed in current_minutes at the moment when time is being calculated.

Now our first objective is to calculate number of minutes passed between the sun rise time and the current time.

So for that, there are 2 cases which need to be considered and the calculations have to be done accordingly. The cases with explanation are as follows:

- **Case 1: current_hour is not equal to sun_rise_hour OR current_hour is equal to sun_rise_hour but current_minutes is less than sun_rise_minutes**

    In this case, the formula which we will use is as follows:

    **minutes_from_day_start = (60 - sun_rise_minutes) + (number_of_hours(sun_rise_hour + 1, current_hour) x 60) + current_minutes**

    - The expression **(60 - sun_rise_minutes)** gives the minutes which were passed in the sun_rise_hour after the rise of the sun.
    - The expression **(number_of_hours(sun_rise_hour + 1, current_hour) x 60)** gives the number of minutes passed between the hour after sun_rise_hour and the current_hour via function number_of_hours() which will be explained in detail below (60 is multiplied to number of hours to convert them into minutes passed).
    - Then, **current_minutes** is added to the result of above expressions to add the number of minutes passed in the current_hour till the moment where time is being calculated.

    For eg. if sun rises at 3:30 AM and we are calculating time at 6:40 AM, then:

    - sun_rise_hour = 3
    - sun_rise_minutes = 30
    - current_hour = 6
    - current_minutes = 40

    Hence, minutes_from_day_start = (60 - 30) + ((number_of_hours(3 + 1, 6) x 60) + 40 = 30 + (2 x 60) + 40 = 30 + 120 + 40 = 190 minutes.

- **Case 2: current_hour is equal to sun_rise_hour and current_minutes is equal to or greater than sun_rise_minutes**

    In this case, since the day has started less than an hour ago, hence we will use this formula which is as follows:

    **minutes_from_day_start = current_minutes - sun_rise_minutes**

    Basically, these are the minutes passed after sun rise in sun_rise_hour.

    For eg. if sun rises at 3:30 AM and we are calculating time at 3:45 AM, then:

    - sun_rise_hour = 3
    - sun_rise_minutes = 30
    - current_hour = 3
    - current_minutes = 45

    Hence, minutes_from_day_start = 45 - 30 = 15 minutes.

Now here's the explanation of **number_of_hours(1st hour, 2nd hour)** function:

So to calculate the number of hours between 2 hours, there are 2 cases which need to be considered and the calculations have to be done accordingly. The cases with explanation are as follows:

- **Case 1: 2nd hour is greater than 1st hour**

    In this case, we simply use the formula which is as follows:

    **hours_difference = 2nd hour - 1st hour**

    For eg. if 1st time is 3:30 AM and 2nd time is 6:30 AM, then:

    - 1st hour = 3
    - 2nd hour = 6

    Hence, hours_difference = 6 - 3 = 3 hours.

- **Case 2: 1st hour is greater than 2nd hour**

    In this case, since the new day has already started according to Current Time System, hence we will use this formula which is as follows:

    **hours_difference = 24 - (1st hour - 2nd hour)**

    - The expression **(1st hour - 2nd hour)** gives the number of hours remaining before the number of hours between 1st hour and 2nd hour is exactly equal to 24 hours (1 Day).

    For eg. if 1st time is 3:30 AM and 2nd time is 1:30 AM, then:

    - 1st hour = 3
    - 2nd hour = 1

    Hence, hours_difference = 24 - (3 - 1) = 24 - 2 = 22 hours.

Now as we have completed our first objective i.e. have calculated the number of minutes passed between the sun rise time and the current time, we are now ready to convert Current System Time into Sanatan System Time with the help of formulas which are listed with their explanation as follows:

### Number of Ghadis = minutes_from_day_start / 24

Since 1 Ghadi = 24 Minutes, hence to find total number of ghadis we simply divide number of minutes passed between the sun rise time and the current time by 24.

### minutes_from_ghadi_start = minutes_from_day_start mod 24

This formula gives us the number of minutes passed in the current ghadi.

### Number of Pals = ((minutes_from_ghadi_start x 60) + current_seconds) / 24

Since 1 Pal = 24 Seconds, hence to find total number of pals, we first convert minutes_from_ghadi_start to seconds by multiplying it with 60, then add current_seconds, hence getting the number of seconds passed in the current ghadi, and then divide that by 24 to get number of pals.

### Number of Lipts = (((minutes_from_ghadi_start x 60) + current_seconds) mod 24) / (2 / 5)

Since 1 Lipt = 400 Milliseconds or 2/5 of a Second, hence to find total number of lipts, we first convert minutes_from_ghadi_start to seconds by multiplying it with 60, then add current_seconds, hence getting the number of seconds passed in the current ghadi, then we mod that by 24 to get number of seconds passed in the current pal, then we divide that by (2/5) to get total number of lipts (lipt is a sub unit to pal like second is a sub unit to minute and hence total number of lipts is number of lipts passed in the current pal).

### Number of Vilipts = ((((minutes_from_ghadi_start x 60) + current_seconds) mod 24) mod (2 / 5)) / (1 / 150)

Since 1 Vilipt = 1/150 of a Second or 6.67 Milliseconds, hence to find total number of vilipts, we first convert minutes_from_ghadi_start to seconds by multiplying it with 60, then add current_seconds, hence getting the number of seconds passed in the current ghadi, then we mod that by 24 to get number of seconds passed in the current pal, then we mod that by (2/5) to get number of seconds passed in the current lipt, then we divide that by (1/150) to get total number of vilipts (vilipt is a sub unit to lipt like millisecond is a sub unit to second and hence total number of vilipts is number of vilipts passed in the current lipt).

These are the formulas derived by me which can convert any Christian System Time to Sanatan System Time.

For eg. if the sun rises at **3:30:00 AM** and we are calculating Sanatan System Time at **7:50:40 AM**, then:

- sun_rise_hour = 3
- sun_rise_minutes = 30
- current_hour = 7
- current_minutes = 50
- current_seconds = 40

So, according to the whole procedure explained above, first we will calculate number of minutes passed between the sun rise time and the current time.

Now since current_hour is not equal to sun_rise_hour, hence:

**minutes_from_day_start = (60 - 30) + (number_of_hours(3 + 1, 7) x 60) + 50**

Now for number_of_hours(), since 2nd hour is greater than 1st hour, hence:

**hours_difference = 7 - 4 = 3 Hours.**

substituting value of **number_of_hours(3 + 1, 7)** in equation for minutes_from_day_start, we get:

**minutes_from_day_start = (60 - 30) + (3 x 60) + 50 = 30 + 180 + 50 = 260 Minutes**

Now,

- **Number of Ghadis = 260 / 24 = 10 Ghadis (Ignore the decimal part as Ghadi Unit is an integer)**

- **minutes_from_ghadi_start = 260 mod 24 = 20 Minutes**

- **Number of Pals = ((20 x 60) + 40) / 24**
    - = (1200 + 40) / 24 
    - = 1240 / 24 
    - = **51 Pals (Ignore the decimal part as Pal Unit is an integer)**

- **Number of Lipts = (((20 x 60) + 40) mod 24) / (2 / 5)** 
    - = ((1200 + 40) mod 24) / (2 / 5) 
    - = (1240 mod 24) / (2 / 5) 
    - = 16 / (2 / 5) 
    - = 16 x (5 / 2) 
    - = 16 x 2.5 
    - = **40 Lipts**

- **Number of Vilipts = ((((20 x 60) + 40) mod 24) mod (2 / 5)) / (1 / 150)**
    - = (((1200 + 40) mod 24) mod (2 / 5)) / (1 / 150) 
    - = ((1240 mod 24) mod (2 / 5)) / (1 / 150) 
    - = (16 mod (2 / 5)) / (1 / 150) 
    - = 0.39 / (1 / 150) 
    - = 0.39 x 150 
    - = **59 Vilipts (Ignore the decimal part as Vilipt Unit is an integer)**

Hence, **7:50:40 AM** (Current System Time) is converted to **10 Ghadi, 51 Pal, 40 Lipt, 59 Vilipt** (Sanatan System Time) using the above explained procedure. So, this is how you can convert any Christian System Time to Sanatan System Time.

Jai Shree Ram!
