---
title: Compreender a delegação de papel de administrador - Azure Ative Directory [ Azure Ative Directory ] Microsoft Docs
description: Modelos de delegação, exemplos e segurança de papéis no Diretório Ativo azure
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fa3c6bf39dbef601fe64e125999f519f725f2e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67083774"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Administração de delegados no Diretório Ativo de Azure

Com o crescimento organizacional vem a complexidade. Uma resposta comum é reduzir parte da carga de trabalho da gestão de acessocom funções de administração do Azure Ative Directory (AD). Pode atribuir o menor privilégio possível aos utilizadores para acederem às suas apps e executarem as suas tarefas. Mesmo que não atribua o papel de Administrador Global a todos os proprietários de aplicações, está a atribuir responsabilidades de gestão de aplicações aos administradores globais existentes. Há muitas razões para uma organização avançar para uma administração mais descentralizada. Este artigo pode ajudá-lo a planear a delegação na sua organização.

<!--What about reporting? Who has which role and how do I audit?-->

## <a name="centralized-versus-delegated-permissions"></a>Permissões centralizadas versus delegadas

À medida que uma organização cresce, pode ser difícil acompanhar quais os utilizadores que têm funções específicas de administração. Se um empregado tem direitos de administrador que não deve, a sua organização pode ser mais suscetível a falhas de segurança. Geralmente, quantos administradores apoia e como as suas permissões são granulares depende do tamanho e complexidade da sua implantação.

* Em pequenas ou prova de conceito, um ou alguns administradores fazem tudo; Não há delegação. Neste caso, crie cada administrador com o papel de Administrador Global.
* Em implementações maiores com mais máquinas, aplicações e desktops, é necessária mais delegação. Vários administradores podem ter responsabilidades funcionais mais específicas (funções). Por exemplo, alguns podem ser Administradores de Identidade Privilegiados, e outros podem ser Administradores de Aplicação. Além disso, um administrador pode gerir apenas certos grupos de objetos, como dispositivos.
* Implementações ainda maiores podem exigir ainda mais permissões granulares, além de possivelmente administradores com funções não convencionais ou híbridas.

No portal Azure AD, pode [ver todos os membros de qualquer função](directory-manage-roles-portal.md), o que pode ajudá-lo a verificar rapidamente a sua implementação e delegar permissões.

Se estiver interessado em delegar acesso aos recursos Azure em vez de acesso administrativo em Azure AD, consulte a atribuição de uma função de [controlo de acesso baseado em Funções (RBAC).](../../role-based-access-control/role-assignments-portal.md)

## <a name="delegation-planning"></a>Planeamento da delegação

É trabalho desenvolver um modelo de delegação que se adapte às suas necessidades. Desenvolver um modelo de delegação é um processo de design iterativo, e sugerimos que siga estes passos:

* Defina os papéis de que precisa
* Administração de aplicativos delegados
* Conceder a capacidade de registar candidaturas
* Delegar a propriedade da aplicação
* Desenvolver um plano de segurança
* Estabelecer contas de emergência
* Proteja as suas funções de administrador
* Tornar a elevação privilegiada temporária

## <a name="define-roles"></a>Definir funções

Determine as tarefas de Diretório Ativo que são executadas pelos administradores e como mapeiam para as funções. Pode [ver descrições detalhadas](directory-manage-roles-portal.md) de papéis no portal Azure.

Cada tarefa deve ser avaliada para frequência, importância e dificuldade. Estes critérios são aspetos vitais da definição de tarefas porque regem se deve ser delegada uma autorização:

* As tarefas que faz rotineiramente, têm risco limitado e são triviais para completar são excelentes candidatos à delegação.
* Tarefas que você raramente faz, mas tem grande impacto em toda a organização e requer altos níveis de habilidade deve ser considerado com muito cuidado antes de delegar. Em vez disso, pode [elevar temporariamente uma conta para o papel necessário](../active-directory-privileged-identity-management-configure.md) ou reatribuir a tarefa.

## <a name="delegate-app-administration"></a>Administração de aplicativos delegados

A proliferação de aplicações dentro da sua organização pode forçar o seu modelo de delegação. Se colocar o fardo para a gestão do acesso a aplicações no Administrador Global, é provável que o modelo aumente a sua sobrecarga à medida que o tempo passa. Se concedeu às pessoas o papel de Administrador Global para coisas como configurar aplicações empresariais, agora pode descarregá-las para os seguintes papéis menos privilegiados. Fazê-lo ajuda a melhorar a sua postura de segurança e reduz o potencial de erros infelizes. As funções de administrador de aplicação mais privilegiada são:

* A função de Administrador de **Aplicação,** que concede a capacidade de gerir todas as aplicações no diretório, incluindo registos, configurações de inscrição única, atribuições de utilizador e grupo e licenciamento, definições de Procuração de Aplicações e consentimento. Não concede a capacidade de gerir o Acesso Condicional.
* A função de Administrador de **Aplicação cloud,** que concede todas as capacidades do Administrador de Aplicação, exceto que não concede acesso às definições de Proxy de Aplicação (porque não tem permissão no local).

## <a name="delegate-app-registration"></a>Registo de aplicativos delegados

Por padrão, todos os utilizadores podem criar registos de aplicações. Conceder seletivamente a capacidade de criar registos de candidaturas:

* Definir **utilizadores podem registar aplicações** para Não nas **definições do Utilizador**
* Atribuir o utilizador à função de Desenvolvedor de Aplicações

Conceder seletivamente a capacidade de consentimento para permitir um pedido de acesso a dados:

* Definir **Os Utilizadores podem consentir em aplicações que acedam aos dados** da empresa em seu nome, Sem nas **definições do Utilizador**
* Atribuir o utilizador à função de Desenvolvedor de Aplicações

Quando um Desenvolvedor de Aplicações cria um novo registo de aplicação, são automaticamente adicionados como primeiro proprietário.

## <a name="delegate-app-ownership"></a>Delegar a propriedade da aplicação

Para uma delegação de acesso a apps com grãos mais finos, pode atribuir propriedade a aplicações empresariais individuais. Isto complementa o apoio existente à atribuição de registos de candidaturas. A propriedade é atribuída numa base de aplicação por empresa na lâmina de Aplicações empresariais. O benefício é que os proprietários só podem gerir as aplicações empresariais que possuem. Por exemplo, pode atribuir um proprietário para a aplicação Salesforce, e esse proprietário pode gerir o acesso e configuração para a Salesforce, e nenhuma outra aplicação. Uma aplicação empresarial pode ter muitos proprietários, e um utilizador pode ser o proprietário de muitas aplicações empresariais. Existem duas funções de proprietário de aplicativos:

* A função **de Proprietário de Aplicações empresariais** concede a capacidade de gerir as "aplicações empresariais que o utilizador detém, incluindo definições únicas de inscrição, atribuições de utilizador e grupo, e adicionar mais proprietários. Não concede a capacidade de gerir as definições de Procuração de Aplicações ou Acesso Condicional.
* A função de Proprietário de Registo de **Aplicação** confere a capacidade de gerir os registos de aplicações para aplicação que o utilizador detém, incluindo o manifesto de aplicação e adicionando mais proprietários.

## <a name="develop-a-security-plan"></a>Desenvolver um plano de segurança

A Azure AD fornece um extenso guia para planear e executar um plano de segurança nas suas funções de administração da Azure AD, [garantindo acesso privilegiado para implantações híbridas e em nuvem.](directory-admin-roles-secure.md)

## <a name="establish-emergency-accounts"></a>Estabelecer contas de emergência

Para manter o acesso à sua loja de gestão de identidade quando surge problema, prepare contas de acesso de emergência de acordo com a [Create contas administrativas de acesso de emergência.](directory-emergency-access.md)

## <a name="secure-your-administrator-roles"></a>Proteja as suas funções de administrador

Os atacantes que obtêm o controlo de contas privilegiadas podem causar danos tremendos, por isso protejam estas contas primeiro, utilizando a política de [acesso de base](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) que está disponível por defeito a todos os inquilinos da AD Azure (em pré-visualização pública). A política impõe a autenticação de vários fatores em contas adad privilegiadas do Azure. As seguintes funções da AD Azure são abrangidas pela política de base da AD Azure:

* Administrador global
* Administrador do SharePoint
* Administrador do Exchange
* Administrador de Acesso Condicional
* Administrador de segurança

## <a name="elevate-privilege-temporarily"></a>Elevar o privilégio temporariamente

Para a maioria das atividades do dia-a-dia, nem todos os utilizadores precisam de direitos de administrador global, e nem todos devem ser permanentemente atribuídos ao papel de Administrador Global. Quando os utilizadores precisam das permissões de um Administrador Global, devem ativar a atribuição de funções na Azure AD [Privileged Identity Management,](../active-directory-privileged-identity-management-configure.md) quer na sua conta própria, quer numa conta administrativa alternativa.

## <a name="next-steps"></a>Passos seguintes

Para uma referência às descrições da função Azure AD, consulte as funções de [atribuírem em Azure AD](directory-assign-admin-roles.md)
