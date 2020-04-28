---
title: Como personalizar uma regra de sincronização no Azure AD Connect [ Ligação AD] Microsoft Docs'
description: Este tópico fornece passos para como resolver problemas com a instalação do Azure AD Connect.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "60351073"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Como personalizar uma regra de sincronização

## <a name="recommended-steps"></a>**Passos Recomendados**

Pode utilizar o editor de regras de sincronização para editar ou criar uma nova regra de sincronização. É preciso ser um utilizador avançado para fazer alterações às regras de sincronização. Quaisquer alterações erradas podem resultar na eliminação de objetos do seu directório-alvo. Leia [documentos recomendados](#recommended-documents) para obter conhecimentos especializados em regras de sincronização. Para modificar uma regra de sincronização, passe por seguintes passos:

* Lance o editor de sincronização a partir do menu de aplicações no ambiente de trabalho, como mostrado abaixo:

    ![Menu de editor de regras de sincronização](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Para personalizar uma regra de sincronização predefinida, clone a regra existente clicando no botão "Editar" no Editor de Regras de Sincronização, que criará uma cópia da regra padrão padrão e a desativará. Salve a regra clonada com uma precedência inferior a 100.  Precedência determina que regra ganha (valor numérico inferior) uma resolução de conflito se houver um conflito de fluxo de atributos.

    ![Editor de Regras de Sincronização](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* Ao modificar um atributo específico, o ideal é manter o atributo modificador na regra clonada.  Em seguida, ativar a regra padrão de modo a que o atributo modificado provém da regra clonada e outros atributos sejam escolhidos a partir da regra padrão padrão padrão. 

* Por favor, note que no caso em que o valor calculado do atributo modificado é NULO na sua regra clonada e não é NULO na regra padrão padrão então, o valor não NULO ganhará e substituirá o valor NULO. Se não quiser que um valor NULO seja substituído por um valor não NULO, atribua o AuthoritativeNull na sua regra clonada.

* Para modificar uma regra de **Saída,** mude o filtro do editor de regras de sincronização.

## <a name="recommended-documents"></a>**Documentos Recomendados**
* [Sincronização azure AD Connect: Conceitos Técnicos](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Sincronização Azure AD Connect: Compreender a arquitetura](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Sincronização Azure AD Connect: Compreensão do Provisionamento Declarativo](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Sincronização Azure AD Connect: Compreender expressões de provisionamento declarativas](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Sincronização do Azure AD Connect: entender a configuração predefinida](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Sincronização azure AD Connect: Compreender utilizadores, grupos e contactos](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Sincronização Azure AD Connect: Atributos de sombra](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Passos Seguintes
- [Sincronização Azure AD Connect](how-to-connect-sync-whatis.md).
- [O que é a identidade híbrida?](whatis-hybrid-identity.md)
