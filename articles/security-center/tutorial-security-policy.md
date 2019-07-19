---
title: Trabalhando com políticas de segurança | Microsoft Docs
description: Este artigo descreve como trabalhar com políticas de segurança na central de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/18/2019
ms.author: v-mohabe
ms.openlocfilehash: 4550532d36753d9b8ed472193bc833855ddd34c9
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314926"
---
# <a name="working-with-security-policies"></a>Trabalhar com políticas de segurança

Este artigo explica como as políticas de segurança são configuradas e como exibi-las na central de segurança. A central de segurança do Azure atribui automaticamente suas [políticas de segurança internas](security-center-policy-definitions.md) em cada assinatura que está integrada. Você pode configurá-los no [Azure Policy](../governance/policy/overview.md), que também permite que você defina políticas em grupos de gerenciamento e em várias assinaturas.

Para obter instruções sobre como definir políticas usando o PowerShell, [consulte início rápido: Crie uma atribuição de política para identificar recursos sem conformidade usando o módulo](../governance/policy/assign-policy-powershell.md)Azure PowerShell.

>[!NOTE]
> A central de segurança iniciou sua integração com o Azure Policy. Os clientes existentes serão migrados automaticamente para a nova iniciativa interna no Azure Policy, em vez das políticas de segurança anteriores na central de segurança. Essa alteração não afetará seus recursos ou ambiente, exceto a presença da nova iniciativa no Azure Policy.

## <a name="what-are-security-policies"></a>O que são políticas de segurança?
As políticas de segurança definem a configuração pretendida para as suas cargas de trabalho e ajudam a garantir a conformidade com os requisitos da empresa ou regulamentares. No Azure Policy, você pode definir políticas para suas assinaturas do Azure e adaptá-las ao seu tipo de carga de trabalho ou à sensibilidade de seus dados. Por exemplo, os aplicativos que usam dados regulamentados, como dados pessoais ou dados do cliente, podem exigir um nível mais alto de segurança do que outras cargas de trabalho. Para definir uma política em assinaturas ou em grupos de gerenciamento, defina-as em [Azure Policy](../governance/policy/overview.md).

Suas políticas de segurança direcionam as recomendações de segurança que você obtém na central de segurança do Azure. Você pode monitorar a conformidade com eles para ajudá-lo a identificar possíveis vulnerabilidades e a reduzir as ameaças. Para obter mais informações sobre como determinar a opção apropriada para você, consulte a lista de políticas de [segurança internas](security-center-policy-definitions.md).

Quando você habilita a central de segurança, a política de segurança interna à central de segurança é refletida em Azure Policy como uma iniciativa interna na central de segurança da categoria. A iniciativa interna é atribuída automaticamente a todas as assinaturas registradas da central de segurança (camadas gratuitas ou Standard). A iniciativa interna contém apenas políticas de auditoria.


### <a name="management-groups"></a>Grupos de gestão
Se a sua organização tiver várias subscrições, poderá precisar de uma forma de gerir eficazmente o acesso, as políticas e a conformidade para essas subscrições. Os Grupos de Gestão do Azure fornecem um nível de âmbito acima das subscrições. Organiza as subscrições em contentores chamados "grupos de gestão" e aplica as políticas de governação aos grupos de gestão. Todas as subscrições num grupo de gestão herdam automaticamente as políticas aplicadas ao grupo de gestão. É atribuído a cada diretório um único grupo de gestão de nível superior denominado grupo de gestão de "raiz". Este grupo de gestão de raiz está incorporado na hierarquia para ter todos os grupos de gestão e subscrições associados ao mesmo. Este grupo de gestão de raiz permite a aplicação de políticas globais e atribuições do RBAC ao nível do diretório. Para configurar grupos de gerenciamento para uso com a central de segurança do Azure, siga as instruções em [obter visibilidade em todo o locatário para a central de segurança do Azure](security-center-management-groups.md).

> [!NOTE]
> É importante que compreenda a hierarquia de grupos de gestão e subscrições. Veja [Organizar os recursos com os Grupos de Gestão do Azure](../governance/management-groups/index.md#root-management-group-for-each-directory) para obter mais informações sobre grupos de gestão, gestão de raiz e acesso ao grupo de gestão.
>

## <a name="how-security-policies-work"></a>Como funcionam as políticas de segurança
O Centro de Segurança cria automaticamente uma política de segurança predefinida para cada uma das suas subscrições do Azure. Você pode editar as políticas no Azure Policy para fazer o seguinte:
- Criar novas definições de políticas.
- Atribuir políticas em subscrições e grupos de gestão, que podem representar uma organização completa ou uma unidade de negócio dentro da organização.
- Monitorizar a conformidade com as políticas.

Para obter mais informações sobre o Azure Policy, veja [Create and manage policies to enforce compliance (Criar e gerir políticas para impor a conformidade)](../governance/policy/tutorials/create-and-manage.md).

Uma política do Azure é composta pelos seguintes componentes:

- Uma **política** é uma regra.
- Uma **iniciativa** é uma coleção de políticas.
- Uma **atribuição** é o aplicativo de uma iniciativa ou uma política para um escopo específico (grupo de gerenciamento, assinatura ou grupo de recursos).

## <a name="view-security-policies"></a>Ver as políticas de segurança

Para ver as suas políticas de segurança no Centro de Segurança:

1. No painel **central de segurança** , selecione **política de segurança**.

    ![O painel Gestão de Políticas](./media/security-center-policies/security-center-policy-mgt.png)

   Na tela **Gerenciamento de política** , você pode ver o número de grupos de gerenciamento, assinaturas e espaços de trabalho, bem como sua estrutura de grupo de gerenciamento.

   > [!NOTE]
   > O painel da central de segurança pode mostrar um número maior de assinaturas sob **cobertura de assinatura** do que o número de assinaturas mostradas em **Gerenciamento de política**. Abrangência da subscrição mostra o número de subscrições Standard, Gratuitas e "não abrangidas". As assinaturas "não cobertas" não têm a central de segurança habilitada e não são exibidas em **Gerenciamento de política**.
   >

2. Selecione a assinatura ou grupo de gerenciamento cujas políticas você deseja exibir.

   - A tela **política de segurança** reflete a ação executada pelas políticas atribuídas na assinatura ou no grupo de gerenciamento selecionado.
   - Na parte superior, use os links fornecidos para abrir cada **atribuição** de política que se aplica à assinatura ou ao grupo de gerenciamento. Você pode usar os links para acessar a atribuição e editar ou desabilitar a política. Por exemplo, se você vir que uma atribuição de política específica está efetivamente negando o Endpoint Protection, você pode usar o link para acessar a política e editá-la ou desabilitá-la.
   - Na lista de políticas, você pode ver o aplicativo efetivo da política em sua assinatura ou grupo de gerenciamento. Isso significa que as configurações de cada política que se aplicam ao escopo são levadas em consideração e você é fornecido com o resultado cumulativo de qual ação é realizada pela política. Por exemplo, se em uma atribuição a política estiver desabilitada, mas em outra estiver definida como AuditIfNotExist, o efeito cumulativo aplicará AuditIfNotExist. O efeito mais ativo sempre tem precedência.
   - O efeito das políticas pode ser: Acrescentar, auditar, AuditIfNotExists, negar, DeployIfNotExists, desabilitado. Para obter mais informações sobre como os efeitos são aplicados, consulte [entender os efeitos da política](../governance/policy/concepts/effects.md).

   ![tela de política](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> Ao exibir políticas atribuídas, você pode ver várias atribuições e pode ver como cada atribuição é configurada por conta própria.

## <a name="edit-security-policies"></a>Editar as políticas de segurança
Pode editar a política de segurança predefinida para cada uma das suas subscrições e dos seus grupos de gestão do Azure no [Azure Policy](../governance/policy/tutorials/create-and-manage.md). Para modificar uma política de segurança, você deve ser um proprietário ou administrador de segurança da assinatura ou o grupo de gerenciamento que o contém.

Para obter instruções sobre como editar uma política de segurança no Azure Policy, consulte e [criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md).

Você pode editar as políticas de segurança por meio do portal de Azure Policy, por meio da API REST ou usando o Windows PowerShell. O exemplo a seguir fornece instruções para editar usando a API REST.


## <a name="disable-security-policies"></a>Desabilitar políticas de segurança
Se a política de segurança padrão estiver gerando uma recomendação que não seja relevante para seu ambiente, você poderá interrompê-la desabilitando a definição de política que envia a recomendação.
Para obter mais informações sobre recomendações, consulte [Gerenciando recomendações de segurança](security-center-recommendations.md).

1. Na central de segurança, na seção **conformidade do & de política** , clique em **política de segurança**.

   ![gerenciamento de políticas](./media/tutorial-security-policy/policy-management.png)

2. Clique na assinatura ou grupo de gerenciamento para o qual você deseja desabilitar a recomendação.

   > [!Note]
   > Lembre-se de que um grupo de gerenciamento aplica suas políticas a suas assinaturas. Portanto, se você desabilitar a política de uma assinatura e a assinatura pertencer a um grupo de gerenciamento que ainda usa a mesma política, você continuará a receber as recomendações de política. A política ainda será aplicada a partir do nível de gerenciamento e as recomendações ainda serão geradas.

1. Clique na política atribuída.

   ![desabilitar política](./media/tutorial-security-policy/security-policy.png)

1. Na seção **parâmetros** , procure a política que invoca a recomendação que você deseja desabilitar e, na lista suspensa, selecione **desabilitada**

   ![desabilitar política](./media/tutorial-security-policy/disable-policy.png)
1. Clique em **Guardar**.
   > [!Note]
   > As alterações de política de desabilitação podem levar até 12 horas para entrar em vigor.


### <a name="configure-a-security-policy-using-the-rest-api"></a>Configurar uma política de segurança usando a API REST

Como parte da integração nativa com o Azure Policy, a central de segurança do Azure permite que você aproveite a API REST do Azure Policy para criar atribuições de política. As instruções a seguir orientam você durante a criação de atribuições de política, bem como a personalização de atribuições existentes. 

Conceitos importantes no Azure Policy: 

- Uma **definição de política** é uma regra 

- Uma **iniciativa** é uma coleção de definições de política (regras) 

- Uma **atribuição** é um aplicativo de uma iniciativa ou uma política para um escopo específico (grupo de gerenciamento, assinatura, etc.) 

A central de segurança tem uma iniciativa interna que inclui todas as suas políticas de segurança. Para avaliar as políticas da central de segurança em seus recursos do Azure, você deve criar uma atribuição no grupo de gerenciamento ou assinatura que deseja avaliar.  

A iniciativa interna tem todas as políticas da central de segurança habilitadas por padrão. Você pode optar por desabilitar determinadas políticas da iniciativa interna, por exemplo, você pode aplicar todas as políticas da central de segurança, exceto o **Firewall do aplicativo Web**, alterando o valor do parâmetro de efeito da política para **desabilitado**. 

### <a name="api-examples"></a>Exemplos de API

Nos exemplos a seguir, substitua essas variáveis:

- **{Scope}** Insira o nome do grupo de gerenciamento ou da assinatura à qual você está aplicando a política.
- **{policyAssignmentName}** Insira o [nome da atribuição de política relevante](#policy-names).
- **{Name}** insira seu nome ou o nome do administrador que aprovou a alteração da política.

Este exemplo mostra como atribuir a iniciativa da central de segurança interna em uma assinatura ou grupo de gerenciamento
 
 ```
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
 ```

Este exemplo mostra como atribuir a iniciativa de central de segurança interna em uma assinatura, com as seguintes políticas desabilitadas: 

- Atualizações do sistema ("systemUpdatesMonitoringEffect") 

- Configurações de segurança ("systemConfigurationsMonitoringEffect") 

- Endpoint Protection ("endpointProtectionMonitoringEffect") 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
Este exemplo mostra como remover uma atribuição:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

### Referência de nomes de política<a name="policy-names"></a>

|Nome da política na central de segurança|Nome da política exibido no Azure Policy |Nome do parâmetro de efeito de política|
|----|----|----|
|Encriptação SQL |Monitorar banco de dados SQL não criptografado na central de segurança do Azure |sqlEncryptionMonitoringEffect| 
|Auditoria do SQL |Monitorar banco de dados SQL não auditado na central de segurança do Azure |sqlAuditingMonitoringEffect|
|Atualizações do sistema |Monitorar atualizações do sistema ausentes na central de segurança do Azure |systemUpdatesMonitoringEffect|
|Encriptação do armazenamento |Auditoria de criptografia de blob ausente para contas de armazenamento |storageEncryptionMonitoringEffect|
|Acesso à rede JIT |Monitorar o acesso possível de rede JIT (just in time) na central de segurança do Azure |jitNetworkAccessMonitoringEffect |
|Controlos de aplicações adaptáveis |Monitorar a lista de permissões de aplicativo possíveis na central de segurança do Azure |adaptiveApplicationControlsMonitoringEffect|
|Grupos de segurança de rede |Monitorar o acesso à rede permissivo na central de segurança do Azure |networkSecurityGroupsMonitoringEffect| 
|Configurações de segurança |Monitorar vulnerabilidades do sistema operacional na central de segurança do Azure |systemConfigurationsMonitoringEffect| 
|Endpoint protection |Monitorar Endpoint Protection ausentes na central de segurança do Azure |endpointProtectionMonitoringEffect |
|Encriptação de disco |Monitorar discos de VM não criptografados na central de segurança do Azure |diskEncryptionMonitoringEffect|
|Avaliação de vulnerabilidades |Monitorar vulnerabilidades de VM na central de segurança do Azure |vulnerabilityAssessmentMonitoringEffect|
|Firewall de aplicação Web |Monitorar aplicativo Web desprotegido na central de segurança do Azure |webApplicationFirewallMonitoringEffect |
|Firewall da próxima geração |Monitorar pontos de extremidade de rede desprotegidos na central de segurança do Azure| |


### <a name="who-can-edit-security-policies"></a>Quem pode editar políticas de segurança?
A central de segurança usa o RBAC (controle de acesso baseado em função), que fornece funções internas que podem ser atribuídas a usuários, grupos e serviços no Azure. Quando os usuários abrem a central de segurança, eles veem apenas as informações relacionadas aos recursos aos quais eles têm acesso. Isso significa que os usuários são atribuídos à função de proprietário, colaborador ou leitor à assinatura ou ao grupo de recursos ao qual um recurso pertence. Além destas funções, há duas funções específicas do Centro de Segurança:

- Leitor de segurança: Ter direitos de exibição para a central de segurança, que inclui recomendações, alertas, política e integridade, mas não podem fazer alterações.
- Administrador de segurança: Ter os mesmos direitos de exibição que o leitor de segurança, e eles também podem atualizar a política de segurança e ignorar recomendações e alertas.



## <a name="next-steps"></a>Passos Seguintes
Neste artigo, você aprendeu a editar as políticas de segurança no Azure Policy. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

* [Guia de planejamento e operações da central de segurança do Azure](security-center-planning-and-operations-guide.md): Saiba como planejar e entender as considerações de design sobre a central de segurança do Azure.
* [Monitoramento de integridade de segurança na central de segurança do Azure](security-center-monitoring.md): Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Gerenciar e responder a alertas de segurança na central de segurança do Azure](security-center-managing-and-responding-alerts.md): Saiba como gerir e responder a alertas de segurança.
* [Monitorar soluções de parceiros com a central de segurança do Azure](security-center-partner-solutions.md): Saiba como monitorizar o estado de funcionamento das soluções dos seus parceiros.
* [Obter visibilidade em todo o locatário para a central de segurança do Azure](security-center-management-groups.md): Saiba como configurar grupos de gerenciamento para a central de segurança do Azure.
* [Perguntas frequentes da central de segurança do Azure](security-center-faq.md): Encontre respostas para as perguntas mais frequentes sobre o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): Encontre mensagens do blogue acerca da segurança e conformidade do Azure.

Para saber mais sobre o Azure Policy, veja [O que é o Azure Policy?](../governance/policy/overview.md)
