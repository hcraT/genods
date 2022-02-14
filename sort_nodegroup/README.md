# Sort field nodegroup

TL;DR: node group that can sort float fields with some limitations.

The geometry nodes group included in this file sorts a float/integer field. The technique that is used to sort the field is the following:

- map the field to the [-1, 1] interval (interval with an high density of float numbers)
- generate a line with the same number of points as the input field and use the remapped field as the x coordinate
- generate a sampler line with a user-specified number of points to cover the inteval [-1, 1]
- use the transfer attribute node with set to the nearest point to access the points in order
- collapse points that landed on the same nearest point. 

With this technique the node is able to output a sorted field. The node, however, suffers of the following limitations:

- incomplete sorting: if the number of sampling points is too small a part of the output will be missed (i.e. the size of the output field will be smaller compared to the input).
- repeated elements are collapsed to a single output value (this is an inherent limitation of the technique used to sort; i.e. the collapsing cannot discern truly duplicated values from multiple samplings of the same point)


## Description

The node have 6 input fields:

- **Geometry**: the geometry where the field to sort is attached to;
- **Field**: the field to sort;
- **Sampler resolution**: the number of points used to sample the input field when sorting; (The minimum number of sampling points will be at least the size of the input field regardless of this value);
- **Merge distance**: if vertices are distant less than this value the points will be merged (default 1e-7);
- **Nudge fraction**: if greater than 0.0 moves the sampling points randomly around by at most nudge_fraction* +-(1/#_sampling_points)
- **Nudge seed**: the seed of the random field

The node have 3 output fields:

- **Sorted field**: the sorted field 
- **Index order**: contains the order in which the indices of the input field should be accessed (for example with a "Field at index" node) in order to obtain the sorted field
- **New field size**: the output size of the sorted field that sometimes can be smaller or larger compared to the input field


## Usage

Because of the limitations described above almost always a part of the field will be missed. The missed values are shown in the tail of the line as a flat section all set to the maximum value.


By increasing the number of sampling points above the number of input points it is possible to obtain a larger section of the sorted field. It seems that with uniformly generated values a sampler with approximately 10 times the points can output the complete field.

If one needs to have the same number of output values as the field size but can accept some amount of duplication in the output is it possible to play with the "Nudge fraction" and "Nudge seed" values. When using the nudges if the user increases the sampler above the size of the input field the output will become larger than the input.

## Support

Supported versions of blender are 3.1 and above.
