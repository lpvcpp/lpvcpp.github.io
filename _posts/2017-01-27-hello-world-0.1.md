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


## Ruby code

~~~ ruby
def what?
  42
end
~~~


## CPP code again 
~~~ cpp
#include <vector>
#include <algorithm>
#include <numeric>
#include <random>
#include <unordered_map>

int CountPair(std::vector<int> input, int target)
{
  auto count = 0;
  std::unordered_map<int, int> table;

  for (auto x : input)
  {
    table[x]++;
  }

  for (auto& pair : table)
  {
    auto value = pair.first;
    if (value < target - value)
    {
      count += table[value] * table[target - value];
    } 
    else if (value == target - value)
    {
      count += table[value] * (table[value] - 1) / 2;
    }
  }

  return count;
}

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

## The END!