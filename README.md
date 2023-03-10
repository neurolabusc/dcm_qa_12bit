## About

`dcm_qa_12bit` demonstrates integers sted with 12-bit precision. While most computers and programming languages use integer types divisible by 8, [DICOM allows 12-bit integers](https://dicom.nema.org/medical/dicom/current/output/html/part05.html#sect_8.1.1). These are identified as [12 Bits Stored (0028,0101)](https://dicom.innolitics.com/ciods/rt-dose/image-pixel/00280101). Curiously, the DICOM standard requires [Bits Allocated](https://dicom.innolitics.com/ciods/rt-dose/image-pixel/00280100) `shall either be 1, or a multiple of 8`. Therefore, DICOMs 12-bit integers require the same disk space as 16-bit integers and readers can `not assume anything about the contents of unused bits, and in particular may not assume that they are zero, or that they contain sign extension bits`. The DICOM tag [Pixel Representation (0028,0103)](https://dicom.innolitics.com/ciods/rt-dose/image-pixel/00280103) allows both signed or unsigned integers. Therefore, when reading **unsigned** 12-bit DICOM images stored in 16-bit arrays, one must mask the top 4-bits resulting in integers in the range 0..4095. On the other hand, when reading **signed** 12-bit DICOM images stored in 16-bit arrays, one must recognize that the sign bit is stored in the [High Bit (0028,0102)](https://dicom.innolitics.com/ciods/rt-dose/image-pixel/00280102) that is 11 rather than the expected 15. Due to this peculiarity, many DICOM tools do not correctly convert signed 12-bit DICOM images, displaying negative (dark) voxels as bright. This data type has no redeeming features: it is poorly-supported, saves no disk space, and incurs a performance penalty. However, it is a valid DICOM data type. This repository includes three DICOM images that test whether tools handle this format correctly.

| Series | Bits Allocated 0028,0100 | Bits Stored 0028,0101  | Pixel Representation 0028,0103 | Notes         |
| ------ | ------------------------ | ---------------------- | ------------------------------ | ------------- |
| 2      | 16                       | 12                     | 0 Unsigned                     | Original Scan |
| 3      | 16                       | 12                     | 0 Unsigned                     | Stripes of [0x0F0F](https://www.rapidtables.com/convert/number/hex-to-decimal.html) (3855) and 0xFFFF (4095) |
| 4      | 16                       | 12                     | 1 Signed                       | Stripes of [0x0F0F](https://www.omnicalculator.com/math/twos-complement) (-241) and 0xFFFF (-1) |

The image below shows the correct conversion of series 2,3,4 respectively. Note that bright bands a seen in series 3, while bright regions are clipped in series 4.

<div style="width: 200%; height: 200%">
  
  ![](.DICOM12bit.png)
  
</div>

