---
layout: post
title:  "Monitoring Network Health with PowerShell: A High Ping and Timeout Logging Script"
---

# Monitoring Network Health with PowerShell: A High Ping and Timeout Logging Script

## Some Background
Over my summer months I began noticing that I was getting internet drop outs during regular use of my family network and it was occurring only on my device. The outage wasn't much longer than a few minutes and would return just as mysteriously and I wanted to get to the bottom of it. This led to a rabbit hole of looking for the problem, was my PC gets its connection via Wi-Fi from my motherboard and uses two antennas in the IO as its source. My first thought was maybe one my antenna's had become disconnected, damaged, or otherwise was non functioning. That was fine, next I made sure my network adapter's drivers were up to date, and then I got a recommendation from a friend to test out how my connection to my router over a public DNS like Google's 8.8.8.8. This method allows someone to see how their connection is with their home network, and their network's connection to the internet.

## Why I made this script
Just to get a basic reading of my current connection I got the IP of my router and like I mentioned earlier used Google's public DNS and threw them into command prompt using the commands:

    ping -t 8.8.8.8 #To test our connection to the internet
    ping -t 192.168.0.1 #To test my PC's connection with the router

For those who didn't know -t will make the ping command run continuously. So I ended up with two command prompt windows, giving me lines and lines of ping requests it looked something like this:

![Command prompt ping example](/assets/ping_example.png)
    
Now I was printing out lines upon lines of ping requests, which does in fact work, but I wanted to automate it and a way to sort through the results, and to know when I had lost connection or otherwise between the two hosts. Hence the entire reason behind this PowerShell ping script.
## Relearning PowerShell
My most recent project posted on GitHub before this was the Hash-GUI script I made **eight years ago**  in python. While I haven't been away from programming for nearly that long and have been working on my own projects I decided that I was going to start uploading again on Github as a good way for me to document my progress, hence this blog. 

First I needed to refamiliarize myself with PowerShell syntax. I still had some programming basics from my previous project in early may. I participated in the [Bullet Hell Game Jam](https://itch.io/jam/bullet-hell-v) Before I made this script and was spending time working in godot script a python like script used for the Godot game engine. So jumping back into programming with PowerShell wasn't that hard following my fundamentals. I can highly recommend [Jacked Programmer's PowerShell playlist](https://www.youtube.com/watch?v=NECE5CX69tk&list=PLnK11SQMNnE4vcvuAahz4KhNOS7zOfmB3)  as it worked for me in conjunction with the [Powershell documentation](https://learn.microsoft.com/en-us/powershell/scripting/lang-spec/chapter-01?view=powershell-7.4) to bring this script to life. 
## What does it actually do?
This script preforms a periodic ping to a list of specified hosts. It monitors for high latency and connection drops, which are then logged into a txt file. You can edit what hosts you want to ping and how high of a ping threshold do you consider too high and want to be logged.

### The Code

I highly recommend you look at the [repo](https://github.com/Mogi83/powershell-ping-logger) for this script as it might help you work through how it all functions. This does work by itself mind you I just think it may be easier to read on the repo. Here is the full script:
```` {
# Define the IP addresses or hostnames to ping
$hosts = @("192.168.0.1", "8.8.8.8")

# Store ping results
$pingResults = @{}

# Define the ping threshold in milliseconds
$pingThreshold = 10

# Function to perform ping and check conditions
function Check-Ping {
    param (
        [string]$TargetHost
    )

    Write-Output "Pinging $TargetHost..."

    try {
        # Perform the ping
        $pingResult = Test-Connection -ComputerName $TargetHost -Count 1 -ErrorAction Stop
        $responseTime = $pingResult.ResponseTime
        $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"

        Write-Output "Ping result for ${TargetHost}: ${responseTime} ms"

        # Store the result
        $pingResults[$TargetHost] = $responseTime

        # Check for high ping
        if ($responseTime -gt $pingThreshold) {
            Write-Output "High ping detected on $TargetHost - ${responseTime} ms at $timestamp"
            
            # Capture the output with timestamp and other hosts' results
            $logMessage = "High ping detected on ${TargetHost} - ${responseTime} ms at ${timestamp}`n"
            foreach ($currentHost in $hosts) {
                if ($pingResults.ContainsKey($currentHost)) {
                    $logMessage += "Ping result for ${currentHost}: ${pingResults[$currentHost]} ms at ${timestamp}`n"
                } else {
                    $logMessage += "Ping result for ${currentHost}: No result at ${timestamp}`n"
                }
            }
            $logMessage | Add-Content -Path "ping_log.txt"
        }
    } catch {
        # Handle timeout or any errors
        $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
        Write-Output "Request timed out or error occurred on $TargetHost at $timestamp"
        
        # Capture the timeout message with timestamp
        $logMessage = "Request timed out or error occurred on ${TargetHost} at ${timestamp}`n"
        $logMessage += $_.Exception.Message
        $logMessage | Add-Content -Path "ping_log.txt"
    }
}

# Main loop to continuously check the hosts
Write-Output "Starting script at $(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')"
while ($true) {
    # Reset ping results before each iteration
    $pingResults.Clear()

    foreach ($currentHost in $hosts) {
        Check-Ping -TargetHost $currentHost
    }
    
    # Output waiting message and add a line break
    Write-Output "Waiting for 1 second before next check..."
    Write-Output ""  # Adds a blank line
    Start-Sleep -Seconds 1
}
Write-Output "Script completed at $(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')"
Read-Host -Prompt "Press Enter to exit"
````
### In summary:
 1. We setup some variables to hold our list of hosts and our ping threshold.
    
 2. Then our ping function (Check-Ping), compares response
    time to the given threshold and logs results accordingly. It is also
    looking for timeouts and errors but I couldn't figure out how to get
    it to be too specific on what kind of error.
    
 3. All of this goes through our main loop so that we can continuously ping our hosts and see what gets logged over a period of time.

### Example Log Output:
Here is what the log.txt file could look like, for this example in the first output my threshold was a low 10ms, on normal function I will usually set the threshold to anything over 200ms as shown on the second output.

````
High ping detected on 8.8.8.8 - 21 ms at 2024-07-31 00:55:04
Ping result for 192.168.0.1: 9 ms at 2024-07-31 00:55:04
Ping result for 8.8.8.8: 21 ms at 2024-07-31 00:55:04

Request timed out or error occurred on 8.8.8.8 at 2024-08-02 19:13:34
Testing connection to computer '8.8.8.8' failed: Error due to lack of resources
High ping detected on 8.8.8.8 - 203 ms at 2024-08-02 19:54:05
Ping result for 192.168.0.1: 12 ms
Ping result for 8.8.8.8: 203 ms
````

## Results:
I monitored my network over the course of a full day, and did that over a week or so. My results gave me even more questions as I was getting a mix of reports saying there was high ping to my router and other times where I had a high ping to 8.8.8.8. However overall I found that more often than not I was getting a drop in overall internet connection and staying connected to my router. My data isn't conclusive by any means, I could keep running this script for a larger sample size and make inferences on that but I found that a few days of data worked for me. 

I never did solve my connection issue and just as it had mysteriously shown up after a few weeks of it occurring it left just as strangely.  Not that I am complaining.

# Key Takeaways:
This project was a big step for me personally, for years I have left and come back to programming without much to show for it. All of my previous programing work consisted of tutorials like code academy, assignments for my AP Computer Science class, and small changes to existing code. This project was an excellent way for me to focus on a real-world problem that I wanted to solve. Instead of learning more fundamentals, or doing an example project like a weather app that is so common, I found it very rewarding that this script is made this for an actual use. 

# Potential Improvements:
If I were to return to this script I would probably rewrite it again, in a language I am more familiar with. I went with PowerShell because I figured it would be the best option for my machine to accurately capture my data, in retrospect I bet any modern programming language could have done this to varying degrees of success. Here are my thoughts on what I would do to improve this script:



 - Overall rework of how the logging system works, in its current state you get very long text files that are hard to parse through. Exporting our data into an csv format would greatly help in visualizing network drops.
 - Automatic start/stop/archive of log files, I was manually stopping the script periodically (12-24hrs) and putting the text file into a separate folder then starting it back up.
 -  A more robust way of detecting error types, for example showing if the connection came back with, unknown host, destination host unreachable, general failure error, or request timed out. Could greatly help in diagnosing a connection instead of just printing "Request timed out or error occurred" for any error.
 - A better way for the user to set their variable in this script. For example say this was in Python I probably would have asked the user using the Input() function for ease of use.
 - The most ambitious thing I would probably do for this project is add a GUI to set our hosts and response times. This is very out of scope for me currently but would add lots of value to this project.

# Conclusion
I hope if needed this script can serve as a tool for anyone facing network issues. Feel free to edit and adapt this code to fit your needs. If you have anything you would like to share about what you think about this project, or how you would change it feel free to put a post in the discussions board in the [repo's Discussions board](https://github.com/Mogi83/powershell-ping-logger/discussions). Thank you for reading!