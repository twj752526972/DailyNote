### 20211226
*   上海QC的MIB分析
    > ***branch v3.1***
    >>**CMCC**：board 1
    >>**CT**：board 3，(2506，271)这个cell的MIB解不好
    >>>~~1.除了将combine count调整为最大128以外~~
    >>>~~2.还需要将收mib起始的系统帧位置对齐640ms的frame boundary~~
    >>> * ~~可是即使是做了这个实验，对于初始解MIB，还是没有帮助，只有存在serving cell之后，才能说对齐640ms，不然MIB这边看到的就只有80ms的帧边界~~

    > ***branch v2.0***
    >>**CT**：board 2

    > ***master***
    >>**CMCC**：board 0，已知PAPR过了threshold，但RSSI没有明显差异时，可能有必要做比对实验：通过find cell来pick出SNR较好的cell

*   ~~jenkins trigger QC for 北京/深圳~~
*   ~~SIB1的combine次数是否需要double~~
*   ~~Verify the flash log about MIB/SIB1/SI in branch v2.0~~
*   深圳的QC
    > **CMCC**：看起来板子的位置没放好，导致在camp on cell时，容易serving cell lost
    > **CT**：access bar
*   ~~Fix the compile warning~~
    > ~~l1c_npbch.c(556): warning: Variable pNpbch in function l1cMibResourceConflict might be used uninitialized~~
*   R15
    > 1.spec学习
    > * ~~Reduced system acquisition time~~
    > * ~~Improved latency - UE specific DRX~~
    > * ~~Presence of NRS on a non-anchor carrier for paging~~
    > * 准备PPT (70%)
    > 
    > 2.VPHY part
    > * ~~WUS 调查~~
    > * Paging with Group Wake Up Signal
*   Freq Scan
    > 1.code trace (50%)
    > 2.study the document given by Luna
*   ~~将MIB/SIB1/SI的code搬到Flash，在branch ***phy_save_ram*** 上执行~~
*   build POSIX报error的问题
*   ~~nprach相关参数在L1C的database里或许可以精简(看使用场景只有Rmax相关参数而已)，讨论完发现少了2个左右~~
