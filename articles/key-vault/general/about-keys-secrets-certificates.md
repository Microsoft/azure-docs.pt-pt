---
title: Chaves, segredos e certificados de Azure Key
description: Visão geral da interface Azure Key Vault REST e detalhes do desenvolvedor para chaves, segredos e certificados.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: 78f228a5e188bc930a9e7484f4c982ba746331dd
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357781"
---
# <a name="azure-key-vault-keys-secrets-and-certificates-overview"></a>Chaves, segredos e certificados do Azure Key Vault

O Azure Key Vault permite que as aplicações e utilizadores do Microsoft Azure armazenem e utilizem vários tipos de dados secretos/chave. O fornecedor de recursos Key Vault suporta dois tipos de recursos: cofres e HSMs geridos.

## <a name="dns-suffixes-for-base-url"></a>Sufixos dns para URL base
 A tabela abaixo mostra o sufixo de URL DE BASE DNS usado pelo ponto final do plano de dados para abóbadas e piscinas HSM geridas em vários ambientes de nuvem.

Ambiente em nuvem | Sufixo DNS para cofres | Sufixo dns para HSMs geridos
---|---|---
Nuvem de Azure | .vault.azure.net | .managedhsm.azure.net
Nuvem de Azure China | .vault.azure.cn | Não suportado
Azure US Government | .vault.usgovcloudapi.net | Não suportado
Nuvem Alemã Azure | .vault.microsoftazure.de | Não suportado
|||


## <a name="object-types"></a>Tipos de objetos
 A tabela abaixo mostra os tipos de objetos e os seus sufixos no URL base.

Tipo de Objeto|Sufixo URL|Cofres|Piscinas HSM geridas
--|--|--|--
**Chaves criptográficas**||
Chaves protegidas por HSM|/chaves|Suportado|Suportado
Chaves protegidas por software|/chaves|Suportado|Não suportado
**Outros tipos de objetos**||
Segredos|/segredos|Suportado|Não suportado
Certificados|/certificados|Suportado|Não suportado
Chaves de contas de armazenamento|/contagem de armazenamento|Suportado|Não suportado
|||
- **Teclas criptográficas** : Suporta vários tipos de chaves e algoritmos e permite a utilização de chaves protegidas por software e protegidas por HSM. Para mais informações, consulte [sobre as teclas.](../keys/about-keys.md)
- **Segredos** : Fornece armazenamento seguro de segredos, tais como palavras-passe e cadeias de conexão de base de dados. Para mais informações, consulte [Sobre os segredos.](../secrets/about-secrets.md)
- **Certificados** : Suporta certificados, que são construídos em cima de chaves e segredos e adicionam uma funcionalidade de renovação automatizada. Para mais informações, consulte [sobre os certificados.](../certificates/about-certificates.md)
- **Chaves da conta Azure Storage** : Pode gerir as chaves de uma conta de armazenamento Azure para si. Internamente, o Key Vault pode listar as teclas (sincronização) com uma Conta de Armazenamento Azure e regenerar (rodar) as teclas periodicamente. Para obter mais informações, consulte [Gerir as chaves da conta de armazenamento com o Key Vault](../secrets/overview-storage-keys.md).

Para obter informações mais gerais sobre o Key Vault, consulte [About Azure Key Vault](overview.md). Para obter mais informações sobre piscinas geridas de HSM, veja o que é [Azure Key Vault Managed HSM?](../managed-hsm/overview.md)


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

Um identificador de objetos tem o seguinte formato geral (dependendo do tipo de recipiente):  

- **Para cofres:**`https://{vault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

- **Para piscinas geridas de HSM:**`https://{hsm-name}.managedhsm.azure.net/{object-type}/{object-name}/{object-version}`  

> [!NOTE]
> Consulte [o suporte do tipo de objeto](#object-types) para tipos de objetos suportados por cada tipo de recipiente.

Em que:  

| Elemento | Descrição |  
|-|-|  
|`vault-name` ou `hsm-name`|O nome de um cofre ou de uma piscina HSM gerida no serviço Microsoft Azure Key Vault.<br /><br />Os nomes do cofre e os nomes de piscinas geridos do HSM são selecionados pelo utilizador e são globalmente únicos.<br /><br />O nome do cofre e o nome da piscina gerida HSM devem ser uma corda de caracteres 3-24, contendo apenas 0-9, a-z, A-Z, e -.|  
|`object-type`|O tipo de objeto, "chaves", "segredos" ou "certificados".|  
|`object-name`|Um `object-name` é um nome fornecido pelo utilizador e deve ser único dentro de um Cofre chave. O nome deve ser uma corda de caracteres 1-127, começando com uma letra e contendo apenas 0-9, a-z, A-Z, e -.|  
|`object-version`|Um `object-version` é um identificador de cadeia de 32 caracteres gerado pelo sistema que é opcionalmente usado para abordar uma versão única de um objeto.|  

## <a name="next-steps"></a>Passos seguintes

- [Acerca de chaves](../keys/about-keys.md)
- [Acerca de segredos](../secrets/about-secrets.md)
- [Acerca de certificados](../certificates/about-certificates.md)
- [Autenticação, pedidos e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Programador do Key Vault](../general/developers-guide.md)
