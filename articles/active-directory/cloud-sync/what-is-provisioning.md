---
title: O que é o fornecimento de identidade com Azure AD? | Microsoft Docs
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
ms.openlocfilehash: a1c85f2a6d58a5dbeae93b951cea812d6aa91326
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98614830"
---
# <a name="what-is-identity-provisioning"></a>O que é o aprovisionamento de identidades?

Hoje em dia, as empresas e as empresas estão a tornar-se cada vez mais uma mistura de aplicações no local e aplicações em nuvem.  Os utilizadores exigem acesso a aplicações tanto no local como na nuvem. É necessário ter uma única identidade nestas várias aplicações (no local, bem como em nuvem).

O provisionamento é o processo de criação de um objeto baseado em determinadas condições, mantendo o objeto atualizado e eliminando o objeto quando as condições já não estão reunidas. Por exemplo, quando um novo utilizador se junta à sua organização, esse utilizador é introduzido no sistema de RH.  Nessa altura, o provisionamento pode criar uma conta de utilizador correspondente na nuvem, no Ative Directory, e diferentes aplicações às que o utilizador necessita de acesso.  Isto permite ao utilizador começar a trabalhar e ter acesso às aplicações e sistemas de que necessita no primeiro dia. 

![Diagrama que mostra provisão de nuvem com diretório ativo Azure.](media/what-is-provisioning/cloud-1.png)

No que diz respeito ao Azure Ative Directory, o provisionamento pode ser discriminado nos seguintes cenários-chave.  

- **[Provisão orientada para os recursos humanos](#hr-driven-provisioning)**  
- **[Fornecimento de aplicativos](#app-provisioning)**  
- **[Provisionamento de diretórios](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>Provisão orientada para os recursos humanos

![Diagrama que mostra provisão orientada para o RH com Cloud HR, On-ins HR e Azure Ative Directory.](media/what-is-provisioning/cloud-2.png)

O fornecimento de RH para a nuvem envolve a criação de objetos (utilizadores, papéis, grupos, etc.) com base na informação que está no seu sistema de RH.  

O cenário mais comum seria, quando um novo empregado entra na sua empresa, eles são inseridos no sistema de RH.  Uma vez que isso ocorre, eles são a provisionados para a nuvem.  Neste caso, Azure AD.  O fornecimento de RECURSOS pode abranger os seguintes cenários. 

- **Contratação de novos colaboradores** - Quando um novo empregado é adicionado à cloud HR, uma conta de utilizador é criada automaticamente em Ative Directory, Azure Ative Directory, e opcionalmente Microsoft 365 e outras aplicações SaaS suportadas pela Azure AD, com a desativação do endereço de e-mail para Cloud HR.
- **Atributos e atualizações de perfis do colaborador** - Quando um registo de empregados é atualizado na cloud HR (como o seu nome, título ou gestor), a sua conta de utilizador será automaticamente atualizada no Ative Directory, no Azure Ative Directory e opcionalmente no Microsoft 365 e noutras aplicações SaaS suportadas pela Azure AD.
- **Rescisões de funcionários** - Quando um empregado é encerrado na cloud HR, a sua conta de utilizador é automaticamente desativada em Ative Directory, Azure Ative Directory, e opcionalmente Office 365 e outras aplicações SaaS apoiadas pela Azure AD.
- **Recontrações de empregados** - Quando um empregado é recontratado na cloud HR, a sua conta antiga pode ser automaticamente reativada ou re-a provisionada (dependendo da sua preferência) para o Ative Directory, Azure Ative Directory e opcionalmente Microsoft 365 e outras aplicações SaaS apoiadas pela Azure AD.


## <a name="app-provisioning"></a>Fornecimento de aplicativos

![Diagrama que mostra o fornecimento de aplicativos com aplicações on-in, aplicações de nuvem não Microsoft e Azure Ative Directory.](media/what-is-provisioning/cloud-3.png)

No Azure Ative Directory (Azure AD), o termo **[provisionamento de aplicações](../app-provisioning/user-provisioning.md)** refere-se à criação automática de identidades e funções dos utilizadores nas aplicações em nuvem a que os utilizadores precisam de acesso. Além da criação de identidades de utilizador, o fornecimento automático inclui a manutenção e remoção das identidades dos utilizadores como alteração de estado ou de funções. Os cenários comuns incluem a disponibilização de um utilizador AZure AD em aplicações como [Dropbox,](../saas-apps/dropboxforbusiness-provisioning-tutorial.md) [Salesforce,](../saas-apps/salesforce-provisioning-tutorial.md) [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)e muito mais.

## <a name="directory-provisioning"></a>Provisionamento de diretórios

![fornecimento de nuvens](media/what-is-provisioning/cloud-4.png)

O provisionamento no local envolve o fornecimento de fontes no local (como o Ative Directory) para a Azure AD.  

O cenário mais comum seria, quando um utilizador em Ative Directory (AD) é a provisionado em Azure AD.

Isto foi realizado por Azure AD Connect sync, Azure AD Connect cloud provisioning e Microsoft Identity Manager. 
 
## <a name="next-steps"></a>Passos seguintes 

- [O que é a sincronização de nuvem AZure AD Connect?](what-is-cloud-sync.md)
- [Instalar provisão de nuvens](how-to-install.md)