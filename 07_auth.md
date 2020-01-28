
# authentication and authorization

## authentication

Authentication is the process of determining whether someone is really who they claim to be. This protection mechanism ensures that contents are accessed only if the user has the right to do so.

Most web applications use username/email or password to authenticate users.

* Authentication is the process of verifying *who you are*
* Authorization is *what you are able to do*

## 3 types of authentication factors

* ownership factors
* knowledge factors
* inheritance factors

## ownership factor

* something user has
* eg. token, phone.

## knowledge factors

* something the user knows
* eg. password, PIN, secret

## inheritance

* something the user is.
* e.g. fingerprint, signature.

==============================================

## Single-factor authentication

* username/email + password

## two-factor or multi-factor authentication MFA

* something you know: the PIN
* something you have: the credit/ATM card

Websites increasingly employ 2 factor authentication typically with SMS messages via mobile phones.

==============================================

# Exploiting authentication flaws in single factor auth.

* vulnerable to man-in-the-middle data interception
* passwords can be guessed using brute force & dictionary attacks

## dictionary attack

Typically involves using a pre-pepared wordlist of guesses that will be tried with the hope the one of the guesses is correct.

:: common wordlists

* http://www.openwall.com/wordlists/
* https://github.com/danielmiessler/SecLists
* https://wiki.skullsecurity.org/Passwords


## password complexity and entropy

Depending on the complexity of the password the entropy increases accordingly.

## common strong password policies

* at least 10 characters in length
* never use the same password twice
* at least one uppercase char
* at least one lowercase char
* at least one digit
* special characters
* do not includes personal information
* change password regularly

## Safe password storage

Servers must never store passwords in plain text. Typically servers store the hash (preferably with a salt) of the password instead.

## lockout/blocking requests

* add an increasing delay after each failed login attempt
* after 3 failed attempts show a CAPTCHA puzzle
* after 10 failed login attempts lock the user out for a period of type

=================================================

## User enumeration

When providing an email, username and password to a login form desciptive error messages can be used to enumerate (guess) other usernames or emails.

The correct message should be...

```

LOGIN FAILED: the password OR the username was incorrect.

```

The error does not give you any clues as to whether your username or password was incorrect. If the error message says... your password was incorrect you know that particular user exists.

## User enumeration via website behaviour

Instead of an error message some other behaviour may occur depending on whether the user exists or not. For example...

```
user does not exist             |         User exists

cookies are deleted                       Cookies are not deleted or new cookie set
user goes to known fixed page             user goes to user-specific page
the HTML is fixed                         the HTML changes or is different

```

## User enumeration via timing attacks

It may be possible to enumerate the users by comparing the timings of...

* (quick) user does not exist in DB: shows error + aborts;
* (slow) user does exist... retrieve user, check hashes etc.

## Performing user enumeration with tools.

* burp suite [see hacking-handbook]

=====================================================

## Easily guessable credentials

You should always try default and easy usernames and passwords.

* identify all components that require credentials
* search for default password against each component

* guest, temporary or developer accounts

## Example easily guessable credentials

* administrator
* admin
* root
* guest
* system
* test
* <blank>
* password
* pass123
* guest
* adminpassword
* 1234

## Abusing remember me functionality

Cookies are sometimes used to allow users to log back in easily without having to keep entering the password. If the remember me cookie can be intercepted via XSS for example. This would give the attacker access to the account.

## Abusing unlimited attempts

Sometimes questions are used if you cannot remember your password... for example... what was your mothers maiden name?

If these mechanisms do not block after a certain number of attempts they may be vulnerable to brute force or dictionary attacks.

## Abusing password reset functionality

Password reset tokens should use a wide character set and be long and random.

* guessable reset link
* predictable tokens
* recycling

A recyclable link can be used more than once, if the link is discovered it can be used to reset the password to one that attacker decides.

## Logout weakness

If the application fails to clear the session on logout an attacker may be able to impersonate the account. This vulnerability is called *incorrect session destruction*.

## Insecure Direct Object References

* https://www.owasp.org/index.php/Top_10_2013-A4-Insecure_Direct_Object_References

This is a vulnerability where the application fails to isolate references so that unauthorized access of resources can occur using enumeration techniques.

For example you have access your thing...

```
http://resources.net/mine/thing.php?id=1
```
If by changing the id parameter you are able to access resources that are other users... that would be deemed an insecure direct object reference.

The remediation for this is ensure access controls or authentication of all protected content.

## References

* https://support.google.com/accounts/answer/180744?hl=en
* https://github.com/danielmiessler/SecLists
* http://web.archive.org/web/20150328012647/https:/www.whitehatsec.com/bAdmin/bAdmin.html
* https://www.cirt.net/passwords
* http://www.openwall.com/wordlists/
* https://wiki.skullsecurity.org/Passwords
* http://www.phenoelit.org/dpl/dpl.html
* http://www.cvedetails.com/cve/CVE-2014-9283/
* http://www.cvedetails.com/vulnerability-list/vendor_id-2337/product_id-12975/year-2008/opxss-1/Wordpress-Captcha.html
* http://cintruder.sourceforge.net/
* http://www.boyter.org/decoding-captchas/
* http://boingboing.net/2012/01/09/virtual-sweatshops-versus-capt.html
* http://www.exploit-db.com/exploits/17728/
* http://www.debasish.in/2012/01/bypass-captcha-using-python-and.html
* https://www.owasp.org/index.php/Testing_for_Captcha
* http://www.npr.org/templates/story/story.php?storyId=130594039
* http://krebsonsecurity.com/2012/01/virtual-sweatshops-defeat-bot-or-not-tests/
* https://www.owasp.org/index.php/Top_10_2013-A4-Insecure_Direct_Object_References
