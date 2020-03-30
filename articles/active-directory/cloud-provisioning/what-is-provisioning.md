---
title: O que é o fornecimento de identidade com a Azure AD? | Microsoft Docs
description: Descreve a visão geral do fornecimento de identidade.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76712548"
---
# <a name="what-is-identity-provisioning"></a>O que é o aprovisionamento de identidades?

Hoje em dia, as empresas e as empresas estão a tornar-se cada vez mais uma mistura de aplicações no local e na nuvem.  Os utilizadores exigem acesso a aplicações tanto no local como na nuvem. É necessário ter uma única identidade nestas várias aplicações (no local e na nuvem).

O provisionamento é o processo de criação de um objeto com base em determinadas condições, mantendo o objeto atualizado e apagando o objeto quando as condições já não estão satisfeitas. Por exemplo, quando um novo utilizador se junta à sua organização, esse utilizador é introduzido no sistema HR.  Nessa altura, o provisionamento pode criar uma conta de utilizador correspondente na nuvem, no Diretório Ativo, e diferentes aplicações às a que o utilizador precisa de acesso.  Isto permite ao utilizador começar a trabalhar e ter acesso às aplicações e sistemas de que necessitam no primeiro dia. 

![fornecimento de nuvem](media/what-is-provisioning/cloud1.png)

No que diz respeito ao Diretório Ativo Azure, o provisionamento pode ser dividido nos seguintes cenários-chave.  

- **[Provisionamento orientado por RH](#hr-driven-provisioning)**  
- **[Fornecimento de aplicativos](#app-provisioning)**  
- **[Fornecimento de diretórios](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>Provisionamento orientado por RH

![fornecimento de nuvem](media/what-is-provisioning/cloud2.png)

O fornecimento de RH para a nuvem envolve a criação de objetos (utilizadores, funções, grupos, etc.) com base na informação que está no seu sistema de RH.  

O cenário mais comum seria que, quando um novo funcionário se junta à sua empresa, eles são inseridos no sistema de RH.  Uma vez que isso ocorre, são provisionados para a nuvem.  Neste caso, Azure AD.  O fornecimento de RH pode abranger os seguintes cenários. 

- **Contratação** de novos colaboradores - Quando um novo colaborador é adicionado à cloud HR, uma conta de utilizador é automaticamente criada em Ative Directory, Azure Ative Directory, e opcionalmente Office 365 e outras aplicações SaaS suportadas pela Azure AD, com redação do endereço de e-mail para cloud HR.
- **Atributos e atualizações** de perfis dos colaboradores - Quando um registo de empregados é atualizado na cloud HR (como o seu nome, título ou gestor), a sua conta de utilizador será automaticamente atualizada em Ative Directory, Azure Ative Directory, e opcionalmente office 365 e outras aplicações SaaS suportadas pela Azure AD.
- **Rescisões** de funcionários - Quando um empregado é rescindido na cloud HR, a sua conta de utilizador é automaticamente desativada em Diretório Ativo, Diretório Ativo Azure e opcionalmente Office 365 e outras aplicações SaaS suportadas pela Azure AD.
- **Recontratações** de funcionários - Quando um empregado é recontratado na cloud HR, a sua conta antiga pode ser automaticamente reativada ou reaprovisionada (dependendo da sua preferência) para ative directory, Azure Ative Directory, e opcionalmente Office 365 e outras aplicações SaaS apoiadas pela Azure AD.


## <a name="app-provisioning"></a>Fornecimento de aplicativos

![fornecimento de nuvem](media/what-is-provisioning/cloud3.png)

No Azure Ative Directory (Azure AD), o fornecimento de aplicações de prazo refere-se à **[criação](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)** automática de identidades e funções de utilizador nas aplicações na nuvem a que os utilizadores precisam de ter acesso. Além de criar identidades de utilizador, o fornecimento automático inclui a manutenção e remoção das identidades dos utilizadores como alteração de estado ou funções. Os cenários comuns incluem o fornecimento de um utilizador de AD Azure em aplicações como [Dropbox,](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial) [Salesforce,](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial) [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)e muito mais.

## <a name="directory-provisioning"></a>Fornecimento de diretórios

![fornecimento de nuvem](media/what-is-provisioning/cloud4.png)

O fornecimento no local envolve o fornecimento de fontes no local (como o Diretório Ativo) à AD Azure.  

O cenário mais comum seria, quando um utilizador em Diretório Ativo (AD) é aprovisionado em Azure AD.

Isto foi conseguido pelo sincronizado Azure AD Connect, pelo fornecimento de nuvem Azure AD Connect e pelo Microsoft Identity Manager. 
 
## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)
- [Instalar o fornecimento de nuvem](how-to-install.md)
