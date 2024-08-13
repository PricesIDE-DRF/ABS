# ABS
Abstract Behavioral Specification (ABS) is a formal modeling language and framework designed for specifying and analyzing distributed systems. It combines object-oriented and concurrent programming concepts with formal methods, offering features such as object-oriented modeling, concurrent programming, functional programming elements, and support for formal verification. ABS is characterized by its modular design, which facilitates separation of concerns and modular reasoning.

In the context of Software Product Line Engineering (SPLE), ABS provides valuable tools for managing variability and developing families of related software products. Its delta-oriented programming approach allows for flexible implementation of product variations, while its support for feature models and product line configurations enables the specification of valid feature combinations and automatic product generation. ABS's formal foundations permit verification of properties across entire product lines, ensuring correctness across all variants. 

This repository showcased the study case for using ABS in context of Delta oriented programming with payment gateway as its topic. 
## How to run
### Running on Browser-Based IDE
- Run this docker image
  `docker run -d --rm -p 8080:80 --name collaboratory abslang/collaboratory:latest`
- Open localhost:8080
- Paste the code snippet on the browser

### Running on local
- Clone the repo
- Run this command
  `java -jar "path/to/absfrontend.jar" "path/to/[file_name].abs"`

## Summary of The Abstract Behavioral Specification Language: A Tutorial Introduction
ABS (Abstract Behavioral Specification) menargetkan sistem perangkat lunak yang bersifat concurrent, distributed, object-oriented, berbasis komponen, dan highly reusable. ABS mengintegrasikan feature models sebagai first-class language concept untuk mendukung product line engineering (PLE). Sebagai bahasa abstrak, ABS cocok untuk memodelkan perangkat lunak yang akan digunakan di lingkungan virtual. ABS menggunakan konsep deployment components untuk merepresentasikan konsep low-level seperti system time, memory, latency, atau scheduling pada level model abstrak. ABS bukan hanya notasi pemodelan, tetapi juga dilengkapi dengan tool set terintegrasi untuk mengotomatisasi proses rekayasa perangkat lunak dengan fokus utama menjamin _predictability of results_, _interoperability_, _usability_. ABS sepenuhnya dapat dijalankan (meskipun dengan cara yang tidak deterministik) dan mendukung pembuatan kode ke Java, Scala, dan Maude. Selain itu, memungkinkan untuk mempelajari model ABS dari perilaku yang diamati. 

### Arsitektur ABS
Arsitektur ABS diorganisir dalam tumpukan layer yang jelas terpisah. Desain ABS bertujuan untuk:
1. Bahasa yang menarik dan mudah dipelajari dengan sintaks yang familiar
2. Pemisahan maksimal antar konsep yang berbeda (orthogonality)

Struktur layer ABS:
1. Empat layer terbawah: Bahasa pemrograman modern berbasis kombinasi algebraic data types (ADTs), pure functions, dan bahasa imperative-OO sederhana.
2. Dua layer berikutnya: Concurrency tightly coupled dan distributed.
3. Layer di atas "Core ABS": Ekstensi orthogonal untuk product line engineering, deployment components, dan runtime components.

Fitur utama ABS:
1. Fully executable, namun mendukung abstraksi melalui:
   - Hanya lima built-in datatypes
   - Fungsi pada datatypes bisa underspecified
   - Penjadwalan concurrent tasks dan urutan message queueing bersifat non-deterministic
2. Memungkinkan spesifikasi perilaku parsial pada tahap desain awal
3. Compact language, namun memiliki lebih banyak non-terminals dibanding Java karena mencakup konsep-konsep tambahan
4. Mendukung spektrum pemodelan dari analisis fitur hingga implementasi
5. Foreign language interface untuk integrasi dengan Java
6. Deployment components untuk spesifikasi concrete schedulers

ABS dirancang untuk mudah digunakan dan fleksibel, meskipun mencakup berbagai konsep bahasa yang kompleks. Tujuannya adalah untuk mendukung seluruh proses pemodelan dari analisis fitur, pemetaan deployment, desain tingkat tinggi, hingga masalah implementasi.

### Product Line Engineering dengan ABS
Salah satu tujuan dari ABS adalah untuk menyediakan framework formal dan seragam untuk PLE. Fase dalam PLE dapat terbagi menjadi dua, yaitu: 
1. Family engineering: Mengidentifikasi _commonality_ antar produk dan merencanakan _variability_.
2. Application engineering: Membangun produk individual dengan memilih fitur dan mengkombinasikan artefak.
   <img width="532" alt="image" src="https://github.com/user-attachments/assets/5a32731c-7359-4421-8210-6bf1006bf44f">
   
Secara singkat elemen ABS untuk pemodelan product line:
1. Feature description language
2. Delta yang memodifikasi model ABS
3. Configuration language yang menghubungkan fitur dengan delta (bisa juga disebut product line configuration)
4. Bahasa untuk konfigurasi produk

## Feature Description
Dalam ABS, digunakan sedikit modifikasi dari bahasa variabilitas tekstual (TVL), yang memiliki keuntungan berupa semantik formal dan representasi tekstual. Varian bahasa yang digunakan dalam ABS disebut μTVL dan berbeda dari TVL dalam dua hal: 
1. Tipe atribut yang tidak diperlukan dihilangkan
2. Adanya kemungkinan untuk memiliki beberapa fitur root, berguna untuk memodelkan variabilitas ortogonal dalam garis produk
Sebagai contoh:
```abs
root Account {
  group allof {
    Type {
      group oneof {
        Check {ifin: Type.i == 0;},
        Save {ifin: Type.i > 0;
              exclude: Overdraft; }
        Int i; // interest rate
    },
    opt Fee {Int amount in [0..5];},
    opt Overdraft
    }
}
```

## Delta Modelling
Realisasi fitur menggunakan delta modules (deltas), varian dari delta-oriented programming (DOP). Dengan menggunakan delta modules ini, kita dapat memisahkan pemodelan data dan variabilitas, dan memiliki basis yang baik untuk spesifikasi dan verifikasi modular perangkat lunak kaya fitur. 
Prinsip delta modeling pada ABS sama dengan berbagai bahasa modelling lainnya, yaitu:
1. Dimulai dengan core/base product dengan fungsionalitas minimal
2. Produk varian dibuat dengan menerapkan satu atau lebih delta

Adapun delta pada ABS dapat melakukan:
1. Menambah, menghapus, atau memodifikasi kelas dan interface
2. Modifikasi kelas meliputi: penambahan/penghapusan fields, penambahan/penghapusan/modifikasi methods, dan _extending_ interface yang diimplementasi

Method modification bisa menggunakan statement `original()` untuk mengakses untuk mengakses versi method sebelumnya. Walaupun mirip dengan super() pada OO, ada beberapa perbedaan diantaranya adalah `original()` diselesaikan saat compile time, tidak ada penalti runtime, dan semantik lebih sederhana. 
Contoh code: 
1. Implementasi interface Account
```
class AccountImpl(Int aid, Int balance, Customer owner) implements Account {
  Int withdraw(Int x) {
    if (balance - x >= 0) { balance = balance - x; } return balance;
  }
}
```
2. Delta untuk modifikasi fitur withdraw
```
delta DFee(Int fee); // Implements feature Fee uses Account;
  modifies class AccountImpl {
    modifies Int withdraw(Int x) {
    Int result = x;
    if (x>=fee) result = original(x+fee); return result;
  }
}
```

### Product Line Configuration
Untuk menghubungkan antara _feature model_ dengan _delta model_ pada ABS harus menyediakan sebuah file yang bertanggung jawab untuk menjadi koneksi antara keduanya, atau dengan kata lain _product line configuration_.
<img width="608" alt="image" src="https://github.com/user-attachments/assets/ca5edcac-9382-40a0-81e6-2cda1e4689f6">

Terdapat tiga aspek penting dalam delta yang dipakai dalam configuration, yaitu: 
1. Application conditions (klausa when): Menentukan fitur yang direalisasikan oleh delta, kehadiran fitur memicu aplikasi delta
2. Parameter delta: Diturunkan dari nilai atribut fitur. Contoh: Type.i dan Fee.amount digunakan langsung sebagai parameter delta
3. Urutan aplikasi delta (klausa after): Menentukan aplikasi delta, well-definedness aplikasi delta dan menyelesaikan konflik

Contoh: 
```
productline Accounts;
features Type, Fee, Overdraft, Check, Save;
delta DType (Type.i) when Type;
delta DFee (Fee.amount) when Fee;
delta DOverdraft after DCheck when Overdraft;
delta DSave (Type.i) after DType when Save;
delta DCheck after DType when Check;
```

### Product Selection
Merupakan tahap terakhir dari PLE. Termasuk dalam fase application engineering, berbeda dengan langkah-langkah sebelumnya yang umumnya bagian dari family engineering. Proses yang dilakukan pada tahap ini cukup dengan mendaftarkan fitur-fitur yang ingin direalisasikan dalam produk dan menginisialisasi atribut-atribut fitur dengan nilai yang diinginkan. 

Contoh:
```
product CheckingAccount (Type{i=0},Check);
product AccountWithFee (Type{i=0},Check,Fee{amount=1});
product AccountWithOverdraft (Type{i=0},Check,Overdraft);
product SavingWithOverdraft (Type{i=1},Save,Overdraft);
```


