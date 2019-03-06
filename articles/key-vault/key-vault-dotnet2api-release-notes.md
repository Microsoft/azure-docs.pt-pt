---
title: Notas de versão de API do .NET 2.x do Cofre de chaves | Documentos da Microsoft
description: Os desenvolvedores de .NET irão utilizar esta API para o código para o Azure Key Vault
services: key-vault
author: msmbaldwin
manager: barbkess
editor: bryanla
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: 3d2543ea2b2ee6261b04396f92d5f3583a89ffb0
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57406918"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Cofre de chaves do Azure 2.0 - Guia de migração e notas de versão do .NET
As seguintes informações ajuda a migrar para a versão 2.0 da biblioteca do Azure Key Vault para c# e .NET.  Tem de aplicativos escritos para versões anteriores à atualização para suportar a versão mais recente.  Estas alterações são necessárias para suportar totalmente as funcionalidades novas e aprimoradas, como **certificados do Key Vault**.

## <a name="key-vault-certificates"></a>Certificados do Key Vault

Certificados do Key Vault gerir x509 certificados e suporta os seguintes comportamentos:  

* Criar certificados através de um processo de criação do Cofre de chaves ou importar certificado existente. Isto inclui ambos auto-assinado e certificado de autoridade (CA) gerados certificados.
* Armazenar e gerir x509 com segurança armazenamento de certificado sem a interação com o material de chave privada.  
* Defina políticas que direcionam o Key Vault para gerir o ciclo de vida do certificado.  
* Fornece informações de contacto para eventos de ciclo de vida, como avisos de expiração e as notificações de renovação.  
* Renovar automaticamente certificados de emissores selecionados (fornecedores de certificados do Key Vault parceiro X509 e autoridades de certificação). * certificado de suporte do alternativa (não partner) fornece e autoridades de certificado (não suporta a renovação automática).  

## <a name="net-support"></a>Suporte de .NET

* **.NET 4.0** não é suportada pela versão 2.0 da biblioteca .NET do Azure Key Vault
* **.NET framework 4.5.2** é suportada pela versão 2.0 da biblioteca .NET do Azure Key Vault
* **1.4 padrão do .NET** é suportada pela versão 2.0 da biblioteca .NET do Azure Key Vault

## <a name="namespaces"></a>Espaços de nomes

* O espaço de nomes para **modelos** é alterado de **Microsoft.Azure.KeyVault** para **Microsoft.Azure.KeyVault.Models**.
* O **Microsoft.Azure.KeyVault.Internal** espaço de nomes é ignorado.
* Tem os seguintes espaços de nomes de dependências de SDK do Azure 

    - **Hyak.Common** agora é **Microsoft.Rest**.
    - **Hyak.Common.Internals** agora é **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Alterações de tipo

* *Segredo* alterada para *SecretBundle*
* *Dicionário* alterada para *IDictionary*
* *Lista<T>, string []* alterado para *IList<T>*
* *NextList* alterada para *NextPageLink*

## <a name="return-types"></a>Tipos de retorno

* **KeyList** e **SecretList** agora retorna *IPage<T>*  em vez de *ListKeysResponseMessage*
* O gerado **BackupKeyAsync** agora retorna *BackupKeyResult*, que contém *valor* (blob de cópia de segurança). Anteriormente, o método foi encapsulado e apenas o valor devolvido.

## <a name="exceptions"></a>Exceções

* *KeyVaultClientException* é alterado para *KeyVaultErrorException*
* O erro de serviço em vez de *exceção. Erro* para *exceção. Body.Error.Message*.
* Remover informações adicionais da mensagem de erro para **[JsonExtensionData]**.

## <a name="constructors"></a>Construtores

* Em vez de aceitar uma *HttpClient* como um argumento do construtor, o construtor apenas aceita *HttpClientHandler* ou *DelegatingHandler [-]*.

## <a name="downloaded-packages"></a>Pacotes de download

Quando um cliente processa uma dependência de Cofre de chaves, são transferidos os seguintes pacotes:

### <a name="previous-package-list"></a>Lista de pacote anterior

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Lista de pacote atual

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Alterações de classe

* **UnixEpoch** classe foi removida.
* **Base64UrlConverter** classe foi mudada para **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Outras alterações

* Foi adicionado suporte para a configuração de política de repetição de operação KV em falhas transitórias para esta versão da API.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Para as operações que devolveram um *cofre*, o tipo de retorno foi uma classe que continha um **cofre** propriedade. O tipo de retorno é agora *cofre*.
* *PermissionsToKeys* e *PermissionsToSecrets* estão agora *Permissions.Keys* e *Permissions.Secrets*
* Determinadas alterações de tipos de retorno aplicam-se para o plano de controlo também.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* O pacote foi interrompido até **Microsoft.Azure.KeyVault.Extensions** e **Microsoft.Azure.KeyVault.Cryptography** para as operações de criptografia.

