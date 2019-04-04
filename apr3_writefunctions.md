"Why don't std functions take lambdas instead of output iterators?" - famous C++ person during internet conversation  
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

Write `my_sample(beg, end, lambda)` that calls `std::sample` for you.
(Or use https://github.com/tvaneerd/code/blob/master/sampling.h that takes a callable)

Or write an adapter that converts any callable into a back-inserter:

    std::sample(data.begin(), data.end(), magic_adapter([t](Value v){t->Add(v);}));
    
That would probably prove useful all over the place!


Similarly **today at work,** _within 10 minutes of the above discussion_, someone said:

"This task would be a lot easier if `ChannelManager` had a way to..."

So look at `ChannelManager`.  Would that functionality *fit* within the responsibilities of `ChannelManager`,
or are you asking it to do something that is only useful for your particular task, and shouldn't be part of `ChannelManager`?

Is there something not so specific to your task that would help?  Would `getChannelMap` (the essence of the thing needed) make your life easier,
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

