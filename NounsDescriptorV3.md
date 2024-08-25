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
   
   ```diff
   -    function backgroundCount() external view override returns (uint256);
   -    function bodyCount() external view override returns (uint256);
   -    function accessoryCount() external view override returns (uint256);
   -    function headCount() external view override returns (uint256);
   -    function glassesCount() external view override returns (uint256);

   +    function backgroundCount() public view override returns (uint256);
   +    function bodyCount() public view override returns (uint256);
   +    function accessoryCount() public view override returns (uint256);
   +    function headCount() public view override returns (uint256);
   +    function glassesCount() public view override returns (uint256);

   ```

   ```solidity
   function updateAccessories(bytes calldata encodedCompressed, uint80 decompressedLength, uint16 imageCount) external override onlyOwner whenPartsNotLocked {
      uint256 originalCount = accessoryCount();
      art.updateAccessories(encodedCompressed, decompressedLength, imageCount);
      require(originalCount == accessoryCount(), 'Image count must remain the same');
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

   ```solidity
    function updateAccessories(
        bytes calldata encodedCompressed,
        uint80 decompressedLength,
        uint16 imageCount
    ) external onlyDescriptor {
        replaceTraitData(accessoriesTrait, encodedCompressed, decompressedLength, imageCount);

        emit AccessoriesUpdated(imageCount);
    }

    function replaceTraitData(
        Trait storage trait,
        bytes calldata encodedCompressed,
        uint80 decompressedLength,
        uint16 imageCount
    ) internal {
        if (encodedCompressed.length == 0) {
            revert EmptyBytes();
        }
        delete trait.storagePages;
        delete trait.storedImagesCount;

        addPage(trait, encodedCompressed, decompressedLength, imageCount);
    }

    function replaceTraitData(
        Trait storage trait,
        address pointer,
        uint80 decompressedLength,
        uint16 imageCount
    ) internal {
        if (decompressedLength == 0) {
            revert BadDecompressedLength();
        }
        if (imageCount == 0) {
            revert BadImageCount();
        }
        delete trait.storagePages;
        delete trait.storedImagesCount;

        addPage(trait, pointer, decompressedLength, imageCount);
    }
   ```

### Implementation

**Mainnet**

| Contract | Address | Link |
|----------|---------|------|
| NFTDescriptorV2 | 0xdEdd7Ec3F440B19C627AE909D020ff037F618336 | [Etherscan](https://etherscan.io/address/0xdEdd7Ec3F440B19C627AE909D020ff037F618336) |
| SVGRenderer | 0x535BD6533f165B880066A9B61e9C5001465F398C | [Etherscan](https://etherscan.io/address/0x535BD6533f165B880066A9B61e9C5001465F398C) |
| NounsDescriptorV3 | 0x33A9c445fb4FB21f2c030A6b2d3e2F12D017BFAC | [Etherscan](https://etherscan.io/address/0x33A9c445fb4FB21f2c030A6b2d3e2F12D017BFAC) |
| Inflator | 0x6c14b7aB60d81d5F734B873126493de2E52d3eee | [Etherscan](https://etherscan.io/address/0x6c14b7aB60d81d5F734B873126493de2E52d3eee) |
| NounsArt | 0x6544bC8A0dE6ECe429F14840BA74611cA5098A92 | [Etherscan](https://etherscan.io/address/0x6544bC8A0dE6ECe429F14840BA74611cA5098A92) |

**Sepolia**

| Contract | Address | Link |
|----------|---------|------|
| NFTDescriptorV2 | 0x0ce4ed8bf0d2ae383ec014886df9191c3a224504 | [Etherscan](https://sepolia.etherscan.io/address/0x0ce4ed8bf0d2ae383ec014886df9191c3a224504) |
| SVGRenderer | 0x3AC6e9B0edC93e0302204eFD86618250925B91a0 | [Etherscan](https://sepolia.etherscan.io/address/0x3AC6e9B0edC93e0302204eFD86618250925B91a0) |
| NounsDescriptorV3 | 0x63fcb466fd59a7806370444ba82bba97d65754c2 | [Etherscan](https://sepolia.etherscan.io/address/0x63fcb466fd59a7806370444ba82bba97d65754c2) |
| Inflator | 0x9A6a5cf5E0e47ed5cB272c413343Ff906bEb3bD1 | [Etherscan](https://sepolia.etherscan.io/address/0x9A6a5cf5E0e47ed5cB272c413343Ff906bEb3bD1) |
| NounsArt | 0x512F8A614173844F223c3f490b0C87B5D49C81b9 | [Etherscan](https://sepolia.etherscan.io/address/0x512F8A614173844F223c3f490b0C87B5D49C81b9) |



