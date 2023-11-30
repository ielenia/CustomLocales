# en_US-ISO

I created a custom locale so that I had a custom date and time format (based on [RFC 3339](https://datatracker.ietf.org/doc/html/rfc3339)). Thanks to [Severo Raz](https://askubuntu.com/users/567/severo-raz) on AskUbuntu who answered [this question about customizing locales](https://askubuntu.com/questions/21316/how-can-i-customize-a-system-locale). I created it on Ubuntu-MATE, but the majority of the instructions should work on any distribution. The only part that is specific to my distribution is selecting it, but since I don't currently have anything else, you'll probably have to figure it out yourself (or take a look at the AskUbuntu thread that I linked....).

## Step 1: Creating a Template

While it is certainly possible to create a locale entirely from scratch, for many cases it is easier to simply modify an existing locale. Locales are located here:

    /usr/share/i18n/locales/

You can make a copy of the locale to edit by running:

    cp /usr/share/i18n/locales/en_US ~/

replacing en_US with your intended base locale and ~/ with your intended editing workspace. You can put it anywhere, and you'll only end up with one file, so it won't clog up your home directory if you put it there. Typically, variants of locales are identified by @ followed by some characters. In my case I used ISO since the dates are formatted like ISO 8601.

    mv en_US en_US@ISO

## Step 2: Editing the Locale

The locale files are written in plaintext, so any text editor can edit them. I used nano, but you're more than welcome to use anything else, like Vim or Emacs.

    nano en_US@ISO

The file is quite large, reaching 176 lines for en_US. You can see information about all of the sections at this guide, but I'll go over some of the groups that you'll most likely want to change.

### LC_TIME

This is the longest section, containing information about date and time strings (i.e. month and day names) and formats (e.g. the long date format). The lines that I wanted to change are d_t_fmt, d_fmt, t_fmt, t_fmt_ampm, and date_fmt. These control various aspects of how dates and times are displayed. The formats use strfmt variables, which are a percent sign followed by some alphanumeric character. A guide on what is valid is available here. Looking at my modification as an example, let's break down d_t_fmt:

    % Appropriate date and time representation (%c)
    d_t_fmt "%a %x %X %Z"

- %a is the abbreviated weekday name, e.g. Wed
- %x is the date format, which is specified later in the locale file
- %X is the time format, which is also specified elsewhere in the locale file
- %Z is the time zone name (in my case PST)

As you can see, the date-time format can depend on the date and time formats, so let's take a look at those too:

```
% Appropriate date representation (%x)
d_fmt   "%Y-%m-%d"
%
% Appropriate time representation (%X)
t_fmt   "%H:%M:%S"
```

- %Y is the four-digit year
- %m is the two-digit month
- %d is the two-digit day
- %H is the hour in 24-hour time
- %M is the two-digit minute
- %S is the two-digit second

You can insert literal characters into your formats, like the dashes and colons in the date and time formats.

### LC_PAPER

If you'd prefer to use A4 paper instead of letter paper, you can change the default paper size here. width and height are both in mm.


### LC_MEASUREMENT

Changing measurement to 1 will set the default units to metric instead of US Customary.

Once you've made all of the changes you want, save and close your locale (but don't close your terminal yet!)

## Step 3: Installing the Locale

Compile your locale by running this command:

    sudo localedef -i en_US@ISO -f UTF-8 en_US@ISO -c -v

replacing en_US@ISO with your locale's name.


Copy your locale to the locales directory - you will need to use sudo:

    sudo cp en_US@ISO /usr/share/i18n/locales

again, replacing en_US@ISO with your locale.


Add your locale to the list of supported locales:

    sudo nano /var/lib/locales/supported.d/en

and append your locale name followed by "UTF-8" - in my case, the last line would look like this:

    en_US@ISO UTF-8


Run the following command:

    sudo locale-gen

Now your locale is ready to use!

## Step 4: Using the Locale

Exactly how to set your locale will probably depend on your specific distribution. I'm using [Ubuntu-MATE 21.10](https://ubuntu-mate.org/), so I can find the language settings in Menu > Preferences > Language Support. Under Regional Formats, I can select my custom locale as the default under "Display numbers, dates, and currency amounts in the usual format for:" and selecting English (United States) - ISO. I can click Apply System-Wide to make it the default for everywhere, and not just for my user.

Logout and log back in, and hopefully your locale should be working! You can check by running the locale command, and the output should look something like this:

```
joshua@ubuntu-MATE:~$ locale
LANG=en_US.UTF-8
LANGUAGE=en_US
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC=en_US.UTF-8@ISO
LC_TIME=en_US.UTF-8@ISO
LC_COLLATE="en_US.UTF-8"
LC_MONETARY=en_US.UTF-8@ISO
LC_MESSAGES="en_US.UTF-8"
LC_PAPER=en_US.UTF-8@ISO
LC_NAME=en_US.UTF-8@ISO
LC_ADDRESS=en_US.UTF-8@ISO
LC_TELEPHONE=en_US.UTF-8@ISO
LC_MEASUREMENT=en_US.UTF-8@ISO
LC_IDENTIFICATION=en_US.UTF-8@ISO
LC_ALL=
```

