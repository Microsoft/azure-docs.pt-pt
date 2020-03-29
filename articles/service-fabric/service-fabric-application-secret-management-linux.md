---
title: Configurar um cert de encriptação em clusters Linux
description: Aprenda a configurar um certificado de encriptação e a encriptar segredos em clusters Linux.
author: shsha
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: b8e0a19e3f654fc561e7c7e26c6a2da463e24d5f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969038"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Configurar um certificado de encriptação e encriptar segredos em clusters Linux
Este artigo mostra como configurar um certificado de encriptação e usá-lo para encriptar segredos em clusters Linux. Para os clusters windows, consulte Configurar um certificado de [encriptação e encriptar segredos em clusters do Windows][secret-management-windows-specific-link].

## <a name="obtain-a-data-encipherment-certificate"></a>Obter um certificado de obtenção de dados
Um certificado de codificação de dados é usado estritamente para encriptação e desencriptação de [parâmetros][parameters-link] nas Definições de um serviço.xml e [variáveis ambientais][environment-variables-link] em serviceManifest.xml de um serviço. Não é utilizado para autenticação ou assinatura de texto cifrado. O certificado deve satisfazer os seguintes requisitos:

* O certificado deve conter uma chave privada.
* A utilização da chave do certificado deve incluir o Encipherment de Dados (10), e não deve incluir a autenticação do servidor ou a autenticação do cliente.

  Por exemplo, podem ser utilizados os seguintes comandos para gerar o certificado necessário utilizando o OpenSSL:
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instale o certificado no seu cluster
O certificado deve ser instalado em cada `/var/lib/sfcerts`nó do cluster abaixo . A conta de utilizador sob a qual o serviço está em execução (sfuser `/var/lib/sfcerts/TestCert.pem` por padrão) deve ter lido o **acesso** ao certificado instalado (isto é, para o exemplo atual).

## <a name="encrypt-secrets"></a>Encriptar os segredos
Os seguintes cortes podem ser usados para encriptar um segredo. Este corte apenas encripta o valor; **não** assina o texto da cifra. **Deve utilizar** o mesmo certificado de encipherment instalado no seu cluster para produzir texto de cifra para valores secretos.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt | tr -d '\n' > plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
A saída de cadeias codificada pela base-64 resultante para encriptado.txt contém tanto o texto secreto da cifra como as informações sobre o certificado que foi usado para o encriptar. Pode verificar a sua validade desencriptando-a com o OpenSSL.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>Passos seguintes
Saiba especificar [segredos encriptados numa aplicação.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
