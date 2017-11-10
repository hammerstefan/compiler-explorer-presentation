# Back to the Godbolt


## Godbolt on the Internet
* [On AWS - godbolt.org](http://godbolt.org)<br>
* [On GitHub - mattgodbolt/compiler-explorer](https://github.com/mattgodbolt/compiler-explorer)<br>
* [On DockerHub - mattgodbolt/compiler-explorer](https://hub.docker.com/r/mattgodbolt/compiler-explorer/) 

Notes:
AWS hosted solution supports almost any compiler imaginable.
Should be restrited to non-proprietary code.

GitHub to contribute, or to clone and run locally 
(node.js on Linux, scans for installed compilers).
Good solution for testing things with sensetive IP.

DockerHub has multiple personal created Docker images with a variety of use cases.
As always, look at what the Dockerimage is doing before blindly running it.
Godbolt's image requires you to mount your own compilers.


## Uses
* Education
* Optimization
* Comparing compilers



## Education
* Flag "-O0"
Notes:
Want to disable optimization to understand basic compiler operation and assembly.


###### Education (...we'll do it live)
<iframe width="900px" height="600px" src="https://godbolt.org/embed-ro#g:!((g:!((g:!((h:codeEditor,i:(j:1,source:'int+testFunction(int*+input,+int+length)+%7B%0A++int+sum+%3D+0%3B%0A++for+(int+i+%3D+0%3B+i+%3C+length%3B+%2B%2Bi)+%7B%0A++++sum+%2B%3D+input%5Bi%5D%3B%0A++%7D%0A++return+sum%3B%0A%7D%0A'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:50,l:'4',m:100,n:'0',o:'',s:0,t:'0'),(g:!((h:compiler,i:(compiler:g72,filters:(b:'0',binary:'1',commentOnly:'0',demangle:'0',directives:'0',execute:'1',intel:'0',trim:'0',undefined:'1'),libs:!(),options:'-O0',source:1),l:'5',n:'0',o:'x86-64+gcc+7.2+(Editor+%231,+Compiler+%231)',t:'0')),header:(),k:50,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4"
	https://goo.gl/VKxfom
</iframe>
Notes: 
[https://goo.gl/VKxfom]
* Highlighting of source lines to matching ASM instructions
* Each ASM instruction has description mouse-over
* Graph output shows call graph



## Understand Your Compiler
* What does your compiler do for you?
* What can you do for your compiler?


###### Compiler Optimization Flags

<iframe width="900px" height="600px" src="https://godbolt.org/embed-ro#g:!((g:!((g:!((h:codeEditor,i:(j:1,source:'int+testFunction(int*+input,+int+length)+%7B%0A++int+sum+%3D+0%3B%0A++for+(int+i+%3D+0%3B+i+%3C+length%3B+%2B%2Bi)+%7B%0A++++sum+%2B%3D+input%5Bi%5D%3B%0A++%7D%0A++return+sum%3B%0A%7D%0A'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:50,l:'4',m:100,n:'0',o:'',s:0,t:'0'),(g:!((h:compiler,i:(compiler:g72,filters:(b:'0',binary:'1',commentOnly:'0',demangle:'0',directives:'0',execute:'1',intel:'0',trim:'0',undefined:'1'),libs:!(),options:'-O0',source:1),l:'5',n:'0',o:'x86-64+gcc+7.2+(Editor+%231,+Compiler+%231)',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4">
	https://goo.gl/85Rw3J
</iframe>
Notes: 
[https://goo.gl/85Rw3J]
Optimization level, O1, O2.  From 0 to 1, to 2 tries to reduce number of operations (redundancy, alternate ops, etc). O3 for the real optimizations. Number of instructions != execution speed.  In modern CPUs, many instructions will be prefetched in L1 cache. CPU architecture and type become very important.


## Optimizations
Notes: 
Let's take a look at some optimization the compiler does for us from source.


## Avoid premature optimization!!!
<!-- .slide: data-background="#f44242" -->
Notes:
Don't sacrifice readability, maintainability, robustnes for performance.
Don't spend time optimizing code that may not be in the critical path.
In the following slides we will look at how the compiler handles and optimizes different use cases. 


## Profile your code!!!
<!-- .slide: data-background="#f44242" -->
Notes:
(Assembly does not tell the whole story)



# Demo
### Effects of `virtual`, Ownership, and `<memory>`


## Spoiler:
Trust your compiler.


###### I'm a Leaf on the Wind
<iframe width="900px" height="600px" src="https://godbolt.org/embed-ro#g:!((g:!((h:codeEditor,i:(j:1,source:'%23include+%3Cmemory%3E%0Ausing+namespace+std%3B%0A%0Ausing+Time+%3D+int%3B%0Ausing+Speed+%3D+int%3B%0Ausing+Distance+%3D+int%3B%0A%0Astruct+Ship+%7B%0A++virtual+Distance+fly(Speed,+Time)+%3D+0%3B%0A%7D%3B%0A%0Astruct+Firefly+:+public+Ship+%7B%0A++static+constexpr+Speed+max_speed+%3D+7%3B%0A++virtual+Distance+fly(Speed+speed,+Time+time)+%7B%0A++++return+speed+*+time%3B%0A++%7D%0A%7D%3B%0A%0Astruct+Captain+%7B%0A++Distance+run(Speed+speed,+Time+time)+%7B%0A++++return+ship.fly(speed,+time)%3B%0A++%7D%0Aprivate:%0A++Firefly+ship%3B%0A%7D%3B%0A%0ADistance+flee_from_feds(Time+time)+%7B%0A++Captain+mal%3B%0A++return+mal.run(Firefly::max_speed,+time)%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),j:__glMaximised,k:100,l:'4',m:100,n:'0',o:'',s:0,t:'0')),version:4">
	https://goo.gl/uDciFf
</iframe>
Notes:
https://goo.gl/uDciFf <br>
This is the code that I will be playing around with.
* `Ship` interface
* `Firefly` implementation
* `Captain` that owns a `Firefly`
* `flee_from_feds` is our test function


###### Watch How I Soar
<iframe width="900px" height="600px" src="https://godbolt.org/embed-ro#g:!((g:!((g:!((h:codeEditor,i:(j:1,source:'%23include+%3Cmemory%3E%0Ausing+namespace+std%3B%0A%0Ausing+Time+%3D+int%3B%0Ausing+Speed+%3D+int%3B%0Ausing+Distance+%3D+int%3B%0A%0Astruct+Ship+%7B%0A++virtual+Distance+fly(Speed,+Time)+%3D+0%3B%0A%7D%3B%0A%0Astruct+Firefly+:+public+Ship+%7B%0A++static+constexpr+Speed+max_speed+%3D+7%3B%0A++virtual+Distance+fly(Speed+speed,+Time+time)+%7B%0A++++return+speed+*+time%3B%0A++%7D%0A%7D%3B%0A%0Astruct+Captain+%7B%0A++Distance+run(Speed+speed,+Time+time)+%7B%0A++++return+ship.fly(speed,+time)%3B%0A++%7D%0Aprivate:%0A++Firefly+ship%3B%0A%7D%3B%0A%0ADistance+flee_from_feds(Time+time)+%7B%0A++Captain+mal%3B%0A++return+mal.run(Firefly::max_speed,+time)%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:50,l:'4',m:100,n:'0',o:'',s:0,t:'0'),(g:!((h:compiler,i:(compiler:g490,filters:(b:'0',binary:'1',commentOnly:'0',demangle:'0',directives:'0',execute:'1',intel:'0',trim:'0',undefined:'1'),libs:!(),options:'-O3+-mtune%3Datom+-std%3Dc%2B%2B14',source:1),l:'5',n:'0',o:'x86-64+gcc+4.9.0+(Editor+%231,+Compiler+%231)',t:'0')),k:50,l:'4',m:100,n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4">
	https://goo.gl/9jEZWR
</iframe>
Notes:
https://goo.gl/9jEZWR<br>
Here is the assembly. Simple, efficient, shiny.


###### Who's Flying this thing?
```c++
struct Captain {
	...
private:
  Firefly ship;
};

Distance flee_from_feds(Time time) {
  Captain mal;
  return mal.run(Firefly::max_speed, time);
}
```
---
```c++
struct Captain {
  Captain(Ship &ship_) : ship(ship_) {}
	...
private:
  Ship &ship;
};

Distance flee_from_feds(Time time) {
  Firefly serenity;
  Captain mal(serenity);
  return mal.run(serenity.max_speed, time);
}
```
Notes:
Change Captain to take a reference, `Ship&`, instead of a fixed member, `Firefly`.


###### Who's Flying This Thing? ...
<iframe width="900px" height="600px" src="https://godbolt.org/embed-ro#z:OYLghAFBqd5TKALEBjA9gEwKYFFMCWALugE4A0BIEAViAIzkDO6ArqatiAOQCkATAGYCAO1QAbVjgDUvQQGEAttkVkAnnNy8ADAEFWTUcGkiAhsqYAHU52lMimOQCEdu1waPSAKgWWzBACLSokTO7oYixgDKltjYmP5BIWF6HpHSAQT2pmLYicEioYIueq72pKyoRNJRSASWsgDsJbrS0gBuBKRErKbiGVlEObYAZuJqEDFxmOTevtgAlPnaKW6NAatlRBVV0gBiXdhjatIg0pasAEbiBKg1dQ28za5t2US30hgi9tgAHpakGqxeLSRSmX4AfSs03yjVWbU63V6/Uy2Vy0mOk2BCWh8VmPj872USyeLTabVI2B6pBEdmx0gAVNIidh4U0NqV1ps9OVKtV5KZLENRE0yQM0bYKiIsTDcTM5oT5iTnnpydJKdTaUwHgA6TFy2YshZsp4c3QAgjtUxELgvfaHY52B6bLnFVyuVFDdFjOIQkakdCKP3xJgQAl5I2iu0CoWmEVg8RsjXsWkJnVSiAHSnHEAgMGQg3MpUugLcBbkcQ8ACs3HIIh42lr6B48gELn4TjsbA4eQEgnotaIDbL5YA1iAq9oKzwACy1xQMKs6gAcADZ1xvNxvGnXh%2BRm9xa0wQFOh9xG%2BW4LAUBgcPhiGRKNQ6PxmN3ODw%2B6IJFJewplKopAaIIWipBExhmBY1i2PYjhuqUYGeOG%2BTJPB%2BjgUCMJyEkhSrGkxiesMf44UULRbDs1S1PUUaqh0XQ9H04peqM4wyniCqLMsJbcrovK7FmRzjKc5xXDcdxUY8KqtHYQzvHcXw/P8gJTCC%2BZQvS2HSHCaEIvRyJMURGKsSpOLYvi8xFsSNHSRSVIpnSMJMiyJpcpyGxoeRfLSDGwq0qS0aCr5kwPLI/Crtq9QQksZwRZYECxVFUaudJhHohmJkOexyGRv5tG2ZqTr1HqrGFkaLlmhaVo2iAdoSaF4XOh5rpkXoqUsb6/qBsGmChtlSrWW0AmOkw2CUiIxDAWKPlxqmfTxaN2DjUQajGjp6p2TSoJ9OmrDSiNY0TTqamlcWTWluWlbcDWu7nk2LZtm2XbsLYfb8IOw4LOWSDYKYOCkNQY4MNoU6XXO5ALsuM46vQgiw3D8Nwzdjb7jwR4nuQZ4XoDk7Ttwgi1vWt0o4eGMfeQV4IPAEA3oGlgEOIo1PhAGCKHTDP/cAM4AJxTiM9M2qQx4QJce6XKIphATwA7kCzyiFAA8iI4x7jgYKRAzKuHFUlrYMeRN/NgqCsNV3DSyE2CXcj2y%2BHuu04HzIjxFLZYVgQlzHpA5boEKBDoN8PAALTy4I0gB4oPSO9h1qBqHsHYagD0dvQYMsM9XD0Bd1YE3uB6/GuAerjO0jAKgdxQ1zOraNIED3iQgJ9ow3m0/To2hf2Czvbdn3kOOOOg/OICCI0OozluY/rkjd0k8ep5kxT1NoM37NMyzbOjSAnM8%2BQfPiALQsi0TYtmJLpu1rLi1EIrytE6rOTABrN9a%2B87S63uBtGybZuFBbe7WwuXeu3dvAL2Ps/Z6yDiHMOEdWSBGjooWODh46JycMnaQR53xcH4JnK62cia53zoXYupdpDl0rtXWuZA26vibqzFu9chBYM7ljcg31frrwgNgsGC5Vz8B1FWBGAjYaT2JmjWeXdsYgx4PjYRB4mEjlxm9GRqNSbiPIC/QWvt6wziAA%3D">
	https://goo.gl/DtC3zV
</iframe>
Notes:
https://goo.gl/DtC3zV<br>
Change Captain to take a reference instead of a fixed member.


#### Add virtual maxspeed
```c++
struct Captain {
  Distance run(Speed speed, Time time) {
    return ship.fly(speed, time);
  }
};
```
---
```c++
struct Ship {
  virtual Speed maxSpeed() const = 0;
};

struct Firefly : public Ship {
  virtual Speed maxSpeed() const { return max_speed; }
};

struct Captain {
  Distance run(Time time) {
    return ship.fly(ship.maxSpeed(), time);
  }
};
```
Notes:
Add `virtual maxSpeed` instead of passing it into run(). 
<br>Expect this to be virtual function call overhead. Every call to `run` requires a virtual lookup for `maxSpeed`.


###### Add virtual maxSpeed ...
<iframe width="900px" height="600px" src="https://godbolt.org/embed-ro#z:OYLghAFBqd5TKALEBjA9gEwKYFFMCWALugE4A0BIEAViAIzkDO6ArqatiAOQCkATAGYCAO1QAbVjgDUvQQGEAttkVkAnnNy8ADAEFWTUcGkiAhsqYAHU52lMimOQCEdu1waPSAKgWWzBACLSokTO7oYixgDKltjYmP5BIWF6HpHSAQT2pmLYicEioYIueq72pKyoRNJRSASWsgDsJbrS0gBuBKRErKbiGVlEObYAZuJqEDFxmOTevtgAlPnaKW6NAatlRBVV0gBiXdhjatIg0pasAEbiBKg1dQ28za5t2US30hgi9tgAHpakGqxeLSRSmX4AfSs03yjVWbU63V6/Uy2Vy0mOk2BCWh8VmPj872USyeLTabVI2B6pBEdmx0gAVNIidh4U0NqV1ps9OVKtV5KZLENRE0ydIBULTKJJg9ZPwAGxMB4QpZnJX1CDqywq0VPDmtAZo2wVERYmG4mZzQnzEnPPTk6SU6m0rUAOkxFtmLIWbL1rgBBHapiIXBe93qcsVD02XOKrlcqKG6LGcQhI1I6EUafiTAgBLy3t19v2h2OdmwlJExA0ceLEuFtLB4k1FewVaIah9tYNTvYjb6rpNLcr1ddYMhnuZNpjAW4C3I4h4AFZuOQRDxtKv0Dx5AIXPwnHY2Bw8gJBPRV0QN3P5wBrEBL7QLngAFlXigf2ldCvlv7///lRo12vcht24VcmBAJ8r24Td5zgWAUAwHB8GIMhKGoOh%2BGYY9OB4M9RAkKRTwUZRVFIGstFSCJjDMCxrFsexHG7cJPHzfJkm7NJonpOQkkKVZuMNJNbD4goihaLYdmqWoI1JMNER6PphOGPJMSmPErUWZY2UU5EgRhccNMwCAli%2BewdJY2NJJ5bY%2BRLSkyzOC5rg%2BWTHjtA03g%2BcyQ3%2BQFjNBcEoV4wJpDhbsES6JSUUGVSMXGM0QUndjC3k4sKSpPs6RhJkWV9Lliz05TAqM7FTM%2BdBvmqdKDUy50gonbECv1P0WNs6TxUFBsiwNespVNdzIy1HU1QeTVlVtFxCoNRN4qHVKbV6h1expOwHndRK3TK6ZTK9adIvZf1SEDYNQ2LIaBCjeoZ25XQ5uTcRU3TTNs0wXNFuJZaHKOcZyxHDs2X6kUm2HNtqy7MVVv7cRB1YU1vRnOdn24FdgNgrcdz3Pcj3YUShH4S9rwWeckGwUwcFIag7wYbQn0Xbg33ID8XwATldRpBC57mee59HN1AngIKg8gYLgmnHxRwRV3XDHBfA0XifIBCEHgCAkMzSwCCeigqAgDBFC1nWQGANmnxGbWQ1ISCIEuEDLlEUwKJ4C9yAN5RCgAeREcYQJwMFIiev3DiqQNsEguW/mwVBWBDF3VxCbAGYF7ZfBA%2BGcAtkR4nj%2BcbkuSDIHndAhQIKqI4AWk9wRpArxQemzvjg0zWumL41BsYPegmZYPGuHoPPlxlkCwN%2BAAOeUK/lF9pGAVA7hfV12e0aQIFQkhATPRhxU17WKzlc8FiJjGSfIe9JYZpmP0l2WBbA4XoKVlX1bQXedYw/W34rE2zfIC3xCtjbO2csHZmGdtwV27s2xEG9r7OW/scjACDvAkO7x2jhxAlHGOccIEJ0KEnECqcPwnwXAQAu8Bi6l3LjwKuNc64N1ZIEZuihW4OHbp3Jw3dpAQVwlwfgg9UbDzlqPCeU8Z5zwXkvV0K816EA3gfbCO9DZ703gTI%2BisT6k3JpTamKMr6fn5pjBWkFH6aLPgYhm0tDHy2PuLFGhNrH3w0XY9B1sy7rhfEAA%3D%3D">
	https://goo.gl/j8vQaB
</iframe>
Notes:
https://goo.gl/j8vQaB<br>
Add `virtual maxSpeed` instead of passing it into run(). 
<br>Expect this to be virtual function call overhead. Every call to `run` requires a virtual lookup for `maxSpeed`.


#### Ship ref to Captain ctor
```c++
Distance flee_from_feds(Time time) {
  Firefly serenity;
  Captain mal(serenity);
}
```
---
```c++
Distance flee_from_feds(Time time) {
  Firefly serenity;
  Ship &ship = serenity;
  Captain mal(ship);
}
```
Notes:
SKIP IF NEED TIME.<br>
Just to make sure that it's not infering data from the function call, let's pass in a `Ship&`


###### Ship ref to Captain ctor ...
<iframe width="900px" height="600px" src="https://godbolt.org/embed-ro#z:OYLghAFBqd5TKALEBjA9gEwKYFFMCWALugE4A0BIEAViAIzkDO6ArqatiAOQCkATAGYCAO1QAbVjgDUvQQGEAttkVkAnnNy8ADAEFWTUcGkiAhsqYAHU52lMimOQCEdu1waPSAKgWWzBACLSokTO7oYixgDKltjYmP5BIWF6HpHSAQT2pmLYicEioYIueq72pKyoRNJRSASWsgDsJbrS0gBuBKRErKbiGVlEObYAZuJqEDFxmOTevtgAlPnaKa0dXT19NbHx0oqmAB5T8RBLGCL2y6u8jQHXeuWV1QBiXdhjatIg0pasAEbiAioGp1Bo3FptbJEIHSc72bAHSykbbTPaHAD6VlRciCjVWbU63V6/Uy2Vy0g%2Bkx2CSx8VmPj80OUS3BrjabVI2B6pBEdmp0gAVNImdh8U07noCRtiSjdvsjtTTrD0BdqqzJezOdzefLMdSxTcJW5bvddI8qtJ5KZLENRE0IZbrbaRJNQbJ%2BAA2Jig9FLb7e%2BoQAOWX32w1sgZk2wVF0MvIilnNCMcrnsXnBgB0lMz8uOmFOswTBpNeiRBHapiIXAjtXq7q9oOuJuKrlcpKG5LGcXRI1I6EUPfiTAgceF80TDtenI%2BdmwnJExA0LY1Vptpjt%2B3EQbn2AXRDUCzFWrTaPEGZjECLy%2BNAW4C3I4h4AFZuOQRDxtK/0Dx5AIXPwnDsNgODyARBHoV8iA/O97wAaxAJ9tAfHgABZX0UBhBAzD1tFwvD8Lwxh324T9yG/bhXyYEAkKgki73IOBYBQDAcHwYgyEoag6H4ZhgM4HgwNECQpFAhRlFUUgly0VIImMMwLGsWx7Eca9wk8UccQKIoWjSaJ%2BU05Jr10yMO1sAzClNc1qlrMEkw1QlNhJQZhjySk83pccrmvKUiS2PM0QVaYlThNVAmkFZVObFoyiICoLSnd5xi%2BH5/kBYEbLDDUoRhEKESRWUEl1WkEk0vFvPWXynKjVzxipVFio8xlPPVNYU21PlUSFEViyNHzHIKgK82ClVLha9lpGPHkAr1aYetbKLWweWKnkdNc7TG1bnVdOsBAbepQ39UEgx9Cdww1dsXIm1hY3mMdmUy1qJtTKbM2zUEM1zRV7zuxY5tLUhy0rasNQy3bgybO5VL0C7O3Ebte37QdMGHUcEwetoEpnJgdz3JcHVBz1g3ybH50XMVV2dU9jvqQ9ysmnU%2BnPa7L3HCH6MfbgXzfaCyJ/P8/yA9gzKEfhIOghZ7yQbBTBwUhqDghhcOQ7g0PIDCAA4PQzFDubo3mKOYajyFoz8JfIeCPS1/hEIATnoRptBtlCPRQlD7eVwRX2I0jyLFuj70YhB4AgZj%2B0sAg4YoKgIAwRRw8jkBgBQm2kJGCOq1IKiID%2BHm/lEUxJJ4CDyFj5RCgAeREcYeZwfZIjhmu3iqctsCovWEWwVBWCrIvXxCbAOdI2LfB566cDTkR4l7%2B9AT%2BKjIHvdAbQIEaeAAWnLwRpDXxQeknnFK37bflJxVB%2BYA%2BhVZYIWuHoGfny9nnyIOTW15d6RgFQYEUIzG2M20aQEA2IkGRGBRglow4RznO6cCCw/amwVohZWqsMJIO9l%2BHglEjYm3ooHEOaBIGR04jHQhc5E7J1TunOcWcc56zzmYQu3Bi6l13EQSu1c9a1xyMABunCm7QnaK3HmHcu49yYX3QoA8ebDwwv7B8BA57wEXsvVe3AN78G3rva6opAiH0UMfBwp9z5OEvtISifEuD8HvpzR%2Betn6v3fp/b%2Bv9/6AOAWQGBPEIFxygaAkWcDjbi0ltLWW8tkHoQQkhdB%2BssE0SCebEAggsKNAoerfgKcnyCCfE%2BdWggdYc09rrH2mDAn%2B2saLIpGCDY4LNoIzOK93woSAA%3D%3D">
	https://goo.gl/uCd35H
</iframe>
Notes:
https://goo.gl/uCd35H <br>
SKIP IF NEED TIME.<br>



## `std::unique_ptr<T>`


#### Pass ownership using unique_ptr
```c++
struct Captain {
  Captain(Ship &ship_) : ship(ship_) {}
}
```
---
```c++
struct Captain {
  Captain(unique_ptr<Firefly> ship_) : ship(move(ship_)) {}
}
Distance flee_from_feds(Time time) {
  auto ship = make_unique<Firefly>();
  Captain mal(move(ship));
}
```
Notes:
Want to pass ownership. We'll use unique_ptr's.<br>
The traditional way it to use raw pointers. :(
We'll look at raw pointers if we have time.


###### Pass ownership using unique_ptr...
<iframe width="900px" height="600px" src="https://godbolt.org/embed-ro#z:OYLghAFBqd5TKALEBjA9gEwKYFFMCWALugE4A0BIEAViAIzkDO6ArqatiAOQCkATAGYCAO1QAbVjgDUvQQGEAttkVkAnnNy8ADAEFWTUcGkiAhsqYAHU52lMimOQCEdu1waPSAKgWWzBACLSokTO7oYixgDKltjYmP5BIWF6HpHSAQT2pmLYicEioYIueq72pKyoRNJRSASWsgDsJbrS0gBuBKRErKbiGVlEObYAZuJqEDFxmOTevtgAlPnaKa0dXT19NbHx0oqmAB5T8RBLGCL2y6u8jQHXeuWV1QBiXdhjatIg0pasAEbiAioGp1Bo3FptbJEIHSc72bAHSykbbTPaHAD6VlRciCjVWbU63V6/Uy2Vy0g%2Bkx2CSx8VmPj80OUS3BrjabVI2B6pBEdmp0gAVNImdh8U07noCRtiSjdvsjtTTrD0BdqqzJezOdzefLMdSxTcJW5bvddI8qtJ5KZLENRE0IZbrbaRJNQbJ%2BAA2Jig9FLb7e%2BoQAOWX32w1sgZk2wVF0MvIilnNCMcrnsXnBgB0lMz8uOmFOswTBpNeiRBHapiIXAjtXq7q9oOuJuKrlcpKG5LGcXRI1I6EUPfiTAgceF80TDtenI%2BdmwnJExA0LY1tbBnuD%2BSYc%2BwC6IS4dVptpjt%2B3EQdBCzFWrTaPEGZjECLy%2BNAW4C3I4h4AFZuOQRDxtF/dAeHkAQXH4Jw7DYDg8gEQR6F/IgALfd8AGsQC/bQPx4AAWX9FAYQQMw9bRSLI8iyMYf9uEA8hgO4X8mBALCkJot9yDgWAUAwHB8GIMhKGoOh%2BGYaDOB4ODRAkKRYIUZRVFIJctFSCJjDMCxrFsexHGfcJPFHHECiKFo0miflDOSZ9TMjDtbAswpTXNapVzDDVCU2ElBmGPJKTzelxyuZ8pSJLY8zRBVpiVOE1UCaQVl05sWjKIgKgtKd3nGL4fn%2BQFgRc9U1ihGFooRJFZQSXVaQSQy8SC9YQs8qMfPGKlUSq/zGQCgr2Wka8eT5VEhRFYsjWCjzyvCvMopVS5us1VN%2Bsq/U6vDUpEtbB4UqeR0jztObD2dCBWAXABHVhsHRG1SDkeR0o%2BTQ7B9P1HsDVR2mwc96l9CdVrWdtvN646R3mMdmVctYU21F7LAAWk0bNQThwRcFzRV31BxYRtcMsKyrEAI2Oggzouq6bru8YHuDJs7l0vR/s7cRu17ftB0wYdRwTcG2gAem56Ryc%2BLd50XMVeZBOsBAbCXYqFncRbq0xWBIaH8n2VCLsJ4mybee7kdOMUDuPHU%2BggN6PuDBZLzqvrjbvB8nyS252M/bgfz/ZC6JAsCwKg9g7KEfhEOQy3yCQbBTBwUhqDQhhSOw7g8PIAiAA4PQzHD3bYz2GOYZjyFYwCQ/QkiM2TwRBAAThwnDGn4CvGg9fhGmT%2BPBF/ajaPooO2PfTiEHgCBuP7SwCEZigqAgDBFBHseQGAHCK6wkZR6rUgmIgP4Pb%2BURTEUngEPIKflEKAB5ERxg9nB9kiRnL7eKpy2wJis4RbBUCVrhuAPkJsBd2iUt8B7Y6OBl4iHiPvZ2BA/hMUgO%2BdANoCAzR4DDE%2BghpAw0UD0MBOJKz9nQdpHEqBvYQXoInFgfsuD0HfC7N2HcgI8AOKnGGHocLSGAKgYEOEMwVwzNoaQEA%2BIkGRHBRglph6jznO6eCCxu6FxjpheOicCIKLodnRiecC7sT7oPNA4ix6CUnnoucc8F5LxXnOdem8s7bzMHvL%2Bv4j47iIGfC%2BWcr45GALfNx99oTvWfrRV%2B788b2MoIUX%2BHsAEER7h%2BKBMCCzwOhEg7gKC0EYKwaKQIuDFD4IcIQ4hThSHSEYmJLg/BqHfnbh7eijCPTMNYewzh3DeH8MEWQKRIkxHTwkcIgOMj87B3fGHCOxiCwuyURhLCqiu65xYgM8g6FK4ZnoB6EiX4vxN20EIWuIkXZt0zp3HgsiULx0Dvs%2BhOdNEh3emvRB/4cJAA%3D%3D%3D">
	https://goo.gl/ptX29z
</iframe>
Notes:
https://goo.gl/ptX29z


#### Abstract unique_ptr
```c++
Captain(unique_ptr<Firefly> ship_) : ship(move(ship_)) {}
private:
  unique_ptr<Firefly> ship;
```
---
```c++
Captain(unique_ptr<Ship> ship_) : ship(move(ship_)) {}
private:
  unique_ptr<Ship> ship;
```
Notes:
Want to use the interface abstraction, so we use the `Ship` interface pointer.


###### Abstract unique_ptr
<iframe width="900px" height="600px" src="https://godbolt.org/embed-ro#z:OYLghAFBqd5TKALEBjA9gEwKYFFMCWALugE4A0BIEAViAIzkDO6ArqatiAOQCkATAGYCAO1QAbVjgDUvQQGEAttkVkAnnNy8ADAEFWTUcGkiAhsqYAHU52lMimOQCEdu1waPSAKgWWzBACLSokTO7oYixgDKltjYmP5BIWF6HpHSAQT2pmLYicEioYIueq72pKyoRNJRSASWsgDsJbrS0gBuBKRErKbiGVlEObYAZuJqEDFxmOTevtgAlPnaKa0dXT19NbHx0oqmAB5T8RBLGCL2y6u8jQHXeuWV1QBiXdhjatIg0pasAEbiAioGp1Bo3FptbJEIHSc72bAHSykbbTPaHAD6VlRciCjVWbU63V6/Uy2Vy0g%2Bkx2CSx8VmPj80OUS3BrjabVI2B6pBEdmp0gAVNImdh8U07noCRtiSjdvsjtTTrD0BdqqzJezOdzefLMdSxTcJW5bvddI8qtJ5KZLENRE0IZbrbaRBBWCICABHVjYdE20hyeSvTkfTR2UHopbfJigiCqdrYCDR%2BoRlnNQ1sgZk2wVF0MvIi1MOjlc9i8pOWAC0mkp5arglw8uOmFOswLBpNeiRBHapiIXAzbs93t9RH9CiD73GofL1xNxVcrlJQ3JYzi6JGpHQinX8SYEDzwvmhYzplYJDD9Xy%2BwA1j7B17ReO3iH66cxVabaY7ftxLH0PHE1BBYFjFLVSzRcQADocwgNt51KW5uAWchxB4ABWbhyBEHhtEw9AeHkAQXH4Jw7DYDg8gEQR6EwogcKQ5DrxANDtBQngABZMMUBhBEggA2bRBKE4ShMYbDuFw8h8O4TCmBAVi6IkpDyDgWAUAwHB8GIMhKGoOh%2BGYcjOB4KjRAkKRKIUZRVFIDR63CTwzAsaxbHsRx4LcVIImMA8cQKIoWjSaJ%2BT85IPKCzNl1sULClNc1qlqS91TWQlNhJQZhjySkm3pI8rg8qUiS2Js0QVaYlThNVAmkFYPPTOqHlHJ5pAnD4vh%2Bf5AWBRKwTTDUoRhSqESRWUEl1WkEj8vECvWIr0qzLLxipVEJtyxk8uS9lpDAnk%2BVRIURXbI1CrS0bSqbCqVUuTbNRLXbxv1Gb0wQu4GrNJqLQ/Z17QzL6vxde9hz9AMeuncNIwvSw/wA8sUx%2Bjs1iXTLtrdfd5kPZkfo1YttUhutcBrUF8cbRVkIxxYjtcLsez7EAB3dB8RzHeRQfrSHZ1elpFwyldxDXDctx3TA9wPAssbWU9z3LK9TFvdFAcfQNnynV8QJmv7vz6aGE3LYDQLunU%2Bmg1G4K5xDkNQ7gMKw%2BipIIoiiLI9hoqEfhaPo4DyCQbBTBwUhqEYhhBLY7hOPIbiAA4%2BMg9ibaUu2ZOYeTyEU3DPaYgTIIjwRBAATnY9jGn4XPGj4/hGgjkPBEw8TJOk92lOQ1SEHgCB1K3SwCD5igqAgDBFE77uQGAdjc9YkYu77Ug5IgP5bb%2BURTFsngaPIfvlEKAB5ERxltnB9kiPm97eKpu2wOT44RbBUDPLhuFXkJsEtyTR18W23RwCeRHiFflMBP45KQGQugG0BAro8ArJvQQ0gKyKB6N/HEvYtwwLcjiVADsSL0DDiwZ2XB6AW3QjXW20kDhRwrHxdi0hgCoGBOxSCudILaGkBALSJBkRUUYJaDuXdsDsKEPghuadA4sRDmHbiIja54R4LJZOqdlLNzbmgbh3ddJ92Ubw4eo9x6T14TPOe8cF5mGXvfTC69sBbx3moPeKgcjACPvHQgnJT7xgvpJK%2BN9aYmMoIUJ%2BttX7cXjh/d4ogf4mItgQAB8BgGgPAdwSB0DYHwMfAEJBigUEODQRgpwWDpCySMlwfgBCrZEPjiQshFCqE0OkHQhhTCWGEDYbIV2sx5DqL4YIQpgiGJex9n7AOoiuLMVYpIhOMiFIe0DnnSC9A%2BICTQmhcu2ghBFwMpbaucc67SJThMkObsNlSMTnIz28Zp5gOwuxIAA%3D%3D%3D">
	https://goo.gl/qJgf1A
</iframe>
Notes:
https://goo.gl/qJgf1A



## Raw pointers?
Change `unique_ptr` to `Ship*`

Who has ownership?


###### Raw pointer is NOT better
<iframe width="900px" height="600px" src="https://godbolt.org/embed-ro#z:OYLghAFBqd5TKALEBjA9gEwKYFFMCWALugE4A0BIEAViAIzkDO6ArqatiAOQCkATAGYCAO1QAbVjgDUvQQGEAttkVkAnnNy8ADAEFWTUcGkiAhsqYAHU52lMimOQCEdu1waPSAKgWWzBACLSokTO7oYixgDKltjYmP5BIWF6HpHSAQT2pmLYicEioYIueq72pKyoRNJRSASWsgDsJbrS0gBuBKRErKbiGVlEObYAZuJqEDFxmOTevtgAlPnaKa0dXT19NbHx0oqmAB5T8RBLGCL2y6u8jQHXeuWV1QBiXdhjatIg0pasAEbiAioGp1Bo3FptbJEIHSc72bAHSykbbTPaHAD6VlRciCjVWbU63V6/Uy2Vy0g%2Bkx2CSx8VmPj80OUS3BrjabVI2B6pBEdmp0gAVNImdh8U07noCRtiSjdvsjtTTrD0BdqqzJezOdzefLMdSxTcJW5bvddI8qtJ5KZLENRE0IZbrbaRBBWCICABHVjYdE20hyeS1eqaOyg9FLb5MUEQVTtbAQKP1cMs5qGtkDMm2Couhl5EUph0crnsXmJywAWk0lLLlcEuHlx0wp1m%2BYNJr0SII7VMRC46bdnu9vqI/oUQcsIbL1xNxVcrlJQ3JYzi6JGpHQilX8SYEFzwvmBfTAHoj9JXpyPnZsJz3UQNLONSeQfVZPwAGxl/JMa/YW/3h2mKwJChi%2BOJogA1j6A5eqKCjnu84yaKcYpWjaph2vs4gxugcYJqCCwLGKWolmi4gAHTZhArYPsaATcAs5DiDwACs3DkCIPDaGx6A8PIAguPwTh2GwHB5AIgj0GxRCcfRDHgSAzHaIxPAACxsYoID8MxZHaG%2Ben6QZBnsTJ5A8dwbFMCASnSdwXEMXAsAoBgOD4MQZCUNQdD8MwImcDw4miBIUhiQoyiqKQ95aKkETGGYFjWLY9iODR4SeHuYHJDRaTRPyGWFKs2UZouth5UULRlCOTzPmCqYaoSmwkoMwx5JSjb0geVw0VKRJbI2aIKtMSpwmqgTSCsKUzuVDyVRa8GXt8vwAjC472umUIwsNCJIrKCS6rSCRgXiXXrD1jWZi14xUqi%2B3tYyHXqmsRbanyqJCiKbZGt1DU7f1jZDSqlwPey0jETy/V6tMH1zpNc7TRUFqoc6q0aoj6EuuOQpluGXwgZYeFJoe7ZrAuzUg26u7zPuzLI49IPFmDNZVpdjN1g2ioMVTixQx2pBdj2fYaitApThNdwpXoJNLuIK5rhuW6YDue75jTbRPtBQ5%2BgG46TqC%2BT7JB6Lq7B8hzYhdbIcdQufmBIjYAA7mebyUoRx2oxhfT45YLsOpLtgHaNmEUeT1EOjg0u9rjRH07yyXlbc9HKdwrHGbZ3G8fx/HCewJVCPwUkyQR5BINgpg4KQ1ByQw2hKUx3BqeQGmNCpZEAJyCO3Hed%2B3Kkp1xpk8BZVnkDZdmV2%2BghkSpb4qYIAAcgiNC3b5N402iz2%2BieCGxHGp/35nDwX5AOQg8AQE5G6WAQ0sUFQEAYIol/XyAwAqS3SkjFfvakJZEB/CZfyiFMBFHgklyD32UIUAA8iIcYJkcD7EiNLOBbwqhdmwJZXeCJsCoCAlwbgoCQjYFrn3EcvgTJuhwB/W2mAQEJ0BH8SykAGLoBtAQAGPByyQMENIcsigei2xxD2DcPCko4lQBnQS9B64sGzlwegDFa7Jx3n3MyBx17lmntIYAqBgTNxbjpaQEBXIkGROJRgloL5X2vK%2BCSCx86p0LvJRSid64aWccotO%2B9LLWUPsfM%2BaBLHXw8nfQJ15n6v3fp/a8P8/67wAWYYB%2BC2LgN/EQaBsDd7wJyMAJBmSUHQjjBgvuWCcG9loZQQoRCTKkI0g4xiBAGHwGYaw9h3BOHcN4fw2CAQhGKBEQ4MREinBSOkBZXyXB%2BAKJYtvEyqj1GaO0bo1uBijGEBMTY7yFiH5WNMbnOxB8HEMWLqXMJzZa6uIUkpDxe9B4%2BMOeQeSgg3xkWYoIZib56Bvn4NoQQWl%2BAt28rXLevdPH2NHonPOIKbkHPBXGb%2BbCOIqSAA%3D%3D%3D">
	https://goo.gl/1kQ6ML
</iframe>
Notes:
https://goo.gl/1kQ6ML



## `std::shared_ptr<T>`
#### Shared ownership


###### Shared Ship
```c++
struct Captain {
  Captain(unique_ptr<Firefly> ship_) : ship(move(ship_)) {}
private:
  unique_ptr<Firefly> ship;
}

Distance flee_from_feds(Time time) {
  auto ship = make_unique<Firefly>();
}
```
---
```c++
struct Captain {
  Captain(shared_ptr<Ship> ship_) : ship(move(ship_)) {}
private:
  shared_ptr<Ship> ship;
}

Distance flee_from_feds(Time time) {
  auto ship = make_shared<Firefly>();
}
```
Notes:
Change from `unique_ptr` to `shared_ptr`. GCC was able to optimize `unique_ptr`s well, but `shared_ptr`s? Ouch!


###### Shared Ship ...
<iframe width="900px" height="600px" src="https://godbolt.org/embed-ro#z:OYLghAFBqd5TKALEBjA9gEwKYFFMCWALugE4A0BIEAViAIzkDO6ArqatiAOQCkATAGYCAO1QAbVjgDUvQQGEAttkVkAnnNy8ADAEFWTUcGkiAhsqYAHU52lMimOQCEdu1waPSAKgWWzBACLSokTO7oYixgDKltjYmP5BIWF6HpHSAQT2pmLYicEioYIueq72pKyoRNJRSASWsgDsJbrS0gBuBKRErKbiGVlEObYAZuJqEDFxmOTevtgAlPnaKa0dXT19NbHx0oqmAB5T8RBLGCL2y6u8jQHXeuWV1QBiXdhjatIg0pasAEbiAioGp1Bo3FptbJEIHSc72bAHSykbbTPaHAD6VlRciCjVWbU63V6/Uy2Vy0g%2Bkx2CSx8VmPj80OUS3BrjabVI2B6pBEdmp0gAVNImdh8U07noCRtiSjdvsjtTTrD0BdqqzJezOdzefLMdSxTcJW5bvddI8qtJ5KZLENRE0IZbrbaRBBWCICABHVjYdE20hyeSvTkfTR2UHopbfJigiCqdrYCDR%2BoRlnNQ1sgZk2wVF0MvIi1MOjlc9i8pOWAC0mkp5arglw8uOmFOswLBpNeiRBHapiIXAzbs93t9RH9CiD73GofL1xNxVcrlJQ3JYzi6JGpHQinX8SYEDzwvmhYzplYJDD9Xy%2BwA1j7B17ReO3iH66cxVabaY7ftxLH0PHE1BBYFjFLVSzRcQADocwgNt51KW5uAWchxB4ABWbhyBEHhtEw9AeHkAQXH4Jw7DYDg8gEQR6EwogcKQ5DrxANDtBQngABZMMUEB%2BAATkg9j2N4%2Bg0P4ND2KERp%2BAADmkrD6PIfDuEwpgQFYujuFw5C4FgFAMBwfBiDIShqDofhmHIzgeCo0QJCkSiFGUVRSA0etwk8MwLGsWx7EceC3FSCJjAPHECiKFo0miflQuSfzIszZdbBiwpTXNapakvdU1kJTYSUGYY8kpJt6SPK5/KlIktibNEFWmJU4TVQJpBWfz01ah5RyeaQJw%2BL4fn%2BQFgQysE0w1KEYQahEkVlBJdVpBJQrxcr1kqvKs0K8YqVReaSsZUqsvZaQwJ5PlUSFEV2yNCrcpmmqm3qlVLgOzUSxOub9WW9MELudqzU6i0P2de0M0Br8XWjUxOUwEcx3kYbp3DSML0sQDk2PDs1iXAqjrdfd5kPZlgY1YttWRutcBrUFycbRVkIJxZLtcLsez7EAMwhqGYYDeH62R2cfpaRd8pXcQ1w3Lcd0wPcDwLIm1lPc9yyvUxb0xJBIfiAMeqnV8QOW0Hvz6VHLD1h1jp1PpoNxuDBcQ5DUO4DD5M0vCCKIoiyPYJKhH4Wj6OA8gkGwUwcFIahGIYbRWIdzjyG4xptEgtCWOk9jGnYtDGgANn4Rp6EYbCXcUngVLU8gNK0iOpMgrOs7TqPtF46TBBk9jGAdwRMML3Di%2BU8v/fIHSEHgCA9K3SwCFFigqAgDBFAnqeQGAITWJGSe%2B1IVSID%2BBS/lESG1B4GjyDn5RCgAeREcYFJwfZIlFm%2B3iqbtsFUouEWwVAzy4bhj5CbAHY91HL4BSbocBrxEPEI%2BSEUIED%2BKpSAyF0A2gII9HgFZz6CGkBWRQPRIE4l7FubBvkcSoHdiRegscWBey4PQe26Eu4KSUgcaSWcKx12kMAVAwJ2KQX4toaQEBDIkGRFRRglpx6T2wKIoQdC/YuwDkxFibFuCx24so7urs%2B6qXUgPIeo80CSKniZWeRjpFLxXuQNe4gN5bx3kXPeZgXLQJPluM%2BRBL7XyLrfHIwAH7eKftCeMb8e4fy/qzX%2BmF/6ANoqQEBRcwHvFEFAyJ9s4EIJbMg6EaDuAYKwTgvBj4AiEMUMQhwpDyFOEodIFSlkuD8HoY7RhRdmGsPYexTh3DpC8P4YI4RZBZA%2B1mPIMxMiW4LHkZXQOwdQ7hxUWo5irFNG91LrohREdc6QUEIIUSvE9naHoFnPOaFm4qM7s7HuSlJkMRUb7C5WjrkB3jJvVB2F2JAA">
	https://goo.gl/RZdJqs
</iframe>
Notes:
https://goo.gl/RZdJqs



## Ownership via stack
Notes:
C++ developers should love the stack.


###### Pass Firefly ownership on stack
<iframe width="900px" height="600px" src="https://godbolt.org/embed-ro#z:OYLghAFBqd5TKALEBjA9gEwKYFFMCWALugE4A0BIEAViAIzkDO6ArqatiAOQCkATAGYCAO1QAbVjgDUvQQGEAttkVkAnnNy8ADAEFWTUcGkiAhsqYAHU52lMimOQCEdu1waPSAKgWWzBACLSokTO7oYixgDKltjYmP5BIWF6HpHSAQT2pmLYicEioYIueq72pKyoRNJRSASWsgDsJbrS0gBuBKRErKbiGVlEObYAZuJqEDFxmOTevtgAlPnaKa0dXT19NbHx0oqmAB5T8RBLGCL2y6u8jQHXeuWV1QBiXdhjatIg0pasAEbiAioGp1Bo3FptbJEIHSc72bAHSykbbTPaHAD6VlRciCjVWbU63V6/Uy2Vy0g%2Bkx2CSx8VmPj80OUS3BrjabVI2B6pBEdmp0gAVNImdh8U07noCRtiSjdvsjtTTrD0BdqqzJezOdzefLMdSxTcJW5bvddI8qtJ5KZLENRE0IZbrbaRBBWCICABHVjYdE20hyeS1eqaOyg9FLb5MUEQVTtbAQKP1cMs5qGtkDMm2Couhl5EUph0crnsXmJywAWk0lLLlcEuHlx0wp1m%2BYNJr0SII7VMRC46bdnu9vqI/oUQcsIbL1xNxVcrlJQ3JYzi6JGpHQilX8SYEFzwvmBfTplYJFD9Xy%2BwA1j6B17RQpXpyPppTmKrTbTHb9uIY%2Bg4wnQQWBYxS1Es0XEAA6bMIFbWdSlubgFnIcQeAAVm4cgRB4bQMPQHh5AEFx%2BCcOw2A4PIBEEegMKIbDEKQy8QFQ7RkJ4AAWDDFBAfhUIg7QADZBKE4ThMwujyDw7gMKYEAWNo7gcKQuBYBQDAcHwYgyEoag6H4ZgyM4HhKNECQpAohRlFUUgNDrcJPDMCxrFsexHDgtxUgiYw9xxAoihaNJon5HzkjcgKM0XWxgsKU1zWqcd7XTQlNhJQZhjySlG3pA8rjcqUiS2Rs0QVaYlThNVAmkFY3LTaqHhHJ5pEfd5xi%2BH5/kBYF4vVNYoRhMqESRWUEl1WkEh8vFcvWfKUszdLxipVFRqyxlsu69lpFAnk%2BVRIURTbI08uSoaisbUqVUuNbNWLLaRv1Sa03gu5arNeqLXfZ0Eo1d7PxdJqPlkfh%2BIEfiz0scNWrLX9/zLZMEvbNYFzSja3V3eZ92ZT61iLbVQYg6tQQghtFSQ9HFn21xO27XsQHTP6WqnaqZxaedUqXcQVzXDct0wHc93zTG2jpz4mGwTl3SIGyHW%2Br8%2Bih%2BMRbF4g1CAkDrp1PooJR2DmYQpCUO4dCxIU3D8MIwjSPYSKhH4Gi6KA8gkGwUwcFIagGIYbQWP1jjyC4xo2IggBOQQQ9DsOQ7Yo2cIknhpNk8h5MU93%2BO0CCAA4Q8Dti2MafhA8afj%2BEaNPWO4QQMKw42Y6khO7fIZSEHgCBVI3SwCHZigqAgDBFDbjuQGANjA5YkZ297UgZIgP5xL%2BURTGsnhqPIHvlEKAB5ERxnEnB9kidnt7eKou2wGSq4RbBUBPLhuCXkJsH16OR18cS3RwUeRHiRfEOQgg/hkyAkLoBtAQc6PByxr0ENIcsigegfxxD2DcUCXI4lQGbYi9AfYsEtlweges0IV3EpJA4ad%2BLln4mxaQwBUDAgDoHPi0gIAaRIMiSijBLSt3bqLAGVEFi22NvbRizFS4%2By4kIyu0dJJxzknXBuzc0AcI7tpbuCjRYDyHiPMeotJ7TyrrPMwC8b4YRXtgdem81DbxUDkYA%2B8q6EE5EfOMp9o7n0vtTQxlBCj33Ek/LiVdX7vFEJ/Qxetf7/2bEA6EoDuDgMgdA2B94AgIMUEghwKC0FOAwdIaSBkuD8DwQbAhVciEkLIRQqhNCg70MYYQZh3C9LsN7pwlh1teG134UhR2ztVHNm9pxJiLFxEmxrjJaR7TyCMUEHQ%2BggltCoVQkXbQQhc56X1uXKOQy%2BFJ1LjbdZ1dNn0XIHGCeICsJsSAA%3D%3D%3D">
	https://goo.gl/DFquEhi
</iframe>


###### Can't pass Ship ownership on stack
<iframe width="900px" height="600px" src="https://godbolt.org/embed-ro#z:OYLghAFBqd5TKALEBjA9gEwKYFFMCWALugE4A0BIEAViAIzkDO6ArqatiAOQCkATAGYCAO1QAbVjgDUvQQGEAttkVkAnnNy8ADAEFWTUcGkiAhsqYAHU52lMimOQCEdu1waPSAKgWWzBACLSokTO7oYixgDKltjYmP5BIWF6HpHSAQT2pmLYicEioYIueq72pKyoRNJRSASWsgDsJbrS0gBuBKRErKbiGVlEObYAZuJqEDFxmOTevtgAlPnaKa0dXT19NbHx0oqmAB5T8RBLGCL2y6u8jQHXeuWV1QBiXdhjatIg0pasAEbiAioGp1Bo3FptbJEIHSc72bAHSykbbTPaHAD6VlRciCjVWbU63V6/Uy2Vy0g%2Bkx2CSx8VmPj80OUS3BrjabVI2B6pBEdmp0gAVNImdh8U07noCRtiSjdvsjtTTrD0BdqqzJezOdzefLMdSxTcJW5bvddI8qtJ5KZLENRE0IZbrbaRBBWCICABHVjYdE20hyeS1eqaOyg9FLb5MUEQVTtbAQKP1cMs5qGtkDMm2Couhl5EUph0crnsXmJywAWk0lLLlcEuHlx0wp1m%2BYNJr0SII7VMRC46bdnu9vqI/oUQcsIbL1xNxVcrlJQ3JYzi6JGpHQilX8SYEFzwvmBfTAHoj9JXpyPnZsJz3UQNLONSeQfVZPwAGxl/JMa/YW/3h2mKwJChi%2BOJogA1j6A5eqKCjnu84yaKcYpWjaph2vs4gxugcYJqCCwLGKWolmi4gAHTZhArYPsaATcAs5DiDwACs3DkCIPDaGx6A8PIAguPwTh2GwHB5AIgj0GxRCcfRDHgSAzHaIxPAACxsYoID8MxZHaG%2Ben6QZBnsTJ5A8dwbFMCASnSdwXEMXAsAoBgOD4MQZCUNQdD8MwImcDw4miBIUhiQoyiqKQ95aKkETGGYFjWLY9iODR4SeHuYHJDRaTRPyGWFKs2UZouth5UULRlCOTzPmCqYaoSmwkoMwx5JSjb0geVw0VKRJbI2aIKtMSpwmqgTSCsKUzuVDyVRa8GXt8vwAjC472umUIwsNCJIrKCS6rSCRgXiXXrD1jWZi14xUqi%2B3tYyHXqmsRbanyqJCiKbZGt1DU7f1jZDSqlwPey0jETy/V6tMH1zpNc7TRUFqoc6q0aoj6EuitAhvpjIGWOGXw49huFlsmq3tmsC7NSDbq7vM%2B7Msjj0g8WYNlmR1agmRDaKgxdOLFDHakF2PZ9hqK1ThNdwpXoFNLuIK5rhuW6YDue75gzbRzeMV43sQ/7pqjGF9IT8bfjrd4EURzM6n0FHU9R5W3PRyncKxxm2dxvH8fxwnsCVQj8FJMkEeQSDYKYOCkNQckMNoSlMdwankBpjQqWRACcgiZ1n2eZypbtcaZPAWVZ5A2XZ0dvtoZEAByZ2nKkqY0/Bp40b78I01fO4IbEce7hfmaXQfkA5CDwBATkbpYBByxQVAQBgihTzPIDACpadKSM0%2B9qQlkQH8Jl/KIpgRTwknkAvyiFAA8iI4wmTg%2ByRHL99vFUXbYJZfcItgqBAVw3BnxCNgeOBcRy%2BBMm6HAm8RDxFPk7QEfxLKQAYugG0BAAY8HLFfQQ0hyyKB6DAnEPYNy4KSjiVAXtBL0ETiwX2XB6AMXjq7XuBczIHGrm%2Bcsb4VLSGAKgYEqc046WkBAVyJBkTiUYJaSe09ryvgkgsQO7tg7yUUs7ROGk1EsI9gPSy1kh4j3HmgGRM8PLzxMdeFea8N5b2vLvfefdD5mBPgAtiF9fxEBvnfPuD8cjAGfj41%2B0I4yfwLt/X%2BvY4GUEKMAkyYCNLKMYgQRB8AUFoIwdwLBOC8EENggEYhihSEOHIZQpw1DpAWV8lwfgjCWI9xMmwjhXCeF8IEenYRojCDiPkd5aRi9ZESP9ooweyiGKh3DpY5s8cNEKSUto/uxd9GjPIPJQQQj6B6W0MxZi7dtBCCbt5eO3d846KUeXZ2AcTkLJGecuMO90EcRUkAA%3D">
	https://goo.gl/V4p4s9F
</iframe>
Notes:
https://goo.gl/V4p4s9F


###### Templated Captain for Generic Solution
<iframe width="900px" height="600px" src="https://godbolt.org/embed-ro#z:OYLghAFBqd5TKALEBjA9gEwKYFFMCWALugE4A0BIEAViAIzkDO6ArqatiAOQCkATAGYCAO1QAbVjgDUvQQGEAttkVkAnnNy8ADAEFWTUcGkiAhsqYAHU52lMimOQCEdu1waPSAKgWWzBACLSokTO7oYixgDKltjYmP5BIWF6HpHSAQT2pmLYicEioYIueq72pKyoRNJRSASWsgDsJbrS0gBuBKRErKbiGVlEObYAZuJqEDFxmOTevtgAlPnaKa0dXT19NbHx0oqmAB5T8RBLGCL2y6u8jQHXeuWV1QBiXdhjatIg0pasAEbiAioGp1Bo3FptbJEIHSc72bAHSykbbTPaHAD6VlRciCjVWbU63V6/Uy2Vy0g%2Bkx2CSx8VmPj80OUS3BrjabVI2B6pBEdmp0gAVNImdh8U07noCRtiSjdvsjtTTrD0BdqqzJezOdzefLMdSxTcJW5bvddI8qtJ5KZLENRE0IZbrbaRBBWCICABHVjYdE20hyeS1eqaOyg9FLb5MUEQVTtbAQKP1cMs5qGtkDMm2Couhl5EUph0crnsXmJywAWk0lLLlcEuHlx0wp1m%2BYNJr0SII7VMRC46bdnu9vqI/oUQcsIbL1xNxVcrlJQ3JYzi6JGpHQilX8SYEFzwvmBfTAHoj9JXpyPnZsJz3UQNLONSeQfVZPwAGxl/JMa/YW/3h2mKwJChi%2BOJogA1j6A5eqKCjnu84yaKcYpWjaph2vs4gxugcYJqCCwLGKWolmi4gAHTZhArYPsaATcAs5DiDwACs3DkCIPDaGx6A8PIAguPwTh2GwHB5AIgj0GxRCcfRDHgSAzHaIxPAACxsYoID8MxZHaG%2Ben6QZBnsTJ5A8dwbFMCASnSdwXEMXAsAoBgOD4MQZCUNQdD8MwImcDw4miBIUhiQoyiqKQ95aKkETGGYFjWLY9iODR4SeHuYHJDRaTRPyGWFKs2UZouth5UULRlCOTzPmCqYaoSmwkoMwx5JSjb0geVw0VKRJbI2aIKtMSpwmqgTSCsKUzuVDyVRa8GXt8vwAjC472umUIwsNCJIrKCS6rSCRgXiXXrD1jWZi14xUqi%2B3tYyHXqmsRbanyqJCiKbZGt1DU7f1jZDSqlwPey0jETy/V6tMH1zpNc56L2iiWOIPYhfId6xHFeReJoFUVBaqHOqtGr4%2BhOavm%2BAhviBljhl8VPYbhZbJqt7ZrAuzUg26u7zPuzKE49IPFmDZZkdWoJkQ2ioMTzixQx2pBdsjIDpl4VPTncKV6GzS7iCua4blumA7nu%2BZ820c3jFeN7EP%2B6bE6IAbm5FpH0/G35W3eBFEYLOp9BRnPUeVtz0cp3CscZtncbx/H8cJ7AlUI/BSTJBHkEg2CmDgpDUHJDDaEpTHcGp5AaY0KlkQAnIIVfVzXVcqeHXGmTwFlWeQNl2Tnb7aGRAAcVflypKmNPw5eNG%2B/CND3IeCGxHER035lt8n5AOQg8AQE5G6WAQOsUFQEAYAjO/XiAwAqeXSkjDvvakJZEB/CZfyiKYEU8JJ5CH8ohQAPIiOMJk4H2JEHWAC3hVC7NgSy88ETYFQEBLg3B34hGwAXRuI5fAmTdDgK%2BIh4hv2DoCP4llIAMXQDaAgAMeDlm/oIaQ5ZFA9FwTiHsG46FJRxKgaOgl6BFxYHHLg9AGIFzDnPRuZkDg9zfOWN8KlpDAFQMCMu5cdLSAgK5EgyJxKMEtFvY%2BmihCCKThHFO8lFIhyLhpMxojI6L0stZZeq8N5oF0bvDyB8XEnzPhfcgV9xA3zvg/eeT8zCv0QWxT%2Bv4iC/3/vPQBORgAgNiWA6EcYoGNxgXA3s%2BDKCFBQSZdBGljGMQIEQ%2BApDyGUO4NQ2h9DGGwQCCwxQbCHAcK4U4Hh0gLK%2BS4PwIRLFZ4mXEZI6Rsj5GKIriotRhANGvkEN5HRR9d6zN6UYjuqd06Z2zuY9SCklLWIXi3exxic6CGUfQPS2hmLMQntoIQw9vIFxng3GxqzZIh0Ts8g5S9jnkDjLfChHEVJAA">
	https://goo.gl/keL4Vi
</iframe>
Notes:
https://goo.gl/keL4Vi



## Comparing Compilers


###### GCC 4.9 vs 7.2 (unique_ptr)
<iframe width="900px" height="600px" src="https://godbolt.org/embed-ro#g:!((g:!((g:!((h:codeEditor,i:(j:1,source:'%23include+%3Cmemory%3E%0Ausing+namespace+std%3B%0A%0Ausing+Time+%3D+int%3B%0Ausing+Speed+%3D+int%3B%0Ausing+Distance+%3D+int%3B%0A%0Astruct+Ship+%7B%0A++virtual+Distance+fly(Speed,+Time)+%3D+0%3B%0A++virtual+Speed+maxSpeed()+const+%3D+0%3B%0A%7D%3B%0A%0Astruct+Firefly+:+public+Ship+%7B%0A++static+constexpr+Speed+max_speed+%3D+7%3B%0A++virtual+Distance+fly(Speed+speed,+Time+time)+%7B%0A++++return+speed+*+time%3B%0A++%7D%0A++virtual+Speed+maxSpeed()+const+%7B%0A++++return+max_speed%3B%0A++%7D%0A%7D%3B%0A%0Astruct+Captain+%7B%0A++Captain(Ship+%26ship_)+:+ship(ship_)+%7B%7D%0A++Distance+run(Time+time)+%7B%0A++++return+ship.fly(ship.maxSpeed(),+time)%3B%0A++%7D%0Aprivate:%0A++Ship+%26ship%3B%0A%7D%3B%0A%0ADistance+flee_from_feds(Time+time)+%7B%0A++unique_ptr%3CShip%3E+ship+%3D+make_unique%3CFirefly%3E()%3B%0A++Captain+mal(*ship)%3B%0A++Distance+d+%3D+mal.run(time)%3B%0A++return+d%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0'),(h:compiler,i:(compiler:g72,filters:(b:'0',binary:'1',commentOnly:'0',demangle:'0',directives:'0',execute:'1',intel:'0',trim:'0',undefined:'1'),libs:!(),options:'-O3+-mtune%3Datom+-std%3Dc%2B%2B14',source:1),l:'5',n:'0',o:'x86-64+gcc+7.2+(Editor+%231,+Compiler+%231)',t:'0'),(h:compiler,i:(compiler:g490,filters:(b:'0',binary:'1',commentOnly:'0',demangle:'0',directives:'0',execute:'1',intel:'0',trim:'0'),libs:!(),options:'-O3+-mtune%3Datom+-std%3Dc%2B%2B14',source:1),l:'5',n:'0',o:'x86-64+gcc+4.9.0+(Editor+%231,+Compiler+%232)',t:'0')),header:(),k:42.99339691856199,l:'4',m:100,n:'0',o:'',s:0,t:'0'),(g:!((h:diff,i:(lhs:1,rhs:2),l:'5',n:'0',o:'Diff+x86-64+gcc+7.2+vs+x86-64+gcc+4.9.0',t:'0')),k:57.006603081438016,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4">
	https://goo.gl/mTisX1
</iframe>
Notes:
https://goo.gl/mTisX1


###### Class Template Deduction 
<div>Previous template class: https://godbolt.org/g/t96hhN</div> <!-- .element: style="font-size:0.6em;line-height:0.6em;"-->
<iframe width="900px" height="550px" src="https://godbolt.org/embed-ro#g:!((g:!((g:!((h:codeEditor,i:(j:2,source:'%23include+%3Cmemory%3E%0Ausing+namespace+std%3B%0A%0Ausing+Time+%3D+int%3B%0Ausing+Speed+%3D+int%3B%0Ausing+Distance+%3D+int%3B%0A%0Astruct+Ship+%7B%0A++virtual+Distance+fly(Speed,+Time)+%3D+0%3B%0A++virtual+Speed+maxSpeed()+const+%3D+0%3B%0A%7D%3B%0A%0Astruct+Firefly+:+public+Ship+%7B%0A++static+constexpr+Speed+max_speed+%3D+7%3B%0A++virtual+Distance+fly(Speed+speed,+Time+time)+%7B%0A++++return+speed+*+time%3B%0A++%7D%0A++virtual+Speed+maxSpeed()+const+%7B%0A++++return+max_speed%3B%0A++%7D%0A%7D%3B%0A%0Atemplate+%3Ctypename+T%3E%0Astruct+Captain+%7B%0A++Captain(T+%26%26+ship_)+:+ship(move(ship_))+%7B%7D%0A++Distance+run(Time+time)+%7B%0A++++return+ship.fly(ship.maxSpeed(),+time)%3B%0A++%7D%0Aprivate:%0A++T+ship%3B%0A%7D%3B%0A%0ADistance+flee_from_feds(Time+time)+%7B%0A++Firefly+serenity%3B%0A++Captain+mal(move(serenity))%3B%0A++return+mal.run(time)%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%232',t:'0')),header:(),k:68.37015314532246,l:'4',m:100,n:'0',o:'',s:0,t:'0'),(g:!((h:compiler,i:(compiler:g71,filters:(b:'0',binary:'1',commentOnly:'0',demangle:'0',directives:'0',execute:'1',intel:'0',trim:'0'),libs:!(),options:'-O3+-mtune%3Datom+-std%3Dc%2B%2B17',source:2),l:'5',n:'0',o:'x86-64+gcc+7.1+(Editor+%232,+Compiler+%232)',t:'0')),header:(),k:31.629846854677535,l:'4',m:100,n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4">
	https://goo.gl/GTgRkW
</iframe> <!-- .element: class="fragment"--> 
Notes:
https://goo.gl/GTgRkW


###### GCC vs CLANG
<iframe width="900px" height="600px" src="https://godbolt.org/embed-ro#g:!((g:!((h:codeEditor,i:(j:2,source:'%23include+%3Cmemory%3E%0Ausing+namespace+std%3B%0A%0Ausing+Time+%3D+int%3B%0Ausing+Speed+%3D+int%3B%0Ausing+Distance+%3D+int%3B%0A%0Astruct+Ship+%7B%0A++virtual+Distance+fly(Speed,+Time)+%3D+0%3B%0A++virtual+Speed+maxSpeed()+const+%3D+0%3B%0A%7D%3B%0A%0Astruct+Firefly+:+public+Ship+%7B%0A++static+constexpr+Speed+max_speed+%3D+7%3B%0A++virtual+Distance+fly(Speed+speed,+Time+time)+%7B%0A++++return+speed+*+time%3B%0A++%7D%0A++virtual+Speed+maxSpeed()+const+%7B%0A++++return+max_speed%3B%0A++%7D%0A%7D%3B%0A%0Astruct+Captain+%7B%0A++Captain(unique_ptr%3CShip%3E+ship_)+:+ship(move(ship_))+%7B%7D%0A++Distance+run(Time+time)+%7B%0A++++return+ship-%3Efly(ship-%3EmaxSpeed(),+time)%3B%0A++%7D%0Aprivate:%0A++unique_ptr%3CShip%3E+ship%3B%0A%7D%3B%0A%0ADistance+flee_from_feds(Time+time)+%7B%0A++auto+ship+%3D+make_unique%3CFirefly%3E()%3B%0A++Captain+mal(move(ship))%3B%0A++return+mal.run(time)%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%232',t:'0'),(h:compiler,i:(compiler:g72,filters:(b:'0',binary:'1',commentOnly:'0',demangle:'0',directives:'0',execute:'1',intel:'0',trim:'0',undefined:'1'),libs:!(),options:'-O3+-mtune%3Datom+-std%3Dc%2B%2B14+',source:2),l:'5',n:'0',o:'x86-64+gcc+7.2+(Editor+%232,+Compiler+%232)',t:'0'),(h:diff,i:(lhs:2,rhs:1),l:'5',n:'0',o:'Diff+x86-64+gcc+7.2+vs+x86-64+clang+5.0.0',t:'0'),(h:compiler,i:(compiler:clang500,filters:(b:'0',binary:'1',commentOnly:'0',demangle:'0',directives:'0',execute:'1',intel:'0',trim:'0'),libs:!(),options:'-O3+-mtune%3Datom+-std%3Dc%2B%2B14+',source:2),l:'5',n:'0',o:'x86-64+clang+5.0.0+(Editor+%232,+Compiler+%231)',t:'0')),header:(),k:100,l:'4',m:100,n:'0',o:'',s:0,t:'0')),version:4">
	https://goo.gl/nmoxu3
</iframe> 
Notes:
https://goo.gl/nmoxu3
