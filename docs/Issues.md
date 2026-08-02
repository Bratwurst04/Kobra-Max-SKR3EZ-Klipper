# Issues I found while doing the conversion

## LeviQ

The LeviQ needed a reset before probing, otherwise it would trigger at completely different Z-values every probe, and a lot of times it would simply trigger before touching the buildplate.
I fixed it by giving it a reset command right before every probe attempt, by putting pin **PB14** to *HIGH*.
> [!TIP]
> You can find my ***.cfg*** files in *~/config*!'

