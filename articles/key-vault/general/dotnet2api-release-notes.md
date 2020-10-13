---
title: Key Vault .NET 2.x Notas de lançamento da API/ Microsoft Docs
description: Saiba como atualizar aplicações escritas para versões anteriores do Azure Key Vault para trabalhar com a versão 2.0 da biblioteca Azure Key Vault para C# e .NET.
services: key-vault
author: msmbaldwin
manager: rkarlin
editor: bryanla
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: 018570019b306dced76760fefa4441ee7d86ad2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88189838"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Cofre da Chave Azure .NET 2.0 - Guia de Lançamento e Migração
As seguintes informações ajudam a migrar para a versão 2.0 da biblioteca Azure Key Vault para C# e .NET.  As aplicações escritas para versões anteriores precisam de ser atualizadas para suportar a versão mais recente.  Estas alterações são necessárias para suportar plenamente novas funcionalidades e **melhores,** tais como certificados Key Vault .

## <a name="key-vault-certificates"></a>Certificados key Vault

Os certificados Key Vault gerem certificados x509 e suportam os seguintes comportamentos:  

* Crie certificados através de um processo de criação do Cofre-Chave ou importe o certificado existente. Isto inclui certificados gerados pela Autoridade auto-assinada e pela Autoridade de Certificados (CA).
* Armazenar e gerir de forma segura o armazenamento de certificados x509 sem interação utilizando material chave privado.  
* Defina políticas que direcionem o Key Vault para gerir o ciclo de vida do certificado.  
* Fornecer informações de contacto para eventos de ciclo de vida, tais como avisos de expiração e notificações de renovação.  
* Renovam automaticamente os certificados com emitentes selecionados (os fornecedores de certificados X509 parceiros do Key Vault e as autoridades de certificados).* Certificado de apoio de entidades alternativas (não parceiras) fornece e certifica as autoridades (não suporta a renovação automática).  

## <a name="net-support"></a>.SUPORTE NET

* **.NET 4.0** não é suportado pela versão 2.0 da biblioteca Azure Key Vault .NET
* **.NET Framework 4.5.2** é suportado pela versão 2.0 da biblioteca Azure Key Vault .NET
* **.NET O Standard 1.4** é suportado pela versão 2.0 da biblioteca Azure Key Vault .NET

## <a name="namespaces"></a>Espaços de nomes

* O espaço de nome para **modelos** é alterado de **Microsoft.Azure.KeyVault** para **Microsoft.Azure.KeyVault.Models**.
* O **microsoft.Azure.KeyVault.Internal** namespace é abandonado.
* Os seguintes espaços de nomes Azure SDK dependências têm 

    - **Hyak.Common** é agora **Microsoft.Rest**.
    - **Hyak.Common.Internals** é agora **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Alterações de tipo

* *Segredo* alterado para *SecretBundle*
* *Dicionário* alterado para *IDictionary*
* *Lista, \<T> string []* alterado para *IList \<T> *
* *NextList* alterado para  *NextPageLink*

## <a name="return-types"></a>Tipos de retorno

* **KeyList** e **SecretList** agora devolve * \<T> IPage* em vez de *ListKeysResponseMessage*
* O **BackupKeyAsync** gerado agora devolve *BackupKeyResult*, que contém *Valor* (blob de backup). Anteriormente, o método foi embrulhado e devolvido apenas o valor.

## <a name="exceptions"></a>Exceções

* *KeyVaultClientException* é alterado para *KeyVaultErrorException*
* O erro de serviço mudou de *exceção. Erro* à *exceção. Body.Error.Message*.
* Removido informações adicionais da mensagem de erro para **[JsonExtensionData]**.

## <a name="constructors"></a>Construtores

* Em vez de aceitar um *HttpClient* como argumento de construtor, o construtor apenas aceita *HttpClientHandler* ou *DelegatingHandler[]*.

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

### <a name="current-package-list"></a>Lista de pacotes atuais

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Mudanças de classe

* A aula **unixEpoch** foi removida.
* A classe **Base64UrlConverter** é renomeada para **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Outras alterações

* O suporte para a configuração da política de reorientação da operação KV em falhas transitórias foi adicionado a esta versão da API.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Para as operações que devolveram um *cofre,* o tipo de retorno era uma classe que continha uma propriedade **vault.** O tipo de retorno é agora *Vault.*
* *PermissõesToKeys* e *PermissõesToSecrets* são agora *Permissões.Chaves* e *Permissões.Segredos*
* Certos tipos de devolução aplicam-se também ao plano de controlo.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* O pacote é dividido em **Microsoft.Azure.KeyVault.Extensions** e **Microsoft.Azure.KeyVault.Cryptography** para as operações de criptografia.

