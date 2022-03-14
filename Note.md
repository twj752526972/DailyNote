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

### 20220311
##### wireshark static related ----By JY
*   Part 1
    > alignment的問題就是做錯了，array element alignment應該是尊重field的offset，在local做
    舉個例子
    struct _attribute_((packed)){u8 c0;struct {    u32 i1;    u8 c1;} s[4];} sss;
    如果沒有packed，sss.s[0].i1應該從offset 4開始，有packed就是offset 1
    sss.s[1]沒有packed就是offset 12，有packed時sss.s[1]....答案是9
    因為packed只有外曾有內層沒有，然後之前只顧到內層，沒顧到外層
    直接把s[0]對著整體的offset做alignment，也就是alignment是4，這沒錯，但不是去把現在的global offset 1做align跳到4，是從global offset 1開始，讀了s[0]後加5變成global offset 6，然後align成9 ((9-1)%4==0)
    簡而言之就是alignment要是local的，不是global的
*   Part 2
    > 那個TIMER_EXPIRY的問題是有個觀念從一開始就錯了，之前cdex的type system是設法不重複，不同途徑參考到同一個type時，就會找同一份，Wireshark要的是徹底發散的tree，他們都refer同一個東西，所以cdex那邊會收斂回一個點，這聽起來應該make sense，但就不是wireshark要的，因為那個filter走不同路到同一個點，要呈現不同的樣子
    > 
    > **Q：** 为什么(primitive.Message_t.MsgType == MSG_ID_LTE_OSPDC_TIMER_EXPIRY) 就没问题but -e primitive.Message_t.MSG_LTE_OSPATM_TIMER_EXPIRY_t.u32TimerMsgType 就需要这样，就是从struct里挑field的时候，这个问题就显现出来了，但是只要不detail到field就没事
    **A：** 第一個不靠type，那個是enum，message-type的enum才是個type，這enum type裡頭的東西一個都不會少，可是後面經過一個路徑是MSG_LTE_OSPXX_TIMER_EXPIRY_t，這是type，
    libcdex在處理時，第一個處理到這type的會留下記錄，
    這處理的過程包括跟wireshark註冊display string，
    比方第一個經過的是OSPATM_XX，有去留下紀錄，這type也記得了那個display filter string，
    之後遇到OSPDC_XX在解的時候，因為循著type的路徑走過來，會走到跟OSPATM_XX同一個點，
    就去用了他註冊的filter string，
    所有實際上是這type的都會用同一個filter string，
    比方你之前說codegen plugin會變成是另一個OSPXX_XX，那個純粹是先經過的typedef不一樣，
    但一樣是第一個踩過去之後後面就會再重複處理，
    但原先不重複的tree是所有cdex功能共用的，而且會重複太肥了，
    所以得再旁邊再種一顆，兩顆中間做適當的橋樑
*   Part 3:
    > 至於3.1的那個問題就真的只是手滑了，
    log_warning("field '%s' contains unsupported character");
    這寫在檢查per-packet field specification時
    有奇怪字眼時會報錯
    因為除了master之外都有奇怪字眼
    (主要是那個xx.yy.zz的".")
    本來warning沒關係
    但那個%s後面沒給東西，就變成random crash
    就看stack裡面剛好是什麼內容
    所以，把那個帶著unsupported character的描述拿掉不會觸發這個問題
    master就不會，3.1就會被警告，但警告的人自己被口水噎到就掛了
