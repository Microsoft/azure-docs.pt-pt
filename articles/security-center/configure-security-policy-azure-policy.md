---
title: Criar e editar políticas de segurança da Política Azure utilizando a API REST
description: Conheça a gestão da política azure através de uma API REST.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: c218b5dc8ca3bfa0358a9b6a0d4867696762a8d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77430946"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Configure uma política de segurança na Política Azure utilizando a API REST

Como parte da integração nativa com a Política Azure, o Azure Security Center permite-lhe aproveitar a API REST da Azure Policy para criar atribuições políticas. As seguintes instruções passam por você através da criação de atribuições políticas, bem como da personalização das atribuições existentes. 

Conceitos importantes na Política Azure: 

- Uma **definição de política** é uma regra 

- Uma **iniciativa** é uma coleção de definições políticas (regras) 

- Uma **atribuição** é uma aplicação de uma iniciativa ou uma política a um âmbito específico (grupo de gestão, subscrição, etc.) 

O Centro de Segurança tem uma iniciativa integrada que inclui todas as suas políticas de segurança. Para avaliar as políticas do Security Center sobre os seus recursos Azure, deverá criar uma atribuição no grupo de gestão ou subscrição que pretende avaliar.

A iniciativa incorporada tem todas as políticas do Centro de Segurança possibilitadas por defeito. Pode optar por desativar certas políticas da iniciativa incorporada. Por exemplo, para aplicar todas as políticas do Security Center, exceto firewall de **aplicações web,** alterar o valor do parâmetro de efeito da apólice para **Deficientes**. 

## <a name="api-examples"></a>Exemplos de API

Nos seguintes exemplos, substitua estas variáveis:

- **{scope}** introduza o nome do grupo de gestão ou subscrição a que está a aplicar a apólice.
- **{policyAssignmentName}** introduza o [nome da atribuição de política relevante](#policy-names).
- **{name}** introduza o seu nome ou o nome do administrador que aprovou a alteração de política.

Este exemplo mostra-lhe como atribuir a iniciativa do Centro de Segurança incorporado num grupo de subscrição ou gestão
 
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

## <a name="policy-names-reference"></a>Referência de nomes de políticas<a name="policy-names"></a>

|Nome de política no Centro de Segurança|Nome de política exibido na Política de Azure |Nome do parâmetro de efeito político|
|----|----|----|
|Encriptação SQL |Monitorize a base de dados SQL não encriptada no Centro de Segurança Azure |sqlEncryptionMonitoringEffect| 
|Auditoria do SQL |Monitorize a base de dados SQL não auditada no Centro de Segurança Azure |sqlAuditingMonitoringEffect|
|Atualizações do sistema |Monitorizar as atualizações do sistema em falta no Centro de Segurança do Azure |systemUpdatesMonitoringEffect|
|Encriptação do armazenamento |Auditar encriptação por blob em falta para contas de armazenamento |armazenamentoEncriptaçãoMonitoreffect|
|Acesso à Rede JIT |Monitorize possíveis acessos de rede just-in-time (JIT) no Centro de Segurança Azure |jitNetworkAccessMonitoringEffect |
|Controlos de aplicações adaptáveis |Monitorize possível aplicação Whitelisting no Azure Security Center |adaptativoAplicaçãoControlEsMonitoringEffect|
|Grupos de segurança de rede |Monitor acesso permissivo à rede no Centro de Segurança Azure |networkSecurityGroupsMonitoringEffect| 
|Configurações de segurança |Monitorize as vulnerabilidades do OS no Centro de Segurança Azure |sistemaConfiguraçõesMonitoringEffect| 
|Endpoint protection |Monitor desaparecido Proteção de Ponto final no Centro de Segurança Azure |ponto finalPontoProtectionMonitoringEffect |
|Encriptação de disco |Monitor de Discos VM não encriptados no Centro de Segurança Azure |diskEncryptionMonitoringEffect|
|Avaliação de vulnerabilidades |Monitor De vulnerabilidades vm no Centro de Segurança Azure |vulnerabilidadeAssessmentMonitoringEffect|
|Firewall de aplicação Web |Monitorize aplicação web desprotegida no Centro de Segurança Azure |webApplicationFirewallMonitoringEffect |
|Firewall da próxima geração |Monitorde pontos finais de rede desprotegidos no Centro de Segurança Azure| |


## <a name="next-steps"></a>Passos seguintes

Para outros materiais relacionados, consulte os seguintes artigos: 

- [Políticas de segurança personalizadas](custom-security-policies.md)
- [Visão geral da política de segurança](tutorial-security-policy.md)