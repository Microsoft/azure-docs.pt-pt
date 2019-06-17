---
title: Compreender a delegação de funções de administrador - Azure Active Directory | Documentos da Microsoft
description: Modelos de delegação, exemplos e segurança de funções no Azure Active Directory
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083774"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Delegar a administração no Azure Active Directory

Com o crescimento organizacional vem complexidade. É uma resposta comum a reduzir algum da carga de trabalho de gestão de acesso com funções de administrador do Azure Active Directory (AD). Pode atribuir os menores privilégios possíveis para os usuários acessam os aplicativos e realizar suas tarefas. Mesmo se não atribuir a função de Administrador Global para cada proprietário da aplicação, está colocando responsabilidades de gestão de aplicações sobre os administradores globais existentes. Existem muitas razões para uma mudança de organização para uma administração mais descentralizada. Este artigo pode ajudá-lo a planear de delegação na sua organização.

<!--What about reporting? Who has which role and how do I audit?-->

## <a name="centralized-versus-delegated-permissions"></a>Centralizado em comparação com as permissões delegadas

À medida que aumenta uma organização, pode ser difícil controlar quais usuários têm funções de administrador específico. Se um funcionário tiver direitos de administrador, que eles não devem, sua organização pode ser mais suscetível a falhas de segurança. Em geral, o número de administradores suportadas e as respetivas permissões granulares como são depende do tamanho e a complexidade da sua implementação.

* Nas implementações de pequenas ou uma prova de conceito, os administradores de um ou alguns fazem tudo. Não há nenhuma delegação. Neste caso, crie cada administrador com função de Administrador Global.
* Nas implementações maiores com mais máquinas, aplicativos e áreas de trabalho, é necessário mais de delegação. Vários administradores podem ter responsabilidades funcionais mais específicas (funções). Por exemplo, algumas podem ter administradores com privilégios de identidade e outros podem ser administradores da aplicação. Além disso, um administrador pode gerir apenas determinados grupos de objetos, tais como dispositivos.
* Implementações ainda maiores podem exigir permissões ainda mais granulares, além disso, possivelmente, os administradores com não convencional ou funções de híbrida.

No portal do Azure AD, pode [ver todos os membros de qualquer função](directory-manage-roles-portal.md), que pode ajudá-lo a verificar rapidamente as suas permissões de implantação e o delegado.

Se estiver interessado em delegar o acesso aos recursos do Azure em vez de acesso administrativo no Azure AD, veja [atribuir uma função (RBAC) do controle de acesso baseado em funções](../../role-based-access-control/role-assignments-portal.md).

## <a name="delegation-planning"></a>Planejamento de delegação

Seu trabalho para desenvolver um modelo de delegação que atende às suas necessidades. Desenvolver um modelo de delegação é um processo de design interativo e sugerimos que siga estes passos:

* Definir as funções de que precisa
* Delegar a administração de aplicação
* Conceder a capacidade de registar aplicações
* Delegar a propriedade de aplicação
* Desenvolver um plano de segurança
* Estabelecer contas de emergência
* Proteger as suas funções de administrador
* Fazer com privilégios de elevação temporária

## <a name="define-roles"></a>Definir funções

Determine as tarefas do Active Directory que são realizadas por administradores e como devem ser mapeadas para as funções. Pode [ver descrições de funções detalhadas](directory-manage-roles-portal.md) no portal do Azure.

Cada tarefa deve ser avaliada por freqüência, importância e dificuldade. Estes critérios aspectos são vitais à definição de tarefas porque definem se deve ser delegada uma permissão:

* Tarefas que fazer rotineiramente, têm risco limitado e são simples para completas, são excelentes candidatas à delegação.
* Tarefas que raramente, mas têm um grande impacto em toda a organização e requerem níveis altos de habilidade devem ser consideradas com muito cuidado antes de delegar. Em vez disso, pode [elevar temporariamente uma conta à função requerida](../active-directory-privileged-identity-management-configure.md) ou reatribuir a tarefa.

## <a name="delegate-app-administration"></a>Delegar a administração de aplicação

A proliferação de aplicações na sua organização tudo isso pode o pressionar de seu modelo de delegação. Se coloca a responsabilidade sobre gestão de acesso de aplicações ao administrador Global, é provável que esse modelo aumenta a sobrecarga, o tempo. Se lhe tiver concedido as pessoas a função de Administrador Global para coisas como a configuração de aplicações empresariais, agora pode descarregá-los para as seguintes funções com menos privilégios. Isso ajuda a melhorar a sua postura de segurança e reduz o potencial para erros infeliz. As funções de administrador do aplicativo com privilégios mais são:

* O **administrador da aplicação** função, que concede a capacidade para gerir todas as aplicações no diretório, incluindo registos, as definições de início de sessão únicas, utilizador e as atribuições de grupo e definições de Proxy de licenciamento, aplicação, e o consentimento. Ele não concede a capacidade de gerir o acesso condicional.
* O **administrador da aplicação Cloud** função, que concede todas as capacidades de administrador da aplicação, exceto que ele não concede acesso às definições de Proxy de aplicações (porque ele não tem nenhuma permissão de no local).

## <a name="delegate-app-registration"></a>Delegar o registo de aplicações

Por predefinição, todos os utilizadores podem criar registos de aplicações. Para conceder seletivamente a capacidade de criar registos de aplicações:

* Definir **os utilizadores podem registar aplicações** para não na **as definições de utilizador**
* Atribua o utilizador à função de programador da aplicação

Para conceder seletivamente a capacidade de dar consentimento para permitir que um aplicativo para aceder a dados:

* Definir **os usuários podem autorizar aplicações aceder aos dados da empresa em nome deles** para não na **as definições de utilizador**
* Atribua o utilizador à função de programador da aplicação

Quando um desenvolvedor de aplicativos cria um novo registo de aplicação, são adicionados automaticamente como o primeiro proprietário.

## <a name="delegate-app-ownership"></a>Delegar a propriedade de aplicação

Para a delegação de acesso da aplicação ainda mais refinada, pode atribuir propriedade às aplicações empresariais individuais. Isso complementa o suporte existente para proprietários de registo de aplicação. Propriedade é atribuída numa base por enterprise aplicação no painel de aplicações empresariais. A vantagem é que os proprietários podem gerir apenas as aplicações da empresa possuem. Por exemplo, pode atribuir um proprietário para a aplicação do Salesforce, e que esse proprietário pode gerir o acesso e a configuração para o Salesforce e nenhum outro aplicativo. Uma aplicação empresarial pode ter muitos proprietários e o utilizador pode ser o proprietário para muitas aplicações empresariais. Existem duas funções de proprietário da aplicação:

* O **proprietário da aplicação empresarial** função concede a capacidade de gerir a "aplicações empresariais que o utilizador é proprietário, incluindo as definições de início de sessão únicas, utilizador e as atribuições de grupo e adicionar proprietários adicionais. Ele não concede a capacidade de gerir as definições de Proxy de aplicações ou de acesso condicional.
* O **proprietário do registo de aplicação** função concede a capacidade de gerir registos de aplicação para a aplicação que o utilizador é proprietário, incluindo o manifesto do aplicativo e adicionar proprietários adicionais.

## <a name="develop-a-security-plan"></a>Desenvolver um plano de segurança

O Azure AD fornece um amplo guia para planejamento e a execução de um plano de segurança em suas funções de administrador do Azure AD, [proteger o acesso privilegiado para implementações híbridas e na cloud](directory-admin-roles-secure.md).

## <a name="establish-emergency-accounts"></a>Estabelecer contas de emergência

Para manter o acesso ao seu arquivo de gestão de identidade quando o problema surge, preparar contas de acesso de emergência de acordo com a [criar contas administrativas de acesso de emergência](directory-emergency-access.md).

## <a name="secure-your-administrator-roles"></a>Proteger as suas funções de administrador

Os invasores que obtém controle de contas com privilégios pode fazer danos enorme, portanto, proteger estas contas em primeiro lugar, com o [política de acesso de linha de base](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) que está disponível por predefinição para todos os inquilinos do Azure AD (em pré-visualização pública). A política impõe a autenticação multifator privilegiado em contas do Azure AD. As seguintes funções do Azure AD são abrangidas pela política de linha de base do Azure AD:

* Administrador global
* Administrador do SharePoint
* Administrador do Exchange
* Administrador de acesso condicional
* Administrador de segurança

## <a name="elevate-privilege-temporarily"></a>Elevar temporariamente o privilégio

Na maioria das atividades diárias, nem todos os utilizadores precisam de direitos de administrador global e não todos eles devem ser permanentemente atribuídos a função de Administrador Global. Quando os utilizadores têm as permissões de um Administrador Global, que os utilizadores devem ativar a atribuição de função no Azure AD [Privileged Identity Management](../active-directory-privileged-identity-management-configure.md) em sua própria conta ou uma conta administrativa alternativa.

## <a name="next-steps"></a>Passos Seguintes

Para obter uma referência para as descrições de função do Azure AD, consulte [atribuir funções de administrador no Azure AD](directory-assign-admin-roles.md)
