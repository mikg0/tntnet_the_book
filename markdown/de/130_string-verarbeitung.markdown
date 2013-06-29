# String-Verarbeitung #

cxxtools::split()
cxxtools::join()
cxxtools::convert<>()

    std::vector<int> numbers = something();
    join(numbers.begin(), numbers.end(), " / ", std::cout);

And there is cxxtools::split for splitting and tolower to convert to
lower case. So you have everything you need.