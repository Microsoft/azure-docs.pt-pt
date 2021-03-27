---
title: Tutorial - Use OpenSSL para criar certificados de teste X.509 para Azure IoT Hub| Microsoft Docs
description: Tutorial - Use OpenSSL para criar certificados de CA e dispositivos para o hub Azure IoT
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: 0d083d856138d7895a6e03f4d290ef3c4ddebd05
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630722"
---
# <a name="tutorial-using-openssl-to-create-test-certificates"></a>Tutorial: Utilizar o OpenSSL para criar certificados de teste

Embora possa adquirir certificados X.509 a uma autoridade de certificação fidedigna, criar a sua própria hierarquia de certificado de teste ou usar certificados auto-assinados é adequado para testar a autenticação do dispositivo ioT. O exemplo a seguir utiliza o [OpenSSL](https://www.openssl.org/) e o [OpenSSL Cookbook](https://www.feistyduck.com/library/openssl-cookbook/online/ch-openssl.html) para criar uma autoridade de certificação (CA), um CA subordinado e um certificado de dispositivo. O exemplo assina então a AC subordinada e o certificado do dispositivo numa hierarquia de certificados. Isto é apresentado apenas por exemplo.

## <a name="step-1---create-the-root-ca-directory-structure"></a>Passo 1 - Criar a estrutura do diretório de CA raiz

Criar uma estrutura de diretório para a autoridade de certificação.

* O **diretório certs** armazena novos certificados.
* O **diretório db** é utilizado para a base de dados de certificados.
* O diretório **privado** armazena a chave privada da AC.

```bash
  mkdir rootca
  cd rootca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-2---create-a-root-ca-configuration-file"></a>Passo 2 - Criar um ficheiro de configuração de CA raiz

Antes de criar um CA, crie um ficheiro de configuração e guarde-o como `rootca.conf` no diretório de rootca.

```xml
[default]
name                     = rootca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Root CA"

[ca_default]
home                     = ../rootca 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = none
default_days             = 3650
default_crl_days         = 365
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

```

## <a name="step-3---create-a-root-ca"></a>Passo 3 - Criar uma raiz CA

Em primeiro lugar, gere a chave e o pedido de assinatura de certificado (RSE) no diretório de rootca.

```bash
  openssl req -new -config rootca.conf -out rootca.csr -keyout private/rootca.key
```

Em seguida, crie um certificado de CA auto-assinado. A auto-assinatura é adequada para testes. Especifique as extensões de ficheiro de configuração ca_ext na linha de comando. Estes indicam que o certificado é para uma CA de raiz e pode ser usado para assinar certificados e listas de revogação de certificados (CRLs). Assine o certificado e comprometa-o na base de dados.

```bash
  openssl ca -selfsign -config rootca.conf -in rootca.csr -out rootca.crt -extensions ca_ext
```

## <a name="step-4---create-the-subordinate-ca-directory-structure"></a>Passo 4 - Criar a estrutura subordinada do diretório ca

Criar uma estrutura de diretório para a AC subordinada.

```bash
  mkdir subca
  cd subca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-5---create-a-subordinate-ca-configuration-file"></a>Passo 5 - Criar um ficheiro de configuração de CA subordinado

Crie um ficheiro de configuração e guarde-o como subca.conf no `subca` diretório.

```bash
[default]
name                     = subca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Subordinate CA"

[ca_default]
home                     = . 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = copy 
default_days           
default_crl_days         = 90 
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash
```

## <a name="step-6---create-a-subordinate-ca"></a>Passo 6 - Criar um CA subordinado

Crie um novo número de série no `rootca/db/serial` ficheiro para o certificado de CA subordinado.

```bash
  openssl rand -hex 16 > db/serial
```

>[!IMPORTANT]
>Deve criar um novo número de série para cada certificado de CA subordinado e todos os certificados de dispositivo que criar. Certificados diferentes não podem ter o mesmo número de série.

Este exemplo mostra-lhe como criar um CA subordinado ou de registo. Como pode usar a raiz da AC para assinar certificados, criar um CA subordinado não é estritamente necessário. Ter uma AC subordinada imita, no entanto, hierarquias de certificados mundiais reais em que a CA raiz é mantida offline e subordinada CAs emitir certificados de cliente.

Utilize o ficheiro de configuração para gerar uma chave e um pedido de assinatura de certificado (RSE).

```bash
  openssl req -new -config subca.conf -out subca.csr -keyout private/subca.key
```

Submeta o CSR à RAIZ CA e utilize a raiz CA para emitir e assinar o certificado de CA subordinado. Especifique sub_ca_ext para o interruptor de extensões na linha de comando. As extensões indicam que o certificado é para um CA que pode assinar certificados e listas de revogação de certificados (CRLs). Quando solicitado, assine o certificado e entregue-o à base de dados.

```bash
  openssl ca -config ../rootca/rootca.conf -in subca.csr -out subca.crt -extensions sub_ca_ext
```

## <a name="step-7---demonstrate-proof-of-possession"></a>Passo 7 - Demonstrar prova de posse

Tem agora um certificado de CA raiz e um certificado de CA subordinado. Pode utilizar qualquer um para assinar certificados de dispositivo. O que escolher deve ser enviado para o seu IoT Hub. Os seguintes passos pressupõem que está a utilizar o certificado de CA subordinado. Para fazer o upload e registar o seu certificado de CA subordinado no seu Hub IoT:

1. No portal Azure, navegue para o seu IoTHub e selecione **Definições > Certificados**.

1. **Selecione Adicionar** para adicionar o seu novo certificado de CA subordinado.

1. Introduza um nome de exibição no campo **Nome de Certificado** e selecione o ficheiro de certificado PEM que criou anteriormente.

1. Selecione **Guardar**. O seu certificado está indicado na lista de certificados com estatuto de **Não verificado.** O processo de verificação provará que é dono do certificado.

   
1. Selecione o certificado para ver o diálogo Detalhes do **Certificado.**

1. Selecione **Código de Verificação de Geração**. Para mais informações, consulte [Prove a posse de um certificado de AC.](tutorial-x509-prove-possession.md)

1. Copie o código de verificação para a área de transferência. Tem de definir o código de verificação como o sujeito do certificado. Por exemplo, se o código de verificação for BB0C656E69AF75E3F3C8D922C1760C58C1DA5B05AAA9D0A, adicione-o como objeto do seu certificado como indicado no passo seguinte.

1. Gere uma chave privada.

  ```bash
    $ openssl req -new -key pop.key -out pop.csr

    -----
    Country Name (2 letter code) [XX]:.
    State or Province Name (full name) []:.
    Locality Name (eg, city) [Default City]:.
    Organization Name (eg, company) [Default Company Ltd]:.
    Organizational Unit Name (eg, section) []:.
    Common Name (eg, your name or your server hostname) []:BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A
    Email Address []:

    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:
    An optional company name []:
 
  ```

9. Crie um certificado utilizando o ficheiro de configuração raiz CA e o CSR.

  ```bash
    openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

  ```

10. Selecione o novo certificado na vista Detalhes do **Certificado**

11. Depois do upload do certificado, **selecione Verificar**. O estado do certificado de CA deve ser alterado para **Verificado**.

## <a name="step-8---create-a-device-in-your-iot-hub"></a>Passo 8 - Crie um dispositivo no seu IoT Hub

Navegue até ao seu Hub IoT no portal Azure e crie uma nova identidade do dispositivo IoT com os seguintes valores:

1. Forneça o **ID** do dispositivo que corresponda ao nome do sujeito dos certificados do seu dispositivo.

1. Selecione o tipo de autenticação **assinada X.509 CA.**

1. Selecione **Guardar**.

## <a name="step-9---create-a-client-device-certificate"></a>Passo 9 - Criar um certificado de dispositivo de cliente

Para gerar um certificado de cliente, tem primeiro de gerar uma chave privada. O seguinte comando mostra como usar o OpenSSL para criar uma chave privada. Crie a chave no diretório subca.

```bash
openssl genpkey -out device.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

Crie um pedido de assinatura de certificado (CSR) para a chave. Não precisa de introduzir uma palavra-passe de desafio ou um nome de empresa opcional. No entanto, deve introduzir o ID do dispositivo no campo de nome comum.

```bash
openssl req -new -key device.key -out device.csr

-----
Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server hostname) []:`<your device ID>`
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

```

Verifique se a RSE é o que espera.

```bash
openssl req -text -in device.csr -noout
```

Envie a RSE para a AC subordinada para assinar na hierarquia do certificado. Especificar `client_ext` no `-extensions` interruptor. Note-se que `Basic Constraints` o certificado emitido indica que este certificado não é para um CA. Se estiver a assinar vários certificados, certifique-se de atualizar o número de série antes de gerar cada certificado utilizando o comando openssl. `rand -hex 16 > db/serial`

```bash
openssl ca -config subca.conf -in device.csr -out device.crt -extensions client_ext
```

## <a name="next-steps"></a>Passos Seguintes

Vá à [Autenticação do Certificado de Teste](tutorial-x509-test-certificate.md) para determinar se o seu certificado pode autenticar o seu dispositivo no seu Hub IoT.
