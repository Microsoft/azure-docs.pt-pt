---
title: Sincronizar atributos ao AZure AD para mapeamento
description: Aprenda a sincronizar atributos do seu Ative Directory para Azure AD. Ao configurar o fornecimento de utilizadores para aplicações SaaS, utilize a funcionalidade de extensão do diretório para adicionar atributos de origem que não são sincronizados por padrão.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/13/2019
ms.author: kenwith
ms.openlocfilehash: 62d035b85850f8ac455a85fd93e4d081bbd386e1
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256090"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Sync um atributo do seu Ative Directy no local para Azure AD para provisionamento a uma aplicação

Ao personalizar mapeamentos de atributos para o fornecimento do utilizador, poderá descobrir que o atributo que pretende mapear não aparece na lista **de atributos Source.** Este artigo mostra-lhe como adicionar o atributo em falta sincronizando-o do seu Ative Directory (AD) no local ao Azure Ative Directory (Azure AD).

O Azure AD deve conter todos os dados necessários para criar um perfil de utilizador ao aprovisionar as contas dos utilizadores do Azure AD para uma aplicação SaaS. Em alguns casos, para disponibilizar os dados poderá necessitar de sincronização de atributos do seu AD no local para Azure AD. O Azure AD Connect sincroniza automaticamente determinados atributos ao AZure AD, mas nem todos os atributos. Além disso, alguns atributos (como o SAMAccountName) que são sincronizados por padrão podem não ser expostos usando a API do Microsoft Graph. Nestes casos, pode utilizar a função de extensão de diretório AD Connect Azure para sincronizar o atributo a Azure AD. Desta forma, o atributo será visível para a API do Microsoft Graph e para o serviço de fornecimento de AD Azure.

Se os dados necessários para o provisionamento estiverem no Ative Directory mas não estiverem disponíveis para provisões devido às razões acima descritas, siga estes passos.
 
## <a name="sync-an-attribute"></a>Sincronizar um atributo 

1. Abra o assistente Azure AD Connect, escolha Tarefas e, em seguida, escolha **Opções de sincronização personalizadas**.

   ![Página de tarefas adicionais do Azure Ative Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Inscreva-se como administrador global da Azure AD. 

3. Na página **Funcionalidades Opcionais,** selecione **sincronização de atributos de extensão do diretório**.
 
   ![Página de funcionalidades opcionais do assistente de ligação ativa do Azure](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Selecione os atributos(s) que pretende estender para Azure AD.
   > [!NOTE]
   > A pesquisa em **Atributos Disponíveis** é sensível a casos.

   ![Screenshot que mostra a página de seleção "Extensões de Diretório"](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Termine o assistente Azure AD Connect e permita que um ciclo completo de sincronização possa ser executado. Quando o ciclo estiver concluído, o esquema é estendido e os novos valores são sincronizados entre o AD no local e o AD AZure.
 
6. No portal Azure, enquanto [está a editar mapeamentos de atributos](customize-application-attributes.md)do utilizador, a lista de **atributos Fonte** irá agora conter o atributo adicionado no formato `<attributename> (extension_<appID>_<attributename>)` . Selecione o atributo e mape-o para a aplicação-alvo para provisionamento.

   ![Página de seleção de extensões de extensões de diretório de assistente azure Ative Connect](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> A capacidade de provisão de atributos de referência de AD no local, como **gerido por ou** **DN/DistinguishedName,** não é suportada hoje em dia. Pode solicitar esta funcionalidade no [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Passos seguintes

* [Definir quem está no âmbito do provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
