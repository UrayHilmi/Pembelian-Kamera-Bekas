.model small
.data
    ; Menu barang
    menu_msg db 13,10, '//==================================================\\'
             db 13,10, '||               SELAMAT DATANG DI                  ||'
             db 13,10, '||               TOKO KAMERA BEKAS                  ||'
             db 13,10, '||==================================================||'
             db 13,10, '||       JENIS KAMERA          |     HARGA   | STOK ||'
             db 13,10, '||==================================================||'
             db 13,10, '[1] Canon EOS M50 Mark II      | Rp8.000.000 | READY||'
             db 13,10, '[2] Sony Alpha 6000            | Rp5.000.000 | READY||'
             db 13,10, '[3] Action Cam Gopro Hero 11   | Rp4.000.000 | READY||'
             db 13,10, '[4] Action Cam Insta360 Go 3   | Rp4.500.000 | READY||'
             db 13,10, '[5] Fujifilm XT-200            | Rp9.000.000 |  X   ||'
             db 13,10, '[6] Fujifilm XT-100            | Rp7.500.000 |  X   ||'
             db 13,10, '[7] Canon EOS M200             | Rp3.500.000 |  X   ||'
             db 13,10, '\\==================================================//','$'

    ; Pesan total harga
    total_msg db 13,10,' ', 0

    ; Variabel untuk menyimpan pilihan pengguna
    selected_camera_msg db 13,10,'Pilih jenis Kamera Yang Tersedia (1-7): $', 0
    user_choice db 0

    ; Variabel harga kamera
    canon_price dw 8000
    sony_price dw 5000
    gopro_price dw 4000
    insta360_price dw 4500

    ; Pesan untuk setiap pilihan kamera
    msg_part1_canon db 13,10,'Anda memilih Canon EOS M50 Mark II. Harga: Rp8.000.000', 13,10, '$', 0
   
    msg_part1_sony db 13,10,'Anda memilih Sony Alpha 6000. Harga: Rp5.000.000', 13,10, '$', 0
  
    msg_part1_gopro db 13,10,'Anda memilih Action Cam Gopro Hero 11. Harga: Rp4.000.000', 13,10, '$', 0
  
    msg_part1_insta360 db 13,10,'Anda memilih Action Cam Insta360 Go 3. Harga: Rp4.500.000', 13,10, '$', 0
    
    ; Pesan untuk konfirmasi pembayaran
    payment_msg db 13,10,'Metode Pembayaran Yang Tersedia Saat Ini:'
                db 13,10,'1. COD'
                db 13,10,'2. Keluar'
                db 13,10,'Pilih : $', 0
    invalid_choice_msg db 13,10,'Pilihan tidak tersedia. Silahkan coba lagi.', 13,10, '$', 0

    ; Variabel untuk menyimpan nama dan alamat pengguna
             
    name_msg db 13,10,'Masukkan nama Anda: $', 0
    address_msg db 13,10,'Masukkan alamat Anda: $', 0
               
    name db 20 dup('$')
    address db 50 dup('$')

    ; Konfirmasi pembelian dan pengiriman
    confirmation_msg db 13,10,'Pembayaran berhasil! Paket anda segera kami proses' 
                     db 13,10,'dan akan dikirim ke alamat tujuan. Terima kasih!', 13,10, '$', 0

.code
main proc
    mov ax, @data
    mov ds, ax

    ; Menu barang
    mov ah, 9
    lea dx, menu_msg
    int 21h

    ; Loop untuk memilih barang
    choose_loop:
        ; Prompt untuk memilih kamera
        mov ah, 9
        lea dx, total_msg
        int 21h

        ; Membaca input dari pengguna
        mov ah, 1
        int 21h
        sub al, '0'  ; Konversi ASCII ke bilangan
        mov user_choice, al   ; Simpan pilihan pengguna

        ; Menangani pilihan pengguna
        cmp user_choice, 1
        je Canon
        cmp user_choice, 2
        je Sony
        cmp user_choice, 3
        je Gopro
        cmp user_choice, 4
        je Insta360
        jmp InvalidChoice

    Canon:
        ; Pilihan Canon Eos M50 Mark II
        mov ah, 9
        lea dx, msg_part1_canon
        int 21h

        add ax, canon_price
        jmp PaymentPrompt

    Sony:
        ; Pilihan Sony Alpha 6000
        mov ah, 9
        lea dx, msg_part1_sony
        int 21h

        add ax, sony_price
        jmp PaymentPrompt

    Gopro:
        ; Pilihan Action Cam Gopro Hero 11
        mov ah, 9
        lea dx, msg_part1_gopro
        int 21h

        add ax, gopro_price
        jmp PaymentPrompt

    Insta360:
        ; Pilihan Action Cam Insta360 Go 3
        mov ah, 9
        lea dx, msg_part1_insta360
        int 21h

        add ax, insta360_price
        jmp PaymentPrompt

    InvalidChoice:
        ; Menangani pilihan yang tidak valid
        mov ah, 9
        lea dx, invalid_choice_msg
        int 21h
        jmp choose_loop

    PaymentPrompt:
        ; Menampilkan pesan konfirmasi pembayaran
        mov ah, 9
        lea dx, payment_msg
        int 21h

        ; Membaca metode pembayaran
        mov ah, 1
        int 21h
        sub al, '0'
        
        cmp al, 1
        je CashOnDelivery
        jmp Exit

    CashOnDelivery:
        ; Meminta nama dari pengguna
        mov ah, 9
        lea dx, name_msg
        int 21h

        ; Membaca nama
        call read_string

        ; Meminta alamat dari pengguna
        mov ah, 9
        lea dx, address_msg
        int 21h

        ; Membaca alamat
        call read_string

        ; Menampilkan pesan konfirmasi
        mov ah, 9
        lea dx, confirmation_msg
        int 21h

        jmp Exit


    Exit:
        ; Akhir program
        mov ah, 4Ch
        int 21h

main endp

read_string proc
    mov si, 0

read_string_loop:
    mov ah, 1
    int 21h

    cmp al, 13  ; Cek apakah Enter ditekan
    je read_string_done

    cmp si, 19  ; Check if the maximum length is reached
    je read_string_done

    mov [si], al
    inc si

    jmp read_string_loop

read_string_done:
    mov [si], '$'  ; Menambahkan akhir string
    ret
read_string endp

end mai
