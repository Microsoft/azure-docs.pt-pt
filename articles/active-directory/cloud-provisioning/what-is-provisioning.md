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
ms.openlocfilehash: 28513c57101af67695d10056b3dc8e6537dcddb2
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712548"
---
# <a name="what-is-identity-provisioning"></a>O que é o aprovisionamento de identidades?

Hoje, as empresas e as empresas estão se tornando cada vez mais uma combinação de aplicativos locais e na nuvem.  Os usuários exigem acesso a aplicativos locais e na nuvem. É necessário ter uma única identidade entre esses vários aplicativos (local, bem como nuvem).

O provisionamento é o processo de criação de um objeto com base em determinadas condições, mantendo o objeto atualizado e apagando o objeto quando as condições já não estão satisfeitas. Por exemplo, quando um novo usuário ingressa em sua organização, esse usuário é inserido no sistema de RH.  Nesse ponto, o provisionamento pode criar uma conta de usuário correspondente na nuvem, em Active Directory e diferentes aplicativos aos quais o usuário precisa acessar.  Isso permite que o usuário inicie o trabalho e tenha acesso aos aplicativos e sistemas de que precisam no primeiro dia. 

![provisionamento de nuvem](media/what-is-provisioning/cloud1.png)

Com relação ao Azure Active Directory, o provisionamento pode ser dividido nos seguintes cenários principais.  

- **[Provisionamento orientado por RH](#hr-driven-provisioning)**  
- **[Fornecimento de aplicativos](#app-provisioning)**  
- **[Fornecimento de diretórios](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>Provisionamento controlado por RH

![provisionamento de nuvem](media/what-is-provisioning/cloud2.png)

O provisionamento de RH para a nuvem envolve a criação de objetos (usuários, funções, grupos, etc.) com base nas informações que estão em seu sistema de RH.  

O cenário mais comum seria, quando um novo funcionário ingressar na sua empresa, ele será inserido no sistema de RH.  Quando isso ocorrer, eles serão provisionados para a nuvem.  Nesse caso, o Azure AD.  O provisionamento do RH pode abranger os cenários a seguir. 

- **Contratação** de novos colaboradores - Quando um novo colaborador é adicionado à cloud HR, uma conta de utilizador é automaticamente criada em Ative Directory, Azure Ative Directory, e opcionalmente Office 365 e outras aplicações SaaS suportadas pela Azure AD, com redação do endereço de e-mail para cloud HR.
- **Atributos e atualizações** de perfis dos colaboradores - Quando um registo de empregados é atualizado na cloud HR (como o seu nome, título ou gestor), a sua conta de utilizador será automaticamente atualizada em Ative Directory, Azure Ative Directory, e opcionalmente office 365 e outras aplicações SaaS suportadas pela Azure AD.
- **Rescisões** de funcionários - Quando um empregado é rescindido na cloud HR, a sua conta de utilizador é automaticamente desativada em Diretório Ativo, Diretório Ativo Azure e opcionalmente Office 365 e outras aplicações SaaS suportadas pela Azure AD.
- **Recontratações** de funcionários - Quando um empregado é recontratado na cloud HR, a sua conta antiga pode ser automaticamente reativada ou reaprovisionada (dependendo da sua preferência) para ative directory, Azure Ative Directory, e opcionalmente Office 365 e outras aplicações SaaS apoiadas pela Azure AD.


## <a name="app-provisioning"></a>Provisionamento de aplicativo

![provisionamento de nuvem](media/what-is-provisioning/cloud3.png)

No Azure Ative Directory (Azure AD), o fornecimento de aplicações de prazo refere-se à **[criação](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)** automática de identidades e funções de utilizador nas aplicações na nuvem a que os utilizadores precisam de ter acesso. Além de criar identidades de usuário, o provisionamento automático inclui a manutenção e a remoção de identidades de usuário à medida que o status ou as funções mudam. Os cenários comuns incluem o fornecimento de um utilizador de AD Azure em aplicações como [Dropbox,](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial) [Salesforce,](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial) [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)e muito mais.

## <a name="directory-provisioning"></a>Provisionamento de diretório

![provisionamento de nuvem](media/what-is-provisioning/cloud4.png)

O provisionamento local envolve o provisionamento de fontes locais (como Active Directory) para o Azure AD.  

O cenário mais comum seria, quando um usuário no Active Directory (AD) é provisionado no Azure AD.

Isso foi realizado por Azure AD Connect sincronização, Azure AD Connect provisionamento e Microsoft Identity Manager de nuvem. 
 
## <a name="next-steps"></a>Passos Seguintes 

- [O que é o fornecimento de nuvem Azure AD Connect?](what-is-cloud-provisioning.md)
- [Instalar o fornecimento de nuvem](how-to-install.md)
