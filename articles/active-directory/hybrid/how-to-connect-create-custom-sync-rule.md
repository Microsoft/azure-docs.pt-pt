---
title: Como personalizar uma regra de sincronização no Azure AD Connect Microsoft Docs'
description: Aprenda a usar o editor de regras de sincronização para editar ou criar uma nova regra de sincronização.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2bb86988454141dc692b4a9967997c4ff7574a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530493"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Como personalizar uma regra de sincronização

## <a name="recommended-steps"></a>**Passos Recomendados**

Pode utilizar o editor de regras de sincronização para editar ou criar uma nova regra de sincronização. É necessário ser um utilizador avançado para fazer alterações às regras de sincronização. Quaisquer alterações erradas podem resultar na eliminação de objetos do seu diretório alvo. Leia [Os Documentos Recomendados](#recommended-documents) para obter conhecimentos especializados em regras de sincronização. Para modificar uma regra de sincronização, passar pelos seguintes passos:

* Lance o editor de sincronização a partir do menu de aplicações no ambiente de trabalho, como mostrado abaixo:

    ![Menu de editor de regras de sincronização](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Para personalizar uma regra de sincronização padrão, clone a regra existente clicando no botão "Editar" no Editor de Regras de Sincronização, que criará uma cópia da regra padrão padrão e a desativará. Salve a regra clonada com uma precedência inferior a 100.  A precedência determina qual a regra que ganha (menor valor numérico) uma resolução de conflito se houver um conflito de fluxo de atributos.

    ![Editor de Regras de Sincronização](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* Ao modificar um atributo específico, o ideal é apenas manter o atributo modificador na regra clonada.  Em seguida, ative a regra padrão de modo que o atributo modificado provém da regra clonada e outros atributos são escolhidos a partir da regra padrão padrão. 

* Por favor, note que no caso de o valor calculado do atributo modificado ser NULO na sua regra clonada e não ser NULO na regra padrão padrão então, o valor não NU GANHARá e substituirá o valor NU. Se não pretender que um valor NULO seja substituído por um valor não NULO, atribua a AuthorititáriaNull na sua regra clonada.

* Para modificar uma regra **de saída,** altere o filtro do editor de regras de sincronização.

## <a name="recommended-documents"></a>**Documentos Recomendados**
* [Azure AD Connect Sync: Conceitos Técnicos](./how-to-connect-sync-technical-concepts.md)
* [Azure AD Connect sync: Understanding the architecture](./concept-azure-ad-connect-sync-architecture.md)
* [Azure AD Connect sync: Understanding Declarative Provisioning](./concept-azure-ad-connect-sync-declarative-provisioning.md)
* [Azure AD Connect sync: Understanding Declarative Provisioning Expressions](./concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Sincronização do Azure AD Connect: entender a configuração predefinida](./concept-azure-ad-connect-sync-default-configuration.md)
* [Azure AD Connect sync: Understanding Users, Groups e Contacts](./concept-azure-ad-connect-sync-user-and-contacts.md)
* [Azure AD Connect Sync: Shadow atributos](./how-to-connect-syncservice-shadow-attributes.md)

## <a name="next-steps"></a>Passos Seguintes
- [Sincronização Azure Ad Connect](how-to-connect-sync-whatis.md).
- [O que é identidade híbrida?](whatis-hybrid-identity.md)