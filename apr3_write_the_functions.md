"Why don't std functions take lambdas instead of output iterators?" - famous C++ person during internet conversation;  
"I have to write this instead:" 

    struct HitEstimatorReference {
        HitEstimator * t;
        using value_type = ValueType;
        void push_back(const ValueType & value) {
            t->Add(value);
        }
    };

    HitEstimatorReference ref{this};
            
    std::sample(data.begin(), data.end(), std::back_inserter(ref), count, std::mt19937{std::random_device{}()});
    
**_Write the functions you want to see in the world._** - Tony

What function did you **wish** the STL had? Something that could be called like:

    sample(data.begin(), data.end(), [this](Value val) mutable { Add(val); }, count, std::mt19937{std::random_device{}()});

Or maybe even

    sample(data, [this] mutable { Add(value); });
    
?

If so, *then write that* helper function!

    template<typename Iterator, typename Lambda, typename Distance, typename URNG>
    void sample(Iterator beg, Iterator end, Lambda & lam, Distance count, URNG & urng)
    {
        struct Adapter
        {
            Lambda & lam;
            template<typename T>
            void push_back(T const & t) { lam(t); }
        };
        std::sample(beg, end, std::back_inserter(Adapter(lam)), count, urng);
    }

And/Or 

    template<typename Container, typename Lambda>
    void sample(Container const & data, Lambda & lam, int count)
    {
        sample(std::begin(cont), std::end(cont), lam, count, std::mt19937{std::random_device{}()});
    }

And/Or realize the real magic is in that Adapter that converts any callable into a back-inserter.
Write a function `make_back_inserter()` that adapts a lambda into a back-inserter.
Then the code you end up with is

    sample(data.begin(), data.end(), make_back_inserter([this](Value v){Add(v);}));
    
That would probably prove useful all over the place!

Note! The above implementations could make better use of forwarding references (ie `Lambda && lam`), but I left them out _on purpose_.
1. To make the examples easier to read.
2. _Because it's fine!_ It doesn't need to be perfect to be useful.  Someone else can always perfect it if they need it.


(P.S. see also https://github.com/tvaneerd/code/blob/master/sampling.h for a `sample()` that takes a callable)



Similarly **today at work,** _within 10 minutes of the above discussion_, someone said:

"This task would be a lot easier if `ChannelManager` had a way to..."

So look at `ChannelManager`.  Would that functionality *fit* within the responsibilities of `ChannelManager`?,
or are you asking it to do something that is only useful for your particular task, and thus shouldn't be part of `ChannelManager`?

If it would be too specific, is there some appropriate _part_ that ChannelManager could do that would help?  Would `getChannelMap` (the essence of the thing needed) make your life easier,
and also be a sensible thing for `ChannelManager` to do?

If it fits, _just change ChannelManager_. It is that simple.

---

Too often we write code in the wrong place.  Either we bend over backwards to _do it here_ and not touch any of the other classes involved,
and the other half of the time, we _do it there_ because one-more-if-statement-right-there fixes the bug.

I find typically new features are written the first way - you know the feature should be part of class X,
so you write whatever code is necessary in class X, using - _but not changing_ - anything else around it.

Bugfixes tend to be written the other way - you trace through the code - any and all code and classes involved - and put the fix
in whatever place results in the smallest code change (ie a single additional if-statement in the middle of where it doesn't belong).

So 50% of the time code is in the wrong place here, 50% code is in the wrong place there;
that adds to 100% of the code is in the wrong place, which seems only a slight exaggeration in my experience.

---

P.S. For an interesting experiment with using callables instead of iterators, see https://godbolt.org/z/in9CHa and look at the assembly differences between lambdas and iterators.

