

### Learning java regex

We will be utilizing a learning methodology called [ELM](https://github.com/mohanadwalo/effective-learning-methods). This methodology involves the following key steps:


1. [Goal](#1goal)
2. [Acquire Basic Knowledge](#2acquire-basic-knowledge)
3. [Summary](#3summary)
4. [Use case list](#4-use-caselist)
5. [Project(Optional)](#5-project)
6. [Improvements & Suggestions)](#6-improvements--suggestions)

For more detailed information about the ELM methodology, you can read further [here](https://github.com/mohanadwalo/effective-learning-methods).

ELM Flowchart

### ELM Flowchart
![Effective Learning Method](https://github.com/mohanadwalo/effective-learning-methods/blob/main/elm.png)

#### 1.Goal

Using java regex, I would like to have the ability to search through any text,
find specific patterns, and remove or update them.
Additionally, I want to be able to validate text against
a pattern to accept or reject it.
Finally, I want to extract useful data from large text.


#### 2.Acquire Basic Knowledge

Before diving into specific use cases, it's important to first understand the basics of regular expressions (regex). Key concepts include:

**Meta characters:** \d, \w, \s, \b, \D, \W, \S, \B  
**Special characters:** +, ?, ., *  
**Groups:** Standard groups, named groups, lookaheads, and lookbehinds  
**Character classes:** []  
**Quantifiers:** {}  
We can explore these topics by reading concise tutorials.  

#### 3.Summary

Based on the knowledge acquired in step 2, you should be able to have any of the following:

- Reference document  
- Regex basics cheat-sheet, you can find one [here](https://github.com/mohanadwalo/cheat-sheets/blob/main/REGEX.md)  

#### 4. Use case list

ELM distinguishes between two different use case types,

- Comprehensive use case list  
- Unlimited use case list  

Learning regex is of type unlimited use case list, meaning there are countless use cases.

Here we are going to list as much as possible use case

#### 4.1 Query & Filter use cases

#### 4.1.1. Use Regex to Extract Protocol, Domain Name, and Path from a URL

In this use case, we will create a basic regex to parse URLs, extracting the protocol, domain name, and path. In a later use case, we will develop more complex and advanced regex patterns.

***Note: While there are many libraries available that can easily parse URLs, the goal here is to understand how to use regex to accomplish the same task***

Before we begin, please note that the general structure of any URL is {protocol}://{subdomain}.{SLD}.{TLD}/path?querystring  

- SLD (Second-Level Domain): This is the unique identifier of the website, such as google in ```www.google.com``` or example in ```www.example.org```  
- TLD (Top-Level Domain): This is the extension that follows the SLD, such as .com, .org, .net, or country-specific TLDs like .uk or .jp  


**Example:**

```java

Regex: "^([a-z]+):\\/\\/((?:[^\\s\\/.]+\\.)+(?:[^\\s\\/.]+))(\\/.*)?$"
Input: https://www.news.com/list?id=1
Expected result:
    Protocol: https
    Domain: www.news.com
    Path: /list

```

java code:

```java

// i.e. url = "https://www.news.com/list?id=1";
public static void parseURL(String url) {
    String regex = "^([a-z]+):\\/\\/((?:[^\\s\\/.]+\\.)+(?:[^\\s\\/.]+))(\\/.*)?$";
    Pattern pattern = Pattern.compile(regex,  Pattern.CASE_INSENSITIVE | Pattern.DOTALL);
    Matcher matcher = pattern.matcher(url);
    if(matcher.matches()){
        System.out.println("protocol: "+matcher.group(1)); // https
        System.out.println("domain: "+matcher.group(2)); // www.news,com
        System.out.println("path: "+matcher.group(3)!=null?matcher.group(3):"N/A"); // /list?id=1
    }else{
        System.out.println("Not valid URL.");
   }
}

```

**Explanation:**

If you are not familiar with regex, please refer to this regex cheat-sheet [here](https://github.com/mohanadwalo/cheat-sheets/blob/main/REGEX.md).

**```First group: (^[a-z]+)```** matches the URL protocol, stopping when it encounters the first:  

**```Second group: ((?:[^\\s\\/.]+\\.)+(?:[^\\s\\/.]+))```** matches the domain name, starting after :// and stopping before the beginning of any possible path (/). It matches 1 or more consequence of characters excluding space. It also contains two non capturing groups **(?:[^\\s\\/.]+\\.)** and **(?:[^\\s\\/.]+)** as we are interested in the parent group(domain part)

**```Third group: (\\/.*)?```** matches the optional path. Starts with / and matches one or more consequence of characters     

**Note:** The regex above will validate the structure of a URL, **but it won't verify whether the URL actually exists.**  

#### 4.1.2 Extract All URLs from Text

We can use the previous regex to search for all URLs in a text by utilizing the find method in combination with a while loop.

```java

// i.e. text = "Check out these websites: https://www.general.news.com and http://www.sport.news.com  Also visit www.example.org";
public static List<String> extractURLsFromText(String text) {
    String regex = "([a-z]+):\\/\\/((?:[^\\s\\/.]+\\.)+(?:[^\\s\\/.]+))(\\/.*)?";
    Pattern pattern = Pattern.compile(regex,  Pattern.CASE_INSENSITIVE | Pattern.DOTALL);
    Matcher matcher = pattern.matcher(text);
    List<String> urls = new ArrayList<>();
    while(matcher.find()){
        urls.add(matcher.group());
    }
    return urls;
}

```

**Explanation:**

The only difference from the previous case is that we need to remove ^ from the beginning of the regex and $ from the end, as the URL can appear anywhere within the text  

**Note:** The previous regex fails to capture the last URL ```www.example.org``` because it lacks the protocol (e.g., 'http://' or 'https://').  


#### 4.1.3 Use regex to Extract Query String Parameters from a URL

Previously, we discussed how to extract the path from a URL. Now, we will focus on writing a regex to extract the query string parameters.

```java

// i.e. queryString = "/path?id=1&b=2&c=qabc";
public static HashMap<String, String> parseQueryString(String queryString) {
    String regex = "[?&]([^=&]+)=([^&]*)";
    Pattern pattern = Pattern.compile(regex);
    Matcher matcher = pattern.matcher(queryString);
    var parametersMap = new HashMap<String, String>();
    while (matcher.find()) {
     parametersMap.put(matcher.group(1), matcher.group(2));
    }
    return parametersMap;
}
```

**Explanation:**

The pattern **[?&]** searches for the location of query string parameters in the URL  

Query string parameters can appear in two forms:  

**?param=value** if it is the first parameter in the query string  
**&param=value** if it is not the first parameter  

**([^=&]+)** captures the key, it matches anything except **=** (the separator between the key and value) or **&** (indicating the start of the next parameter)  

**=** serves as the separator between the key and the value 

**([^&]*)** captures the value, which can be anything except &, as this indicates the beginning of the next parameter key, if any  

In our example **/path?id=1&b=2&c=qabc**  
**First match** ?id=1 group1 -> id, group2 -> 1    
**Second match** &b=2 group1 -> b, group2 -> 2    
**Third match** &c=qabc group1 -> c, group2 -> qabc    

#### 4.1.4 Write a Regex to Extract URL Information (Protocol, Domain, Subdomain, Top-Level Domain, Port, Path, Query String)

Let's consider the URL: https://latest.news.com/category=sport  
The general structure(mentioned earlier) can be represented as:  

{protocol}://{subdomain}.{SLD}.{TLD}/path  

Protocol: anything before ://  
Domain: everything between :// and /  
Path: everything after /  

**However,** it becomes challenging when trying to extract more detailed information, such as:  

Subdomain: latest  
Second-Level Domain (SLD): news  
Top-Level Domain (TLD): com  

The challenge arises because the Top-Level Domain (TLD) can vary, and it is not always the last part of the domain preceded by a dot.  

**For example:**  

URL: www.example.co.uk  
TLD: co.uk (it is not just the last part preceded by a dot)  
SLD: example  
Subdomain: www  

**Fortunately,** we can use the [Public Suffix List](https://publicsuffix.org/) to identify the location of the top-level domain (TLD). Once the TLD is determined, extracting the second-level domain (SLD) and subdomain becomes straightforward. A subdomain can consist of multiple levels, separated by dots and SLD is the level placed immediately before TLD level.  

**The Public Suffix List is a comprehensive list of domain suffixes (such as .com, .co.uk, and .github.io) under which internet users can directly register domain names.**  

**For example:** a.b.c.co.uk

Subdomain: a.b  
SLD: c  
TLD: co.uk  
Path: no path  

In order to extract all the details of the URL, we need to follow the following steps:

**1. Obtain the Public Suffix List:**

Start by downloading the Public Suffix List and storing it in a map. You can retrieve the list directly from the Public Suffix List website.

**2. Understanding the List:**

The file contains two primary types of patterns:

**Wildcard Rule:**  

For example, *.ck This means any word (excluding periods) that immediately precedes the pattern ".ck" will be treated as a public suffix and cannot be registered as a regular domain by end users. Since domain names are composed of multiple blocks separated by periods, the wildcard rule only restricts the immediate word before the TLD. However, further levels like a.abc.ck are still possible to be registered by end users.

**Exception Rule (!):**

This indicates an exception to the wildcard rule. For example, ```!www.ck``` is a valid domain ```www.ck``` that can be registered by end users, even though abc.ck is not available.

Now it's time to write java code  

The code below has comments to explain how it works  

```java

package com.validation;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.URI;
import java.util.Arrays;
import java.util.HashMap;
import java.util.HashSet;
import java.util.Set;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class EmailValidator {
    
    private final static Set<String> publicSuffixes = new HashSet<>();
    private final static Set<String> exceptions = new HashSet<>();

    public static URLInfo validateURL(String url) {
        URLInfo urlInfo = new URLInfo();
        try {
            // load the public suffix list
            EmailValidator.loadPublicSuffixList("https://publicsuffix.org/list/public_suffix_list.dat");
            
            // (^[a-z]+):\\/\\/ -> captures  the protocol
            // ( -> captures the domain section
            //   ((?:(?<!-)[p{L}\\w&&[^/.]]{1,63}(?!-)\\.)+) -> captures subdomain and SLD
            //   (?:(?<!-)[p{L}\\w&&[^/.]]{1,63}(?!-)) -> TLD part
            // ) -> end of domain section

            // (:[\\d]{1,5})? -> captures the port
            // (\\/[^?]+)? -> captures the path
            // (\\?.*)?$ -> captures the query string
            String regex = "(^[a-z]+):\\/\\/(((?:(?<!-)[p{L}\\w&&[^/.]]{1,63}(?!-)\\.)+)(?:(?<!-)[p{L}\\w&&[^/.]]{1,63}(?!-)))(:[\\d]{1,5})?(\\/[^?]+)?(\\?.*)?$";
            Pattern pattern = Pattern.compile(regex, Pattern.DOTALL);
            Matcher matcher = pattern.matcher(url);
            if (matcher.matches()) {
                String domain = matcher.group(2);
                extractDomainParts(domain, urlInfo);
                String port = matcher.group(3);
                String queryString = matcher.group(5);
                urlInfo.protocol= matcher.group(1);
                urlInfo.port= port;
                urlInfo.path= matcher.group(4) != null ? matcher.group(4) : "";
                urlInfo.queryParameters= queryString != null ? parseQueryString(queryString) : null;
                System.out.println("The URL was parsed successfully");
                urlInfo.state = URLParseState.OK;
                return urlInfo;
            } else {
                tryParseIPOrLocalhost(url, urlInfo);
               if(urlInfo.ipAddress == null){
                urlInfo.state = URLParseState.FAIL;
                 System.out.println("Not valid URL.");
               }else{
                System.out.println("The URL was parsed successfully");
                urlInfo.state = URLParseState.OK;
                return urlInfo;
               }
            }
        } catch (IOException e) {
            urlInfo.state = URLParseState.FAIL;
            System.out.println(e);
        }
        return urlInfo;
    }
    // Explained in 4.1.3 use case
    private static HashMap<String, String> parseQueryString(String queryString) {
        String regex = "[?&]([^=&]+)=([^&]*)";
        Pattern pattern = Pattern.compile(regex);
        Matcher matcher = pattern.matcher(queryString);
        var parametersMap = new HashMap<String, String>();
        while (matcher.find()) {
            parametersMap.put(matcher.group(1), matcher.group(2));
        }
        return parametersMap;
    }

    private static void tryParseIPOrLocalhost(String domain,URLInfo urlInfo) {
        // Matches {http or any protocol}://{IPv4 or IPv6 or localhost}
        // We need to handle different variations of IPv6
        // -> omitting leading zeros  2001:0db8:0000:0042:0000:0000:0000:0001 -> 2001:db8:0:42:0:0:0:1 
        // -> using double colons (::) for consecutive zero blocks -> 2001:db8:0:0:0:0:0:1 -> 2001:db8::1
        String regex = "(^[a-z]+):\\/\\/((?:localhost)|" +
                "(?:\\d{1,3}\\.\\d{1,3}\\.\\d{1,3}\\.\\d{1,3})|" +
                "(?:[0-9A-Fa-f]{1,4}:){7}(?:[0-9A-Fa-f]{1,4})|" + // full
                "(?:(?:[0-9A-Fa-f]{1,4}:){1,7}:)|" + // compress at th end -.-.-.-.-.-.-.here
                "(?:(?:[0-9A-Fa-f]{1,4}:){1,6}:[0-9A-Fa-f]{1,4})|" + // compress before the end -.-.-.-.-.-.here.-
                "(?:(?:[0-9A-Fa-f]{1,4}:){1,5}(?::[0-9A-Fa-f]{1,4}){1,2})|" +// compress at -.-.-.-.-.here.-.-
                "(?:(?:[0-9A-Fa-f]{1,4}:){1,4}(?::[0-9A-Fa-f]{1,4}){1,3})|" +// compress at -.-.-.-.here.-.-.-
                "(?:(?:[0-9A-Fa-f]{1,4}:){1,3}(?::[0-9A-Fa-f]{1,4}){1,4})|" +// compress at -.-.-.here.-.-.-.-
                "(?:(?:[0-9A-Fa-f]{1,4}:){1,2}(?::[0-9A-Fa-f]{1,4}){1,5})|" +// compress at -.-.here.-.-.-.-.-
                "(?:[0-9A-Fa-f]{1,4}:(?:(?::[0-9A-Fa-f]{1,4}){1,6}))|" +     // compress at -.here.-.-.-.-.-.-
                "(?::(?:(?::[0-9A-Fa-f]{1,4}){1,7}|:)))" + // //compress at here.-.-.-.-.-.-.-
                "(:(\\d+))?$";// port number (optional)
        Pattern pattern = Pattern.compile(regex);
        Matcher matcher = pattern.matcher(domain);

        if (matcher.matches()) {
            urlInfo.protocol= matcher.group(1);
            urlInfo.ipAddress= matcher.group(2);
            urlInfo.port= matcher.group(3);
        }

    }

     private static void extractDomainParts(String domain, URLInfo urlInfo) {
        // Split the domain by period www.example.uk.com -> ["www", "example", "uk","com"]
        String[] parts = domain.split("\\.");
        // We skip the first item because we are looking for the TLD and it should't be the first item
        // The first item might be a subdomain or SLD
        for (int i = 1; i < parts.length; i++) {
            // First iteration i = 1,  possibleTLD = example.uk.com
            // Second iteration i = 2,   possibleTLD = uk.com
            String possibleTLD = String.join(".", Arrays.copyOfRange(parts, i, parts.length));
            // Check if possibleTLD in the publicSuffixes or it is an exception (! rule)
            // Rembember that we stored exceptions without ! (loadPublicSuffixList function below)
            // The second iteration i=2, possibleTLD = uk.com, it is in the public suffix list
            if (exceptions.contains(possibleTLD) || publicSuffixes.contains(possibleTLD)) {
                // From i to the end of parts -> TLD
                // i-1 -> SLD
                // 0 to i-2(inclusive) -> subdomain
                // Check if there is a subdomain because it is optional
                if (i - 2 >= 0) {
                    urlInfo.subdomain = String.join(".", Arrays.copyOfRange(parts, 0, i - 1));
                }
                urlInfo.SLD = parts[i - 1];
                urlInfo.TLD = possibleTLD;
            }
            // cCheck if possibleTLD is a wildcard rule
            // i.e. !.ck (a.b.c.ck) If we use this example, there will be a wildcard rule at iteration i = 3
            String wildcardSuffix = "*." + possibleTLD;
            if (publicSuffixes.contains(wildcardSuffix)) {
                // The wildcard rule says that the part before possibleTLD also considered to be a part of TLD
                // i.e. a.b.c.ck 
                // *.ck is a wildcard rule, refer to the public suffixes list at https://publicsuffix.org/list/public_suffix_list.dat
                // so the TLD is c.ck
                // Check if there is a subdomain because it is optional
                if (i - 3 >= 0) {
                    urlInfo.subdomain = String.join(".", Arrays.copyOfRange(parts, 0, i - 2));
                }
                urlInfo.SLD = parts[i - 2];
                urlInfo.TLD =  String.join(".", Arrays.copyOfRange(parts, i-1, parts.length));
            }
        }
    }

    // Load the public suffix list from a file
    private static void loadPublicSuffixList(String fileURL) throws IOException {
        if(publicSuffixes.size() == 0){
            try  {
                var url = new URI(fileURL).toURL();
                BufferedReader reader = new BufferedReader(new InputStreamReader(url.openStream()));
                String line;
                while ((line = reader.readLine()) != null) {
                    line = line.trim();
                    // Skip empty lines and comments
                    if (line.isEmpty() || line.startsWith("//")) {
                        continue;
                    }
                    if (line.startsWith("!")) {
                        // Add exceptions without the "!"
                        exceptions.add(line.substring(1));
                    } else {
                        // Add regular public suffixes or wildcard rule
                        publicSuffixes.add(line); 
                    }
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }

    public enum URLParseState {
        OK, FAIL
    }

    public static class URLInfo {
        public  String ipAddress;
        public  String protocol;
        public  String subdomain;
        public  String SLD;
        public  String port;
        public  String TLD;
        public  String path;
        public  URLParseState state;
        public  HashMap<String, String> queryParameters;

        @Override
        public String toString() {
            return "URLInfo {" +
                   "ipAddress='" + ipAddress + '\'' +
                   ", protocol='" + protocol + '\'' +
                   ", subdomain='" + subdomain + '\'' +
                   ", SLD='" + SLD + '\'' +
                   ", port='" + port + '\'' +
                   ", TLD='" + TLD + '\'' +
                   ", path='" + path + '\'' +
                   ", queryParameters=" + queryParameters +
                   '}';
        }
    }
}
// main package
package com.main;


import com.validation.EmailValidator;
import com.validation.EmailValidator.URLInfo;
import com.validation.EmailValidator.URLParseState;

public class Main {

    public static void main(String[] args) {
        String url = "http://2001:0db8:0000:0000::0000:3000";
        URLInfo urlInfo =  EmailValidator.validateURL(url);
        if(urlInfo.state == URLParseState.OK){
          System.out.println(urlInfo);
        }
     }
}
   
```



#### 4.2 String use cases

#### 4.2.1. Remove a collection of words from text

```java

public static String removeCollectionOfWords(String text, String[] words) {
    // We will use regex group (hello|word)
    // \\b makes sure to match a full word, 
    String regex = "\\b(" + String.join("|", words) + ")\\b[\\p{Punct}]?";
    Pattern pattern = Pattern.compile(regex, Pattern.CASE_INSENSITIVE);
    return pattern.matcher(text).replaceAll("");
}

String text = "Please don't forget to come very early tomorrow morning.";
String[] words = {"very", "morning"};
removeCollectionOfWords(text, words);// Please don't forget to come  early tomorrow 

```

**Explanation:**

**```\\b```**  Makes sure to match a full word i.e. work doesn't match work in homework   
**```[\\p{Punct}]?```** Captures punctuation mark  

**Note:** There will be extra spaces, the following use case will handle and remove extra spaces in a text.



#### 4.2.2. Remove spaces at the beginning and at the end of a string and all more than one space between words

```java

public static String removeExtraSpacesFromString(String text) {
    String regex = "(^[\\s]+|\\s+$|((?<!\\s)\\s+(?=\\s)))";
    Pattern pattern = Pattern.compile(regex, Pattern.MULTILINE);
    return pattern.matcher(text).replaceAll("");
}

String text = "   This   is  a    sample text     spaces.   ";
removeExtraSpacesFromString(text); // This is a sample text spaces.

```

**Explanation:**

**```^[\\s]+```** Checks if the string starts with one or more spaces.  
**```\\s+$```** Checks if the string ends with one or more spaces.  
**```((?<!\\s)\\s+(?=\\s))```** Spaces in the middle, checks for consecutive spaces that are preceded and followed by non-space characters.    

#### 4.2.3. Remove a sentence from a text

```java

public static String removeSentenceFromText(String text, String sentenceToMatch) {
    // Search for a sentence to match
    // Deal with extra spaces between words
    // Handles whether a sentence spans multiple lines
    String regex = "\\s*\\b" + String.join("\\b\\s*\\b", sentenceToMatch.split("\\s+")) + "\\b\\s*";
    Pattern pattern = Pattern.compile(regex, Pattern.MULTILINE | Pattern.CASE_INSENSITIVE);
    Matcher matcher = pattern.matcher(text);
    return matcher.replaceAll(" ");
}

String text = "Hello, you are\n" +
           "kind you are strong";
String sentenceToMatch = "You are kind";
removeSentenceFromText(text, sentenceToMatch); // Hello, you are strong
```

**Explanation:**

**```\\s```** Captures extra spaces before the sentence or after it.  
**```\\b```** Validate the boundary of a sentence.  
**```\\s+```** Split the sentence by a one or more spaces between words.   
**Pattern.MULTILINE** The sentence might span to multiple lines.  


#### 4.2.4 Scan the text and add a new line after any period if there is not already a new line and capitalize the first word of the next sentence if there is one.

```java

 public static String capitalizeAndNewLine(String text) {
        // Search for 0 or more spaces, followed by a period, 
        // followed by 0 or more spaces, followed by a word.
        // or 
        // followed by a new line
        // followed by small letter
        String regex = "\\s*\\.\\s*([^\\s])";
        Pattern pattern = Pattern.compile(regex, Pattern.MULTILINE);
        Matcher matcher = pattern.matcher(text);
        StringBuffer result = new StringBuffer();
        while (matcher.find()) {
            // capture the first letter using the second group
            String firstLetter = matcher.group(1).toUpperCase();
            matcher.appendReplacement(result, ".\n" + firstLetter);
        }
        matcher.appendTail(result);
        return result.toString();
    }

String text = "I slept peacefully all night.however, it woke up early in the morning.";
capitalizeAndNewLine(text); /* I slept peacefully all night. 
                               However, it woke up early in the morning.*/

```

**Explanation:**

We used appendReplacement method with string buffer to update the text.  
appendReplacement appends all the text before the first match or the current match and the previous match to the buffer.  In addition, it updates the current match and append it to the buffer  
i.e.  input text: hello world, nice to meet you  
      pattern to match: world -> team  
      The first match is world so it appends every thing before it to the buffer  
      -> buffer: hello  
      -> replace world by team and append it to the buffer  
      -> buffer: hello team  
There are no more matches so appendTail will append the remaining text to the buffer -> buffer: hello team, nice to meet you   

**```\\s*\\.\\s*([^\\s])```** First pattern Capture zero or more spaces, period, zero or more spaces and then any character i.e. some text  .some text -> some text  .  Some text  
**Pattern.MULTILINE** The sentence might span to multiple lines.


#### 4.3 Number use cases

#### 4.3.1 Format a numric date
  
Some systems store date as a number yearmonthday
- 20220105,

Comments included in the code  

```java
   public static void formatDate(String numricDate) {
        // We assume the date always has 8 digits and it is ordered as year, month and day
        String regex = "(\\d{4})(\\d{2})(\\d{2})";
        Pattern pattern = Pattern.compile(regex);
        Matcher matcher = pattern.matcher(numricDate);
        // we can use $ to refer to groups in the regex
        // $1=first group (\\d{4})  in the regex and so on
        String result = matcher.replaceAll("$1-$2-$3");
        System.out.println(result);
    }
    formatDate("20241213");
```

#### 4.3.2 Format a number

We would like to make large numbers more readable by adding commas in the right place  

If the number of digits is a multiple of 3 (e.g., 6, 9, or 12), all we need to separate every 3 digits with commas.  
However, if the digit count is not a multiple of 3, we'll end up with 1 or 2 extra digits at the end,   
which results in incorrect formatting (e.g., 123,54 or 152,1).  
To correct this, we can reverse the number, add commas, and then reverse it back.  

**For example**: 12354 -> reverse -> 45321 -> add commas -> 453,21 -> reverse back -> 12,354  

```java

    public static void formatNumber(String num) {
        // We check if the number has decimal places
        // If it has then numberParts[1] contains the decimal places
        String[] numberParts = num.split("\\.");
        // Look a head to see if every 3 digit set is followed by a digit 
        // i.e. 1234567.9 -> 123 3 digits set followed by the digit 4
        String regex = "(\\d{3})(?=\\d)";
        Pattern pattern = Pattern.compile(regex);
        // There is a decimal places
        if (numberParts.length == 2) {
            // Reverse and execute regex
            // i.e 1234567.911 -> split -> numberParts = [1234567, 911] -> reverse numberParts[0] -> 7654321 -> execute -> 
            // First match (765)(4)
            // Second match (432)(1)
            Matcher matcher = pattern.matcher(reverseString(numberParts[0]));
            // Replace group 1 by group 1 with comma attached
            // First match (765)(4) -> (765,)(4)
            // Second match (432)(1) -> (432,)(1)
            // Result 765,432,1
            String result = matcher.replaceAll("$1,");
            // Reverse back 1,234,567
            // Format decimals
            // Result 1,234,567.911
            String out = reverseString(result) + "." + formatDecimals(numberParts[1], 3);
            return out;
        } else {
            // i.e 1234567 -> reverse -> 7654321 -> execute -> 
            // First match (765)(4)
            // Second match (432)(1)
            Matcher matcher = pattern.matcher(reverseString(num));
            // Replace group 1 by group 1 with comma attached
            // First match (765)(4) -> (765,)(4)
            // Second match (432)(1) -> (432,)(1)
            // Result 765,432,1
            String result = matcher
            String result = matcher.replaceAll("$1,");
            // Reverse back 1,234,567
            return reverseString(result);
        }
    }

    public static String reverseString(String str) {
        return new StringBuilder(str).reverse().toString();
    }
   
    public static String formatDecimals(String decimalPart, int decimalPlaces) {
        // Remove extra digits 
        // i.e decimalPart = 91125 and  decimalPlaces = 3 -> return 911
        if (decimalPart.length() > decimalPlaces) {
            decimalPart = decimalPart.substring(0, decimalPlaces);
        } else {
            // If no enough digits add zeros
            // i.e decimalPart = 9 and  decimalPlaces = 3 -> return 900
            while (decimalPart.length() < decimalPlaces) {
                decimalPart += "0";
            }
        }
        return decimalPart;
    }
    formatNumber("20241213.23566");

```

#### 4.4 Form validation use cases

#### 4.4.1 Validate allowed chars, max length, min length

In the example below, only Latin letters, underscores, and numbers are accepted. Additionally, the input length must be at least 1 and not more than 4 characters.

```markdown

^[\w]{1,4}

```

#### 4.4.2 Validate number(decimal places)

In the example below, only numbers (1 or more) are accepted. Additionally, decimal input is accepted but is optional.

```markdown

^\d+(\.\d+)?

```


#### 4.4.3 Validate email

```markdown

^([a-zA-Z0-9]+\.?)+[a-zA-Z0-9]+@([a-zA-Z0-9]+[.-]*)+[a-zA-Z0-9]+\.[a-zA-Z]{2,63}$

```

**Explanation**

**```^([a-zA-Z0-]+\.?)+[a-zA-Z0-9]+```** This part of the regex will match the part before @ (local part), it starts with Latin letters, digits, accepts period in the middle and ends with Latin letters and digits
**```([a-zA-Z0-9]+[.-]*)+[a-zA-Z0-9]+```** This part of the regex will match the email main domain such as gmail in ```abc@gmail.com```  
**```\.[a-zA-Z]{2,63}$```** This part of the regex will match a top-level domain (TLD) such as .com in ```abc@gmail.com```, and must be at least 2 characters long and no longer than 63 characters.

#### 4.5 HTML text use cases

#### 4.5.1 Extract data list from HTML text

Below we have HTML text that displays a list of news and our task to extract the news list.

We need to utilize HTML attributes to construct our regex. It’s straightforward: we need to identify the parent HTML tag. In our case, 
It's the ```<div class="news-item">...</div>``` element. In addition we need to know how the news list laid out on the html page.

```java
   public static void extractDataFromHTML() {
        String htmlTemplate = """
                <!DOCTYPE html>
                <html lang="en">
                <head>
                    <meta charset="UTF-8">
                    <meta name="viewport" content="width=device-width, initial-scale=1.0">
                    <title>News List</title>
                </head>
                <body>
                    <header>
                        <h1>Latest News</h1>
                    </header>
                    <div class="news-list">
                        <div class="news-item">
                            <h2>News Title 1</h2>
                            <p>Lorem ipsum dolor sit amet</p>
                            <div class="publish-info">
                                 <span class="publisher">publisher name</span>
                                 <span class="date">2-2-2024</span>
                            </div>
                        </div>
                         <div class="news-item">
                            <h2>News Title 1</h2>
                            <p>Lorem ipsum dolor sit amet</p>
                            <div class="publish-info">
                                 <span class="publisher">publisher name</span>
                                 <span class="date">2-2-2024</span>
                            </div>
                        </div>
                         <div class="news-item">
                            <h2>News Title 1</h2>
                            <p>Lorem ipsum dolor sit amet</p>
                            <div class="publish-info">
                                 <span class="publisher">publisher name</span>
                                 <span class="date">2-2-2024</span>
                            </div>
                        </div>
                    </div>
                </body>
                </html>
                """;

        String regex = "<div class=\"news-item\">\\s*" +
                "<h2[^>]*>(.*?)</h2>\\s*" +
                "<p[^>]*>(.*?)</p>\\s*" +
                "<div[^>]*>\\s*" +
                "<span[^>]*>(.*?)</span>\\s*" +
                "<span[^>]*>(.*?)</span>\\s*" +
                "</div>\\s*" +
                "</div>";
        Pattern pattern = Pattern.compile(regex, Pattern.DOTALL);
        Matcher matcher = pattern.matcher(htmlTemplate);
        while (matcher.find()) {
            System.out.println("Title: " + matcher.group(1));
            System.out.println("Content: " + matcher.group(2));
            System.out.println("Publisher: " + matcher.group(3));
            System.out.println("Date: " + matcher.group(4));
            System.out.println("======================");
        }
    }

```

**Explanation**

**```\\s*```** matches any number of spaces  
**```[^>]*```** matches all html tag attributes. It stops when there is tag close ```>```  
**First group (.*?)** matches the title text between ```<h2>the text here</h2>```  
**Second group (.*?)** matches the content text between ```<p>the text here</p>```  
**Third group (.*?)** matches the publisher text between ```<span>the text here</span>```  
**Forth group (.*?)** matches the date text between ```<span>the text here</span>```  


#### 4.6. Log file use cases

#### 4.6.1 Search and get all rows which have a specific keywords

We have the below sample log file  

sample.log

```
2024-07-28 10:15:32 INFO  [main] com.example.Main - Application started
2024-07-28 10:15:33 DEBUG [main] com.example.Main - Initializing components
2024-07-28 10:15:34 INFO  [main] com.example.Service - Service started
2024-07-28 10:15:35 WARN  [main] com.example.Service - Configuration file is missing, using defaults
2024-07-28 10:15:36 ERROR [main] com.example.Service - Failed to start service due to missing dependency
2024-07-28 10:15:37 INFO  [main] com.example.Controller - Controller initialized
2024-07-28 10:15:38 DEBUG [main] com.example.Controller - Handling request for /api/data
2024-07-28 10:15:39 INFO  [main] com.example.Database - Connection established to database
2024-07-28 10:15:40 WARN  [main] com.example.Database - Query execution took longer than expected
2024-07-28 10:15:41 ERROR [main] com.example.Database - Failed to execute query: syntax error at or near "FROM"
2024-07-28 10:15:42 INFO  [main] com.example.UserService - User service started
2024-07-28 10:15:43 DEBUG [main] com.example.UserService - Fetching user details for userId=12345
2024-07-28 10:15:44 WARN  [main] com.example.UserService - User with userId=12345 not found
2024-07-28 10:15:45 ERROR [main] com.example.UserService - Error occurred while processing user request: userId=12345
2024-07-28 10:15:46 INFO  [main] com.example.Main - Application shutdown initiated
2024-07-28 10:15:47 DEBUG [main] com.example.Main - Releasing resources
2024-07-28 10:15:48 INFO  [main] com.example.Main - Application stopped
```

A function to read the file content  

```java
    public static List<String> readAndFilterLogFile(String filePath, String regex) throws IOException {
        List<String> matchingLines = new ArrayList<>();
        Pattern pattern = Pattern.compile(regex, Pattern.CASE_INSENSITIVE);

        try (InputStream inputStream = Main.class.getClassLoader().getResourceAsStream(filePath);
             BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream))) {

            if (inputStream == null) {
                throw new IOException("File not found: " + filePath);
            }

            String line;
            while ((line = reader.readLine()) != null) {
                Matcher matcher = pattern.matcher(line);
                if (matcher.matches()) {
                    matchingLines.add(line);
                }
            }
        }

        return matchingLines;
    }

    // Return all rows that contain a given keyword
    // i.e. return all rows that contain "start application"
    public static void logSearch(String keyword) {
        String filePath = "sample.log";
        String regex = ".*"+keyword+".*";
        try {
            List<String> matchingLines = readAndFilterLogFile(filePath, regex);
            for (String line : matchingLines) {
                System.out.println(line);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    logSearch("Application started");
```

**Explanation**

We read the file and search for all the lines that contain the string keyword

#### 5. Project

The project is optional and we will skip it because our use cases are practical enough

#### 6. Improvements & Suggestions

- Study the algorithm behind regex
- Add more use cases
- Implement  simple regex engine
- Make it faster
- Validate against xss


#### See also

Effective Learning Method [here](https://github.com/mohanadwalo/effective-learning-methods)  
Cheat sheets [here](https://github.com/mohanadwalo/cheat-sheets)  
Learn java Through Practical Use Cases [here](https://github.com/mohanadwalo/java-use-cases)  
Learning a new language effectively [here](https://github.com/mohanadwalo/learning-a-new-language)  

