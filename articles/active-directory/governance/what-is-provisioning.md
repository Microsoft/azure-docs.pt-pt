---
title: O que está a providenciar com o Azure Ative Directory? | Microsoft Docs
description: Descreve a visão geral do fornecimento de identidade e dos cenários do ILM.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 640367d1b833f61e8a83fe9ce6b14d6d799cf9b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172457"
---
# <a name="what-is-provisioning"></a>O que é o aprovisionamento?

O provisionamento e a desprovisionamento são os processos que garantem a consistência das identidades digitais em vários sistemas.  Estes processos são normalmente alavancados como parte da gestão do ciclo de vida da [identidade.](what-is-identity-lifecycle-management.md)

**O provisionamento** são os processos de criação de uma identidade num sistema-alvo baseado em determinadas condições.  **A desesvisão** é o processo de remoção da identidade do sistema-alvo, quando as condições já não estão reunidas. **A sincronização** é o processo de manter o objeto alocionado, atualizado, de modo a que o objeto de origem e o objeto alvo sejam semelhantes.

Por exemplo, quando um novo empregado se junta à sua organização, esse funcionário é inscrito no sistema de RH.  Nessa altura, o fornecimento **de** RH **para** Azure Ative Directory (Azure AD) pode criar uma conta de utilizador correspondente em Azure AD. As aplicações que consultam a Azure AD podem ver a conta do novo empregado.  Se existirem aplicações que não utilizem o Azure AD, então o fornecimento do AZure AD **às** bases **de** dados dessas aplicações, garante que o utilizador poderá aceder a todas as aplicações a que o utilizador precisa de acesso.  Este processo permite ao utilizador iniciar o seu trabalho e ter acesso às aplicações e sistemas de que necessita no primeiro dia.  Da mesma forma, quando as suas propriedades, como o seu departamento ou o seu estatuto laboral, mudam no sistema de RH, a sincronização dessas atualizações do sistema de RH para a Azure AD e, além disso, para outras aplicações e bases de dados-alvo, garante a coerência.

A Azure AD fornece atualmente três áreas de provisão automatizada.  A saber:  

- Provisionamento de um sistema de registo não-directório externo para Azure AD, através **[de provisão orientada para os recursos humanos](#hr-driven-provisioning)**  
- Provisionamento da Azure AD às aplicações, através do **[provisionamento de aplicações](#app-provisioning)**  
- Provisionamento entre a Azure AD e os serviços de domínio do diretório ativo, através do **[provisionamento inter-directório](#inter-directory-provisioning)** 

![gestão do ciclo de vida identitário](media/what-is-provisioning/provisioning.png)

## <a name="hr-driven-provisioning"></a>Provisão orientada para os recursos humanos

![Fornecimento de RH](media/what-is-provisioning/cloud-2a.png)

O fornecimento de RH a AZure AD envolve a criação de objetos, tipicamente identidades de utilizador representando cada colaborador, mas em alguns casos outros objetos que representam departamentos ou outras estruturas, com base na informação que está no seu sistema de RH.  

O cenário mais comum seria, quando um novo empregado entra na sua empresa, eles são inseridos no sistema de RH.  Uma vez que isso ocorre, eles são automaticamente a provisionados como um novo utilizador em Azure AD, sem necessidade de envolvimento administrativo para cada novo aluguer.  Em geral, o fornecimento de RECURSOS pode abranger os seguintes cenários.

- **Contratação de novos colaboradores** - Quando um novo empregado é adicionado a um sistema de RH, uma conta de utilizador é criada automaticamente em Ative Directory, Azure AD, e opcionalmente nos diretórios para outras aplicações suportadas pela Azure AD, com a desativação do endereço de e-mail para o sistema rh.
- **Atributos e atualizações de perfis do colaborador** - Quando um registo de empregados é atualizado nesse sistema de RH (como o seu nome, título ou gestor), a sua conta de utilizador será automaticamente atualizada em Ative Directory, Azure AD, e opcionalmente outras aplicações suportadas pela Azure AD.
- **Rescisões do empregado** - Quando um empregado é encerrado em RH, a sua conta de utilizador é automaticamente bloqueada de iniciar sação ou remoção em Ative Directory, Azure AD e noutras aplicações.
- **Recontrações de empregados** - Quando um empregado é recontratado na cloud HR, a sua conta antiga pode ser automaticamente reativada ou re-provisionada (dependendo da sua preferência).

Existem três opções de implementação para o fornecimento orientado por RH com Azure AD:

1. Para organizações com uma única subscrição para Workday ou SuccessFactors, e não usar o Ative Directory
1. Para organizações com uma única subscrição para Workday ou SuccessFactors, e têm diretório ativo e Ad AD AZure
1. Para organizações com múltiplos sistemas de RH, ou um sistema de RH no local, como SAP, Oracle eBusiness ou PeopleSoft

Para obter mais informações, veja [o que é o fornecimento conduzido por RH?](what-is-hr-driven-provisioning.md)

## <a name="app-provisioning"></a>Fornecimento de aplicativos

![fornecimento de aplicativos](media/what-is-provisioning/cloud-3b.png)

No Azure AD, o termo **[provisionamento de aplicações](../app-provisioning/user-provisioning.md)** refere-se à criação automática de cópias das identidades dos utilizadores nas aplicações a que os utilizadores precisam de acesso, para aplicações que tenham a sua própria loja de dados, distintas da AZure AD ou Ative Directory. Além de criar identidades de utilizadores, o fornecimento de aplicações inclui a manutenção e remoção das identidades dos utilizadores dessas aplicações, à medida que o estado ou as funções do utilizador mudam. Os cenários comuns incluem a disponibilização de um utilizador Azure AD em aplicações como [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow,](../saas-apps/servicenow-provisioning-tutorial.md)uma vez que cada uma destas aplicações tem o seu próprio repositório de utilizadores distinto do AD Azure.

Para obter mais informações, veja [o que é o fornecimento de aplicativos?](what-is-app-provisioning.md)

## <a name="inter-directory-provisioning"></a>Provisionamento inter-directório

![provisão inter-directório](media/what-is-provisioning/cloud-4a.png)

Muitas organizações confiam tanto no Ative Directory como no Azure AD, e podem ter aplicações ligadas ao Ative Directory, como servidores de ficheiros no local.

Como muitas organizações historicamente têm implementado o fornecimento de RH no local, eles podem já ter identidades de utilizador para todos os seus funcionários em Ative Directory.   O cenário mais comum para o provisionamento inter-directório é quando um utilizador já em Ative Directory é adcedido no Azure AD.  Este provisionamento é geralmente realizado por Azure AD Connect sync ou Azure AD Connect provisioning cloud. 

Além disso, as organizações podem também querer prever sistemas no local a partir da Azure AD.  Por exemplo, uma organização pode ter trazido os hóspedes para o diretório AD Azure, mas esses hóspedes precisarão de acesso a aplicações web baseadas no Windows Integrated Authentication (WIA) através do proxy da aplicação.  Isto requer o provisionamento de contas AD no local para os utilizadores em Azure AD.

Para obter mais informações, veja [o que é o fornecimento de inter-directórios?](what-is-inter-directory-provisioning.md)

 
## <a name="next-steps"></a>Passos seguintes 
- [O que é a gestão do ciclo de vida de identidades?](what-is-identity-lifecycle-management.md)
- [O que é o fornecimento conduzido pela HR?](what-is-hr-driven-provisioning.md)
- [O que é o aprovisionamento de aplicações?](what-is-app-provisioning.md)
- [O que é o aprovisionamento entre diretórios?](what-is-inter-directory-provisioning.md)