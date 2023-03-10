This is a story of how functions and variable naming work together.

There once was some code

```cpp

    bool disableBLB = false;
    auto channels = m_model->getChannelManager()->getChannels();
    for (auto channel : channels)
    {
        auto device = m_model->getChannelManager()->getAbstractDeviceRefactor(channel.getProjectorConfigId());
        if (!device->supports(Geometry::GetActiveBlackLevelBlendSlotInfo()))
        {
            disableBLB = true;
            // any projector that doesn't support BLB ruins it for everyone
            break;
        }
    }
```

followed by about 40 lines of more code in the function.

Of note:
- `disableBLB = false;` double negatives ("disable", "false") make me cringe. Similar to `if (!disabled)` . Why not `enableBLB = true`, `if (enabledBLB)`.
- I see a for loop

There were changes..., in particular the case where there are 0 projectors.

```cpp
    bool allProjectorsSupportBLB = true;
    auto channels = m_model->getChannelManager()->getChannels();

    // We should check if there are any projectors at all
    if (channels.size() != 0)
    {
        for (Channel channel : channels)
        {
            auto projector = m_model->getChannelManager()->getAbstractDeviceRefactor(channel.getProjectorConfigId());
            if (!projector->supports(Geometry::GetActiveBlackLevelBlendSlotInfo()))
            {
                // any projector that doesn't support BLB ruins it for everyone
                allProjectorsSupportBLB = false;
                break;
            }
        }
    } 
    else
    {
        allProjectorsSupportBLB = false;
    }
```

Then it was suggested to just get rid of the for loop ("No raw loops" - Sean Parent)

to be continued...
