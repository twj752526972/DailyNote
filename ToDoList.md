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

*   jenkins trigger QC for 北京/深圳
*   ~~Verify the flash log about MIB/SIB1/SI in branch v2.0~~
*   深圳的QC
    > **CMCC**：看起来板子的位置没放好，导致在camp on cell时，容易serving cell lost
    > **CT**：access bar
*   Fix the compile warning
*   R15
    > 1.spec学习
    > 2.VPHY part
*   Freq Scan
    > 1.code trace (50%)
    > 2.study the document given by Luna
