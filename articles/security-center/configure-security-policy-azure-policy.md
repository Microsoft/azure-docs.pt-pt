---
title: Criar e editar políticas de segurança de Azure Policy usando a API REST | Microsoft Docs
description: Saiba mais sobre o gerenciamento de políticas de Azure Policy por meio de uma API REST.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 56c5ca8c4e1d70e002a338c753f9ab1f0b1aa411
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522024"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Configurar uma política de segurança no Azure Policy usando a API REST

Como parte da integração nativa com o Azure Policy, a central de segurança do Azure permite que você aproveite a API REST do Azure Policy para criar atribuições de política. As instruções a seguir orientam você durante a criação de atribuições de política, bem como a personalização de atribuições existentes. 

Conceitos importantes no Azure Policy: 

- Uma **definição de política** é uma regra 

- Uma **iniciativa** é uma coleção de definições de política (regras) 

- Uma **atribuição** é um aplicativo de uma iniciativa ou uma política para um escopo específico (grupo de gerenciamento, assinatura, etc.) 

A central de segurança tem uma iniciativa interna que inclui todas as suas políticas de segurança. Para avaliar as políticas da central de segurança em seus recursos do Azure, você deve criar uma atribuição no grupo de gerenciamento ou assinatura que deseja avaliar.

A iniciativa interna tem todas as políticas da central de segurança habilitadas por padrão. Você pode optar por desabilitar determinadas políticas da iniciativa interna. Por exemplo, para aplicar todas as políticas da central de segurança, exceto o **Firewall do aplicativo Web**, altere o valor do parâmetro de efeito da política para **desabilitado**. 

## <a name="api-examples"></a>Exemplos de API

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

## Referência de nomes de política<a name="policy-names"></a>

|Nome da política na central de segurança|Nome da política exibido no Azure Policy |Nome do parâmetro de efeito de política|
|----|----|----|
|Encriptação SQL |Monitorar banco de dados SQL não criptografado na central de segurança do Azure |sqlEncryptionMonitoringEffect| 
|Auditoria do SQL |Monitorar banco de dados SQL não auditado na central de segurança do Azure |sqlAuditingMonitoringEffect|
|Atualizações do sistema |Monitorar atualizações do sistema ausentes na central de segurança do Azure |systemUpdatesMonitoringEffect|
|Encriptação do armazenamento |Auditoria de criptografia de blob ausente para contas de armazenamento |storageEncryptionMonitoringEffect|
|Acesso à rede JIT |Monitorar um possível acesso JIT (just-in-time) de rede na central de segurança do Azure |jitNetworkAccessMonitoringEffect |
|Controlos de aplicações adaptáveis |Monitorar a lista de permissões de aplicativo possíveis na central de segurança do Azure |adaptiveApplicationControlsMonitoringEffect|
|Grupos de segurança de rede |Monitorar o acesso à rede permissivo na central de segurança do Azure |networkSecurityGroupsMonitoringEffect| 
|Configurações de segurança |Monitorar vulnerabilidades do sistema operacional na central de segurança do Azure |systemConfigurationsMonitoringEffect| 
|Endpoint protection |Monitorar Endpoint Protection ausentes na central de segurança do Azure |endpointProtectionMonitoringEffect |
|Encriptação de disco |Monitorar discos de VM não criptografados na central de segurança do Azure |diskEncryptionMonitoringEffect|
|Avaliação de vulnerabilidades |Monitorar vulnerabilidades de VM na central de segurança do Azure |vulnerabilityAssessmentMonitoringEffect|
|Firewall de aplicação Web |Monitorar aplicativo Web desprotegido na central de segurança do Azure |webApplicationFirewallMonitoringEffect |
|Firewall da próxima geração |Monitorar pontos de extremidade de rede desprotegidos na central de segurança do Azure| |


## <a name="next-steps"></a>Passos seguintes

Para obter outros materiais relacionados, consulte os seguintes artigos: 

- [Políticas de segurança personalizadas](custom-security-policies.md)
- [Visão geral da política de segurança](tutorial-security-policy.md)