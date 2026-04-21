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

## unpackme3

<img width="673" height="591" alt="image" src="https://github.com/user-attachments/assets/d4bd8680-753d-40cc-bca8-19fd22b2ca80" />

Pefile bị pack bằng ASPack 2.x

Tương tự upx, ta tìm `popa` là tìm được oep

<img width="542" height="419" alt="image" src="https://github.com/user-attachments/assets/dc006615-e1cc-4311-afc8-aa1ed6797fe0" />

<img width="701" height="267" alt="image" src="https://github.com/user-attachments/assets/04443175-8591-440a-af86-912630bf86d4" />

<img width="668" height="63" alt="image" src="https://github.com/user-attachments/assets/989b897a-79d2-4eb3-bd62-571425d0e983" />

<img width="658" height="641" alt="image" src="https://github.com/user-attachments/assets/2b4de417-3c64-4b84-84c4-71111330df97" />

bỏ node invalid, dump và fixdump:

<img width="1919" height="1026" alt="image" src="https://github.com/user-attachments/assets/3e6b2506-447f-461f-8b45-7a035cc79721" />

<img width="1915" height="1033" alt="image" src="https://github.com/user-attachments/assets/3a1394a2-bb55-40c5-99b7-16293fb40401" />

execute:

<img width="1919" height="1024" alt="image" src="https://github.com/user-attachments/assets/d819efef-bc1a-47bc-b621-0037b2605a25" />

## unpackme5

<img width="671" height="582" alt="image" src="https://github.com/user-attachments/assets/7c68a981-c4d4-48e7-a004-0fb8d1e04d8a" />

Pefile bị pack bằng MPRESS 2.x

Bài này ta tìm oep bằng cách dõi theo luồng dựa trên dấu hiệu đã bị bật messagebox hay chưa

<img width="536" height="254" alt="image" src="https://github.com/user-attachments/assets/e1065360-b49d-4ee0-8ffa-c0725ee59d5b" />

<img width="1071" height="463" alt="image" src="https://github.com/user-attachments/assets/c0ac62b0-c58c-40e8-8a9f-866390833124" />

<img width="1330" height="487" alt="image" src="https://github.com/user-attachments/assets/47859bfc-00d9-44df-b06a-f7b373a2d019" />

<img width="1323" height="482" alt="image" src="https://github.com/user-attachments/assets/6af7148d-00e2-43c1-85ff-dbb7f2619af6" />

<img width="1337" height="472" alt="image" src="https://github.com/user-attachments/assets/4dabebec-19d4-415b-a4c5-983920c7bf8b" />

<img width="1343" height="395" alt="image" src="https://github.com/user-attachments/assets/b1f3240f-7fa4-4372-972d-b1dfb7b6c1cd" />

<img width="1351" height="475" alt="image" src="https://github.com/user-attachments/assets/c186a11a-ba3e-4837-89df-dbfc5e649c40" />

<img width="455" height="90" alt="image" src="https://github.com/user-attachments/assets/af49d6f5-4e9a-4acc-9393-8afa40ecfb50" />

Ta thấy đã vào oep

dump and fix import:

<img width="654" height="640" alt="image" src="https://github.com/user-attachments/assets/e686d911-1c49-4921-9660-785ccea0bf41" />

<img width="1917" height="1028" alt="image" src="https://github.com/user-attachments/assets/2af7eaab-4d6e-43fc-bb09-be59ade755cc" />

<img width="1916" height="1033" alt="image" src="https://github.com/user-attachments/assets/f56bc4ef-19d6-4b56-b019-1a06297ca929" />

execute:

<img width="1919" height="1031" alt="image" src="https://github.com/user-attachments/assets/908d2aab-657f-4252-ae13-6d408f9c8540" />

## unpackme6

<img width="675" height="592" alt="image" src="https://github.com/user-attachments/assets/5083a0b8-7592-4086-baf5-d3a0f3ee4cdb" />

Pefile bị pack bằng VMProtect 3.0.9

Để có thể reverse hết stub của vmp để tìm oep thì rất khó, vì vậy ta cần dựa vào dự đoán kinh nghiệm

Ta biết rằng chương trình C/C++ được compile bằng MSVC thì mặc định đều có pattern init C stub giống nhau, trừ khi dev cố tình tắt đi

Ta có thể dựa vào API `GetSystemTimeAsFileTime` để xref ngược về oep:

<img width="812" height="559" alt="image" src="https://github.com/user-attachments/assets/21d05625-f191-40ea-ae02-28bb323980c9" />

<img width="779" height="58" alt="image" src="https://github.com/user-attachments/assets/95a7120d-e45a-4db1-af74-f009dcd110ad" />

Ta đặt breakpoint tại `GetSystemTimeAsFileTime` để đợi vmp stub tự unpack pefile xong:

<img width="1919" height="1033" alt="image" src="https://github.com/user-attachments/assets/ff524f97-ac9f-4c50-b0d8-64e3a26a99cc" />

Đỉnh stack là địa chỉ mà hàm return

<img width="575" height="233" alt="image" src="https://github.com/user-attachments/assets/b01c16a7-c99a-468f-bb6a-bda6c782afd3" />

<img width="1919" height="1026" alt="image" src="https://github.com/user-attachments/assets/c6e14000-63ef-4f04-951f-328f3cd23774" />

Tuy nhiên khi trỏ vào các hàm, ta có thể thấy import đã bị protect, khiến khó khăn cho việc tìm oep và fix import:

<img width="954" height="393" alt="image" src="https://github.com/user-attachments/assets/4237893a-5057-4c90-aa98-12abb2effc01" />

<img width="931" height="350" alt="image" src="https://github.com/user-attachments/assets/f7bc370e-f271-4202-b6bb-71cea4c2b711" />

Ta dùng https://github.com/KuNgia09/vmp3-import-fix để fix import

<img width="1327" height="224" alt="image" src="https://github.com/user-attachments/assets/62cfb369-fd30-41f0-b0cf-504c393ae680" />

mở file đã được fix:

<img width="1919" height="857" alt="image" src="https://github.com/user-attachments/assets/9047c7e3-bbdb-4404-8022-f6448c04e8fa" />

<img width="1919" height="1025" alt="image" src="https://github.com/user-attachments/assets/8f20b08f-0821-40d5-a2c3-d6acb1801d25" />

xref về oep:

<img width="491" height="50" alt="image" src="https://github.com/user-attachments/assets/e1ddf0bd-e1e2-4c65-a72c-392856af0a28" />

Tuy nhiên vì khi dump ta chưa trỏ oep đúng nên cần fix entrypoint:

<img width="759" height="492" alt="image" src="https://github.com/user-attachments/assets/5f45912e-845c-4534-916e-9683632bdb70" />

<img width="1919" height="1029" alt="image" src="https://github.com/user-attachments/assets/c28f4795-a1b8-406c-97d5-96c6a9204809" />



## unpackme8

<img width="724" height="628" alt="image" src="https://github.com/user-attachments/assets/044f9079-e348-418b-a874-759441e56cf5" />


<img width="1919" height="1027" alt="image" src="https://github.com/user-attachments/assets/0a41bb2a-afd0-408f-b8d4-781e358ce43f" />

Bài này ta nhận thấy chương trình VirtualAlloc khá nhiều lần và thực thực thi code trong đó

Để tìm oep ta cho `break on access` ở 1 section được alloc rồi lại `break on access` tại section chứa code của pe bị pack:

<img width="720" height="404" alt="image" src="https://github.com/user-attachments/assets/32e22c6f-74b8-412f-a1ba-3c067244c4b2" />

<img width="730" height="408" alt="image" src="https://github.com/user-attachments/assets/605838c9-f142-40ac-85aa-abb9905ca081" />

Dặt breakpoint lại section được alloc

<img width="567" height="238" alt="image" src="https://github.com/user-attachments/assets/f66f0bac-5352-47b6-8f3c-d173525688d2" />

<img width="1329" height="446" alt="image" src="https://github.com/user-attachments/assets/c0a3a8c2-e224-477e-97ef-11a0c449dddd" />

ta gỡ breakpoint tại đây và đặt breakpoint tại section được chương trình unpack pe file ra:

<img width="1311" height="451" alt="image" src="https://github.com/user-attachments/assets/4a86bafd-5698-40a1-9db4-b0581db1a5f9" />

<img width="1341" height="412" alt="image" src="https://github.com/user-attachments/assets/5b6908ed-eba6-4254-8f44-dd1040b4e917" />

<img width="1339" height="439" alt="image" src="https://github.com/user-attachments/assets/05b6effe-ac38-4235-8190-ba97451b1472" />

Ta đã đên được oep

dump and fix import:

Ta thử dump và fix dump bằng scylla:

<img width="591" height="638" alt="image" src="https://github.com/user-attachments/assets/db036aa1-d0e7-4b30-9e4c-ff0b4f4a6ce4" />

<img width="415" height="262" alt="image" src="https://github.com/user-attachments/assets/832f15d4-5362-4244-9bd7-c3f7744714c0" />

mở ida để biết tại sao:

<img width="1919" height="1029" alt="image" src="https://github.com/user-attachments/assets/d4b63a47-cf4d-499a-a190-0b96145baad1" />

<img width="1919" height="1032" alt="image" src="https://github.com/user-attachments/assets/c9c7c619-c536-45ad-a192-9f2fe8e32f7c" />

Ta thấy phần lớn import đã được fix khá ok, tuy nhiên một số API chưa được fix

kiểm tra trên mem gốc:

<img width="1003" height="219" alt="image" src="https://github.com/user-attachments/assets/bb6b5b55-bcd7-479c-b483-77460d22033c" />

<img width="515" height="130" alt="image" src="https://github.com/user-attachments/assets/d412013f-29e2-4b1e-8755-6cdf22bcc592" />

<img width="880" height="158" alt="image" src="https://github.com/user-attachments/assets/5b33a167-201b-4e92-b6bb-cbc56d420599" />

<img width="970" height="323" alt="image" src="https://github.com/user-attachments/assets/10849332-c20e-4c2c-ab9a-2b9d36a74e66" />

Thấy rằng chương trình thay vì call thẳng đến API thì một phần api bị đưa vào các thunk, nối nhau bằng jmp và push + ret khiến cho việc xác định API được gọi khá khó khăn 

Ta có thể trace theo push/jmp để đến được section có chứa .dll rồi lấy symbols export tại đó để xác định API được gọi là gì

Tuy nhiên một số API còn bị nuốt hẳn vào thunk mà không đến được section .dll như sau:

<img width="576" height="128" alt="image" src="https://github.com/user-attachments/assets/061a49cf-b050-4601-9ebc-1727de9cf253" />

<img width="785" height="187" alt="image" src="https://github.com/user-attachments/assets/e2350318-43d1-4f93-bc58-41d224489e39" />

<img width="976" height="102" alt="image" src="https://github.com/user-attachments/assets/7076f6fa-6e8c-4752-981c-b55de26e28cc" />

Ta có thể dump luôn section này ra chung với pe để chương trình cô thể gọi vào.

Ý tưởng:
- Tìm các call tương đối `0xe8` và phải call ra ngoài section khác (để tránh nhầm với call hàm thông thường)
- trace các ins theo push-ret và jmp để tìm đến section có chứa `.dll`, khi đến nơi ta duyệt ngược lên để lấy tên API (vì bị gọi vào giữa hàm)
- duyệt các API để lấy các import không bị obfuscate kết hợp với các API vừa rồi để fix IAT
- trong IAT, các API đã được resolve sẵn và các địa chỉ trỏ đến ntdll nhưng không có symbols thì ta giữ nguyên vị trí, các vị trí thừa thì sẽ ghi các API mới tìm được vào
- patch các call tương đối trên thành call tuyệt đối `0xff` để call vào IAT


script fix iat:

```python
import idaapi
import idc
import ida_bytes
import ida_segment
import ida_name
import ida_ua

START = 0x401000
END   = 0x40C000

IAT_START = 0x40C008
IAT_SIZE  = 0x134

MAX_TRACE_STEPS = 512
visited_global  = {}

def seg_name(ea):
    s = ida_segment.getseg(ea)
    return ida_segment.get_segm_name(s) if s else None

def is_in_code_seg(ea):
    s = ida_segment.getseg(START)
    return bool(s) and (s.start_ea <= ea < s.end_ea)

def dll_prefix(ea):
    n = seg_name(ea)
    if not n:
        return None
    nl = n.lower()
    if nl.endswith(".dll"):
        return nl[:-4]
    return None

def is_dll_seg(ea):
    return dll_prefix(ea) is not None

def get_raw_name(ea):
    for getter in (ida_name.get_ea_name, idc.get_name, idc.get_func_name):
        try:
            n = getter(ea)
        except TypeError:
            n = getter(ea, 0) if getter is ida_name.get_ea_name else None
        if n:
            return n
    return None

def resolve_in_dll(ea, back=128):
    prefix = dll_prefix(ea)
    if not prefix:
        return None, None
    needle = prefix + "_"
    for off in range(0, back + 1):
        cur = ea - off
        if dll_prefix(cur) != prefix:
            break
        n = get_raw_name(cur)
        if n and n.lower().startswith(needle):
            return n, cur
    return None, None

def decode(ea):
    insn = ida_ua.insn_t()
    return insn if ida_ua.decode_insn(insn, ea) > 0 else None

def trace_chain(start_ea):
    ea = start_ea
    steps = 0
    seen = set()

    while steps < MAX_TRACE_STEPS:
        steps += 1
        if ea is None or ea == idaapi.BADADDR:
            return None, None

        if is_dll_seg(ea):
            name, name_ea = resolve_in_dll(ea)
            if name:
                return name, name_ea

        if ea in seen:
            return None, None
        seen.add(ea)

        insn = decode(ea)
        if insn is None:
            return None, None

        mnem = insn.get_canon_mnem().lower()
        op0  = insn.ops[0]

        if mnem == "jmp":
            if op0.type in (idaapi.o_near, idaapi.o_far):
                ea = op0.addr
                continue
            if op0.type == idaapi.o_mem:
                tgt = op0.addr
                if is_dll_seg(tgt):
                    return resolve_in_dll(tgt)
                ptr = ida_bytes.get_dword(tgt)
                if ptr:
                    ea = ptr
                    continue
                return None, None
            return None, None

        if mnem == "push":
            nxt = decode(ea + insn.size)
            if nxt and nxt.get_canon_mnem().lower() in ("ret", "retn", "retf"):
                tgt = None
                if op0.type == idaapi.o_imm:
                    tgt = op0.value & 0xFFFFFFFF
                elif op0.type in (idaapi.o_near, idaapi.o_far, idaapi.o_mem):
                    tgt = op0.addr & 0xFFFFFFFF
                if tgt:
                    ea = tgt
                    continue
            ea += insn.size
            continue

        ea += insn.size

    return None, None

def read_existing_iat():
    name2slot = {}
    free_slots = []
    for slot in range(IAT_START, IAT_START + IAT_SIZE, 4):
        ptr = ida_bytes.get_dword(slot)
        name = None
        prefix = None
        if ptr:
            name, _ = resolve_in_dll(ptr)
            prefix = dll_prefix(ptr)
        if name:
            if name not in name2slot:
                name2slot[name] = slot
        elif prefix == "ntdll":
            continue
        else:
            free_slots.append(slot)
    return name2slot, free_slots

def ensure_iat_slot(name, api_ea, name2slot, state):
    if name in name2slot:
        return name2slot[name]
    if state["free"]:
        slot = state["free"].pop(0)
    else:
        slot = state["overflow"]
        state["overflow"] = slot + 4
    ida_bytes.patch_dword(slot, api_ea)
    idc.set_name(slot, "IAT_" + name, idc.SN_NOWARN | idc.SN_NOCHECK)
    name2slot[name] = slot
    return slot

def patch_call_to_iat(call_ea, slot_ea):
    ida_bytes.patch_byte(call_ea, 0xFF)
    ida_bytes.patch_byte(call_ea + 1, 0x15)
    ida_bytes.patch_dword(call_ea + 2, slot_ea & 0xFFFFFFFF)

def main():
    print("[*] Scan {:#x}..{:#x} for obfuscated E8 calls".format(START, END))
    resolved = []
    ea = START
    while ea < END:
        if ida_bytes.get_byte(ea) == 0xE8:
            insn = decode(ea)
            if insn and insn.get_canon_mnem().lower() == "call":
                op0 = insn.ops[0]
                if op0.type in (idaapi.o_near, idaapi.o_far):
                    target = op0.addr
                    if not is_in_code_seg(target):
                        if ea in visited_global:
                            entry = visited_global[ea]
                        else:
                            entry = trace_chain(target)
                            visited_global[ea] = entry
                        name, api_ea = entry
                        if name and api_ea:
                            resolved.append((ea, name, api_ea))
                            print("  {:#010x}  call {:#010x}  ->  {}  @{:#010x}".format(
                                ea, target, name, api_ea))
                        else:
                            print("  {:#010x}  call {:#010x}  ->  <unresolved>".format(ea, target))
                ea += insn.size
                continue
        ea += 1
    print("[*] Resolved {} wrapper calls".format(len(resolved)))

    print("[*] Scan existing IAT [{:#x}..{:#x})".format(IAT_START, IAT_START + IAT_SIZE))
    name2slot, free_slots = read_existing_iat()
    print("    kept API entries : {}".format(len(name2slot)))
    print("    free slots       : {}".format(len(free_slots)))

    state = {"free": free_slots, "overflow": IAT_START + IAT_SIZE}
    new_slots = 0
    patched   = 0
    for call_ea, name, api_ea in resolved:
        before = len(name2slot)
        slot = ensure_iat_slot(name, api_ea, name2slot, state)
        if len(name2slot) > before:
            new_slots += 1
            print("  + IAT slot {:#x} = {} ({:#x})".format(slot, name, api_ea))
        patch_call_to_iat(call_ea, slot)
        idc.set_cmt(call_ea, "call ds:[{:#x}] ; {}".format(slot, name), 0)
        patched += 1

    print("[*] {} new IAT slots filled, {} calls patched".format(new_slots, patched))
    print("[*] IAT range [{:#x}..{:#x}), overflow up to {:#x}".format(
        IAT_START, IAT_START + IAT_SIZE, state["overflow"]))

if __name__ == "__main__":
    main()
```

Ta dump các section chung với PE, nơi mà các API bị nuốt vào thunk không tìm được symbols

<img width="1919" height="1032" alt="image" src="https://github.com/user-attachments/assets/8468faaa-a5fa-471e-906a-c1d8c7d8823d" />

<img width="1919" height="1032" alt="image" src="https://github.com/user-attachments/assets/2a26c38d-d2e0-40dd-acb2-7d1c31687ced" />



Về lý thuyết thì đến đây đã gần xong vì gần như đã fix được toàn bộ import, tuy nhiên:

<img width="411" height="260" alt="image" src="https://github.com/user-attachments/assets/a1332a87-1f70-480b-b4b0-7dfd83cb15ec" />

<img width="404" height="95" alt="image" src="https://github.com/user-attachments/assets/4b10eab6-b89b-446d-9d65-31f08bc3e482" />

<img width="316" height="161" alt="image" src="https://github.com/user-attachments/assets/f743a174-b75b-40ca-81d6-6160e94035a9" />

ta thấy chương trình push args vào stack nhưng lại call 2 lần, dẫn đến sai args

chỉ cần sửa lại thành jmp là được 
















