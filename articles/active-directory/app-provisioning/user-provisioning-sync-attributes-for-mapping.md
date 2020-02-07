---
title: Sincronizar atributos ao Azure AD para mapeamento / Microsoft Docs
description: Aprenda a sincronizar atributos do seu Diretório Ativo no local para o Azure AD. Ao configurar o fornecimento de utilizadores para aplicações SaaS, utilize a funcionalidade de extensão de diretório para adicionar atributos de origem que não são sincronizados por padrão.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f49fce985484e85bcba2883a66ec0b1e6d032a8
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066047"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Sincronizar um atributo do seu Diretório Ativo no local para a AD Azure para o fornecimento a uma aplicação

Ao personalizar mapeamentos de atributos para o fornecimento de utilizadores, pode descobrir que o atributo que pretende mapear não aparece na lista de **atributos Fonte.** Este artigo mostra-lhe como adicionar o atributo em falta sincronizando-o do seu Diretório Ativo (AD) no local para o Azure Ative Directory (Azure AD).

A Azure AD deve conter todos os dados necessários para criar um perfil de utilizador ao fornecer contas de utilizador de Azure AD para uma aplicação SaaS. Em alguns casos, para disponibilizar os dados poderá necessitar de atributos sincronizados desde o seu AD no local até à AD Azure. O Azure AD Connect sincroniza automaticamente certos atributos ao Azure AD, mas nem todos os atributos. Além disso, alguns atributos (como o SAMAccountName) que são sincronizados por padrão podem não ser expostos através da API do Gráfico AD Azure. Nestes casos, pode utilizar a função de extensão do diretório Azure AD Connect para sincronizar o atributo ao Azure AD. Desta forma, o atributo será visível para a API do Gráfico AD Azure e para o serviço de provisionamento de AD Azure.

Se os dados necessários para o fornecimento estiverem no Diretório Ativo, mas não estiverem disponíveis para o fornecimento devido às razões acima descritas, siga estes passos.
 
## <a name="sync-an-attribute"></a>Sincronizar um atributo 

1. Abra o assistente Azure AD Connect, escolha Tarefas e, em seguida, escolha **personalizar opções**de sincronização .

   ![Página adicional de tarefas de ligação do diretório ativo Azure](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Inscreva-se como Administrador Global da AD Azure. 

3. Na página **Funcionalidades Opcionais,** selecione sincronização de atributo de **extensão do Diretório**.
 
   ![Página opcional de funcionalidades opcional do diretório Ativo Azure](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Selecione o atributo(s) que pretende estender para Azure AD.
   > [!NOTE]
   > A pesquisa em **Atributos Disponíveis** é sensível a casos.

   ![Página de seleção de extensões de direção de diretório de hiperdireção do diretório ativo azure](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Termine o assistente Azure AD Connect e deixe funcionar um ciclo de sincronização completo. Quando o ciclo está concluído, o esquema é estendido e os novos valores são sincronizados entre o seu AD no local e o Azure AD.
 
6. No portal Azure, enquanto está [a editar mapeamentos de atributos](customize-application-attributes.md)de utilizador, a lista de **atributos Source** irá agora conter o atributo adicionado no formato `<attributename> (extension_<appID>_<attributename>)`. Selecione o atributo e mapeie-o para a aplicação-alvo para o fornecimento.

   ![Página de seleção de extensões de direção de diretório de hiperdireção do diretório ativo azure](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> A capacidade de fornecer atributos de referência a partir de AD no local, tais como **gerido sou** **DN/DistinguishedName,** não é suportado hoje. Pode solicitar esta funcionalidade na [Voz do Utilizador](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Passos seguintes

* [Definir quem está no âmbito do provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
