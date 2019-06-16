---
title: Configurar um certificado de encriptação e encriptar os segredos em clusters do Linux do Azure Service Fabric | Documentos da Microsoft
description: Saiba como configurar um certificado de encriptação e encriptar os segredos em clusters do Linux.
services: service-fabric
documentationcenter: .net
author: shsha
manager: ''
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: 9589d6ea69a2293d592a9e63f2b726f1a620bb9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62126992"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Configurar um certificado de encriptação e encriptar os segredos em clusters do Linux
Este artigo mostra como configurar um certificado de encriptação e utilizá-la para encriptar os segredos em clusters do Linux. Para clusters do Windows, consulte [definir um certificado de encriptação a cópia de segurança e encriptar os segredos em clusters do Windows][secret-management-windows-specific-link].

## <a name="obtain-a-data-encipherment-certificate"></a>Obter um certificado de encriptação de dados
Um certificado de encriptação de dados é estritamente utilizado para encriptação e desencriptação de [parâmetros] [ parameters-link] em Settings um serviço e [variáveis de ambiente] [ environment-variables-link] no servicemanifest. XML de um serviço. Não é utilizado para autenticação ou assinatura de texto de cifra. O certificado tem de cumprir os seguintes requisitos:

* O certificado tem de conter uma chave privada.
* Utilização da chave do certificado tem de incluir cifragem de dados (10) e não deve incluir a autenticação de servidor ou a autenticação de cliente.

  Por exemplo, os comandos seguintes podem ser utilizados para gerar o certificado necessário com OpenSSL:
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalar o certificado no seu cluster
O certificado tem de ser instalado em cada nó do cluster em `/var/lib/sfcerts`. A conta de utilizador sob a qual o serviço está em execução (sfuser por padrão) **devem ter acesso de leitura** para o certificado instalado (ou seja, `/var/lib/sfcerts/TestCert.pem` no exemplo atual).

## <a name="encrypt-secrets"></a>Encriptar os segredos
O fragmento seguinte pode ser utilizado para encriptar um segredo. Este fragmento só criptografa o valor; ele faz **não** inscrever-se o texto de cifra. **Tem de utilizar** o mesmo certificado de encriptação que é instalado no seu cluster para produzir o texto cifrado para valores secretos.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
A saída resultante da cadeia de caracteres com codificação base 64 para encrypted.txt contém tanto o texto cifrado SECRETO, bem como informações sobre o certificado que foi usada para criptografá-la. Pode verificar sua validade, desencriptá-lo com OpenSSL.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>Passos Seguintes
Saiba como [especificar os segredos encriptados num aplicativo.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
