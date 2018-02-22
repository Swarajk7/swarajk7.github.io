---
layout: post
title: "Numerical Issues in Maps/Sets"
author: "Swaraj"
---

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


