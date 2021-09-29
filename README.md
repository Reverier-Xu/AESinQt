# AES in Qt

## !IMPORTANT

Intel CPUs has AESNI instructions to accelerate AES, but this project doesn't use it.

plz don't use this project if you are care about performance issues.

## Usage

just copy these files into your project, place at anywhere you want.

### Available Methods
```
// Encode of rawText with key
// iv is used in CBC mode
// return the encrypted byte array
QByteArray encrypt(const QByteArray rawText, const QByteArray key, const QByteArray iv = QByteArray());

// Decode of rawText with key
// iv is used in CBC mode
// return the decrypted byte array
QByteArray decrypt(const QByteArray rawText, const QByteArray key, const QByteArray iv = QByteArray());

// Key expansion in Rijndael schedule
// return the new expanded key as byte array
QByteArray expandKey(const QByteArray key);
```
The same methods are available as static calls
```
QAESEncryption::Encrypt => encrypt(...)
QAESEncryption::Decrypt => decrypt(...)
QAESEncryption::ExpandKey => expandKey(...)
```

#### AES Levels
The class supports all AES key lenghts

* AES_128
* AES_192
* AES_256

#### Modes
The class supports the following operating modes

* ECB
* CBC
* CFB
* OFB

#### Padding
By default the padding method is `ISO`, however, the class supports:

* ZERO
* PKCS7
* ISO

### Example
Sample code using a 128bit key in ECB mode
```
#include "qaesencryption.h"

QAESEncryption encryption(QAESEncryption::AES_128, QAESEncryption::ECB);
QByteArray encodedText = encryption.encrypt(plainText, key);

QByteArray decodedText = encryption.decrypt(encodedText, key);
```

Example for 256bit CBC using QString
```
#include <QCryptographicHash>
#include "qaesencryption.h"

QAESEncryption encryption(QAESEncryption::AES_256, QAESEncryption::CBC);

QString inputStr("The Advanced Encryption Standard (AES), also known by its original name Rijndael "
                 "is a specification for the encryption of electronic data established by the U.S. "
                "National Institute of Standards and Technology (NIST) in 2001");
QString key("your-string-key");
QString iv("your-IV-vector");

QByteArray hashKey = QCryptographicHash::hash(key.toLocal8Bit(), QCryptographicHash::Sha256);
QByteArray hashIV = QCryptographicHash::hash(iv.toLocal8Bit(), QCryptographicHash::Md5);

QByteArray encodeText = encryption.encrypt(inputStr.toLocal8Bit(), hashKey, hashIV);
QByteArray decodeText = encryption.decrypt(encodeText, hashKey, hashIV);

QString decodedString = QString(encryption.removePadding(decodeText));

//decodedString == inputStr !!
```

### Example via static invocation
Static invocation without creating instances, 256 bit key, ECB mode, starting from *QString* text/key
```
#include <QCryptographicHash>
#include "qaesencryption.h"

QString inputStr("The Advanced Encryption Standard (AES), also known by its original name Rijndael "
                 "is a specification for the encryption of electronic data established by the U.S. "
                "National Institute of Standards and Technology (NIST) in 2001");
QString key("your-string-key");
QString iv("your-IV-vector");

QByteArray hashKey = QCryptographicHash::hash(key.toLocal8Bit(), QCryptographicHash::Sha256);
QByteArray hashIV = QCryptographicHash::hash(iv.toLocal8Bit(), QCryptographicHash::Md5);

//Static invocation
QByteArray encrypted = QAESEncryption::Encrypt(QAESEncryption::AES_256, QAESEncryption::CBC, 
                        inputStr.toLocal8Bit(), hashKey, hashIV);
//...
// Removal of Padding via Static function
QString decodedString = QString(QAESEncryption::RemovePadding(decodeText));
```

## Dependencies
* QtCore >= 5.14

No OpenSSL required.
