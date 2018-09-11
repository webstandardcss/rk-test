# Site Name
http://attorneysanchez.com

# Responsive Chrome Screenshots

## Links

* https://github.com/sindresorhus/pageres-cli
* https://stackoverflow.com/questions/33511772/read-file-line-by-line-in-powershell/33511982
* https://www.booleanworld.com/take-screenshots-webpages-command-line/
* https://www.xml-sitemaps.com/
* https://wummel.github.io/linkchecker/

## Install pageres (and npm if needed)

`sudo npm install -g pageres-cli`

## Clean up the sitemaps.xml file and rename it to sitemaps.txt

Get lines from the XML sitemap and use the following regex to clean the file.

* Find <loc>(.*?)</loc>
* Replace $1

* Find \n\n
* Replace \n

* Find \n
* Replace *WITH NOTHING*

## Powershell script generates Chrome screenshots.

Run The following powershell after creating the sitemap.txt using the above steps.

```powershell
foreach($line in [System.IO.File]::ReadLines("sitemap.txt")) {
pageres --overwrite $line 1280x800 1920x1080 768x1024 640x800 375x800 --format=jpg --filename="screenshots/chrome/<%= url %>-<%= size %><%= crop %>"}
```

### Or try Linux BASH

```bash
export WORKDIR="/home/trey/treyhome/Desktop/eta-tests";
export IMGDIR="${WORKDIR}/screenshots/chrome";
export SITEMAP="${WORKDIR}/sitemap.txt";
readarray -t sites < ${SITEMAP};
for site in "${sites[@]}"; do 
pageres --overwrite ${site} 1280x800 1920x1080 768x1024 640x800 375x800 --format=jpg --filename="${IMGDIR}/<%= url %>-<%= size %><%= crop %>";
done;
ls -al ${IMGDIR}
```

<!-- 

```powershell
foreach($line in [System.IO.File]::ReadLines("sitemap.txt")) { pageres --overwrite $line 1280x800 1920x1080 768x1024 640x800 375x800 --format=jpg --filename="screenshots/chrome/<%= date %>_<%= url %>-<%= size %><%= crop %>"}
```

**Must add firefox to PATH**

```powershell
foreach($url in [System.IO.File]::ReadLines("./sitemap.txt")) {
    foreach($res in @("1280","1920","768","640","375")) {
        $name = $url
        $name = $name -replace "https://",''
        $name = $name -replace "http://",''
        $name = $name -replace '/','-'
        $name = "$name-$res"
        # Write-Host $url $name $res;
        "C:\Program Files\Mozilla Firefox\firefox.exe -screenshot $url --window-size=$res"
    }
}
```

-->

## BASH script generates Firefox screenshots.
https://docs.microsoft.com/en-us/windows/wsl/install-win10
http://www.techoism.com/how-to-install-git-bash-on-windows/

```bash
# Get URL From sitemap.txt
# Write-Host $url $name $res;
ln -s /mnt/c ~/c; # Ubuntu mountpoint for c drive
ln -s ~/c/Program\ Files/Mozilla\ Firefox/ ~/c/bin/moz;
export WORKDIR="/home/trey/treyhome/Desktop/eta-tests";
export IMGDIR="${WORKDIR}/screenshots/firefox";
export SITEMAP="${WORKDIR}/sitemap.txt";
export FIREFOXdir="/home/trey/c/bin/moz";
export FIREFOXexe="${FIREFOXdir}/firefox.exe";
export FIREFOXres=(1280 1920 768 640 375);

readarray -t sites < ${SITEMAP};
cd ${IMGDIR};
for site in "${sites[@]}"; do 
  for res in ${FIREFOXres[@]}; do
    # echo hey $site $res; 
    filename=${site};
    filename=${filename/http:\/\/attorneysanchez\.com\///};
    filename=${filename/\.php$//};
    filename=$(echo ${filename} | sed -e 's/\//_/g');
    filename=$(echo ${filename} | sed -e 's/^_//g');
    filename="${filename}_${res}.png";
    cmd="${FIREFOXexe} --screenshot ${filename} ${site} --window-size=${res}";
    echo "Starting: ${cmd}";
    ${cmd};
    echo "Done: ${cmd}";
    done; 
done;
ls ${IMGDIR};
cd ${WORKDIR};
```

### Bash Single Screenshot

```bash
# Single screenshot prototype custom directory
# Write-Host $url $name $res;
ln -s /mnt/c ~/c
ln -s ~/c/Program\ Files/Mozilla\ Firefox/ ~/c/bin/moz;
export FIREFOXdir="/home/trey/c/bin/moz";
export FIREFOXexe="$FIREFOXdir/firefox.exe";
export FIREFOXdst="/home/trey/c/bin/firefox"
export FIREFOXurl="http://google.com";
export FIREFOXres="1024";
export FIREFOXimg="screenshot1c.png";
export FIREFOXcmd="$FIREFOXexe --screenshot $FIREFOXimg $FIREFOXurl --window-size=$FIREFOXres";
echo $FIREFOXcmd;
mkdir -p $FIREFOXdst;
$($FIREFOXcmd) && ls $FIREFOXimg;
mv $FIREFOXdir/$FIREFOXimg $FIREFOXdst;
ls -al $FIREFOXdst;
```

