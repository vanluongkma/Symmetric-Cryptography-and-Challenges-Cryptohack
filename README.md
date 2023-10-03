# AES and Block Cipher Modes of Operation

![image](https://github.com/0x35p4triot/Deadline/assets/127461439/232bf645-a40b-4bf2-a1fb-49cdaa5d27d0)

- **AES (Advanced Encryption Standard)**: là một thuật toán mã hóa đối xứng 
  - AES sử dụng một khối mã hóa đối xứng với kích thước khối là 128 bit
  - Độ dài khóa là 128, 192 hoặc 256 bits tương ứng với số vòng lặp 10, 12 hoặc 14
  - AES hoạt động trên một mảng thứ tự 4 × 4 cột chính gồm 16 byte 
- **Vòng lặp chính của AES thực hiện các bước sau**:
  - SubBytes : thực hiện phép thay thế các byte của mảng trạng thái bằng cách sử dụng một bảng thay thế [AES S-box](https://en.wikipedia.org/wiki/Rijndael_S-box)
  - ShiftRows : áp dụng lên mảng trạng thái bằng cách dịch vòng ba hàng cuối của mảng trạng thái, [Image](https://en.wikipedia.org/wiki/File:AES-ShiftRows.svg)
  - MixColumns thực hiện phép nhân ma trận như [Image](https://wikimedia.org/api/rest_v1/media/math/render/svg/b35516e14dcf7ed323058752cfbe832f2db5f305)
  - AddRoundKey : một khóa vòng (Round key) sẽ được cộng vào mảng trạng thái bằng một thao tác XOR bit. [Image](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard#/media/File:AES-AddRoundKey.svg)
- **Encrypt and Decrypt AES**
- ![image](https://github.com/0x35p4triot/Deadline/assets/127461439/24a01c41-1ed4-4968-a28f-b84f1a3fe31b)
# 1. Electronic codebook (ECB)
 - AES ECB (Advanced Encryption Standard Electronic Codebook) là một chế độ hoạt động của thuật toán mã hóa AES (Advanced Encryption Standard). Đây là chế độ đơn giản nhất và dễ hiểu nhất trong các chế độ mã hóa của AES.
 - Mã hóa ECB: Plaintext là đầu vào trực tiếp để thực thi thuật toán mã hóa với khóa mã K để tạo ra ciphertext.

 - Giải mã ECB: Ciphertext là đầu vào trực tiếp để thực thi thuật toán giải mã với khóa mã K để tạo ra plaintext.

 - Ưu điểm của ECB:
      - ECB là một trong những chế độ mã hóa đơn giản nhất để hiện thực. Quá trình mã hóa và giải mã đơn giản, chỉ đơn giản là áp dụng thuật toán mã hóa lên từng khối dữ liệu.
      - Dễ dàng xử lý song song: Vì mỗi khối dữ liệu được mã hóa độc lập, việc xử lý song song trên các khối có thể được thực hiện dễ dàng. Điều này có thể đem lại tốc độ mã hóa và giải mã nhanh hơn trong một số trường hợp.
  - Nhược điểm của ECB:

      - Thiếu bảo mật: ECB không cung cấp tính bảo mật cao.
      - Không đảm bảo tính toàn vẹn
      - Không hỗ trợ xử lý dữ liệu lớn


![image](https://github.com/0x35p4triot/Deadline/assets/127461439/2b075168-8bb8-496b-a6c6-5c8bea7ed786)
![image](https://github.com/0x35p4triot/Deadline/assets/127461439/2a98fe99-a528-45c6-a2f6-976ac754434b)


```python3
from Crypto.Cipher import AES
from Crypto.Util.Padding import*

def encrypt_ecb(plaintext):
    cipher = AES.new(key, AES.MODE_ECB)
    ciphertext = cipher.encrypt(plaintext)
    return ciphertext

def decrypt_ecb(ciphertext):
    cipher = AES.new(key, AES.MODE_ECB)
    plaintext = cipher.decrypt(ciphertext)
    return unpad(plaintext, AES.block_size)

key = b'abcdefghijklmnop' #key 16bytes - AES 128
text = b'Hey guys, Im not that dumb!'
plaintext = pad(text, AES.block_size)

# enc
ciphertext = encrypt_ecb(plaintext)
print("Ciphertext (hex):", ciphertext.hex())

#dec
decrypt = decrypt_ecb(ciphertext)
print("Decrypt", decrypt)

```
___
# 2. Cipher block chaining (CBC)
 - AES CBC (Advanced Encryption Standard Cipher Block Chaining) là một chế độ hoạt động của thuật toán mã hóa AES (Advanced Encryption Standard). Đây là một trong những chế độ mã hóa phổ biến và được sử dụng rộng rãi trong các hệ thống bảo mật. Là chế độ mã hóa chuỗi, kết quả mã hóa của khối dữ liệu trước (ciphertext) sẽ được tổ hợp với khối dữ liệu kế tiếp (plaintext) trước khi thực thi mã hóa. 
 - Mã hóa CBC:
     - Lần mã hóa đầu tiên: 
       - Plaintext XOR với vector khởi tạo IV 
       - Kết quả bước trên là đầu vào cho việc thực thi thuật toán mã hóa với khóa mã K 
     - Lần mã hóa sau lần đầu tiên: 
       - Plaintext XOR với ciphertext của lần mã hóa trước đó. 
       - Kết quả bước trên là đầu vào cho việc thực thi thuật toán mã hóa với khóa mã K 
 - Giải mã CBC:

     - Lần giải mã đầu tiên:
        - Ciphertext được thực thi quá trình giải mã với khóa mã K
        - Kết quả bước trên được XOR với vector khởi tạo IV để tạo ra plaintext
     - Lần giải mã sau lần đầu tiên:
        - Ciphertext được thực thi quá trình giải mã với khóa mã K
        - Kết quả bước trên được XOR với ciphertext sử dụng trong lần giải mã trước để tạo ra plaintext
 - Ưu điểm của CBC:
    - Khả năng bảo mật cao hơn ECB. 
    - Quá trình giải mã (mã hóa nghịch) vẫn có thể thực hiện song song nhiều khối dữ liệu.
 - Nhược điểm: 
    - Thiết kế phần cứng phức tạp hơn ECB
    - Lỗi bit bị lan truyền. Nếu một lỗi bit xuất hiện trên ciphertext của một khối dữ liệu thì nó sẽ làm sai kết quả giải mã của khối đữ liệu đó và khối dữ liệu tiếp theo. 
    - Không thể thực thi quá trình mã hóa song song vì xử lý của khối dữ liệu sau phụ thuộc vào ciphertext của khối dữ liệu trước, trừ lần mã hóa đầu tiên.


![image](https://github.com/piroxxx/Exercise/assets/127461439/c53abca9-d467-4b8c-9a7d-d262a61a946d)
![image](https://github.com/piroxxx/Exercise/assets/127461439/16c3ab5d-2f13-4100-a3cc-ed8a88afe175)


```python3
from Crypto.Cipher import AES
from Crypto.Util.Padding import*

def encrypt_cbc(plaintext):
    cipher = AES.new(key, AES.MODE_CBC, iv)
    ciphertext = cipher.encrypt(plaintext)
    return ciphertext

def decrypt_cbc(ciphertext):
    cipher = AES.new(key, AES.MODE_CBC, iv)
    plaintext = cipher.decrypt(ciphertext)
    return unpad(plaintext, AES.block_size)

iv = b'1111111111111111'  #vector khởi tạo 16bytes - AES 128
key = b'abcdefghijklmnop' #key 16bytes - AES 128
text = b'Hey guys, Im not that dumb!'
plaintext = pad(text, AES.block_size)

# enc
ciphertext = encrypt_cbc(plaintext)
print("Ciphertext (hex):", ciphertext.hex())

#dec
decrypt = decrypt_cbc(ciphertext)
print("Decrypt", decrypt)
```
___
# 3. Propagating CBC (PCBC)
 - AES PCBC (Advanced Encryption Standard Propagating Cipher Block Chaining) là một chế độ hoạt động khác của thuật toán mã hóa AES (Advanced Encryption Standard). PCBC là một biến thể của chế độ CBC và thêm vào một bước thay đổi (propagation) để tăng tính ngẫu nhiên và đảm bảo tính toàn vẹn của dữ liệu.

![image](https://github.com/piroxxx/Exercise/assets/127461439/e1eb3fd6-57ce-45d0-8b7e-a68c58ee5cb7)
![image](https://github.com/piroxxx/Exercise/assets/127461439/26c9bf9b-879e-48c7-b862-a381f87a6374)


```python3
```

___
# 4. Cipher feedback (CFB)
 - AES CFB (Advanced Encryption Standard Cipher Feedback) là một chế độ hoạt động của thuật toán mã hóa AES (Advanced Encryption Standard). Đây là chế độ mã hóa mà ciphertext của lần mã hóa hiện tại sẽ được phản hồi (feedback) đến đầu vào của lần mã hóa tiếp theo. Nghĩa là, ciphertext của lần mã hóa hiện tại sẽ được sử dụng để tính toán ciphertext của lần mã hóa kế tiếp. Mô tả có vẻ giống CBC nhưng quá trình trực hiện lại khác.
 - Mã hóa CFB:
    - Lần mã hóa đầu tiên:
       - Khối dữ liệu ngõ vào của quá trình mã hóa lấy từ IV.
       - Vector khởi tạo IV được mã hóa để tạo ra một khối giá trị chứa b bit.
       - s bit MSB của kết quả trên sẽ được dùng để XOR với s bit dữ liệu (plaintext) để tạo ra s bit ciphertext.
    - Lần mã hóa sau lần đầu tiên:
       - Khối dữ liệu ngõ vào của quá trình mã hóa được ghép giã b-s bit LSB của khối ngõ vào của lần mã hóa trước đó và s bit của ciphertext của lần mã hóa trước đó.
       - Giá trị của bước trên được mã hóa để tạo ra một khối giá trị chứa b bit.
       - s bit MSB của kết quả trên sẽ được dùng để XOR với s bit dữ liệu (plaintext) để tạo ra s bit ciphertext.
 - Giải mã CFB:
 - Ưu điểm:
    - Khả năng bảo mật cao hơn ECB
    - Quá trình giải mã (mã hóa nghịch) vẫn có thể thực hiện song song nhiều khối dữ liệu.
    - Tùy biến được độ dài khối dữ liệu mã hóa, giải mã thông qua thông số s
 - Nhược điểm:
    - Thiết kế phần cứng phức tạp hơn CBC
    - Lỗi bit bị lan truyền. Nếu một lỗi bit xuất hiện trên ciphertext của một khối dữ liệu thì nó sẽ làm sai kết quả giải mã của khối đữ liệu đó và khối dữ liệu tiếp theo.
    - Không thể thực thi quá trình mã hóa song song vì xử lý của khối dữ liệu sau phụ thuộc vào ciphertext của khối dữ liệu trước, trừ lần mã hóa đầu tiên


![image](https://github.com/piroxxx/Exercise/assets/127461439/a611e42a-c566-49e2-9ab1-f87a6ae21b01)
![image](https://github.com/piroxxx/Exercise/assets/127461439/e8b3b8fa-e32a-4626-9342-2cbf0e169d66)


```python3
from Crypto.Cipher import AES
from Crypto.Util.Padding import*

def encrypt_cfb(plaintext):
    cipher = AES.new(key, AES.MODE_CFB, iv)
    ciphertext = cipher.encrypt(plaintext)
    return ciphertext

def decrypt_cfb(ciphertext):
    cipher = AES.new(key, AES.MODE_CFB, iv)
    plaintext = cipher.decrypt(ciphertext)
    return unpad(plaintext, AES.block_size)

iv = b'1111111111111111'  #vector khởi tạo 16bytes - AES 128
key = b'abcdefghijklmnop' #key 16bytes - AES 128
text = b'Hey guys, Im not that dumb!'
plaintext = pad(text, AES.block_size)

# enc
ciphertext = encrypt_cfb(plaintext)
print("Ciphertext (hex):", ciphertext.hex())

#dec
decrypt = decrypt_cfb(ciphertext)
print("Decrypt", decrypt)
```

___
# 5. Output feedback (OFB)
 - AES OFB (Advanced Encryption Standard Output Feedback) là một chế độ hoạt động của thuật toán mã hóa AES (Advanced Encryption Standard). Là một thuật toán mã hóa đối xứng được sử dụng rộng rãi để bảo vệ thông tin. Đây là chế độ mã hóa mà giá trị ngõ ra của khối thực thi thuật toán mã hóa, không phải ciphertext, của lần mã hóa hiện tại sẽ được phản hồi (feedback) đến ngõ vào của lần mã hóa kế tiếp.
 - Mã hóa OFB:
   - Lần mã hóa đầu tiên:
      - Giá trị IV được lấy làm khối giá trị đầu vào mã hóa.
      - Thực thi giải thuật mã hóa cho khối trên với khóa mã K.
      - XOR plaintext và kết quả của bước trên.
   - Lần mã hóa sau lần đầu tiên và trước lần cuối cùng:
      - Giá trị của khối ngõ ra (output block) trước đó được lấy làm khối giá trị đầu vào mã hóa.
      - Thực thi giải thuật mã hóa cho khối trên với khóa mã K.
      - XOR plaintext và kết quả của bước trên.
   - Lần mã hóa cuối cùng:
      - Giá trị của khối ngõ ra (output block) trước đó được lấy làm khối giá trị đầu vào mã hó.
      - Thực thi giải thuật mã hóa cho khối trên với khóa mã K.
      - XOR plaintext và với các bit MSB của kết quả của bước trên theo độ dài của plaintext.
 - Giải mã OFB:
   - Lần giải mã đầu tiên:
      - Giá trị IV được lấy làm khối giá trị đầu vào mã hóa.
      - Thực thi giải thuật mã hóa cho khối trên với khóa mã K.
      - XOR ciphertext và kết quả của bước trên.
   - Lần giải mã sau lần đầu tiên và trước lần cuối cùng:
      - Giá trị của khối ngõ ra (output block) trước đó được lấy làm khối giá trị đầu vào mã hóa.
      - Thực thi giải thuật mã hóa cho khối trên với khóa mã K.
      - XOR ciphertext và kết quả của bước trên.
   - Lần giải mã cuối cùng (thứ n):
      - Giá trị của khối ngõ ra (output block) trước đó được lấy làm khối giá trị đầu vào mã hóa.
      - Thực thi giải thuật mã hóa cho khối trên với khóa mã K.
      - XOR ciphertext và với các bit MSB của kết quả của bước trên theo độ dài của ciphertext.
 - Ưu điểm:
    - Khả năng bảo mật cao hơn ECB. Ciphertext của một khối dữ liệu plaintext có thể khác nhau cho mỗi lần mã hóa vì nó phụ thuộc vào IV hoặc khối ngõ ra của lần mã hóa trước đó.
    - Lỗi bit không bị lan truyền. Khi một lỗi bit xuất hiện trên một ciphertext, nó chỉ ảnh hưởng đến kết quả giải mã của khối dữ liệu hiện tại
    - Thiết kế phần cứng đơn giản hơn CFB.
 - Nhược điểm:
    - Không thể thực hiện mã hóa/giải mã song song nhiều khối dữ liệu vì lần mã hóa/giải mã sau phụ thuộc vào khối ngõ ra của lần mã hóa/giải mã liền trước nó.    
![image](https://github.com/piroxxx/Exercise/assets/127461439/55f85c24-8339-4789-a96a-6172868b67f2)
![image](https://github.com/piroxxx/Exercise/assets/127461439/5a55d636-1c97-4427-9ca9-f12d850300b5)


```python3
from Crypto.Cipher import AES
from Crypto.Util.Padding import*

def encrypt_ofb(plaintext):
    cipher = AES.new(key, AES.MODE_OFB, iv)
    ciphertext = cipher.encrypt(plaintext)
    return ciphertext

def decrypt_ofb(ciphertext):
    cipher = AES.new(key, AES.MODE_OFB, iv)
    plaintext = cipher.decrypt(ciphertext)
    return unpad(plaintext, AES.block_size)

iv = b'1111111111111111'  #vector khởi tạo 16bytes - AES 128
key = b'abcdefghijklmnop' #key 16bytes - AES 128
text = b'Hey guys, Im not that dumb!'
plaintext = pad(text, AES.block_size)

# enc
ciphertext = encrypt_ofb(plaintext)
print("Ciphertext (hex):", ciphertext.hex())

#dec
decrypt = decrypt_ofb(ciphertext)
print("Decrypt", decrypt)

```

___
# 6. Counter(CTR)
 - AES-CTR (Advanced Encryption Standard - Counter Mode) là một chế độ hoạt động của thuật toán AES (Advanced Encryption Standard) được sử dụng để mã hóa thông tin. Đây là chế độ mã hóa sử dụng một tập các khối ngõ vào, gọi là các counter, để sinh ra một tập các giá trị ngõ ra thông qua một thuật toán mã hóa. Sau đó, giá trị ngõ ra sẽ được XOR với plaintext để tạo ra ciphertext trong quá trình mã hóa, hoặc XOR với ciphertext để tạo ra plaintext trong quá trình giải mã.
 - Mã hóa CTR:
    - Giá trị của khối ngõ ra (output block) được tạo từ giá trị của khối bộ đếm thứ j bằng cách thực thi giải thuật mã hóa với khóa K.
    - Giá trị trên được XOR với plaintext thứ j để tạo ra ciphertext thứ j.
    - Đối với khối dữ liệu cuối cùng của chuỗi dữ liệu, khối thứ n, nếu độ dài bit của plaintext ít hơn độ dài bit được quy định bởi chuẩn mã hóa thì chỉ lấy các bit trọng số cao của khối ngõ ra (output block) XOR với plaintext.
 - Giải mã CTR:
    - Giá trị của khối ngõ ra (output block) được tạo từ giá trị của khối bộ đếm thứ j bằng cách thực thi giải thuật mã hóa với khóa K.
    - Giá trị trên được XOR với ciphertext thứ j để tạo ra plaintext thứ j.
    - Đối với khối dữ liệu cuối cùng của chuỗi dữ liệu, khối thứ n, nếu độ dài bit của ciphertext ít hơn độ dài bit được quy định bởi chuẩn mã hóa thì chỉ lấy các bit trọng số cao của khối ngõ ra (output block) XOR với ciphertext.

 - Ưu điểm:
    - Khả năng bảo mật cao hơn ECB. Tuy quá trình mã hóa/giải mã của mỗi khối dữ liệu là độc lập nhưng mỗi plaintext có thể ảnh xạ đến nhiều ciphertext tùy vào giá trị bộ đếm của các lần mã hóa. 
    - Có thể mã hóa/giải mã song song nhiều khối dữ liệu.
 - Nhược điểm: Phần cứng cần thiết kế thêm các bộ đếm counter hoặc giải thuật tạo các giá trị counter không lặp lại. 


![image](https://github.com/piroxxx/Exercise/assets/127461439/852a7f21-f212-4a49-877d-27569c73c92c)
![image](https://github.com/piroxxx/Exercise/assets/127461439/c9d1f4e0-9163-4520-bcec-43c919a1576c)


```python3
from Crypto.Cipher import AES
from Crypto.Util import*
import secrets

def encrypt_ctr(plaintext):
    counter = Counter.new(128, initial_value=int.from_bytes(nonce, byteorder='big'))   # Tạo đối tượng counter
    cipher = AES.new(key, AES.MODE_CTR, counter=counter)
    ciphertext = cipher.encrypt(plaintext)
    return ciphertext

def decrypt_ctr(ciphertext):
    counter = Counter.new(128, initial_value=int.from_bytes(nonce, byteorder='big'))   # Tạo đối tượng counter
    cipher = AES.new(key, AES.MODE_CTR, counter=counter)
    plaintext = cipher.decrypt(ciphertext)
    return plaintext
 
nonce = secrets.token_bytes(8) #radom nonce ngẫu nhiên 8bytes
key = b'abcdefghijklmnop'      #key 16bytes - AES 128
plaintext = b'Hey guys, Im not that dumb!'
 
#enc
ciphertext = encrypt_ctr(plaintext)
print('Ciphertext (hex): ', ciphertext.hex())

#dec
decrypted = decrypt_ctr(ciphertext)
print('Decrypted: ', decrypted)

```
 -	s – số bit của một đoạn dữ liệu
 -	b – kích thước theo bit của một khối (block)
 -	IV - Vector khởi tạo (Initialization Vector)
 -	K - Khóa mã
___
- Tham khảo thêm tại: [Advanced Encryption Standard (AES)](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) & [Block cipher mode of operation](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation)
