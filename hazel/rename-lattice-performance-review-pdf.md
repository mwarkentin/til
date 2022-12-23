We do performance reviews at Sentry twice a year using [Lattice](https://lattice.com), so I figured - why not automate the 30s process of renaming and organizing the downloaded packets PDF into my Dropbox.

I was able to set up a rule in [Hazel](https://www.noodlesoft.com) to do this:

![image](https://user-images.githubusercontent.com/67560/209260316-df3e0233-f76f-4391-8f32-77d6acb6880e.png)

The downloaded packet filenames look like: `Michael-Warkentin-2022-Mid-Year-Review-Cycle-.pdf`

The `Name matches ...` bit matches the filename template and extracts the year as a variable I can use for sorting. I had to do this as the end of year performance review won't be available until January so I can't just use the `Created Date` attribute:

![image](https://user-images.githubusercontent.com/67560/209260586-303ff289-9883-4e31-bfcc-7c64a963dd4f.png)

I use the `Rename` action to remove my name from the filename as I'm not too concerned about having to deal with anyone else's packets. :) 

![image](https://user-images.githubusercontent.com/67560/209260755-e5b419c2-d57b-4cd5-aca1-aee4beb1c5ca.png)

At the end of the day I'm left with the PDF sorted properly and grouped by year:

![image](https://user-images.githubusercontent.com/67560/209261253-351ddce7-7288-44ae-a63b-72f7449dc85f.png)
