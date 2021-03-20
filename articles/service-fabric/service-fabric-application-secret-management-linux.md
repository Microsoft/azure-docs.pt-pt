---
title: Configurar um certificado de encriptação em clusters Linux
description: Saiba como configurar um certificado de encriptação e encriptar segredos em clusters Linux.
author: shsha
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: b8e0a19e3f654fc561e7c7e26c6a2da463e24d5f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "78969038"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Crie um certificado de encriptação e criptografe segredos em clusters Linux
Este artigo mostra como configurar um certificado de encriptação e usá-lo para encriptar segredos em clusters Linux. Para os clusters Windows, consulte [configurar um certificado de encriptação e encriptar segredos em clusters Windows][secret-management-windows-specific-link].

## <a name="obtain-a-data-encipherment-certificate"></a>Obter um certificado de cifra de dados
Um certificado de cifra de dados é utilizado estritamente para encriptação e desencriptação de [parâmetros][parameters-link] nas [variáveis][environment-variables-link] de Settings.xml e ambiente de um serviço na ServiceManifest.xml de um serviço. Não é utilizado para autenticação ou assinatura de texto cifrado. O certificado deve satisfazer os seguintes requisitos:

* O certificado deve conter uma chave privada.
* A utilização da chave do certificado deve incluir o Encipherment de Dados (10), e não deve incluir a Autenticação do Servidor ou a Autenticação do Cliente.

  Por exemplo, os seguintes comandos podem ser utilizados para gerar o certificado necessário utilizando o OpenSSL:
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instale o certificado no seu cluster
O certificado deve ser instalado em cada nó do cluster sob `/var/lib/sfcerts` . A conta de utilizador sob a qual o serviço está em execução (sfuser por padrão) **deveria ter lido** o acesso ao certificado instalado (isto é, pelo exemplo `/var/lib/sfcerts/TestCert.pem` atual).

## <a name="encrypt-secrets"></a>Encriptar os segredos
O seguinte corte pode ser usado para encriptar um segredo. Este corte apenas encripta o valor; **não** assina o texto da cifra. **Deve utilizar** o mesmo certificado de cifra que está instalado no seu cluster para produzir texto cifrado para valores secretos.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt | tr -d '\n' > plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
A saída de cadeia codificada base-64 resultante para encrypted.txt contém tanto o texto de cifra secreta como as informações sobre o certificado que foi usado para criptografá-lo. Pode verificar a sua validade desencriptadando-a com o OpenSSL.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>Passos seguintes
Saiba como [especificar segredos encriptados numa aplicação.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
