---
title: Crie políticas de segurança personalizadas no Azure Security Center | Microsoft Docs
description: Definições de política personalizadas Azure monitorizadas pelo Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.author: memildin
zone_pivot_groups: manage-asc-initiatives
ms.openlocfilehash: a901e71da640f8413e5714ad59073324f582c1b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102441062"
---
# <a name="create-custom-security-initiatives-and-policies"></a>Criar iniciativas e políticas de segurança personalizadas

Para ajudar a proteger os seus sistemas e ambiente, o Azure Security Center gera recomendações de segurança. Estas recomendações baseiam-se nas melhores práticas do setor, que são incorporadas na política de segurança genérica e padrão fornecida a todos os clientes. Também podem vir do conhecimento do Centro de Segurança sobre a indústria e as normas regulamentares.

Com esta funcionalidade, pode adicionar as suas próprias iniciativas *personalizadas.* Em seguida, receberá recomendações se o seu ambiente não seguir as políticas que cria. Quaisquer iniciativas personalizadas que crie aparecerão ao lado das iniciativas integradas no painel de conformidade regulamentar, conforme descrito no tutorial Melhorar a [sua conformidade regulamentar.](security-center-compliance-dashboard.md)

Como discutido na [documentação da Política Azure,](../governance/policy/concepts/definition-structure.md#definition-location)quando especificar um local para a sua iniciativa personalizada, deve ser um grupo de gestão ou uma subscrição. 

> [!TIP]
> Para uma visão geral dos conceitos-chave nesta página, veja [o que são políticas de segurança, iniciativas e recomendações?](security-policy-concept.md)

::: zone pivot="azure-portal"

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Para adicionar uma iniciativa personalizada à sua subscrição 

1. Da barra lateral do Centro de Segurança, abra a página **da política de segurança.**

1. Selecione uma subscrição ou Grupo de Gestão ao qual gostaria de adicionar uma iniciativa personalizada.

    [![Selecionando uma subscrição para a qual irá criar a sua política personalizada](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Deve adicionar padrões personalizados ao nível de subscrição (ou superior) para que sejam avaliados e exibidos no Centro de Segurança. 
    >
    > Quando se adiciona um padrão personalizado, atribui uma *iniciativa* a esse âmbito. Por isso, recomendamos que selecione o âmbito mais amplo necessário para essa atribuição.

1. Na página de política de Segurança, no âmbito das suas iniciativas personalizadas, clique em **Adicionar uma iniciativa personalizada**.

    [![Clique Em Adicionar uma iniciativa personalizada](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    É apresentada a seguinte página:

    ![Criar ou adicionar uma política](media/custom-security-policies/create-or-add-custom-policy.png)

1. Na página De iniciativas personalizadas Add, reveja a lista de políticas personalizadas já criadas na sua organização. Se vir uma que pretende atribuir à sua subscrição, clique em **Adicionar**. Se não houver uma iniciativa na lista que atenda às suas necessidades, ignore este passo.

1. Para criar uma nova iniciativa personalizada:

    1. Clique **em Criar novo**.
    1. Insira a localização e o nome da definição.
    1. Selecione as políticas para incluir e clique **Em Adicionar**.
    1. Introduza os parâmetros desejados.
    1. Clique em **Guardar**.
    1. Na página De iniciativas personalizadas Adicionar, clique em atualização. A sua nova iniciativa será mostrada como disponível.
    1. Clique **em Adicionar** e atribua-o à sua subscrição.

    > [!NOTE]
    > A criação de novas iniciativas requer credenciais de proprietário de assinaturas. Para obter mais informações sobre as funções da Azure, consulte [permissões no Centro de Segurança Azure](security-center-permissions.md).

    A sua nova iniciativa entra em vigor e pode ver o impacto das duas seguintes formas:

    * A partir da barra lateral do Centro de Segurança, no âmbito da Conformidade & Política, **selecione conformidade regulamentar**. O painel de conformidade abre-se para mostrar a sua nova iniciativa personalizada ao lado das iniciativas incorporadas.
    
    * Começará a receber recomendações se o seu ambiente não seguir as políticas que definiu.

1. Para ver as recomendações resultantes para a sua política, clique em **Recomendações** da barra lateral para abrir a página de recomendações. As recomendações aparecerão com uma etiqueta "Personalizada" e estarão disponíveis dentro de aproximadamente uma hora.

    [![Recomendações personalizadas](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

::: zone-end

::: zone pivot="rest-api"

## <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Configure uma política de segurança na Política Azure usando a API REST

Como parte da integração nativa com a Azure Policy, o Azure Security Center permite-lhe aproveitar a API REST da Azure Policy para criar atribuições políticas. As seguintes instruções acompanham-no através da criação de atribuições políticas, bem como da personalização das atribuições existentes. 

Conceitos importantes na Política Azure: 

- Uma **definição de política** é uma regra 

- Uma **iniciativa** é um conjunto de definições políticas (regras) 

- Uma **atribuição** é uma aplicação de uma iniciativa ou de uma política a um âmbito específico (grupo de gestão, subscrição, etc.) 

O Centro de Segurança tem uma iniciativa integrada, a Azure Security Benchmark, que inclui todas as suas políticas de segurança. Para avaliar as políticas do Security Center sobre os seus recursos Azure, deverá criar uma atribuição no grupo de gestão ou subscrição que pretende avaliar.

A iniciativa incorporada tem todas as políticas do Centro de Segurança habilitados por defeito. Pode optar por desativar certas políticas da iniciativa incorporada. Por exemplo, para aplicar todas as políticas do Security Center, exceto a firewall de **aplicações web,** altere o valor do parâmetro de efeito da apólice para **Desativado**.

## <a name="api-examples"></a>Exemplos de API

Nos seguintes exemplos, substitua estas variáveis:

- **{scope}** insira o nome do grupo de gestão ou subscrição a que está a aplicar a política
- **{policyAssignmentName}** insira o nome da atribuição de política relevante
- **{nome}** insira o seu nome, ou o nome do administrador que aprovou a alteração de política

Este exemplo mostra-lhe como atribuir a iniciativa do Centro de Segurança incorporado a um grupo de subscrição ou gestão
 
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

Este exemplo mostra-lhe como atribuir a iniciativa do Centro de Segurança incorporado numa subscrição, com as seguintes políticas desativadas: 

- Atualizações do sistema ("systemUpdatesMonitoringEffect") 

- Configurações de segurança ("systemConfigurationsMonitoringEffect") 

- Proteção do ponto final ("endpointProtectionMonitoringEffect") 

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
Este exemplo mostra-lhe como remover uma atribuição:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

::: zone-end


## <a name="enhance-your-custom-recommendations-with-detailed-information"></a>Melhore as suas recomendações personalizadas com informações detalhadas

As recomendações incorporadas fornecidas com o Azure Security Center incluem detalhes como níveis de gravidade e instruções de reparação. Se pretender adicionar este tipo de informação às suas recomendações personalizadas para que apareça no portal Azure ou onde quer que aceda às suas recomendações, terá de utilizar a API REST. 

Os dois tipos de informação que pode adicionar são:

- **RemediationDDescrição** – Cadeia
- **Severidade** – Enum [Baixo, Médio, Alto]

Os metadados devem ser adicionados à definição de política para uma política que faça parte da iniciativa personalizada. Deve estar na propriedade "SecurityCenter", como mostrado:

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High"
    },
```

Abaixo está um exemplo de uma política personalizada, incluindo a propriedade metadados/securityCenter:

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "RemediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "Severity": "High"
        }
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

Para outro exemplo de utilização da propriedade securityCenter, consulte [esta secção da documentação REST API](/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples).


## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar políticas de segurança personalizadas. 

Para outros materiais relacionados, consulte os seguintes artigos: 

- [A visão geral das políticas de segurança](tutorial-security-policy.md)
- [Uma lista das políticas de segurança incorporadas](./policy-reference.md)