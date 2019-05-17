---
title: Sincronizar atributos para o Azure AD para o mapeamento | Documentos da Microsoft
description: Saiba como sincronizar atributos do Active Directory no local para o Azure AD. Ao configurar o aprovisionamento de utilizadores para aplicações SaaS, utilize a funcionalidade de extensão de diretório para adicionar os atributos de origem que não são sincronizados por predefinição.
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
ms.openlocfilehash: a9460bc924ea662646360d1a3f5087949a39a03f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806120"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Sincronizar um atributo do Active Directory no local ao Azure AD para aprovisionamento para uma aplicação

Quando personalizar mapeamentos de atributos para o aprovisionamento de utilizador, pode achar que o atributo que pretende mapear não aparece na **atributo de origem** lista. Este artigo mostra-lhe como adicionar o atributo em falta por sincronizá-lo a partir do seu local do Active Directory (AD) para o Azure Active Directory (Azure AD).

O Azure AD tem de conter todos os dados necessários para criar um perfil de utilizador quando o aprovisionamento de contas de utilizador do Azure AD para uma aplicação SaaS. Em alguns casos, para disponibilizar os dados poderá precisar de sincronizar atributos do seu local AD para o Azure AD. Azure AD Connect sincroniza automaticamente determinados atributos para o Azure AD, mas nem todos os atributos. Além disso, alguns atributos (por exemplo, SAMAccountName), que são sincronizados por predefinição não podem ser expostos através da API do Azure AD Graph. Nestes casos, pode usar a funcionalidade de extensão de diretório do Azure AD Connect para sincronizar o atributo para o Azure AD. Dessa forma, o atributo estarão visível para o Azure AD Graph API e o serviço de aprovisionamento do AD do Azure.

Se os dados que necessários para o aprovisionamento estiver no Active Directory, mas não estão disponíveis para o aprovisionamento pelos motivos descritos acima, siga estes passos.
 
## <a name="sync-an-attribute"></a>Um atributo de sincronização 

1. Abrir o Assistente do Azure AD Connect, escolha as tarefas e, em seguida, escolha **personalizar as opções de sincronização**.

   ![Página do Azure Active Directory Connect assistente tarefas adicionais](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Inicie sessão como Administrador Global do Azure AD. 

3. Sobre o **funcionalidades opcionais** página, selecione **sincronização de atributos de extensão de diretórios**.
 
   ![Página do Azure Active Directory Connect assistente funcionalidades opcionais](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Selecione os atributos que quer expandir para o Azure AD.
   > [!NOTE]
   > A pesquisa em **atributos disponíveis** diferencia maiúsculas de minúsculas.

   ![O Azure Active Directory Connect Directory extensões seleção página do Assistente](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Concluir o Assistente do Azure AD Connect e permitir que um ciclo de sincronização completa executar. Quando o ciclo de estiver concluído, o esquema é expandido e os novos valores são sincronizados entre o ambiente AD e o Azure AD.
 
6. No portal do Azure, enquanto estiver [editar mapeamentos de atributos de utilizador](customize-application-attributes.md), o **atributo de origem** lista agora irá conter o atributo adicionado no formato `<attributename> (extension_<appID>_<attributename>)`. Selecione o atributo e mapeá-lo para o aplicativo de destino para o aprovisionamento.

   ![O Azure Active Directory Connect Directory extensões seleção página do Assistente](media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> A capacidade de aprovisionar os atributos de referência do AD no local, tal como **managedby** ou **DN/DistinguishedName**, não é suportada atualmente. Pode pedir esta funcionalidade no [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Passos Seguintes

* [Definir a quem é no âmbito de aprovisionamento](define-conditional-rules-for-provisioning-user-accounts.md)
