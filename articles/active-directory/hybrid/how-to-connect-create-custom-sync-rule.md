---
title: Como personalizar uma regra de sincronização no Azure AD Connect | Documentos da Microsoft
description: Este tópico fornece os passos sobre como resolver problemas com a instalação do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a65d4c477d0e3aa9d5feea53e3e667ece651c83f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60351073"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Como personalizar uma regra de sincronização

## <a name="recommended-steps"></a>**Passos Recomendados**

Pode usar o editor de regra de sincronização para editar ou criar uma nova regra de sincronização. Tem de ser um usuário avançado para fazer alterações às regras de sincronização. Quaisquer alterações incorretas podem resultar na eliminação de objetos do diretório de destino. Leia [recomendado documentos](#recommended-documents) para obter conhecimentos sobre as regras de sincronização. Para modificar uma regra de sincronização passar por seguindo estes passos:

* Inicie o editor de sincronização no menu do aplicativo no desktop, conforme mostrado abaixo:

    ![Menu do Editor de regras de sincronização](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Para personalizar uma regra de sincronização padrão, clone a regra existente ao clicar no botão "Editar" no Editor de regras de sincronização, que irá criar uma cópia da regra predefinida padrão e desativá-la. Guarde a regra de clonado com uma precedência inferior a 100.  Precedência determina quais regra wins (valor numérico inferior) uma resolução de conflitos se houver um conflito de fluxo de atributo.

    ![Editor de regras de sincronização](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* Ao modificar um atributo específico, idealmente deve manter apenas o atributo de modificação na regra clonada.  Em seguida, ative a regra predefinida para que o atributo modificado vem da regra clonada e outros atributos são escolhidos de regra predefinida do padrão. 

* Tenha em atenção que no caso em que o valor calculado do atributo modificado está como NULL na sua regra clonada e não é nulo no padrão padrão de regra, em seguida, não um valor nulo vencem e substituir o valor NULL. Se não pretender que um valor nulo para ser substitua um valor NULL não, em seguida, atribuir AuthoritativeNull na sua regra clonada.

* Para modificar uma **saída** de regra, altere o filtro do editor de regra de sincronização.

## <a name="recommended-documents"></a>**Documentos Recomendados**
* [Sincronização do Azure AD Connect: Technical Concepts](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts) (Sincronização do Azure AD Connect: Conceitos Técnicos)
* [Sincronização do Azure AD Connect: Understanding the architecture](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture) (Sincronização do Azure AD Connect: Compreender a arquitetura)
* [Sincronização do Azure AD Connect: Understanding Declarative Provisioning](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning) (Sincronização do Azure AD Connect: Compreender o Aprovisionamento Declarativo)
* [Sincronização do Azure AD Connect: Understanding Declarative Provisioning Expressions](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions) (Sincronização do Azure AD Connect: Compreender as Expressões do Aprovisionamento Declarativo)
* [Sincronização do Azure AD Connect: Understanding the default configuration](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration) (Sincronização do Azure AD Connect: Compreender a configuração predefinida)
* [Sincronização do Azure AD Connect: Understanding Users, Groups, and Contacts](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts) (Sincronização do Azure AD Connect: Compreender Utilizadores e Contactos)
* [Sincronização do Azure AD Connect: Atributos sombra](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Próximos Passos
- [Sincronização do Azure AD Connect](how-to-connect-sync-whatis.md).
- [O que é a identidade híbrida? ](whatis-hybrid-identity.md).
