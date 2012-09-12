---
layout: post
title: "iOS Wireless App Distribution [Updated]"
category: iphone
---



UPDATE: There’s been a lot of interest in this post and Ad Hoc distribution methods. This is a very simple example so if you’re looking for an off-the-shelf solution, I suggest you check out testflightapp.com or Hockey which provide more robust features.

With the new iPhone iOS 4, you can distribute apps wirelessly without iTunes intervention. You still need to collect the appropriate devices id’s and create the appropriate provisioning profiles but if you already have those sending out files is easy.

First, select “Build and Archive” from your XCode build menu. Your archived project will be stored in the “Archived Applications” section of the the XCode organizer (Window > Organizer).

Next, select the archive you want to distribute in the XCode organizer and select “Share Application…” at the bottom of the window. Pick the appropriate provisioning profile and then “Distribute for Enterprise”.

In the distribution window, enter the title and the full url to the ipa file (where you plan to host your app) for example, http://jeffreysambells.com/example.ipa.

Along with the generated .plist and the .ipa files, you’ll need the provisioning profile and a simple index file, for example:


bc.. <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<title>My Cool app</title>
</head>
<body>
<ul>
    <li><a href="http://jeffreysambells.com/example.mobileprovision">
                Install Example Provisioning File</a></li>
    <li><a href="itms-services://?action=download-manifest&url=http://jeffreysambells.com/example.plist">
                Install Example Application</a></li>
</ul>
</body>
</html>


p.With these all uploaded to your server all you need to do is point people at the index file and they can select the links to install the provisioning profile and app directly from mobile safari on their iOS devices. A much nicer experience compared to installing through the iTunes sync process.

UPDATE

After re-publishing updates to my apps several times, I was finding it a little tedious to re-type the full url into the XCode tool—plus I could never remember what I named the damn files—so I came up with this quick and dirty index.php script to do the grunt work for me:

bc.. <?php

$ipas = glob('*.ipa');
$provisioningProfiles = glob('*.mobileprovision');
$plists = glob('*.plist');

$sr = stristr( $_SERVER['SCRIPT_URI'], '.php' ) === false ? 
    $_SERVER['SCRIPT_URI'] : dirname($_SERVER['SCRIPT_URI']) . '/';
$provisioningProfile = $sr . $provisioningProfiles[0];
$ipa = $sr . $ipas[0];
$itmsUrl = urlencode( $sr . 'index.php?plist=' . str_replace( '.plist', '', $plists[0] ) );


if ($_GET['plist']) {
    $plist = file_get_contents( dirname(__FILE__) 
        . DIRECTORY_SEPARATOR 
        . preg_replace( '/![A-Za-z0-9-_]/i', '', $_GET['plist']) . '.plist' );
    $plist = str_replace('_URL_', $ipa, $plist);
    header('content-type: application/xml');
    echo $plist;
    die();
}


?><!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<title>Install iOS App</title>
<style type="text/css">

li {
    padding: 1em;
}

</style>
</head>
<body>
<ul>
    <li><a href="<? echo $provisioningProfile; ?>">Install Team Provisioning File</a></li>
    <li><a href="itms-services://?action=download-manifest&url=<? echo $itmsUrl; ?>">
         Install Application</a></li>
</ul>
</body>
</html>

p. It’s nothing fancy so you’ll probably want to spiff it up but to use it (you’ll need PHP), just create a folder and drop in the index.php along with the .mobileprovision file, the exported .ipa and the exported .plist. To make things easy just enter _URL_ (uppercase url with underscores) as the “url” in XCode and the script will automatically fill in the proper URL as necessary. It doesn’t matter what the file names are as long as there is only one .moblieprovision, .ipa and .plist.
