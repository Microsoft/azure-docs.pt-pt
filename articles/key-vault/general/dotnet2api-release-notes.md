---
title: Cofre chave .NET 2.x Notas de lançamento da API Microsoft Docs
description: Os desenvolvedores .NET usarão esta API para codificar o Cofre de Chaves Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
editor: bryanla
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: 436b9c1569d7c33f79a126cd4d0513bac9385d8a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431920"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Cofre chave Azure .NET 2.0 - Notas de lançamento e guia de migração
As seguintes informações ajudam a migrar para a versão 2.0 da biblioteca Azure Key Vault para C# e .NET.  As aplicações escritas para versões anteriores precisam de ser atualizadas para suportar a versão mais recente.  Estas alterações são necessárias para suportar totalmente novas e melhoradas **funcionalidades,** tais como certificados Key Vault .

## <a name="key-vault-certificates"></a>Certificados de cofre chave

Os certificados Key Vault gerem certificados x509 e suportam os seguintes comportamentos:  

* Crie certificados através de um processo de criação do Cofre Chave ou de importação de certificadoexistente. Isto inclui certificados gerados pela Autoridade de Certificados (CA) auto-assinados e certificados gerados pela Autoridade de Certificados (AC).
* Armazenar e gerir de forma segura o armazenamento de certificadox509 sem interação utilizando material chave privado.  
* Defina políticas que direcionem o Key Vault para gerir o ciclo de vida do certificado.  
* Forneça informações de contacto para eventos de ciclo de vida, tais como avisos de validade e notificações de renovação.  
* Renovar automaticamente os certificados com emitentes selecionados (parceiro key Vault X509 fornecedores de certificados e autoridades de certificados).* Certificado de suporte de fontes alternativas (não-parceiro) fornece e as autoridades de certificados (não suporta a renovação automática).  

## <a name="net-support"></a>suporte .NET

* **.NET 4.0** não é suportado pela versão 2.0 da biblioteca Azure Key Vault .NET
* **.NET Framework 4.5.2** é suportado pela versão 2.0 da biblioteca Azure Key Vault .NET
* **.NET Standard 1.4** é suportado pela versão 2.0 da biblioteca Azure Key Vault .NET

## <a name="namespaces"></a>Espaços de nomes

* O espaço de nome para **modelos** é alterado de **Microsoft.Azure.KeyVault** para **Microsoft.Azure.KeyVault.Models**.
* O **Microsoft.Azure.KeyVault.Espaço** de nome interno foi abandonado.
* Os seguintes espaços de nome sdk de dependência sdk Azure têm 

    - **Hyak.Common** é agora **Microsoft.Rest**.
    - **Hyak.Common.Internals** é agora **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Alterações de tipo

* *Segredo* alterado para *SecretBundle*
* *Dicionário* alterado para *IDictionary*
* *Lista\<T>, string []* mudou para *\<IList T>*
* *NextList* alterado para *NextPageLink*

## <a name="return-types"></a>Tipos de retorno

* **KeyList** e **SecretList** devolvem agora *o IPage\<T>* em vez de *ListKeysResponseMessage*
* O **BackupKeyAsync** gerado devolve agora *o BackupKeyResult,* que contém *valor* (blob de reserva). Anteriormente, o método foi embrulhado e devolvido apenas o valor.

## <a name="exceptions"></a>Exceções

* *KeyVaultClientException* é alterado para *KeyVaultErrorException*
* O erro de serviço mudou de *exceção. Erro* à *exceção. Corpo.Error.Message*.
* Removeu informações adicionais da mensagem de erro para **[JsonExtensionData]**.

## <a name="constructors"></a>Construtores

* Em vez de aceitar um *HttpClient* como argumento de construtor, o construtor apenas aceita *httpClientHandler* ou *DelegatingHandler[]*.

## <a name="downloaded-packages"></a>Pacotes descarregados

Quando um cliente processa uma dependência do Cofre chave, os seguintes pacotes são descarregados:

### <a name="previous-package-list"></a>Lista de pacotes anteriores

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

## <a name="class-changes"></a>Mudanças de classe

* A classe **UnixEpoch** foi removida.
* A classe **Base64UrlConverter** é renomeada para **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Outras alterações

* Foi adicionado suporte para a configuração da política de retry da operação KV sobre falhas transitórias foi adicionado a esta versão da API.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Para as operações que devolveram um *cofre,* o tipo de retorno era uma classe que continha uma propriedade **vault.** O tipo de retorno é agora *Vault.*
* *PermissõesToKeys* e *PermissionsToSecrets* são agora *Permissões.Keys* e *Permissões.Segredos*
* Certos tipos de retorno alterações aplicam-se também ao plano de controlo.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* O pacote é dividido em **Microsoft.Azure.KeyVault.Extensions** e **Microsoft.Azure.KeyVault.Cryptography** para as operações de criptografia.

