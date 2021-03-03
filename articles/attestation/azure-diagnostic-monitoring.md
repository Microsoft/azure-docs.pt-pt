---
title: Monitorização de diagnóstico Azure - Azure Attestation
description: Monitorização de diagnóstico Azure para Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d01e7817906927295591353b710afe2899aacdf1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726483"
---
# <a name="setting-up-diagnostics-with-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Criação de diagnósticos com Módulo de Plataforma Fidedigna (TPM) ponto final da Attestation Azure

[Os registos da plataforma](../azure-monitor/essentials/platform-logs-overview.md) em Azure, incluindo os registos de atividades Azure e registos de recursos, fornecem informações detalhadas de diagnóstico e auditoria para os recursos do Azure e para a plataforma Azure em que dependem. [As métricas da plataforma](../azure-monitor/essentials/data-platform-metrics.md) são recolhidas por padrão e normalmente armazenadas na base de dados de métricas do Azure Monitor. Este artigo fornece detalhes sobre a criação e configuração de configurações de diagnóstico para enviar métricas de plataforma e registos de plataforma para diferentes destinos. 

O serviço de ponto final TPM está ativado com definição de diagnóstico e pode ser utilizado para monitorizar a atividade. Para configurar [o Azure Monitoring](../azure-monitor/overview.md) para o ponto final de serviço TPM utilizando o PowerShell siga gentilmente os passos abaixo. 

Configurar o serviço Azure Attestation. 

[Configurar a Azure Attestation com a Azure PowerShell](./quickstart-powershell.md)

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```
Os registos de atividade podem ser encontrados na secção de Contentores da conta de armazenamento. Informações detalhadas podem ser encontradas em [registos de recursos de um recurso Azure e analisar com O Azure Monitor - Azure Monitor](../azure-monitor/essentials/tutorial-resource-logs.md)
