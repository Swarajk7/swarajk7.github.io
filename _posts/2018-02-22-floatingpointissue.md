---
layout: post
title: "Issues using floats as key in Maps/Sets"
author: "Swaraj"
---
### Let's understand this with a simple problem?
Given a list of numbers find all unique fractions A[p]/A[q] for given p < q.

[1,2,4] => [1/2,1/4,2/4] => [0.5,0.25,0.5] => so Answer is 2.

So obvious solution is to build a set of floats and keep putting all these fractions and then return the size of set. Will this work? 

Well, let's see what the code below prints. Make a guess?

        #include <bits/stdc++.h>
        using namespace std;

        int getCount(vector<float> v) {
            set<float> s;
            for(auto a:v) s.insert(a);
            return s.size();
        }

        int main() {
            // your code goes here
            float c = 94911152;
            vector<float> v;
            v.push_back(1/c);
            v.push_back(1/(c-1));
            
            cout<<"Length of set containing all fractions: " << getCount(v) <<endl;
            
            return 0;
        }

Above code prints 1 instead of 2. Confused? Every machine has a limitation in representing a floating point number. Each machine has a machine epsilon generally denoted as eps. So for any stored floating number, the next floating point number we can represent is x(1+eps). So we are losing some precision. [More Details](https://www.mathworks.com/help/matlab/ref/eps.html)

Let's print the values and see why above code doesn't work. Carefully see the output of below code. Mathematically x1 and x2 are different numbers, still we are getting same floating point as output. 

        #include <iostream>
        using namespace std;
        
        int main() {
            // your code goes here
        
            float c = 94911152;
            float x1 = 1/c, x2 = (1/(c-1));
        
            printf("X1 : %.50f\n", x1);
            printf("X2 : %.50f\n", x2);
        
            return 0;
        }


X1 : 0.00000001053616927038092399016022682189941406250000<br>
X2 : 0.00000001053616927038092399016022682189941406250000

So if we want to use A[p]/A[q] as fraction, we need to pull out some math. For any given fraction we can reduce this to its irreducible fraction representation and then use those numerator and denominator pair as key. Ex: 2/4 can be represented as 1/2. So we can store 2/4 as a pair (1,2) instead storing it as 0.5. for above example we can store x1 and x2 as 2 different numbers. (1,94911152) and (1,94911151). This is now error free. To reduce the fraction we can diving both num and den by gcd(num,den). See the code below for an idea.

        #include <bits/stdc++.h>
        using namespace std;

        int getCount(vector<pair<int,int>> v) {
            set<pair<int,int>> s;
            for(auto a:v) s.insert(a);
            return s.size();
        }

        int gcd(int a,int b) {
            if(b==0) return a;
            return gcd(b,a%b);
        }

        int main() {
            // your code goes here
            int c = 94911152;
            vector<pair<int,int>> v;
            v.push_back(make_pair(1/gcd(1,c),c/gcd(1,c)));
            v.push_back(make_pair(1/gcd(1,c-1),(c-1)/gcd(1,c-1)));
            
            cout<<"Length of set containing all fractions: " << getCount(v) <<endl;

            return 0;
        }

This idea can be applied in many problem settings. 

Sample problem:
[Max Points in A Line](https://leetcode.com/problems/max-points-on-a-line/description/)