---
title: "Hello Github Page"
author: lvpcpp
description: "First post"
---



## Start


{% highlight cpp %}
#include <iostream>

// Hello World
int main()
{
    std::cout << "Happy New Year" << std::endl;
    int a = 10;
}
{% endhighlight %}


~~~~~~~~
Here comes some other code.
~~~~~~~~


##Ruby code

~~~ ruby
def what?
  42
end
~~~


## CPP code again 
~~~ cpp
int main()
{
  std::vector<int> input(1000000);
  std::iota(input.begin(), input.end(), 1);
  std::shuffle(input.begin(), input.end(), std::mt19937{ std::random_device{}() });

  auto s = 12345;

  auto pairs = CountPair(input, s);

  return 0;
}
~~~

## Over