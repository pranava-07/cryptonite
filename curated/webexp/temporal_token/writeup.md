# WEB EXPLOITATION
# TEMPORAL TOKEN
## CHALLENGE
we are given a website and login credentials. it shows expired token.
## FLAG
`nite{50_y0u_kn0w_h0w_jw75_w0rk_n0w?}`
## MY SOLVE
1. i enter the given credentials.
2. it shows expired cookie
3. ![alt text](image.png)
4. so i fire up burp and check the post request
5. from it i get following data 
    1. Cookie: token=`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJCZWxlZ2FyIiwiZXhwIjowfQ.L4UuI63oMzkmh4clZMZPoANwNxnjiYTtZO90IBhQ8B8 `
    2. `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9` is base64 encoded. decoding that gives `{"alg":"HS256","typ":"JWT"}`
    3. `eyJzdWIiOiJCZWxlZ2FyIiwiZXhwIjowfQ` is also base64 and decoding that provides `{"sub":"Belegar","exp":0}`
    4. so now we know that the exp tag here must be modified to enter the website.
    5. last part of the token `L4UuI63oMzkmh4clZMZPoANwNxnjiYTtZO90IBhQ8B8` is the JWT ( JSON WEB token). this must be manipulated to set expiration to a later time.
6. the encoding algo is HS256.
7. i now lookup the working of hs256 and i learn that its a symettric encoding
8. i try to modify algo as None in burp and increase expiry time. didnt work. it showed invalid encodin
9. ![alt text](image-1.png)
10. so now we have to get the secret key. bruteforcing is the way.
11. i fire up hashcat and run this `hashcat -m 16500 -a 0 <jwtt0ken> jwt.secrets.list` but it did not return matches.
12. i traceback my steps. i increase time to be sufficiently higher. and keep alg as none. it works now
13. i edit the token for `/api/flag` in burp to this  `eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0.eyJzdWIiOiJCZWxlZ2FyIiwiZXhwIjoxODkzNDU2MDAwfQ.`


14. ![alt text](image-2.png)
15. what i essentially did was i removed the secret key from token. thats why there are only 2 parts to the updated token. also in the header part of token, i kept alg as "none"
## MISTAKES
1. not knowing how time works. i assumed 999 would be max value but 999 is just 999 seconds post jan 1,1970. thats why my first attempt failed
2. i wasted lot of time trying to bruteforce the secret key
3. also i did not pay attention to my case in the first attempt to keep alg as none. i entered None instead of none which made it absolete
## LEARNING
1. how json web tokens work. 
2. the different types of authentication they have
3. how keeping alg as none bypasses the security mechanisms and doesnt verify with the key
## RESOURCES
1. https://medium.com/@vipulparveenjain/brute-forcing-secret-keys-jwt-series-part-3-9d35d644ab1f
2. https://github.com/wallarm/jwt-secrets/blob/master/jwt.secrets.list
3. https://github.com/aress31/jwtcat

