---
title: Sobre chaves, segredos e certificados azure key vault - Cofre chave Azure
description: Visão geral da interface rest do Cofre de Chaves Azure e detalhes do desenvolvedor para chaves, segredos e certificados.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: 627dfee81cb10e4e442b3cefb10d786d87d5c81d
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005881"
---
# <a name="about-keys-secrets-and-certificates"></a>Acerca de chaves, segredos e certificados

O Azure Key Vault permite que as aplicações e utilizadores do Microsoft Azure armazenem e utilizem vários tipos de dados secretos/chave:

- Chaves criptográficas: Suporta vários tipos e algoritmos de chave e permite a utilização de Módulos de Segurança de Hardware (HSM) para teclas de alto valor. Para mais informações, consulte [as chaves.](../keys/about-keys.md)
- Segredos: Fornece armazenamento seguro de segredos, tais como senhas e cadeias de ligação de base de dados. Para mais informações, consulte [sobre segredos.](../secrets/about-secrets.md)
- Certificados: Suporta certificados, que são construídos em cima de chaves e segredos e adicionam uma funcionalidade de renovação automatizada. Para mais informações, consulte [sobre certificados](../certificates/about-certificates.md).
- Armazenamento Azure: Pode gerir chaves de uma conta de Armazenamento Azure para si. Internamente, o Cofre chave pode listar (sincronizar) chaves com uma Conta de Armazenamento Azure e regenerar (rodar) as teclas periodicamente. Para mais informações, consulte [Gerir as chaves da conta de armazenamento com o Cofre chave](../secrets/overview-storage-keys.md).

Para obter informações mais gerais sobre o Cofre chave, consulte [o Cofre de Chaves Azure](overview.md).

## <a name="data-types"></a>Tipos de dados

Consulte as especificações JOSE para os tipos de dados relevantes para chaves, encriptação e assinatura.  

-   **algoritmo** - um algoritmo suportado para uma operação chave, por exemplo, RSA1_5  
-   **cifra-valor -** octetos de texto cifrados, codificados usando Base64URL  
-   **valor digestão** - a saída de um algoritmo de hash, codificado usando Base64URL  
-   **tipo chave** - um dos tipos-chave suportados, por exemplo RSA (Rivest-Shamir-Adleman).  
-   **valor simples -** octetos de texto simples, codificados usando Base64URL  
-   **assinatura-valor** - saída de um algoritmo de assinatura, codificado usando Base64URL  
-   **base64URL** - um valor binário codificado de Base64URL [RFC4648]  
-   **boolean -** verdadeiro ou falso  
-   **Identidade** - uma identidade do Azure Ative Directory (AAD).  
-   **IntDate** - um valor decimal JSON que representa o número de segundos de 1970-01-01T0:0:0Z UTC até à data/hora UTC especificada. Consulte o RFC3339 para obter mais informações sobre data/horários, em geral e utc em particular.  

## <a name="objects-identifiers-and-versioning"></a>Objetos, identificadores e versonagem

Os objetos armazenados no Cofre chave são versonizados sempre que for criada uma nova instância de um objeto. A cada versão é atribuída a um identificador e URL únicos. Quando um objeto é criado pela primeira vez, é dado um identificador de versão única e marcado como a versão atual do objeto. A criação de um novo exemplo com o mesmo nome de objeto dá ao novo objeto um identificador de versão único, fazendo com que se torne a versão atual.  

Os objetos no Cofre chave podem ser abordados especificando uma versão ou omitindo a versão para operações na versão atual do objeto. Por exemplo, dada uma `MasterKey`Chave com o nome , executar operações sem especificar uma versão faz com que o sistema utilize a versão mais recente disponível. A realização de operações com o identificador específico da versão faz com que o sistema utilize essa versão específica do objeto.  

Os objetos são identificados exclusivamente dentro do Cofre chave utilizando um URL. Nenhum dos dois objetos no sistema tem o mesmo URL, independentemente da geolocalização. O URL completo de um objeto chama-se Identificador de Objetos. O URL consiste num prefixo que identifica o Cofre de Chave, tipo de objeto, utilizador fornecido Nome do Objeto e uma Versão de Objeto. O Nome do Objeto é insensível e imutável. Os identificadores que não incluem a Versão do Objeto são referidos como Identificadores de Base.  

Para mais informações, consulte [Autenticação, pedidos e respostas](authentication-requests-and-responses.md)

Um identificador de objetos tem o seguinte formato geral:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Em que:  

|||  
|-|-|  
|`keyvault-name`|O nome de um cofre chave no serviço Microsoft Azure Key Vault.<br /><br /> Os nomes key vault são selecionados pelo utilizador e são globalmente únicos.<br /><br /> O nome do cofre de chaves deve ser uma corda de caracteres 3-24, contendo apenas 0-9, a-z, A-Z, e ..|  
|`object-type`|O tipo de objeto, "chaves", "segredos" ou "certificados".|  
|`object-name`|Um `object-name` é um utilizador fornecido nome e deve ser único dentro de um Cofre chave. O nome deve ser uma cadeia de caracteres 1-127, começando com uma letra e contendo apenas 0-9, a-z, A-Z, e -.|  
|`object-version`|Um `object-version` é um identificador de cadeia de 32 caracteres gerado pelo sistema que é opcionalmente usado para abordar uma versão única de um objeto.|  

## <a name="next-steps"></a>Passos seguintes

- [Acerca de chaves](../keys/about-keys.md)
- [Acerca de segredos](../secrets/about-secrets.md)
- [Acerca de certificados](../certificates/about-certificates.md)
- [Autenticação, pedidos e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Programador do Cofre de Chaves](../general/developers-guide.md)
