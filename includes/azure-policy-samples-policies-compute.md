---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 667d017e8febcf1abcc1cfe21ecfaa43ae75ea6d
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172752"
---
|Nome |Descrição |Efeitos(s) |Versão |
|---|---|---|---|
|[Máquina virtual permitida SKUs](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSkusAllowed_Deny.json) |Esta política permite-lhe especificar um conjunto de SKUs de máquinas virtuais que a organização pode implementar. |Negar |1.0.0 |
|[Auditar máquinas virtuais sem recuperação de desastres configurada](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |Audite máquinas virtuais que não tenham recuperação de desastres configurada. Para saber mais sobre a recuperação de desastres, visite https://aka.ms/asr-doc. |auditoriaIfNotExists |1.0.0 |
|[VMs de auditoria que não usam discos geridos](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |Esta política audita VMs que não usam discos geridos |auditoria |1.0.0 |
|[Implementar extensão padrão microsoft IaaSAntimalware para Windows Server](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |Esta política implementa uma extensão Microsoft IaaSAntimalware com uma configuração predefinida quando um VM não está configurado com a extensão antimalware. |implementarIfNotExists |1.0.0 |
|[Os registos de diagnóstico em conjuntos de escala de máquinas virtuais devem ser ativados](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |Recomenda-se ativar os Registos para que o rasto de atividade possa ser recriado quando são necessárias investigações em caso de incidente ou compromisso. |AuditoriaIfNotExists, Deficiente |1.0.0 |
|[Microsoft Antimalware para Azure deve ser configurado para atualizar automaticamente assinaturas de proteção](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |Esta política audita qualquer máquina virtual do Windows não configurada com atualização automática de assinaturas de proteção Antimalware da Microsoft. |AuditoriaIfNotExists, Deficiente |1.0.0 |
|[Extensão Microsoft IaaSAntimalware deve ser implementada nos servidores do Windows](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/WindowsServers_AntiMalware_AuditIfNotExists.json) |Esta política audita qualquer VM do servidor Windows sem a extensão Microsoft IaaSAntimalware implementada. |AuditoriaIfNotExists, Deficiente |1.0.0 |
|[Só devem ser instaladas extensões VM aprovadas](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |Esta política rege as extensões de máquinas virtuais que não são aprovadas. |Auditoria, Negação, Deficientes |1.0.0 |
|[Remendar a imagem automática do OS em conjuntos de escala de máquina virtual](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSSOSUpgradeHealthCheck_Deny.json) |Esta política impõe que a correção automática de imagem de OS em conjuntos de escala de máquinavirtual mantenha sempre as Máquinas Virtuais seguras aplicando com segurança os mais recentes patches de segurança todos os meses. |negar |1.0.0 |
|[Os discos não ligados devem ser encriptados](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |Esta política audita qualquer disco não ligado sem encriptação ativada. |Auditoria, Deficientes |1.0.0 |
|[Máquinas virtuais devem ser migradas para novos recursos do Gestor de Recursos Do Azure](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |Utilize o novo Gestor de Recursos Azure para as suas máquinas virtuais para fornecer melhorias de segurança tais como: controlo de acesso mais forte (RBAC), melhor auditoria, implantação e governação baseadas em ARM, acesso a identidades geridas, acesso a cofre chave para segredos, Azure Autenticação e suporte baseados em AD para etiquetas e grupos de recursos para uma gestão mais fácil da segurança |Auditoria, Negação, Deficientes |1.0.0 |
