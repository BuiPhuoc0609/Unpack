# Unpack

## unpackme1

<img width="840" height="631" alt="image" src="https://github.com/user-attachments/assets/602e6c7b-e93c-4892-a2c0-9c5c5053dcf9" />

chương trình bị pack bằng upx, thực tế upx đã có tool unpack sẵn, tuy nhiên ta sẽ thực hiện manual unpack chương trình này

tham khảo: https://whitehat.vn/threads/upx-pack-va-unpack.4696/

Tìm oep:

<img width="371" height="452" alt="image" src="https://github.com/user-attachments/assets/00b3f798-8113-426c-95f8-8eb010c1b9e6" />

<img width="686" height="147" alt="image" src="https://github.com/user-attachments/assets/30bb8a40-6e0b-4a86-8324-f1bb6038cbe9" />

OEP tại `0x81131C`, dùng công cụ OllydumpEx để dump và unmap pefile:

<img width="1918" height="873" alt="image" src="https://github.com/user-attachments/assets/0e8682cd-4486-4708-bc12-62d69994a1b7" />

<img width="1917" height="1022" alt="image" src="https://github.com/user-attachments/assets/762b4477-70c0-4947-88b3-ecbcc7896e24" />

<img width="1918" height="1020" alt="image" src="https://github.com/user-attachments/assets/e04ded6d-74fd-469c-a696-b9bb9869260b" />

Công cụ trên chỉ có thể dump pefile, để có thể fix import thì ta dùng công cụ Scylla

<img width="720" height="796" alt="image" src="https://github.com/user-attachments/assets/3cbdbec1-510a-4fe1-8e88-8c2a9e929ae3" />

<img width="727" height="800" alt="image" src="https://github.com/user-attachments/assets/77a6ed90-987c-4d3b-ba9a-304d11fd0c27" />

Ta thấy tại `0x81C124` không trỏ đến API nào nên sẽ Delete tree node đó đi:

<img width="720" height="793" alt="image" src="https://github.com/user-attachments/assets/b9ff44db-5717-40c0-a06f-c6a919ed934e" />

Chọn Fix dump để fix import pe ta vừa dump

<img width="1918" height="1021" alt="image" src="https://github.com/user-attachments/assets/b9c23e2b-5e0f-46cb-8991-f90a686407e5" />

<img width="1918" height="1015" alt="image" src="https://github.com/user-attachments/assets/a802cfcd-1900-4d3a-9b1a-5c03a5202d40" />


Ta thấy rằng import đã được sửa thành công

execute:

<img width="1918" height="1017" alt="image" src="https://github.com/user-attachments/assets/172dc8cd-177c-4fcb-b59d-3daa86cdfc4a" />

## unpackme2

<img width="835" height="731" alt="image" src="https://github.com/user-attachments/assets/ab49977c-f367-4cdb-b2a7-3e5bfc5a04ac" />

Pe x86 bị pack bằng petite

<img width="357" height="167" alt="image" src="https://github.com/user-attachments/assets/110a3060-6883-4e55-bd79-a3f0d7e0ffe5" />

Tìm oep:

<img width="1336" height="413" alt="image" src="https://github.com/user-attachments/assets/eb728c25-289f-4b11-a480-052b4ab4715c" />

<img width="942" height="58" alt="image" src="https://github.com/user-attachments/assets/a72475f3-79d7-4fb4-a28d-153cb957d318" />

Ta thấy chương trình gọi `VirtualAlloc`, đặt breakpoint sau call để lấy địa chỉ được cấp phát:

<img width="767" height="133" alt="image" src="https://github.com/user-attachments/assets/afda5f77-4a49-4106-a34d-b0dbf7b859f8" />

<img width="577" height="232" alt="image" src="https://github.com/user-attachments/assets/a69d6095-c186-4dad-8372-eedb980ec1a9" />

<img width="1322" height="372" alt="image" src="https://github.com/user-attachments/assets/07bc2850-5d46-4dca-a7c7-4e4de4ff8eaf" />

<img width="1337" height="205" alt="image" src="https://github.com/user-attachments/assets/a5b558da-e330-4898-b7c0-bfb6bdc96135" />


Chương trình cấp phát 1 vùng nhớ với quyền `RWX` chứng tỏ chương trình có thể sẽ thực thi vào đây

Ta `break on access` để chương trình ghi code vào vùng nhớ được cấp phát trên và đợi cho nó thực thi đến:

<img width="1352" height="467" alt="image" src="https://github.com/user-attachments/assets/e0b78eb2-d799-4a95-b4b3-35299795cc84" />

<img width="1327" height="432" alt="image" src="https://github.com/user-attachments/assets/97d4e40a-0bb5-4d3f-ac9a-f49c5e84fdb0" />

Tiếp tục debug ta thấy chương trình gọi `VirtualFree`:

<img width="691" height="383" alt="image" src="https://github.com/user-attachments/assets/f92d494a-bbde-43b6-b615-18002b1e4405" />

<img width="802" height="103" alt="image" src="https://github.com/user-attachments/assets/d665adfe-4b87-4751-8407-7341dc0bde3b" />

Vì đây là lệnh `jmp` nên sau khi gọi `VirtualFree` thì chương trình sẽ `ret` về địa chỉ được push cuối cùng vào đỉnh stack tức `push edx`

Trỏ vào `edx` ta thấy đây chính là oep của chương trình:

<img width="588" height="51" alt="image" src="https://github.com/user-attachments/assets/f63bd2e0-80de-4e50-ac4e-002466d9c70c" />

dump and fix import:

<img width="822" height="788" alt="image" src="https://github.com/user-attachments/assets/5941e9fa-e756-4421-b6d0-c2a121d060dd" />

<img width="670" height="437" alt="image" src="https://github.com/user-attachments/assets/5a2d2511-fdad-4d01-82c4-3180910881df" />

<img width="666" height="436" alt="image" src="https://github.com/user-attachments/assets/bb679de8-644b-4d8b-8243-b2c8a905e6e8" />

Thấy rằng bảng IAT có 1 số API bị obfuscate dạng `push -> rol -> ret` khiến cho Scylla không parse được API

Ta viết script IDAPython quét qua bảng IAT, nếu code nó trỏ đến có dạng `push -> rol -> ret` thì tính địa chỉ trả về và ghi đè lại vào IAT

script:

```python
import ida_bytes
BASE = 0x005EC000
SIZE = 128*4
def rol(x, r):
    r &= 31  
    return ((x << r) | (x >> (32 - r))) & 0xFFFFFFFF

for i in range(0, SIZE, 4):
    ea = BASE + i
    ptr = ida_bytes.get_dword(ea)
    if ida_bytes.get_byte(ptr) == 0x68 and \
       ida_bytes.get_byte(ptr+5) == 0xC1 and \
       ida_bytes.get_byte(ptr+6) == 0x04 and \
       ida_bytes.get_byte(ptr+7) == 0x24 and \
       ida_bytes.get_byte(ptr+9) == 0xC3:

        imm = ida_bytes.get_dword(ptr+1)
        r   = ida_bytes.get_byte(ptr+8)
        real = rol(imm, r)
        ida_bytes.patch_dword(ea, real)
        print(hex(ea), "->", hex(real))
print("done")
```

<img width="813" height="797" alt="image" src="https://github.com/user-attachments/assets/a00a11c2-5417-4a52-b2a1-f8c38ea588d8" />

Sau khi chạy script thì Scylla đã có thể parse được API

<img width="1918" height="1017" alt="image" src="https://github.com/user-attachments/assets/22d601d0-f3a7-4a48-a884-430c382f9039" />

<img width="1918" height="1021" alt="image" src="https://github.com/user-attachments/assets/6996bdb8-2e5a-423a-8a79-012235ee5d78" />

execute

<img width="762" height="342" alt="image" src="https://github.com/user-attachments/assets/126adbfc-316d-4714-8913-1711734dd502" />





