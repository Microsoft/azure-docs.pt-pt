---
title: Configurar um certificado de encriptação e encriptar os segredos em clusters do Windows de recursos de infraestrutura do serviço do Azure | Documentos da Microsoft
description: Saiba como configurar um certificado de encriptação e encriptar os segredos em clusters do Windows.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: 3d324c54d10433520a73f2bd836c26bd79f1b3bb
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662317"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Configurar um certificado de encriptação e encriptar os segredos em clusters do Windows
Este artigo mostra como configurar um certificado de encriptação e utilizá-la para encriptar os segredos em clusters do Windows. Para clusters do Linux, consulte [definir configurar um certificado de encriptação e encriptar os segredos em clusters do Linux.][secret-management-linux-specific-link]

[O Azure Key Vault] [ key-vault-get-started] serve aqui como uma localização de armazenamento seguro para certificados e como uma forma de obter certificados instalados em clusters do Service Fabric no Azure. Se não estiver a implementar para o Azure, não é necessário utilizar o Key Vault para gerir segredos em aplicações do Service Fabric. No entanto, *usando* segredos num aplicativo está na cloud independente de plataforma para permitir que os aplicativos a serem implantados para um cluster alojado em qualquer lugar. 

## <a name="obtain-a-data-encipherment-certificate"></a>Obter um certificado de encriptação de dados
Um certificado de encriptação de dados é estritamente utilizado para encriptação e desencriptação de [parâmetros] [ parameters-link] em Settings um serviço e [variáveis de ambiente] [ environment-variables-link] no servicemanifest. XML de um serviço. Não é utilizado para autenticação ou assinatura de texto de cifra. O certificado tem de cumprir os seguintes requisitos:

* O certificado tem de conter uma chave privada.
* O certificado tem de ser criado para troca de chaves, exportável para um ficheiro Personal Information Exchange (. pfx).
* Utilização da chave do certificado tem de incluir cifragem de dados (10) e não deve incluir a autenticação de servidor ou a autenticação de cliente. 
  
  Por exemplo, ao criar um certificado autoassinado com o PowerShell, o `KeyUsage` sinalizador deve ser definido como `DataEncipherment`:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalar o certificado no seu cluster
Este certificado tem de ser instalado em cada nó no cluster. Ver [como criar um cluster com o Azure Resource Manager] [ service-fabric-cluster-creation-via-arm] para obter instruções de configuração. 

## <a name="encrypt-application-secrets"></a>Encriptar os segredos da aplicação
O seguinte comando do PowerShell é utilizado para encriptar um segredo. Este comando só criptografa o valor; ele faz **não** inscrever-se o texto de cifra. Tem de utilizar o mesmo certificado de encriptação que é instalado no seu cluster para produzir o texto cifrado para valores secretos:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

A cadeia codificada de base 64 resultante contém tanto o texto cifrado SECRETO, bem como informações sobre o certificado que foi usada para criptografá-la.

## <a name="next-steps"></a>Passos Seguintes
Saiba como [especificar os segredos encriptados num aplicativo.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
