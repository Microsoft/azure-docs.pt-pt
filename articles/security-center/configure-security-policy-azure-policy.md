---
title: Criar e editar políticas de segurança da Política Azure utilizando a API REST
description: Saiba mais sobre a gestão de políticas da Azure Política através de uma API REST.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 6fcfed585aece4fd57c085e0c9f6fdcd6cbeae74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448361"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Configure uma política de segurança na Política Azure usando a API REST

Como parte da integração nativa com a Azure Policy, o Azure Security Center permite-lhe aproveitar a API REST da Azure Policy para criar atribuições políticas. As seguintes instruções acompanham-no através da criação de atribuições políticas, bem como da personalização das atribuições existentes. 

Conceitos importantes na Política Azure: 

- Uma **definição de política** é uma regra 

- Uma **iniciativa** é um conjunto de definições políticas (regras) 

- Uma **atribuição** é uma aplicação de uma iniciativa ou de uma política a um âmbito específico (grupo de gestão, subscrição, etc.) 

O Centro de Segurança tem uma iniciativa integrada que inclui todas as suas políticas de segurança. Para avaliar as políticas do Security Center sobre os seus recursos Azure, deverá criar uma atribuição no grupo de gestão ou subscrição que pretende avaliar.

A iniciativa incorporada tem todas as políticas do Centro de Segurança habilitados por defeito. Pode optar por desativar certas políticas da iniciativa incorporada. Por exemplo, para aplicar todas as políticas do Security Center, exceto a firewall de **aplicações web,** altere o valor do parâmetro de efeito da apólice para **Desativado**. 

## <a name="api-examples"></a>Exemplos de API

Nos seguintes exemplos, substitua estas variáveis:

- **{scope}** insira o nome do grupo de gestão ou subscrição à qual está a aplicar a apólice.
- **{policyAssignmentName}** insira o [nome da atribuição de política relevante](#policy-names).
- **{nome}** insira o seu nome, ou o nome do administrador que aprovou a alteração de política.

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

## <a name="policy-names-reference"></a>Referência de nomes de política <a name="policy-names"></a>

|Nome da apólice no Centro de Segurança|Nome da política exibido na Política Azure |Nome do parâmetro de efeito de política|
|----|----|----|
|Encriptação SQL |Monitor desencrito base de dados SQL no Centro de Segurança Azure |sqlEncryptionMonitoringEffect| 
|Auditoria do SQL |Monitore a base de dados SQL não auditada no Azure Security Center |sqlAuditingMonitoringEffect|
|Atualizações do sistema |Monitorize atualizações do sistema em falta no Centro de Segurança Azure |sistemaUpdatesMonitoringEffect|
|Encriptação do armazenamento |Auditar encriptação por blob em falta para contas de armazenamento |storageEncryptionMonitoringEffect|
|Acesso à rede JIT |Monitorize possível acesso à rede just-in-time (JIT) no Azure Security Center |jitNetworkAccessMonitoringEffect |
|Controlos de aplicações adaptáveis |Monitorize possíveis listas de aplicações no Azure Security Center |adaptiveApplicationControlsMonitoringEffect|
|Grupos de segurança de rede |Monitore o acesso à rede permissiva no Centro de Segurança Azure |networkSecurityGroupsMonitoringEffect| 
|Configurações de segurança |Monitorize vulnerabilidades de SO no Centro de Segurança Azure |sistemaConfigurationsMonitoringEffect| 
|Proteção de ponto final |Monitor que falta proteção de ponto final no Centro de Segurança Azure |endpointProtectionMonitoringEffect |
|Encriptação de disco |Monitor de discos VM não encriptados no Centro de Segurança Azure |diskEncryptionMonitoringEffect|
|Avaliação de vulnerabilidades |Monitorizar vulnerabilidades VM no Centro de Segurança Azure |vulnerabilidadeAssessmentMonitoringEffect|
|Firewall de aplicação Web |Monitorize aplicação web desprotegida no Centro de Segurança Azure |webApplicationFirewallMonitoringEffect |
|Firewall da próxima geração |Monitorize pontos finais de rede desprotegidos no Centro de Segurança Azure| |


## <a name="next-steps"></a>Passos seguintes

Para outros materiais relacionados, consulte os seguintes artigos: 

- [Políticas de segurança personalizadas](custom-security-policies.md)
- [Visão geral da política de segurança](tutorial-security-policy.md)