---
title: Monitorização de diagnóstico Azure para Azure Attestation
description: Monitorização de diagnóstico Azure para Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d2773be4bc67e125c18d5d38c951685e4f4fceaf
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168353"
---
# <a name="set-up-diagnostics-with-a-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Configurar diagnósticos com um módulo de plataforma fidedigna (TPM) ponto final da Attestation Azure

Este artigo ajuda-o a criar e configurar configurações de diagnóstico para enviar métricas de plataforma e registos de plataforma para diferentes destinos. [Os registos da plataforma](/azure/azure-monitor/platform/platform-logs-overview) em Azure, incluindo os registos de atividades azure e registos de recursos, fornecem informações detalhadas de diagnóstico e auditoria para os recursos do Azure e para a plataforma Azure de que dependem. [As métricas da plataforma](/azure/azure-monitor/platform/data-platform-metrics) são recolhidas por padrão e são armazenadas na base de dados Azure Monitor Metrics.

Antes de começar, certifique-se de ter [configurado a Azure Attestation com a Azure PowerShell](quickstart-powershell.md).

O serviço de ponto final do Módulo plataforma fidedigna (TPM) está ativado nas definições de diagnóstico e pode ser utilizado para monitorizar a atividade. Confiúdice [Azure Monitoring](/azure/azure-monitor/overview) para o ponto de terminamento do serviço TPM utilizando o seguinte código.

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```

Os registos de atividade estão na secção **de Contentores** da conta de armazenamento. Para obter mais informações, consulte [Recolher e analisar registos de recursos a partir de um recurso Azure.](/azure/azure-monitor/learn/tutorial-resource-logs)
