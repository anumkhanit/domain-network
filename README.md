<p align="center">
<img src="https://i.imgur.com/KcNPs2m.jpeg" alt="Active Directory" width=500 height=300/> 
</p>

<h1>Understanding DNS – DNS Records & Cache Management</h1>
<p>DNS (Domain Name System) is often described as the “phonebook of the internet.” It translates human-readable names (like www.google.com) into IP addresses that computers use to communicate.</p>

<h1>🧠 Overview</h1>
<p>In this lab, we’ll explore A-records, CNAME records, and DNS caching through hands-on exercises in an Azure-based environment.</p>

<h2>Environments and Technologies to use</h2>

- Microsoft Azure (Virtual Machines)
- Microsoft RD Client (Remote Desktop)

<h2>Operating Systems to use</h2>

- macOS Sonoma ***(if you own Macbook Air M1 or M2; it does not matter what type of macOS you own)***
- Windows 10 or Windows 11 Home or Pro ***(if you own either of them)***

-----

<h1>📚 DNS Concepts to Understand Before Starting</h1>

- `A Record (Address Record)`: Maps a hostname to an IPv4 address.
- `CNAME Record (Canonical Name)`: Creates an alias from one name to another.
- `PTR Record`: Maps an IP address to a hostname (reverse lookup).
- `DNS Cache`: Temporary storage of DNS lookups to speed up name resolution.
- `Forward Lookup Zone`: Where DNS stores hostname → IP mappings.
- `nslookup`: Command-line tool to query DNS records.
- `ipconfig /flushdns`: Clears the local DNS cache.

-----

### Part 1: A-Record Creation & Testing

1.	Log into Both VMs:
   - `DC-1`: `mydomain.com\jane_admin` / `Cyberlab123!`
   - `Client-1`: `mydomain.com\jane_admin` / `Cyberlab123!`

-----

### Part 2: Initial Ping and nslookup (Fails)

1. On `Client-1`, open `Command Prompt` and type: `ping mainframe`

***You should see "Ping request could not find host mainframe.”***

2. Check `DNS` resolution, run: `nslookup mainframe`

***This will fail because mainframe does not exist in DNS yet.***

-----

### Part 3: Create an `A-Record` on `DC-1`

1.	On `DC-1`, open `Server Manager` → `Tools` > `DNS`.
2.	Expand:
     - Your server name
     - Forward Lookup Zones
     - Your domain (e.g., mydomain.com)
3.	Right-click → `New Host (A or AAAA)`:
     - Name: `mainframe`
     - IP Address: `DC-1`’s Private IP (look for that in Azure) (e.g., `10.0.0.4`)
     - Check: Create associated pointer (PTR) record
     - Click Add Host

***You’ll get a success confirmation.***

-----

### Part 4: Test the `A-Record`

- Back on `Client-1`, in `Command Prompt` type: `ping mainframe`

***This time, it should successfully ping the DC’s private IP address! You’ve just created and tested an A-record DNS entry.***

-----

### Part 5: Local DNS Cache in Action

1. Change the mainframe `A-record`’s IP in DNS Manager on `DC-1` to:
    - Open DNS Manager again
    - Go to `mydomain.com` zone
    - Right-click the mainframe A-record > `Properties`
    - Change the IP to: `8.8.8.8`
    - Click `OK`
  
-----

### Part 6: Observe Cached Entry

1. On `Client-1` on `Command Prompt`, type: `ping mainframe`

***It will still ping the OLD IP — because it’s cached locally.***

2. View the DNS cache: `ipconfig /displaydns`

***You should see mainframe mapped to the old IP.***

-----

### Part 7: Flush the Cache

1. On `Client-1`, clear the cache by running this code: `ipconfig /flushdns`

***You should see: “Successfully flushed the DNS Resolver Cache.” To confirm it’s gone: `ipconfig /displaydns` It will now be empty or very limited.***

2. Check again by running this code: `ipconfig /displaydns`

***Now it’s empty or minimal.***

-----

### Part 8: Verify Updated Resolution

1. Ping again: `ping mainframe`

***✅ This should now show 8.8.8.8 from the updated DNS record.***

-----

### Part 9: Create a CNAME Record

1. On `DC-1`:
	- In `DNS Manager`, under `mydomain.com` → Right-click → `New Alias (CNAME)`.
2.	Fill out:
     - Alias name: `search`
	 - FQDN for target host: `www.google.com`
3.	Click `OK`.

-----

### Part 10: Test the CNAME Record

1. Test the CNAME Record: `ping search.mydomain.com`

***Ping might fail (Google may block ICMP), but DNS resolution still works.***

2. Test: `nslookup search`
3. You should see:
   - Name: `search.mydomain.com`
	 - Alias: `www.google.com`
	 - Address: `[Google’s IP address]`

***This confirms the CNAME works.***

-----

<h1>🔍 Why This Matters in the Real World</h1>

- `A-Records` let you map local services to IPs for easy access.
- `CNAME`s simplify name changes by creating easy-to-remember aliases.
- `DNS Caching` speeds up lookups but can cause issues if old records linger — flushing helps resolve them.
- `PTR Records` are critical for email servers and reverse lookups.

<p>Understanding DNS is foundational for network administration, web hosting, and cybersecurity. It’s the silent backbone of how users and systems find resources across networks.</p>

-----

### Conclusion

<p>You've understood now on how DNS work and how it was created and resolved “mainframe” to DC-1’s IP including the DNS Cache. You observed and flushed local DNS cache and mapping how the “search” to www.google.com and resolved it.</p>
