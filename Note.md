### 20211214
##### 跨folder的include ---- By Jimmy
*   給它合理的 include path, 讓 compiler 能找到 CEVA_Xx_DRIVER_Lib_Common.h
    > 假設這個 function cevaDriversGetMssReg是在 foo.c 裡
    > compile foo.c 時，給 compiler 的 option 有一大堆 "-I<path>"
    > 需要給它合理的 include path，讓 compiler 能找到 CEVA_Xx_DRIVER_Lib_Common.h
    > 可以看 foo.c 所在的 Makefile.am
    > 那個 Makefile.am 裡應該有定義 CPPFLAGS 加進一大堆 -I<path>

### 20211215
##### 需求：cooper_sdk 需要 token_log ---- By Jimmy
*   方案1：做出這個修改 "[CDEXDB] file search follows symbolic links while tokenizing"
    > 讓使用者自行在 modem 資料夾內做出一個 symbolic link to cooper_sdk
    > 然後在 modem 裡去 $ make -C tools/cdex
    > 後來發現問題
    > 有一些使用者的 codebase 是cooper_sdk    cooper_sdk/modem
    > 如果是 cooper_sdk/modem 裡做出 symbolic link to cooper_sdk
    > 會在 file search 裡出現無限循環
    > cooper_sdk/modem/cooper_sdk/modem/cooper_sdk/modem/...
*   才有了方案2："extra path"

##### global/local variable init相关 ---- By Ted
*   對CEVA compiler來說多餘的init動作會紥實的增加code size & data size
    > 比較新的GCC比較正常，
    > CEVA compiler太舊行為不良，
    > 寫個{0}就算它能接受，
    > 還有另一個問題是，這樣的寫法出現在function裡的話它會去copy一組initialization data
    > 所以目前是要保證有init function 去init這種global
    > 或是function local variable
    > local最好沒事不要特別去variable initializer的寫法，
    > 那會增加code size & data size(只有CEVA compiler會這樣)
    > variable initializer是指UINT32 var = 0;這樣，
    > 本來就會有其他initial value來源就不要這樣多寫一動，
    > MyStruct s = {0}; 也是類似的意思，這樣寫就叫variable initializer

### 20220120
##### 在使用wip时，确认git commit中带入的某个macro是否生效 ---- By JY
*   如果那個variable有生效的話會立刻停下
    >```sh
    >ifeq ($(PET_TRACKER), 1)
    >$(warning PET_TRACKER = 1!!!)
    >INCLUDES += -I../../../project/cooper_example/example_sources/yek
    >...
    >endif
    >```

### 20220208
##### git push --force && git-rewrite-history ----By JY

### 20220210
##### Redundancy Version ----By Hendry
*   [Redundancy Version in 5G](https://devopedia.org/5g-nr-hybrid-arq)
*   [Why rv’s decoding is done as 0,2,3 1?](http://telcosought.com/4g-ran/harq-rvs-with-pdsch-channel-processing-in-lte/)


