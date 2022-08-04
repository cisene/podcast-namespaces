Acast Namespace - acast:*

This is basically a raw capture of https://learn.acast.com/en/articles/4465740-flex-advanced-encrypted-xml in case if it would go away.



Acast Namespace - podaccess:*

Short summary of what podcaccess are - there is zero documents available declaring this namespace.



[TOC]





## Flex: Advanced - Encrypted XML

Send Acast information directly on the RSS feed   

[Picure of author]

Written by  Katy Hearne-Church

Updated over a week ago    



In the Flex system, it is possible to pass show and episode settings using encoded XML tags. To make use of this feature, you will need:

- To add the schema for the Acast namespace
- Add encoded acast:settings at either show or episode level with your choice of settings
- Optionally, you may also encrypt the encoded settings using the acast:signature setting



## Acast namespace schema

Include the following information from the extended RSS tags looking for the Acast namespace *`xmlns:acast="https://schema.acast.com/1.0/"`*



For example:

```xml
<rss xmlns:atom="http://www.w3.org/2005/Atom" xmlns:itunes="http://www.itunes.com/dtds/podcast-1.0.dtd" xmlns:acast="https://schema.acast.com/1.0/" version="2.0">
```





## XML Tags

## acast:settings

This can be both at the show or 'channel' level and the episode or  'item' level. It’s encoded as base64 utf-8 string, either of a json  stringified, or optionally with the json string encrypted with the  information provided in the signature object (see **acast:signature** details below.)

Example:

```xml
<acast:settings>
<![CDATA[
eyJhZFNldHRpbmdzIjp7ImFkc0VuYWJsZWQiOnRydWUsInNwb25zRW5hYmxlZCI6dHJ1ZSwic2xvdHMiOlt7InR5cGUiOiJzcG9ucyIsInBsYWNlbWVudCI6InByZXJvbGwiLCJzdGFydCI6NDc5LCJkdXJhdGlvbiI6NjB9LHsidHlwZSI6ImFkcyIsInBsYWNlbWVudCI6InByZXJvbGwiLCJzdGFydCI6NDc5LCJkdXJhdGlvbiI6NjB9XX19]]>
</acast:settings>
```





### acast:settings at the Channel level (Show data)

At the channel (show) level, the settings object contains the base information for the show ad targeting, plus some default data for the episodes. Once decrypted and decoded, we expect the information to look like this (all values are optional):

```json
{
  defaults: {
    "intro": "", // Optional intro audio to attach to the start of the episodes
    "outro": "", // Optional outro audio to attach to the end of the episodes
    "adInSound": "", // URL of an ad break in sting
    "adOutSound": "", // URL of an ad break out sting
  }
 },
```





### **acast:settings at the Item level (Episode data)**

At the item (episode) level, it contains the episode-level information, the ad positions and number of ads.

```json
{{
   intro: 'mp3_url', // Optional intro audio to attach to the start of the episode
   outro: 'mp3_url', // Optional outro audio to attach to the end of the episode
   adSettings: {
   sponsEnabled: false, // set to false to disable sponsorships for the episode, overriding show level settings
   adsEnabled: false, // set to false to disable ads for the episode
    slots: [
     {
   type: 'spons', // 'spons' or 'ads'
   placement: 'midroll', // ‘preroll’ or ‘midroll’ or ‘postroll’
   start: 312, // Position of the slot in seconds 
     }
    ],...
   }
}
```





### **acast:signature**

It is possible to use base6e encoding on the acast:settings. If you would like to encrypt the tags, the acast:signature will need to be added to the feed.

At the channel level, it contains the information to decrypt the payloads of the rest of the values:

```xml
<acast:signature key="EXAMPLE" algorithm="aes-256-cbc">
<![CDATA[ quRO51a5m/mAho8azGcwFw== ]]>
</acast:signature>
```

**key =** corresponds to the public key that matches the private secret used as a passphrase on the encryption.



**algorithm -** the algorithm used for encrypting the data (should support any algorithm supported by node crypt functions).



For the technical stuff: the payload of the signature object is a base64 representation of the binary initialization vector. It’s both used as a IV for the aes encryption and used as a salt to prime the secret passphrase for encoding/decoding.



> ***If you are interested in adding encryption to your XML tags, please reach out to your Acast contact who will create a secret pair for you to implement.\***

The above paragraph indicates there is NO automation in this but rather just manual setup/configuration for each and every feed.





## podaccess

This namespace appears to have some to do with access to the content of items in feeds.



```xml
<podaccess:item locked="true" tier="1596254" ads="false" spons="false" premium="true">
  <itunes:title>Throwing Down: Episode 13</itunes:title>
  <pubDate>Mon, 11 May 2020 07:00:52 GMT</pubDate>
  <itunes:duration>57:39</itunes:duration>
  <guid isPermaLink="false">5eb751614daf164a540fac49</guid>
  <itunes:explicit>yes</itunes:explicit>
  <link>https://shows.acast.com/afterthoughtmedia/episodes/episode</link>
  <acast:episodeId>5eb751614daf164a540fac49</acast:episodeId>
  <acast:episodeUrl>throwing-down-episode-13</acast:episodeUrl>
  <acast:settings>dbckZ.. snip ..K0Dx</acast:settings>
  <itunes:episodeType>full</itunes:episodeType>
  <itunes:summary>..</itunes:summary>
  <podaccess:premium locked="true"/>
</podaccess:item>
```



## podaccess:item

This appears to be a prefixed item element with attributes. Example https://access.acast.com/rss/5e965e89862e9aff0d7d765f/Z5C8ZPJt

| Attribute | Type    |                                                              |
| --------- | ------- | ------------------------------------------------------------ |
| locked    | Boolean | TRUE or FALSE for locked or unlocked                         |
| tier      | Numeric | Reference to unknown Identity of something, single value or comma separated list |
| ads       | Boolean | TRUE or FALSE for adverts in episode?                        |
| spons     | Boolean | TRUR or FALSE for sponsor messages in episodes?              |
| premium   | Boolean | TRUE or FALSE for premium (and locked?) content              |

within podaccess:item element the podaccess:premium element occur, with attribute locked as a boolean.

<podaccess:premium locked="true"/>



Variation observed:

item element without the podaccess-prefix but with all the attributes mentioned above with the addition of podaccess:image with attributes **locked**, **tier**, **ads** and **spons**. 



### podaccess:auth

```xml
<podaccess:auth>
  <podaccess:login type="acast-access">
    <podaccess:provider>acast-open</podaccess:provider>
    <podaccess:url/>
    <podaccess:showId>5e96..765f</podaccess:showId>
  </podaccess:login>
  <podaccess:user status="valid">
    <podaccess:username>927eb911-..-..-..-e060143d0871</podaccess:username>
    <podaccess:provider>acast-open</podaccess:provider>
  </podaccess:user>
</podaccess:auth>
```

Acast Namespace - acast:*

This is basically a raw capture of https://learn.acast.com/en/articles/4465740-flex-advanced-encrypted-xml in case if it would go away.



Acast Namespace - podaccess:*

Short summary of what podcaccess are - there is zero documents available declaring this namespace.



[TOC]





## Flex: Advanced - Encrypted XML

Send Acast information directly on the RSS feed   

[Picure of author]

Written by  Katy Hearne-Church

Updated over a week ago    



In the Flex system, it is possible to pass show and episode settings using encoded XML tags. To make use of this feature, you will need:

- To add the schema for the Acast namespace
- Add encoded acast:settings at either show or episode level with your choice of settings
- Optionally, you may also encrypt the encoded settings using the acast:signature setting



## Acast namespace schema

Include the following information from the extended RSS tags looking for the Acast namespace *`xmlns:acast="https://schema.acast.com/1.0/"`*



For example:

```xml
<rss xmlns:atom="http://www.w3.org/2005/Atom" xmlns:itunes="http://www.itunes.com/dtds/podcast-1.0.dtd" xmlns:acast="https://schema.acast.com/1.0/" version="2.0">
```





## XML Tags

## acast:settings

This can be both at the show or 'channel' level and the episode or  'item' level. It’s encoded as base64 utf-8 string, either of a json  stringified, or optionally with the json string encrypted with the  information provided in the signature object (see **acast:signature** details below.)

Example:

```xml
<acast:settings>
<![CDATA[
eyJhZFNldHRpbmdzIjp7ImFkc0VuYWJsZWQiOnRydWUsInNwb25zRW5hYmxlZCI6dHJ1ZSwic2xvdHMiOlt7InR5cGUiOiJzcG9ucyIsInBsYWNlbWVudCI6InByZXJvbGwiLCJzdGFydCI6NDc5LCJkdXJhdGlvbiI6NjB9LHsidHlwZSI6ImFkcyIsInBsYWNlbWVudCI6InByZXJvbGwiLCJzdGFydCI6NDc5LCJkdXJhdGlvbiI6NjB9XX19]]>
</acast:settings>
```





### acast:settings at the Channel level (Show data)

At the channel (show) level, the settings object contains the base information for the show ad targeting, plus some default data for the episodes. Once decrypted and decoded, we expect the information to look like this (all values are optional):

```json
{
  defaults: {
    "intro": "", // Optional intro audio to attach to the start of the episodes
    "outro": "", // Optional outro audio to attach to the end of the episodes
    "adInSound": "", // URL of an ad break in sting
    "adOutSound": "", // URL of an ad break out sting
  }
 },
```





### **acast:settings at the Item level (Episode data)**

At the item (episode) level, it contains the episode-level information, the ad positions and number of ads.

```json
{{
   intro: 'mp3_url', // Optional intro audio to attach to the start of the episode
   outro: 'mp3_url', // Optional outro audio to attach to the end of the episode
   adSettings: {
   sponsEnabled: false, // set to false to disable sponsorships for the episode, overriding show level settings
   adsEnabled: false, // set to false to disable ads for the episode
    slots: [
     {
   type: 'spons', // 'spons' or 'ads'
   placement: 'midroll', // ‘preroll’ or ‘midroll’ or ‘postroll’
   start: 312, // Position of the slot in seconds 
     }
    ],...
   }
}
```





### **acast:signature**

It is possible to use base6e encoding on the acast:settings. If you would like to encrypt the tags, the acast:signature will need to be added to the feed.

At the channel level, it contains the information to decrypt the payloads of the rest of the values:

```xml
<acast:signature key="EXAMPLE" algorithm="aes-256-cbc">
<![CDATA[ quRO51a5m/mAho8azGcwFw== ]]>
</acast:signature>
```

**key =** corresponds to the public key that matches the private secret used as a passphrase on the encryption.



**algorithm -** the algorithm used for encrypting the data (should support any algorithm supported by node crypt functions).



For the technical stuff: the payload of the signature object is a base64 representation of the binary initialization vector. It’s both used as a IV for the aes encryption and used as a salt to prime the secret passphrase for encoding/decoding.



> ***If you are interested in adding encryption to your XML tags, please reach out to your Acast contact who will create a secret pair for you to implement.\***

The above paragraph indicates there is NO automation in this but rather just manual setup/configuration for each and every feed.





## podaccess

This namespace appears to have some to do with access to the content of items in feeds.



```xml
<podaccess:item locked="true" tier="1596254" ads="false" spons="false" premium="true">
  <itunes:title>Throwing Down: Episode 13</itunes:title>
  <pubDate>Mon, 11 May 2020 07:00:52 GMT</pubDate>
  <itunes:duration>57:39</itunes:duration>
  <guid isPermaLink="false">5eb751614daf164a540fac49</guid>
  <itunes:explicit>yes</itunes:explicit>
  <link>https://shows.acast.com/afterthoughtmedia/episodes/episode</link>
  <acast:episodeId>5eb751614daf164a540fac49</acast:episodeId>
  <acast:episodeUrl>throwing-down-episode-13</acast:episodeUrl>
  <acast:settings>dbckZ.. snip ..K0Dx</acast:settings>
  <itunes:episodeType>full</itunes:episodeType>
  <itunes:summary>..</itunes:summary>
  <podaccess:premium locked="true"/>
</podaccess:item>
```



## podaccess:item

This appears to be a prefixed item element with attributes. Example https://access.acast.com/rss/5e965e89862e9aff0d7d765f/Z5C8ZPJt

| Attribute | Type    |                                                              |
| --------- | ------- | ------------------------------------------------------------ |
| locked    | Boolean | TRUE or FALSE for locked or unlocked                         |
| tier      | Numeric | Reference to unknown Identity of something, single value or comma separated list |
| ads       | Boolean | TRUE or FALSE for adverts in episode?                        |
| spons     | Boolean | TRUR or FALSE for sponsor messages in episodes?              |
| premium   | Boolean | TRUE or FALSE for premium (and locked?) content              |

within podaccess:item element the podaccess:premium element occur, with attribute locked as a boolean.

<podaccess:premium locked="true"/>



Variation observed:

item element without the podaccess-prefix but with all the attributes mentioned above with the addition of podaccess:image with attributes **locked**, **tier**, **ads** and **spons**. 



### podaccess:auth

```xml
<podaccess:auth>
  <podaccess:login type="acast-access">
    <podaccess:provider>acast-open</podaccess:provider>
    <podaccess:url/>
    <podaccess:showId>5e96..765f</podaccess:showId>
  </podaccess:login>
  <podaccess:user status="valid">
    <podaccess:username>927eb911-..-..-..-e060143d0871</podaccess:username>
    <podaccess:provider>acast-open</podaccess:provider>
  </podaccess:user>
</podaccess:auth>
```

