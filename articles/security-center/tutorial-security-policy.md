---
title: Trabalhar com políticas de segurança | Documentos da Microsoft
description: Este artigo descreve como trabalhar com políticas de segurança no Centro de segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/14/2019
ms.author: monhaber
ms.openlocfilehash: 98fffbc7a3b287dd59cfc681beec2107a5fd4ed6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085053"
---
# <a name="working-with-security-policies"></a>Trabalhar com políticas de segurança

Este artigo explica como as políticas de segurança são configuradas e, como visualizá-los no Centro de segurança. Centro de segurança do Azure atribui automaticamente seus [políticas de segurança incorporadas](security-center-policy-definitions.md) em cada subscrição que está incluído. Pode configurá-las no [do Azure Policy](../governance/policy/overview.md), que também permite-lhe definir políticas em grupos de gestão e em várias subscrições.

Para obter instruções sobre como definir políticas com o PowerShell, consulte [início rápido: Criar uma atribuição de política para identificar recursos incompatíveis com o módulo Azure PowerShell](../governance/policy/assign-policy-powershell.md).

>[!NOTE]
> Centro de segurança iniciada sua integração com o Azure Policy. Os clientes existentes serão migrados automaticamente para a nova iniciativa incorporada no Azure Policy, em vez das políticas de segurança anteriores no Centro de segurança. Esta alteração não afetará seu ambiente, exceto a presença da nova iniciativa na política do Azure ou de recursos.

## <a name="what-are-security-policies"></a>O que são políticas de segurança?
As políticas de segurança definem a configuração pretendida para as suas cargas de trabalho e ajudam a garantir a conformidade com os requisitos da empresa ou regulamentares. No Azure Policy, pode definir políticas para as suas subscrições do Azure e adaptá-los para o tipo de carga de trabalho ou à sensibilidade dos seus dados. Por exemplo, as aplicações que utilizam dados regulados como, por exemplo, informações de identificação pessoal, podem exigir um nível mais elevado de segurança que outras cargas de trabalho. Para definir uma política nas subscrições ou em grupos de gestão, defina-os no [do Azure Policy](../governance/policy/overview.md).

As políticas de segurança orientar as recomendações de segurança, que obtém no Centro de segurança do Azure. Pode monitorizar a conformidade com os mesmos para o ajudar a identificar potenciais vulnerabilidades e a mitigar ameaças. Para obter mais informações sobre como determinar a opção adequada para si, consulte a lista de [políticas de segurança incorporadas](security-center-policy-definitions.md).

Quando ativar o Centro de segurança, a política de segurança incorporada ao centro de segurança é refletida na política do Azure como uma iniciativa incorporada na categoria de centro de segurança. A iniciativa incorporada é atribuída automaticamente a todas as subscrições do Centro de segurança registado (escalões gratuito ou Standard). A iniciativa interna contém apenas as políticas de auditoria.


### <a name="management-groups"></a>Grupos de gestão
Se a sua organização tiver várias subscrições, poderá precisar de uma forma de gerir eficazmente o acesso, as políticas e a conformidade para essas subscrições. Os Grupos de Gestão do Azure fornecem um nível de âmbito acima das subscrições. Organiza as subscrições em contentores chamados "grupos de gestão" e aplica as políticas de governação aos grupos de gestão. Todas as subscrições num grupo de gestão herdam automaticamente as políticas aplicadas ao grupo de gestão. É atribuído a cada diretório um único grupo de gestão de nível superior denominado grupo de gestão de "raiz". Este grupo de gestão de raiz está incorporado na hierarquia para ter todos os grupos de gestão e subscrições associados ao mesmo. Este grupo de gestão de raiz permite a aplicação de políticas globais e atribuições do RBAC ao nível do diretório. Para configurar grupos de gestão para utilizar com o Centro de segurança do Azure, siga as instruções em [ganhar visibilidade ao nível do inquilino para o Centro de segurança do Azure](security-center-management-groups.md).

> [!NOTE]
> É importante que compreenda a hierarquia de grupos de gestão e subscrições. Veja [Organizar os recursos com os Grupos de Gestão do Azure](../governance/management-groups/index.md#root-management-group-for-each-directory) para obter mais informações sobre grupos de gestão, gestão de raiz e acesso ao grupo de gestão.
>

## <a name="how-security-policies-work"></a>Como funcionam as políticas de segurança
O Centro de Segurança cria automaticamente uma política de segurança predefinida para cada uma das suas subscrições do Azure. Pode editar as políticas no Azure Policy para efetue os seguintes procedimentos:
- Criar novas definições de políticas.
- Atribuir políticas em subscrições e grupos de gestão, que podem representar uma organização completa ou uma unidade de negócio dentro da organização.
- Monitorizar a conformidade com as políticas.

Para obter mais informações sobre o Azure Policy, veja [Create and manage policies to enforce compliance (Criar e gerir políticas para impor a conformidade)](../governance/policy/tutorials/create-and-manage.md).

Uma política do Azure é composta pelos seguintes componentes:

- R **política** é uma regra.
- Uma **iniciativa** é uma coleção de políticas.
- Uma **atribuição** é a aplicação de uma iniciativa ou uma política para um âmbito específico (grupo de gestão, subscrição ou grupo de recursos).

## <a name="view-security-policies"></a>Ver as políticas de segurança

Para ver as suas políticas de segurança no Centro de Segurança:

1. Na **Centro de segurança** dashboard, selecione **política de segurança**.

    ![O painel Gestão de Políticas](./media/security-center-policies/security-center-policy-mgt.png)

   Na **gestão de políticas** tela, pode ver o número de grupos de gestão, subscrições e áreas de trabalho, bem como a estrutura de grupo de gestão.

   > [!NOTE]
   > - Dashboard do Centro de segurança pode mostrar um número superior de assinaturas sob **abrangência da subscrição** que o número de subscrições apresentadas na **gestão de políticas**. Abrangência da subscrição mostra o número de subscrições Standard, Gratuitas e "não abrangidas". As subscrições "não abrangido" não é necessário o Centro de segurança ativado e não são apresentadas sob **gestão de políticas**.
   >

   As colunas na tabela apresentam:

   - **Atribuição de iniciativa de política** – o Centro de segurança [as políticas incorporadas](security-center-policy-definitions.md) e iniciativas que são atribuídas a um grupo de gestão ou de subscrição.
   - **Cobertura** – identifica o escalão de preço, gratuito ou Standard, que o grupo de gestão, a subscrição ou a área de trabalho está a ser executada.  Veja [Preços](security-center-pricing.md) para saber mais sobre os escalões de preços do Centro de Segurança.
   - **As definições** – as subscrições têm a hiperligação **editar definições**. Selecionando **editar definições** permite que Atualize sua [definições do Centro de segurança](security-center-policies-overview.md) para cada grupo de gestão ou de subscrição.
   - **Pontuação segura** – a [pontuação segura](security-center-secure-score.md) fornece uma medida de quão segura postura de segurança de sua carga de trabalho e o ajuda a priorizar as recomendações para aprimoramento.

2. Selecione o grupo de subscrição ou gestão cujas políticas que pretende visualizar.

   - O **política de segurança** ecrã reflete a ação tomada pelas políticas atribuídas no grupo de gestão ou de subscrição que selecionou.
   - Na parte superior, utilize as ligações fornecidas para abrir a cada política **atribuição** que aplica-se no grupo de subscrição ou gestão. Pode utilizar as ligações para aceder a atribuição e editar ou desativar a política. Por exemplo, se vir que uma atribuição de política específico com eficiência é negar a proteção de ponto de extremidade, pode utilizar a ligação para a política de acesso e editar ou desativá-la.
   - Na lista de políticas, pode ver o aplicativo em vigor a partir da política na sua subscrição ou grupo de gestão. Isso significa que as definições de cada política que se aplicam ao âmbito são levadas em consideração e são fornecidos com o resultado cumulativo da ação que está a ser utilizada pela política. Por exemplo, se numa atribuição a política está desativada, mas em outro está definido como AuditIfNotExist, em seguida, o efeito cumulativo aplica AuditIfNotExist. O efeito mais ativo sempre tem precedência.
   - Efeito de diretiva pode ser: Acrescentar, auditoria, AuditIfNotExists, negar, DeployIfNotExists, desativado. Para obter mais informações sobre como os efeitos são aplicados, consulte [efeitos de política de compreender](../governance/policy/concepts/effects.md).

   ![ecrã de política](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> - Quando exibe atribuídas políticas, pode ver várias atribuições e pode ver como cada atribuição está configurada por si.

## <a name="edit-security-policies"></a>Editar as políticas de segurança
Pode editar a política de segurança predefinida para cada uma das suas subscrições do Azure e grupos de gestão no [do Azure Policy](../governance/policy/tutorials/create-and-manage.md). Para modificar uma política de segurança, tem de ser proprietário, contribuidor ou administrador de segurança dessa subscrição ou do grupo de gestão que a contém.

Para obter instruções sobre como editar uma política de segurança no Azure Policy, veja e [criar e gerir políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md).

Pode editar as políticas de segurança através do portal do Azure Policy, por meio da REST API ou com o Windows PowerShell. O exemplo seguinte fornece as instruções para editar com a REST API.


## <a name="disable-security-policies"></a>Desativar políticas de segurança
Se a política de segurança predefinida está a gerar uma recomendação que não é relevante para o seu ambiente, pode impedi-lo ao desativar a definição de política que envia a recomendação.
Para obter mais informações sobre as recomendações, veja [recomendações de segurança](security-center-recommendations.md).

1. No Centro de segurança, do **política e conformidade** secção, clique em **política de segurança**.

   ![Gestão de políticas](./media/tutorial-security-policy/policy-management.png)

2. Clique a subscrição para o qual pretende desativar a recomendação.

1. Clique em política atribuída.

   ![Desativar política](./media/tutorial-security-policy/security-policy.png)

1. Na **parâmetros** secção, a pesquisa para a política que invoca a recomendação de que pretende desativar e, na lista pendente, selecione **desativado**

   ![Desativar política](./media/tutorial-security-policy/disable-policy.png)
1. Clique em **Guardar**.
> [!Note]
> As alterações de política de desactivação podem demorar até 12 horas para entrar em vigor.


### <a name="configure-a-security-policy-using-the-rest-api"></a>Configurar uma política de segurança com a API REST

Como parte da integração nativa com o Azure Policy, o Centro de segurança do Azure permite-lhe tirar partido do Azure Policy API de REST para criar atribuições de política. As instruções seguintes explicam como criação de atribuições de política, bem como a personalização de atribuições existentes. 

Conceitos importantes na política do Azure: 

- R **definição de política** é uma regra 

- Uma **iniciativa** é uma coleção de definições de política (regras) 

- Uma **atribuição** é uma aplicação de uma iniciativa ou uma política para um âmbito específico (grupo de gestão, subscrição, etc.) 

Centro de segurança tem uma iniciativa incorporada que inclui todas as políticas de segurança. Para avaliar as políticas do Centro de segurança nos recursos do Azure, deve criar uma atribuição no grupo de gestão ou assinatura que deseja avaliar.  

A iniciativa incorporada tem todas as políticas do Centro de segurança ativadas por predefinição. Pode optar por desabilitar determinadas políticas da iniciativa de incorporada, por exemplo pode aplicar todas as políticas do Centro de segurança, exceto **firewall de aplicações web**, alterando o valor do parâmetro de efeito da política para  **Desativado**. 

### <a name="api-examples"></a>Exemplos de API

Nos exemplos a seguir, substitua estas variáveis:

- **{âmbito}**  introduza o nome do grupo de gestão ou subscrição se aplicar a política.
- **{policyAssignmentName}**  introduza o [nome da atribuição de política relevante](#policy-names).
- **{name}**  introduza o nome ou o nome do administrador que aprovou a alteração de política.

Este exemplo mostra como atribuir a iniciativa de centro de segurança incorporada numa subscrição ou grupo de gestão
 
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 

Este exemplo mostra como atribuir a iniciativa de centro de segurança incorporada numa assinatura, com as seguintes políticas desativadas: 

- Atualizações do sistema ("systemUpdatesMonitoringEffect") 

- Configurações de segurança ("systemConfigurationsMonitoringEffect") 

- Proteção de ponto final ("endpointProtectionMonitoringEffect") 


    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Corpo do pedido (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName": "Ativar monitorização no Centro de segurança do Azure", 
    
    "metadata":{ 
    
    "assignedBy": "{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 

Este exemplo mostra como remover uma atribuição:

    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 


### Referência de política de nomes <a name="policy-names"></a>

|Nome da política no Centro de segurança|Nome da política apresentado na política do Azure |Nome do parâmetro de efeito de política|
|----|----|----|
|Encriptação SQL |Monitorizar a base de dados SQL não encriptada no Centro de segurança do Azure |sqlEncryptionMonitoringEffect| 
|Auditoria do SQL |Monitorizar a base de dados SQL não auditada no Centro de segurança do Azure |sqlAuditingMonitoringEffect|
|Atualizações do sistema |Monitorizar atualizações de sistema em falta no Centro de segurança do Azure |systemUpdatesMonitoringEffect|
|Encriptação do armazenamento |Auditar encriptação de BLOBs em falta para contas de armazenamento |storageEncryptionMonitoringEffect|
|Acesso à rede JIT |Monitorizar o acesso de apenas no Time (JIT) de rede possível no Centro de segurança do Azure |jitNetworkAccessMonitoringEffect |
|Controlos de aplicações adaptáveis |Monitorizar aplicação possíveis listas de permissões no Centro de segurança do Azure |adaptiveApplicationControlsMonitoringEffect|
|Grupos de segurança de rede |Monitorizar o acesso de rede permissivo no Centro de segurança do Azure |networkSecurityGroupsMonitoringEffect| 
|Configurações de segurança |Monitorizar vulnerabilidades do SO no Centro de segurança do Azure |systemConfigurationsMonitoringEffect| 
|Endpoint protection |Monitorizar o Endpoint Protection em falta no Centro de segurança do Azure |endpointProtectionMonitoringEffect |
|Encriptação de disco |Monitorizar discos de VM não encriptados no Centro de segurança do Azure |diskEncryptionMonitoringEffect|
|Avaliação de vulnerabilidades |Monitorizar Vulnerabilidades de VM no Centro de Segurança do Azure |vulnerabilityAssessmentMonitoringEffect|
|Firewall de aplicação Web |Monitorizar aplicações web desprotegidas no Centro de segurança do Azure |webApplicationFirewallMonitoringEffect |
|Firewall da próxima geração |Monitorizar pontos finais de redes desprotegidos no Centro de segurança do Azure| |


### <a name="who-can-edit-security-policies"></a>Quem pode editar as políticas de segurança?
Centro de segurança utiliza baseada em funções controlo de acesso (RBAC), que fornece funções incorporadas que podem ser atribuídas a utilizadores, grupos e serviços no Azure. Quando os utilizadores abrem o Centro de segurança, veem apenas informações relacionadas com a recursos que aos quais têm acesso. Isto significa que os utilizadores são atribuídos a função de proprietário, contribuinte ou leitor para o subscrição ou grupo de recursos que um recurso pertence. Além destas funções, há duas funções específicas do Centro de Segurança:

- Leitor de segurança: Tem a ver direitos ao centro de segurança, que incluem recomendações, alertas, políticas e estado de funcionamento, mas eles não podem fazer alterações.
- Administrador de segurança: Ter os mesmos direitos de exibição de leitor de segurança, e também pode atualizar a política de segurança e ignorar recomendações e alertas.



## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu como editar as políticas de segurança na política do Azure. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

* [Guia de operações e planeamento do Centro de segurança do Azure](security-center-planning-and-operations-guide.md): Saiba como planear e compreender as considerações de design sobre o Centro de segurança do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md): Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md): Saiba como gerir e responder a alertas de segurança.
* [Monitorizar soluções de parceiros no Centro de segurança do Azure](security-center-partner-solutions.md): Saiba como monitorizar o estado de funcionamento das soluções dos seus parceiros.
* [Obtenha visibilidade ao nível do inquilino para o Centro de segurança do Azure](security-center-management-groups.md): Saiba como configurar grupos de gestão para o Centro de segurança do Azure.
* [FAQ do Centro de segurança do Azure](security-center-faq.md): Encontre respostas para as perguntas mais frequentes sobre o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): Encontre mensagens do blogue acerca da segurança e conformidade do Azure.

Para saber mais sobre o Azure Policy, veja [O que é o Azure Policy?](../governance/policy/overview.md)
