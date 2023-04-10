# virtual-assembly-haskell
Creating Virtual Assembly Language on Haskell


Sanal bir dil oluşturmak için öncelikle bir sözdizimi (syntax) belirlememiz gerekiyor.
Bu durumda sanal dilin sözdizimini şu şekilde tasarlayabiliriz:

    <instruction> <operand1>, <operand2>

Burada  <instruction> komutlarımızı ifade eder, <operand1> ve <operand2> ise ilgili komutun çalışması için gerekli olan verileri ifade eder.
Şimdi, bu sözdizimini kullanarak, "xmov" ve "cadd" komutlarımızı tanımlayabiliriz:

    xmov <source>, <destination>
    cadd <operand1>, <operand2>

Burada, "xmov" komutu, <kaynak> registerındaki veriyi <hedef> registerına kopyalar.
"cadd" komutu ise <islem1> ve <islem1> kaynak registerlarındaki sayıları toplar ve sonucu hedef registera yazar.

Sanal dilimizde kullanacağımız registerların sayısı 7'ye kadar olacak, yani "reg0" ile "reg6" arasında olacaklar.
Her register 256 bit genişliğinde olacak.


    for <iteration_count>
        <instruction1>
        <instruction2>
        ...
        <instructionN>
    next

Sanal dilimizde temel for döngüsü işlemlerini gerçekleştirmek için "for" komutunu kullandık.
Burada, <iteration_count> döngünün kaç kez çalışacağını belirtir ve <instruction> komutları döngü içinde gerçekleştirilecek işlemleri tanımlar.

Aşağıdaki gibi bir x64 assembly kodu, sanal dilimizdeki "for" döngüsü için örnek bir kod olarak verilebilir:

    mov <iteration_count>, <counter_register>
        loop_start:
            <instruction1>
            <instruction2>
            ...
            <instructionN>
            sub $1, <counter_register>
            cmp $0, <counter_register>
            jne loop_start

Burada, "mov" komutu ile döngünün kaç kez çalışacağını belirtmek için sayaç registerı oluşturduk. 
Döngü başlangıcı "loop_start" etiketi ile belirtilir. 
Döngü içindeki her bir işlem gerçekleştirildikten sonra, sayaç registerındaki sayı 1 azaltılır. "cmp" komutu, 
sayaç registerındaki sayı 0 ile karşılaştırır ve "jne" komutu ile döngünün başlangıcına geri dönülür.
Bu şekilde, belirli bir sayıda işlem yapmak için "for" döngüsünü x64 assembly koduna çevirebiliriz.

Ayrıca sanal dilimizde , diğer temel işlemleri gerçekleştirebileceğimiz birkaç komut daha ekleyebiliriz:

    add <operand1>, <operand2>      ; <operand1> ve <operand2>'yi topla ve sonucu <operand1> yaz
    sub <operand1>, <operand2>      ; <operand1>'den <operand2>'yi çıkar ve sonucu <operand1> yaz
    mul <operand1>, <operand2>      ; <operand1> ve <operand2>'yi çarp ve sonucu <operand1> yaz
    div <operand1>, <operand2>      ; <operand1>'i <operand2>'ye böl ve sonucu <operand1> yaz

Bu komutların hepsi, belirli işlemler gerçekleştirmek için kullanılabilir ve <operand1> ve <operand2> parametreleri 
kullanarak kaynak ve hedef registerlarını belirler.

Son olarak, sanal dilimizde bir "print" komutu da ekleyebiliriz. "print" komutu, belirli bir register veya bellek adresindeki değeri 
yazdırmak için kullanılabilir. Aşağıdaki gibi bir sözdizimi kullanabiliriz:

print <operand>

Burada, <operand> yazdırılacak olan veriyi belirler. Örneğin, "print reg1" komutu, "reg1" registerındaki değeri yazdıracaktır.

Bu şekilde, sanal dilimizi tanımlayabilir ve bu dilde yazılan kodları x64 assembly koduna çevirebiliriz.

Öncelikle, bir sayının faktöriyelini hesaplamak için bir kod yazalım. Bu kod, "mov" ve "mul" komutlarını kullanacak ve 
sonucu "reg1" registerında saklayacaktır.

    xmov reg1, 1
    xmov reg2, 5
    for reg3, 1, reg2
        mul reg1, reg3
    endfor

u kod, "reg1" registerını 1 ile başlatır ve "reg2" registerına 5 değerini atar. 
Daha sonra, "for" döngüsü kullanarak "reg3" registerını 1'den "reg2" registerındaki değere kadar artırır ve her 
adımda "mul" komutu ile "reg1" registerındaki değeri "reg3" ile çarparak sonucu "reg1" registerında saklar. 
Bu işlem, faktöriyel hesaplamanın temel algoritmasıdır.

Bu kodu x64 assembly koduna çevirmek için, aşağıdaki gibi bir kod yazabiliriz:

    mov rax, 1        ; reg1'e 1 değerini atar
    mov rbx, 5        ; reg2'ye 5 değerini atar
    mov rcx, 1        ; döngü sayacını başlatır
    mov rdx, 1        ; sonuc için çarpanı başlatır

    .loop:
        cmp rcx, rbx  ; döngü koşulunu kontrol eder
        jg .endloop   ; döngüden çıkılır
        mul rcx, rdx  ; reg1'i reg3 ile çarpar ve sonucu reg1'e yazar
        inc rcx       ; döngü sayacını artırır
        jmp .loop     ; döngünün başına geri döner

    .endloop:
        ; sonuc "reg1" registerında saklanır

Bu kod, "mov" komutlarını x64 assembly diline doğrudan çevirir ve "for" döngüsünü "cmp", "jg", "mul", "inc" ve "jmp" komutları ile gerçekleştirir.
Sonuç "reg1" registerında saklanır ve daha sonra kullanılabilir.

Benzer şekilde, başka bir örnek kod yazarak prototipimizi test edebiliriz. 
Örneğin, aşağıdaki kod, "reg1" ve "reg2" registerlarında saklanan iki sayının toplamını hesaplar ve sonucu "reg3" registerında saklar:

    xmov reg1, 10
    xmov reg2, 20
    add reg3, reg1, reg2

Bu kodu x64 assembly diline çevirmek için, aşağıdaki gibi bir kod yazabiliriz:

    mov rax, 10       ; reg1'e 10 değerini atar
    mov rbx, 20       ; reg2'ye 20 değerini atar
    add rax, rbx      ;

Bir sonraki adımımız, sanal dilimizdeki "cadd" komutunu x64 assembly diline çevirmek olacak. 
Bu komut, iki sayıyı toplayarak sonucu üçüncü bir sayıda saklar. 
Bu işlem, daha karmaşık hesaplamalar için temel bir yapı taşıdır.

Öncelikle, sanal dildeki "cadd" komutunu aşağıdaki gibi tanımlayabiliriz:

    cadd reg1, reg2, reg3

Bu komut, "reg2" ve "reg3" registerlarındaki sayıları toplayarak sonucu "reg1" registerında saklar.

Bunu x64 assembly diline çevirmek için, aşağıdaki gibi bir kod yazabiliriz:

    mov rax, [reg2]   ; "reg2" registerındaki değeri "rax" registerına atar
    mov rbx, [reg3]   ; "reg3" registerındaki değeri "rbx" registerına atar
    add rax, rbx      ; "rax" ve "rbx" registerlarındaki değerleri toplar
    mov [reg1], rax   ; "rax" registerındaki değeri "reg1" registerına yazar

Bu kod, "reg2" ve "reg3" registerlarındaki değerleri "rax" ve "rbx" registerlarına sırasıyla yükler.
Daha sonra, "add" komutu ile "rax" ve "rbx" registerlarındaki değerleri toplar. 
Son olarak, "mov" komutu ile "rax" registerındaki sonucu "reg1" registerına yazar.

Bu şekilde, sanal dilimizdeki "cadd" komutunu x64 assembly diline doğrudan çevirebiliriz.
Benzer şekilde, diğer komutları da çevirerek sanal dilimizi tamamlamış oluruz.

Sanal dilimizi tamamladıktan ve x64 assembly diline çevirdikten sonra, şimdi bir for döngüsünün nasıl yazılacağını da inceleyebiliriz.
For döngüleri, programlama dillerinde sıkça kullanılan kontrol yapılarından biridir ve belirli bir sayıda tekrar eden işlemler için kullanılır.

Sanal dilimizde bir for döngüsü tanımlamak için öncelikle bir sayaç registerı ve bir başlangıç değeri belirlememiz gerekiyor.
Döngüde kaç kez tekrarlanacağına karar vermek için bir bit sayısı da belirleyebiliriz. Bu sayı, en büyük değeri belirlemek için kullanılacaktır.

Aşağıdaki örnek, sanal dilimizdeki for döngüsünü x64 assembly diline çevirir:

    ; Sayaç registerı için "reg1" kullanılıyor
    ; Başlangıç değeri 1, bit sayısı 8 olarak belirleniyor
    xmov reg1, 1
    xmov reg2, 8

    ; For döngüsünü başlatmak için etiket kullanılıyor
    start:
        ; Döngü gövdesi burada
        ; ...

        ; Sayaç registerı arttırılıyor
        add reg1, 1

        ; Bit sayısı kontrol ediliyor
        cmp reg1, reg2
        jle start   ; Sayaç registerı bit sayısından küçükse, döngüye devam et

    ; Döngü bitti

Bu kod, "reg1" registerını sayaç registerı olarak kullanır ve başlangıç değeri 1 olarak ayarlanır. 
"reg2" registerındaki değer 8 olduğundan, döngü 8 kez tekrar edecek. 
Döngü gövdesi, "start" etiketi ile belirlenir ve "cmp" ve "jle" komutlarıyla sayaç registerının bit sayısından küçük olup olmadığı kontrol edilir.

Döngü gövdesi kısmı, döngüde yapılacak işlemler için özelleştirilebilir. 
Bu örnekte, sadece bir etiket kullandık ama döngü gövdesinde başka işlemler de yapılabilir. 
Örneğin, "cadd" komutunu kullanarak sayaç registerındaki değeri başka bir registerla toplayabiliriz.

Ayrıca, for döngüsü içinde başka for döngüleri de kullanabiliriz. 
Bu durumda, iç içe for döngüleri kullanarak daha karmaşık işlemler gerçekleştirebiliriz. 
Örneğin, aşağıdaki kod, bir matrisin tüm elemanlarını toplamak için iki adet iç içe for döngüsü kullanır:

    ; Matris elemanları, "matrix" adlı bellek bölgesinde saklanıyor
    ; Her eleman 4 byte olduğundan, toplam eleman sayısı "reg3" registerında tutuluyor
    ; Toplam değer "reg4" registerına yazılıyor
    xmov reg1, 0        ; i indeksi
    xmov reg2, 0        ; j indeksi
    xmov reg3, 16       ; eleman sayısı (4x4 matris için)
    xmov reg4, 0        ; toplam

    ; Dış for döngüsü
    for1:
        ; İç for döngüsü
        for2:
            ; Elemanın bellek adresi hesaplanıyor
            mul reg5, reg1, 4
            add reg5, reg2
            add reg5, matrix

            ; Eleman değeri "reg6" registerına yükleniyor
            xmov reg6, [reg5]

            ; Toplam değer "reg4" registerına ekleniyor
            add reg4, reg6

            ; J indeksi arttırılıyor
            add reg2, 1
            cmp reg2, 4
            jne for2     ; j indeksi 4'ten küçük olduğu sürece iç for döngüsü devam ediyor

        ; J indeksi sıfırlanıyor
        xmov reg2, 0

        ; I indeksi arttırılıyor
        add reg1, 1
        cmp reg1, 4
        jne for1     ; i indeksi 4'ten küçük olduğu sürece dış for döngüsü devam ediyor

    ; Toplam değer "reg4" registerında saklanıyor

Bu örnekte, "for1" ve "for2" etiketleri kullanarak iki adet iç içe for döngüsü tanımlanmıştır. 
Dış for döngüsü, "for1" etiketi ile belirlenir ve "for2" etiketi içeren iç for döngüsü ile birleştirilir. 
İki adet sayaç registerı, "reg1" ve "reg2", kullanarak matrisin elemanlarına erişilir ve her eleman "reg6" registerına yüklenir. 
Toplam değer, "reg4" registerında tutulur ve her eleman eklenerek toplam değeri hesaplanır. Döngü sonunda, toplam değer "reg4" registerında saklanır.

Oluşturulabilecek diğer kod blokları: 

; Sanal assembly dili

section .data
    ; Burada veri tanımları yapılabilir.

section .text
    global _start

; Toplam fonksiyonu
topla:
    ; reg1 ve reg2 registerlarında bulunan değerleri topla ve sonucu reg1'e yaz
    xadd reg1, reg2
    xmov reg3, reg4
    ; Geri dönüş
    ret

; Çıkarma fonksiyonu
cikar:
    ; reg1'den reg2'yi çıkar ve sonucu reg1'e yaz
    xsub reg1, reg2
    xmov reg3, reg4
    ; Geri dönüş
    ret

; Çarpma fonksiyonu
carp:
    ; reg1 ile reg2'yi çarp ve sonucu reg1'e yaz
    xmul reg2, reg1
    xmov reg3, reg4
    ; Geri dönüş
    ret

; Bölme fonksiyonu
bol:
    ; reg1'i reg2'ye böl ve sonucu reg1'e yaz
    xdiv reg2, reg1
    xmov reg3, reg4
    ; Geri dönüş
    ret

; Mod alma fonksiyonu
mod_al:
    ; reg1'i reg2'ye böl ve kalanı reg1'e yaz
    xmod reg2, reg1
    xmov reg3, reg4
    ; Geri dönüş
    ret

; For döngüsü
for_loop:
    ; Başlangıç değerini reg1'e yaz
    xmov reg1, loop_start
    ; Bitiş değerini reg2'ye yaz
    xmov reg2, loop_end
    ; Artış miktarını reg3'e yaz
    xmov reg3, loop_step
    ; Döngü değişkeni için reg4'ü ayarla
    xmov reg4, loop_var
    ; Döngü başlangıcı
    loop_start:
    ; Döngü değişkenini artır
    xadd reg4, reg3
    ; Döngü değişkeni bitiş değerine eşit veya büyükse döngüden çık
    xcmp reg4, reg2
    xjge loop_exit
    ; Döngü gövdesi burada olacak
    ; ...
    ; Döngü sonu
    xjmp loop_start
    ; Döngü çıkışı
    loop_exit:
    ; Geri dönüş
    ret

; Başlangıç noktası
_start:
    ; Ana program burada olacak
    ; ...

    ; Programdan çıkış yap
    mov eax, 1
    xor ebx, ebx
    int 0x80

