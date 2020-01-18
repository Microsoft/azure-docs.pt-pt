---
title: O que é o provisionamento de identidade com o Azure AD? | Microsoft Docs
description: Descreve a visão geral do provisionamento de identidade.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 305108e576ad4e60089c929c9b780e8cee559540
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76167614"
---
# <a name="what-is-identity-provisioning"></a>O que é o aprovisionamento de identidades?

Hoje, as empresas e as empresas estão se tornando cada vez mais uma combinação de aplicativos locais e na nuvem.  Os usuários exigem acesso a aplicativos locais e na nuvem. É necessário ter uma única identidade entre esses vários aplicativos (local, bem como nuvem).

O provisionamento é o processo de criação de um objeto com base em determinadas condições, mantendo o objeto até a data e excluindo o objeto quando as condições não forem mais atendidas. Por exemplo, quando um novo usuário ingressa em sua organização, esse usuário é inserido no sistema de RH.  Nesse ponto, o provisionamento pode criar uma conta de usuário correspondente na nuvem, em Active Directory e diferentes aplicativos aos quais o usuário precisa acessar.  Isso permite que o usuário inicie o trabalho e tenha acesso aos aplicativos e sistemas de que precisam no primeiro dia. 

![provisionamento de nuvem](media/what-is-provisioning/cloud1.png)

Com relação ao Azure Active Directory, o provisionamento pode ser dividido nos seguintes cenários principais.  

- **[Provisionamento controlado por RH](#hr-driven-provisioning)**  
- **[Provisionamento de aplicativo](#app-provisioning)**  
- **[Provisionamento de diretório](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>Provisionamento controlado por RH

![provisionamento de nuvem](media/what-is-provisioning/cloud2.png)

O provisionamento de RH para a nuvem envolve a criação de objetos (usuários, funções, grupos, etc.) com base nas informações que estão em seu sistema de RH.  

O cenário mais comum seria, quando um novo funcionário ingressar na sua empresa, ele será inserido no sistema de RH.  Quando isso ocorrer, eles serão provisionados para a nuvem.  Nesse caso, o Azure AD.  O provisionamento do RH pode abranger os cenários a seguir. 

- **Contratando novos funcionários** – quando um novo funcionário é adicionado ao RH da nuvem, uma conta de usuário é criada automaticamente em Active Directory, Azure Active Directory e, opcionalmente, o Office 365 e outros aplicativos SaaS com suporte do Azure AD, com write-back do endereço de email para a nuvem de RH.
- **Atualizações de perfil e atributo de funcionário** – quando um registro de funcionário é atualizado no RH da nuvem (como seu nome, título ou gerente), sua conta de usuário será atualizada automaticamente no Active Directory, Azure Active Directory e, opcionalmente, no Office 365 e em outros aplicativos SaaS com suporte do Azure AD.
- **Encerramentos de funcionários** – quando um funcionário é encerrado na nuvem de RH, sua conta de usuário é automaticamente desabilitada em Active Directory, Azure Active Directory e, opcionalmente, o Office 365 e outros aplicativos SaaS com suporte do Azure AD.
- **Recontratação de funcionário** -quando um funcionário é recontratado na nuvem de RH, sua conta antiga pode ser reativada automaticamente ou reprovisionada (dependendo da sua preferência) para Active Directory, Azure Active Directory e, opcionalmente, o Office 365 e outros aplicativos SaaS com suporte do Azure AD.


## <a name="app-provisioning"></a>Provisionamento de aplicativo

![provisionamento de nuvem](media/what-is-provisioning/cloud3.png)

No Azure Active Directory (AD do Azure), o termo **[provisionamento de aplicativo](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)** refere-se à criação automática de identidades de usuário e funções nos aplicativos de nuvem aos quais os usuários precisam acessar. Além de criar identidades de usuário, o provisionamento automático inclui a manutenção e a remoção de identidades de usuário à medida que o status ou as funções mudam. Os cenários comuns incluem o provisionamento de um usuário do Azure AD em aplicativos como [Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial), [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)e muito mais.

## <a name="directory-provisioning"></a>Provisionamento de diretório

![provisionamento de nuvem](media/what-is-provisioning/cloud4.png)

O provisionamento local envolve o provisionamento de fontes locais (como Active Directory) para o Azure AD.  

O cenário mais comum seria, quando um usuário no Active Directory (AD) é provisionado no Azure AD.

Isso foi realizado por Azure AD Connect sincronização, Azure AD Connect provisionamento e Microsoft Identity Manager de nuvem. 
 
## <a name="next-steps"></a>Passos seguintes 

- [O que é Azure AD Connect provisionamento de nuvem?](what-is-cloud-provisioning.md)
- [Instalar o provisionamento de nuvem](how-to-install.md)
