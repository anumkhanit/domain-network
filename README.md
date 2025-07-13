<h1>Understanding DNS</h1>
<p>DNS Records and DNS Cache Management</p>

<h2>Environments and Technologies to use</h2>

- Microsoft Azure (Virtual Machines)
- Microsoft RD Client (Remote Desktop)

<h2>Operating Systems to use</h2>

- macOS Sonoma ***(if you own Macbook Air M1 or M2; it does not matter what type of macOS you own)***
- Windows 10 or Windows 11 Home or Pro ***(if you own either of them)***

-----

### Part 1: A-Record Creation & Testing

- Log Into Both VMs
 - Log into `DC-1` as `mydomain.com\jane_admin` and password `Cyberlab123!`
- Log into `Client-1` as `mydomain.com\jane_admin` and password `Cyberlab123!`

### Part 2: Ping and nslookup Fails

- On `Client-1`:
  - Open `Command Prompt`
	- Type `ping mainframe`

***You should see "Ping request could not find host mainframe.”***

- Next, run `nslookup mainframe`

***This will fail because mainframe does not exist in DNS yet.***

### Part 3: Create an `A-Record` on `DC-1`

- On `DC-1`
	- Open `Server Manager`
	- Go to `Tools` > `DNS`
	- In the left pane:
	  - Expand your server name
	  - Expand Forward Lookup Zones
	  - Click your domain (e.g., mydomain.com)
- In the right pane, right-click > `New Host` (A or AAAA)
   - Fill out:
	   - Name: `mainframe`
	   - IP address: Enter `DC-1’s Private IP` (e.g., `10.0.0.4`)
	   - Check: “Create associated pointer (PTR) record”
	   - Click `Add Host`
- You’ll get a success message. Click `Done`

### Part 4: Ping Again from `Client-1`

- Back on `Client-1`, in `Command Prompt`: `ping mainframe`

***This time, it should successfully ping the DC’s private IP address! You’ve just created and tested an A-record DNS entry.***

### Part 5: Local DNS Cache Exercise

- Change the A-Record’s IP on `DC-1`

- Open DNS Manager again
- Go to `mydomain.com` zone
- Right-click the mainframe A-record > `Properties`
- Change the IP to: `8.8.8.8`
- Click `OK`

### Part 6: Observe Cached Entry on `Client-1`

- Try pinging again: `ping mainframe`

***It will still ping the OLD IP — because it’s cached.***

- View the DNS cache: `ipconfig /displaydns`

***Look for the mainframe entry showing the old IP (DC-1’s IP).***

### Part 7: Flush the Cache

- On `Client-1`, run `ipconfig /flushdns`

***You should see: “Successfully flushed the DNS Resolver Cache.”***

***To confirm it’s gone: `ipconfig /displaydns` It will now be empty or very limited.***

### Part 8: Re-Ping mainframe

- Now ping again: `ping mainframe`

***You’ll now see the new IP: `8.8.8.8`, as pulled from the updated DNS record.***

### Part 9: CNAME Record Exercise

- Create a CNAME Record on `DC-1`
- Back to `DC-1`
  - In DNS Manager, under `mydomain.com`
	   - Right-click > `New Alias (CNAME)`
	   - Alias name: `search`
	   - Fully qualified domain name (FQDN) for target host: `www.google.com`
	   - Click `OK`

### Part 10: Test the CNAME Record from `Client-1`

- Back on `Client-1`
  - In Command Prompt: `ping search.mydomain.com`

***It may not return Google’s IP directly (some external CNAME targets may not respond to ping), but the name resolution should succeed.***

- Try: `nslookup search`
- You should see:
   - Name: `search.mydomain.com`
	 - Alias: `www.google.com`
	 - Address: `[Google’s IP address]`

***This confirms your CNAME record is resolving properly!***

-----

### Conclusion

<p>You've understood on how DNS work and how it was created and resolved “mainframe” to DC-1’s IP including the DNS Cache. You observed and flushed local DNS cache and mapping how the “search” to www.google.com and resolved it.</p>
