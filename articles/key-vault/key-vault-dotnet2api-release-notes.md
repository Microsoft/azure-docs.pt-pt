---
title: Notas de versão da API do Key Vault .NET 2. x | Microsoft Docs
description: Os desenvolvedores do .NET usarão essa API para codificar para Azure Key Vault
services: key-vault
author: msmbaldwin
manager: barbkess
editor: bryanla
ms.service: key-vault
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: e173ff54020f2d365348ae037793cfbba3f9ed7f
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68260430"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2,0-notas de versão e guia de migração
As informações a seguir ajudam a migrar para a versão 2,0 da biblioteca C# de Azure Key Vault para o e .net.  Aplicativos escritos para versões anteriores precisam ser atualizados para dar suporte à versão mais recente.  Essas alterações são necessárias para dar suporte total a recursos novos e aprimorados, como **Key Vault certificados**.

## <a name="key-vault-certificates"></a>Key Vault certificados

Key Vault certificados gerenciam certificados X509 e oferece suporte aos seguintes comportamentos:  

* Crie certificados por meio de um processo de criação de Key Vault ou importe um certificado existente. Isso inclui certificados autoassinados e gerados por AC (autoridade de certificação).
* Armazene e gerencie com segurança o armazenamento de certificado X509 sem interação usando o material de chave privada.  
* Defina as políticas que direcionam Key Vault para gerenciar o ciclo de vida do certificado.  
* Forneça informações de contato para eventos de ciclo de vida, como avisos de expiração e notificações de renovação.  
* Renove automaticamente os certificados com os emissores selecionados (Key Vault provedores de certificado X509 do parceiro e autoridades de certificação). * suporte a certificados de fornecedores alternativos (não parceiros) e autoridades de certificação (não oferece suporte à renovação automática).  

## <a name="net-support"></a>Suporte ao .NET

* O **.net 4,0** não é suportado pela versão 2,0 da biblioteca do .net Azure Key Vault
* **.NET Framework 4.5.2** é compatível com a versão 2,0 da biblioteca do Azure Key Vault .net
* **.NET Standard 1,4** é compatível com a versão 2,0 da biblioteca do Azure Key Vault .net

## <a name="namespaces"></a>Espaços de nomes

* O namespace para **modelos** é alterado de **Microsoft. Azure. keyvault** para **Microsoft. Azure. keyvault. Models**.
* O namespace **Microsoft. Azure. keyvault. Internal** é Descartado.
* Os namespaces de dependências do SDK do Azure a seguir têm 

    - **Hyak. Common** agora é **Microsoft. REST**.
    - **Hyak. Common. Internals** agora é **Microsoft. REST. Serialization**.

## <a name="type-changes"></a>Alterações de tipo

* *Segredo* alterado para *SecretBundle*
* *Dicionário* alterado para *IDictionary*
* *List\<t >, String []* alterado para *IList\<T >*
* A próximalist foi alterada para *NextPageLink*

## <a name="return-types"></a>Tipos de retorno

* **KeyList** e **secretolist** agora retorna *iPage\<T >* em vez de *ListKeysResponseMessage*
* O **BackupKeyAsync** gerado agora retorna *BackupKeyResult*, que contém o *valor* (blob de backup). Anteriormente, o método foi encapsulado e retornava apenas o valor.

## <a name="exceptions"></a>Exceções

* *KeyVaultClientException* é alterado para *KeyVaultErrorException*
* O erro de serviço mudou de *exceção. Erro* de *exceção. Body. erro. Message*.
* Informações adicionais removidas da mensagem de erro para **[JsonExtensionData]** .

## <a name="constructors"></a>Construtores

* Em vez de aceitar um *HttpClient* como um argumento de construtor, o construtor aceita somente *HttpClientHandler* ou *DelegatingHandler []* .

## <a name="downloaded-packages"></a>Pacotes baixados

Quando um cliente processa uma dependência de Key Vault, os seguintes pacotes são baixados:

### <a name="previous-package-list"></a>Lista de pacotes anterior

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Lista de pacotes atual

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Alterações de classe

* A classe **UnixEpoch** foi removida.
* A classe **Base64UrlConverter** foi renomeada para **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Outras alterações

* O suporte para a configuração da política de repetição de operação KV em falhas transitórias foi adicionado a esta versão da API.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Para as operações que retornaram um *cofre*, o tipo de retorno era uma classe que continha uma propriedade de **cofre** . O tipo de retorno agora é *cofre*.
* *PermissionsToKeys* e *PermissionsToSecrets* agora são *Permissions. Keys* e *Permissions. Secrets*
* Certas alterações de tipos de retorno também se aplicam ao plano de controle.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* O pacote é dividido em **Microsoft. Azure. keyvault. Extensions** e **Microsoft. Azure. keyvault. Cryptography** para as operações de criptografia.

