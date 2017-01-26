---
title: "Hello world 0.1"
author: lvpcpp
description: ""

layout: default
comments: true
# other options

---



## A section


{% highlight cpp %}
#include <iostream>

// Hello World
int main()
{
    std::cout << "Happy New Year" << std::endl;
    int a = 10;
}
{% endhighlight %}



Here is some cpp code.

<pre>
  <code class="cpp">

    int main()
    {
      std::cout << "Hello Github Page" << std::endl;
      return 0;
    }

  </code>
</pre>



~~~~~~~~
Here comes some code.
~~~~~~~~


~~~~~~~~~~~~
~~~~~~~
code with tildes
~~~~~~~~
~~~~~~~~~~~~~~~~~~

~~~
def what?
  42
end
~~~
{: .language-ruby}




~~~ ruby
def what?
  42
end
~~~


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

* list 1 item 1
 * list 1 item 2 (indent 1 space)
  * list 1 item 3 (indent 2 spaces)
   * list 1 item 4  (indent 3 spaces)
    * lazy text belonging to above item 4

1. list 1 item 1
 2. list 1 item 2 (indent 1 space)
  3. list 1 item 3 (indent 2 spaces)
   4. list 1 item 4  (indent 3 spaces)
    5. lazy text belonging to above item 4