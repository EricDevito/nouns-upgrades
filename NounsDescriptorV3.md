# Nouns Descriptor V3

## Simple Summary

An updated descriptor version that allows for updating or "patching over" existing Nouns art mistakes while maintaining the efficiency and composability improvements introduced in V2.

## Abstract

This specification defines the third version of the `NounsDescriptor` contract. The new contract provides the following benefits:

- Ability to update existing Nouns art traits (accessories, bodies, heads, glasses)
- Ensures consistency between the number of images and trait counts when updating traits
- Maintains the cheaper deployment cost and improved composability from V2

## Technical Specification

### Overview

The main improvements in this version focus on:

1. Updating existing Nouns art traits
2. Ensuring consistency in art updates

#### Updating Existing Nouns Art Traits

New functions are introduced to allow updating of existing batches of images for accessories, bodies, heads, and glasses. These functions include checks to ensure the image count matches the existing trait count.

#### Maintaining Efficient Storage and Compression

The V3 descriptor continues to use the techniques introduced in V2:

1. RLE encoding supporting multilines
2. Compressing the data using DEFLATE
3. Storing data using `SSTORE2`

#### Ensuring Consistency in Nouns Art Traits

When updating traits, the contract ensures that the number of new images matches the existing trait count. This prevents accidental mismatches between the number of traits and the number of images.

### Contract Changes

1. `INounsDescriptorV3`

   New functions for updating over traits:

   ```solidity
   function updateAccessories(bytes calldata encodedCompressed, uint80 decompressedLength, uint16 imageCount) external;
   function updateBodies(bytes calldata encodedCompressed, uint80 decompressedLength, uint16 imageCount) external;
   function updateHeads(bytes calldata encodedCompressed, uint80 decompressedLength, uint16 imageCount) external;
   function updateGlasses(bytes calldata encodedCompressed, uint80 decompressedLength, uint16 imageCount) external;

   function updateAccessoriesFromPointer(address pointer, uint80 decompressedLength, uint16 imageCount) external;
   function updateBodiesFromPointer(address pointer, uint80 decompressedLength, uint16 imageCount) external;
   function updateHeadsFromPointer(address pointer, uint80 decompressedLength, uint16 imageCount) external;
   function updateGlassesFromPointer(address pointer, uint80 decompressedLength, uint16 imageCount) external;
   ```

2. `NounsDescriptorV3`

   Implementation of the new update functions, including checks for consistency:

   ```solidity
   function updateAccessories(bytes calldata encodedCompressed, uint80 decompressedLength, uint16 imageCount) external override onlyOwner whenPartsNotLocked {
       uint256 count = art.accessoryCount();
       require(count == imageCount, 'Image count must equal trait count');
       art.updateAccessories(encodedCompressed, decompressedLength, imageCount);
   }

   // Similar implementations for updateBodies, updateHeads, updateGlasses,
   // updateAccessoriesFromPointer, updateBodiesFromPointer, updateHeadsFromPointer, and updateGlassesFromPointer
   ```

3. `INounsArt`

   New functions to support updating Nouns art traits:

   ```solidity
   function updateAccessories(bytes calldata encodedCompressed, uint80 decompressedLength, uint16 imageCount) external;
   function updateBodies(bytes calldata encodedCompressed, uint80 decompressedLength, uint16 imageCount) external;
   function updateHeads(bytes calldata encodedCompressed, uint80 decompressedLength, uint16 imageCount) external;
   function updateGlasses(bytes calldata encodedCompressed, uint80 decompressedLength, uint16 imageCount) external;

   function updateAccessoriesFromPointer(address pointer, uint80 decompressedLength, uint16 imageCount) external;
   function updateBodiesFromPointer(address pointer, uint80 decompressedLength, uint16 imageCount) external;
   function updateHeadsFromPointer(address pointer, uint80 decompressedLength, uint16 imageCount) external;
   function updateGlassesFromPointer(address pointer, uint80 decompressedLength, uint16 imageCount) external;
   ```

### Implementation

Not started.
