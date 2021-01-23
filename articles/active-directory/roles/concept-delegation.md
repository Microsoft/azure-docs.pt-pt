---
title: Compreender a delegação de funções de administração - Azure Ative Directory | Microsoft Docs
description: Modelos de delegação, exemplos e segurança de papéis no Azure Ative Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77ba93b9017fa75f7247bda639880415eda1d280
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740317"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Delegada administração em Diretório Ativo Azure

Com o crescimento organizacional vem a complexidade. Uma resposta comum é reduzir parte da carga de trabalho da gestão de acessos com funções de administração Azure Ative (AD). Pode atribuir o mínimo privilégio possível aos utilizadores para acederem às suas apps e executarem as suas tarefas. Mesmo que não atribua o papel de Administrador Global a todos os proprietários de aplicações, está a atribuir responsabilidades de gestão de aplicações aos administradores globais existentes. Há muitas razões para uma organização avançar para uma administração mais descentralizada. Este artigo pode ajudá-lo a planear a delegação na sua organização.

<!--What about reporting? Who has which role and how do I audit?-->

## <a name="centralized-versus-delegated-permissions"></a>Permissões centralizadas versus delegadas

À medida que uma organização cresce, pode ser difícil acompanhar quais os utilizadores que têm funções específicas de administração. Se um empregado tem direitos de administrador que não deve, a sua organização pode ser mais suscetível a falhas de segurança. Geralmente, quantos administradores suporta e como as suas permissões são granular depende do tamanho e complexidade da sua implantação.

* Em implementações pequenas ou com prova de conceito, um ou alguns administradores fazem tudo; Não há delegação. Neste caso, crie cada administrador com o papel de Administrador Global.
* Em maiores implementações com mais máquinas, aplicações e desktops, é necessária mais delegação. Vários administradores podem ter responsabilidades funcionais mais específicas (funções). Por exemplo, alguns podem ser Administradores de Identidade Privilegiada, e outros podem ser Administradores de Aplicações. Além disso, um administrador pode gerir apenas certos grupos de objetos, como dispositivos.
* Implementações ainda maiores podem exigir ainda mais permissões granulares, além de possíveis administradores com funções não convencionais ou híbridas.

No portal AD AZure, pode [visualizar todos os membros de qualquer papel](manage-roles-portal.md), o que pode ajudá-lo a verificar rapidamente as permissões de implementação e delegado.

Se estiver interessado em delegar o acesso aos recursos da Azure em vez de acesso administrativo em Azure AD, consulte [atribuir um papel de Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="delegation-planning"></a>Planeamento da delegação

É um trabalho para desenvolver um modelo de delegação que se adapte às suas necessidades. Desenvolver um modelo de delegação é um processo de design iterativo, e sugerimos que siga estes passos:

* Defina os papéis de que precisa
* Delegar administração de aplicativos
* Conceder a capacidade de registar candidaturas
* Delegar propriedade de aplicativos
* Desenvolver um plano de segurança
* Estabelecer contas de emergência
* Proteja as suas funções de administrador
* Tornar a elevação privilegiada temporária

## <a name="define-roles"></a>Definir funções

Determine as tarefas do Diretório Ativo que são executadas pelos administradores e como mapeiam as funções. Pode [ver descrições detalhadas](manage-roles-portal.md) de papéis no portal Azure.

Cada tarefa deve ser avaliada em termos de frequência, importância e dificuldade. Estes critérios são aspetos vitais da definição de tarefas, pois regem se uma autorização deve ser delegada:

* As tarefas que faz rotineiramente, têm um risco limitado, e são triviais para completar são excelentes candidatos à delegação.
* Tarefas que raramente faz, mas que têm um grande impacto em toda a organização e que requerem elevados níveis de habilidade, devem ser consideradas com muito cuidado antes de delegar. Em vez disso, pode [elevar temporariamente uma conta para a função necessária](../privileged-identity-management/pim-configure.md) ou reatribuir a tarefa.

## <a name="delegate-app-administration"></a>Delegar administração de aplicativos

A proliferação de aplicações dentro da sua organização pode condicionar o seu modelo de delegação. Se colocar o fardo para a gestão do acesso a aplicações no Administrador Global, é provável que o modelo aumente a sua sobrecarga à medida que o tempo passa. Se concedeu às pessoas o papel de Administrador Global para coisas como configurar aplicações empresariais, pode agora descarregá-las para as seguintes funções menos privilegiadas. Ao fazê-lo, ajuda a melhorar a sua postura de segurança e reduz o potencial de erros infelizes. As funções de administrador de aplicação mais privilegiadas são:

* A função **de Administrador de Aplicação,** que concede a capacidade de gerir todas as aplicações no diretório, incluindo registos, definições de inscrição única, atribuição de utilizadores e grupos e licenciamento, definições de Procuração de Aplicação e consentimento. Não dá a capacidade de gerir o Acesso Condicional.
* A função **de Administrador de Aplicação cloud,** que concede todas as capacidades do Administrador de Aplicação, exceto que não concede acesso às definições de Procuração de Aplicações (porque não tem permissão no local).

## <a name="delegate-app-registration"></a>Registo de aplicativos de delegado

Por predefinição, todos os utilizadores podem criar registos de aplicações. Para garantir seletivamente a capacidade de criar registos de pedidos:

* **Definir Utilizadores podem registar aplicações** a Não nas **definições do Utilizador**
* Atribuir o utilizador à função de Desenvolvedor de Aplicações

Para conceder seletivamente a capacidade de consentir para permitir que uma aplicação aceda aos dados:

* **Definir Os utilizadores podem consentir que as aplicações acedam aos dados da empresa em seu nome** Para Não nas **definições do Utilizador**
* Atribuir o utilizador à função de Desenvolvedor de Aplicações

Quando um Desenvolvedor de Aplicações cria um novo registo de aplicações, são automaticamente adicionados como o primeiro proprietário.

## <a name="delegate-app-ownership"></a>Delegar propriedade de aplicativos

Para uma delegação de acesso a aplicações mais fina, pode atribuir a propriedade a aplicações empresariais individuais. Isto complementa o apoio existente para a atribuição de registos de pedidos. A propriedade é atribuída numa base de aplicação por empresa na lâmina de Aplicações da Empresa. O benefício é que os proprietários só podem gerir as aplicações empresariais que possuem. Por exemplo, pode atribuir um proprietário para a aplicação Salesforce, e esse proprietário pode gerir o acesso e configuração para a Salesforce, e nenhuma outra aplicação. Uma aplicação da empresa pode ter muitos proprietários, e um utilizador pode ser o proprietário de muitas aplicações empresariais. Existem duas funções de proprietário de aplicativos:

* A função **Enterprise Application Owner** concede a capacidade de gerir as "aplicações empresariais que o utilizador possui, incluindo definições únicas de inscrição, atribuições de utilizador e grupo, e adicionando proprietários adicionais. Não concede a capacidade de gerir as definições de Procuração de Aplicações ou Acesso Condicional.
* A função de Titular do Registo de **Aplicações** confere a possibilidade de gerir os registos de candidaturas para aplicações que o utilizador detém, incluindo o manifesto de aplicação e a adição de proprietários adicionais.

## <a name="develop-a-security-plan"></a>Desenvolver um plano de segurança

A Azure AD fornece um extenso guia para planear e executar um plano de segurança nas suas funções de administrador AD AD Azure, [garantindo acesso privilegiado para implantações híbridas e em nuvem.](security-planning.md)

## <a name="establish-emergency-accounts"></a>Estabelecer contas de emergência

Para manter o acesso à sua loja de gestão de identidade quando surgir emissão, prepare as contas de acesso de emergência de acordo com [a Create emergency access.](security-emergency-access.md)

## <a name="secure-your-administrator-roles"></a>Proteja as suas funções de administrador

Os atacantes que obtêm o controlo de contas privilegiadas podem causar danos tremendos, por isso proteja estas contas primeiro, usando a [política de acesso de base](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) que está disponível por padrão para todas as organizações AD Azure (em pré-visualização pública). A política aplica a autenticação de vários fatores nas contas AD privilegiadas da Azure. As seguintes funções AZure AD são abrangidas pela política de base Azure AD:

* Administrador global
* Administrador do SharePoint
* Administrador do Exchange
* Administrador de acesso condicional
* Administrador de segurança

## <a name="elevate-privilege-temporarily"></a>Elevar o privilégio temporariamente

Para a maioria das atividades do dia-a-dia, nem todos os utilizadores precisam de direitos de administrador global, e nem todos devem ser permanentemente atribuídos ao papel de Administrador Global. Quando os utilizadores precisam das permissões de um Administrador Global, devem ativar a atribuição de funções na Azure AD [Privileged Identity Management,](../privileged-identity-management/pim-configure.md) quer na sua própria conta, quer numa conta administrativa alternativa.

## <a name="next-steps"></a>Próximos passos

Para uma referência às descrições de funções da AD Azure, consulte [atribuir funções de administrador em Azure AD](permissions-reference.md)