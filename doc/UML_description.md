# Description of Entities and Relationships

The set of entities consists of Orbit, Path, Block, Pixel, and Camera. We chose this set of entities because they are distinct things in the structure of the MISR data. Each Orbit of the satellite follows one of 233 Paths. Every path is broken down into 180 Blocks. Each block is composed of a set of Pixels (128 x 512 for the 1.1 km imagery). Each pixel belongs to one of nine Cameras. Pixels is a weak entity because radiance is uniquely identified by index_x, index_y, Camera.name, block_id, and orbit_id. Thus, Pixel is a weak entity since we need to follow three many-to-one relationships from Pixel. 

- Given this structure of the MISR data, the cardinality of the relationships are as follows:
- An Orbit follows exactly one Path and a Path may have zero or many Orbits associated with it. (many-to-one)
- Each of the 233 Paths has 180 Blocks and each Block may be associated with one or many Paths. (many-to-many)
- Each Block has a set of many Pixels and each Pixel belongs to exactly one block. (many-to-one)
- Each Pixel belongs to exactly one Orbit and an Orbit has many Pixels. (many-to-one)
- Each Pixel belongs to exactly one Camera and a Camera has many Pixels. (many-to-one)
