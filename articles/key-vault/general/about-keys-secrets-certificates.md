---
title: Sobre chaves, segredos e certificados do Azure Key Vault - Azure Key Vault
description: Visão geral da interface Azure Key Vault REST e detalhes do desenvolvedor para chaves, segredos e certificados.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: cb8a29c5d2eff46eecb2cf977bfb492f28731e68
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87043631"
---
# <a name="about-keys-secrets-and-certificates"></a>Acerca de chaves, segredos e certificados

O Azure Key Vault permite que as aplicações e utilizadores do Microsoft Azure armazenem e utilizem vários tipos de dados secretos/chave:

- Chaves criptográficas: Suporta vários tipos de chaves e algoritmos e permite a utilização de Módulos de Segurança de Hardware (HSM) para teclas de alto valor. Para mais informações, consulte [sobre as teclas.](../keys/about-keys.md)
- Segredos: Fornece armazenamento seguro de segredos, tais como palavras-passe e cadeias de conexão de base de dados. Para mais informações, consulte [Sobre os segredos.](../secrets/about-secrets.md)
- Certificados: Suporta certificados, que são construídos em cima de chaves e segredos e adicionam uma funcionalidade de renovação automatizada. Para mais informações, consulte [sobre os certificados.](../certificates/about-certificates.md)
- Armazenamento Azure: Pode gerir as chaves de uma conta de Armazenamento Azure para si. Internamente, o Key Vault pode listar as teclas (sincronização) com uma Conta de Armazenamento Azure e regenerar (rodar) as teclas periodicamente. Para obter mais informações, consulte [Gerir as chaves da conta de armazenamento com o Key Vault](../secrets/overview-storage-keys.md).

Para obter informações mais gerais sobre o Key Vault, consulte [About Azure Key Vault](overview.md).

## <a name="data-types"></a>Tipos de dados

Consulte as especificações JOSE para tipos de dados relevantes para chaves, encriptação e assinatura.  

-   **algoritmo** - um algoritmo suportado para uma operação chave, por exemplo, RSA1_5  
-   **cifratexto-valor** - octetos de texto de cifra, codificados através da Base64URL  
-   **digestão-valor** - a saída de um algoritmo de haxixe, codificado usando base64URL  
-   **tipo-chave** - um dos tipos de chaves suportados, por exemplo RSA (Rivest-Shamir-Adleman).  
-   **valor de texto simples** - octetos de texto simples, codificados usando base64URL  
-   **signature-value** - saída de um algoritmo de assinatura, codificado através da Base64URL  
-   **base64URL** - um valor binário codificado Base64URL [RFC4648]  
-   **booleano** - seja verdadeiro ou falso  
-   **Identidade** - uma identidade do Azure Ative Directory (AAD).  
-   **IntDate** - um valor decimal JSON que representa o número de segundos de 1970-01-01T0:0Z UTC até à data/hora utc especificada. Consulte o RFC3339 para obter mais informações sobre datas/horários, em geral e UTC em particular.  

## <a name="objects-identifiers-and-versioning"></a>Objetos, identificadores e versões

Os objetos armazenados no Cofre de Chaves são versados sempre que é criada uma nova instância de um objeto. A cada versão é atribuído um identificador e URL únicos. Quando um objeto é criado pela primeira vez, é dado um identificador de versão único e marcado como a versão atual do objeto. A criação de um novo caso com o mesmo nome de objeto dá ao novo objeto um identificador de versão único, fazendo com que se torne a versão atual.  

Os objetos no Cofre chave podem ser endereçados especificando uma versão ou omitindo a versão para operações na versão atual do objeto. Por exemplo, dada uma Chave com o nome `MasterKey` , realizar operações sem especificar uma versão faz com que o sistema utilize a versão mais recente disponível. A realização de operações com o identificador específico da versão faz com que o sistema utilize essa versão específica do objeto.  

Os objetos são identificados exclusivamente dentro do Key Vault usando um URL. Nenhum dos dois objetos no sistema tem o mesmo URL, independentemente da geolocalização. O URL completo de um objeto é chamado de Identificador de Objetos. O URL consiste num prefixo que identifica o Cofre de Chaves, o tipo de objeto, o nome do objeto fornecido pelo utilizador e uma versão do objeto. O Nome do Objeto é insensível e imutável. Os identificadores que não incluem a versão object são referidos como Identificadores de Base.  

Para mais informações, consulte [Autenticação, pedidos e respostas](authentication-requests-and-responses.md)

Um identificador de objetos tem o seguinte formato geral:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Em que:  

| Elemento | Descrição |  
|-|-|  
|`keyvault-name`|O nome de um cofre chave no serviço Microsoft Azure Key Vault.<br /><br /> Os nomes key Vault são selecionados pelo utilizador e são globalmente únicos.<br /><br /> O nome do Cofre da Chave deve ser uma corda de caracteres 3-24, contendo apenas 0-9, a-z, A-Z, e ..|  
|`object-type`|O tipo de objeto, "chaves", "segredos" ou "certificados".|  
|`object-name`|Um `object-name` é um nome fornecido pelo utilizador e deve ser único dentro de um Cofre chave. O nome deve ser uma corda de caracteres 1-127, começando com uma letra e contendo apenas 0-9, a-z, A-Z, e -.|  
|`object-version`|Um `object-version` é um identificador de cadeia de 32 caracteres gerado pelo sistema que é opcionalmente usado para abordar uma versão única de um objeto.|  

## <a name="next-steps"></a>Passos seguintes

- [Acerca de chaves](../keys/about-keys.md)
- [Acerca de segredos](../secrets/about-secrets.md)
- [Acerca de certificados](../certificates/about-certificates.md)
- [Autenticação, pedidos e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Programador do Key Vault](../general/developers-guide.md)
