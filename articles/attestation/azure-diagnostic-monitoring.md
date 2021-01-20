---
title: Monitorização de diagnóstico Azure - Azure Attestation
description: Monitorização de diagnóstico Azure para Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 40bc76f839cf6757b8f874112504249e611c3e1a
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98606122"
---
# <a name="setting-up-diagnostics-with-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Criação de diagnósticos com Módulo de Plataforma Fidedigna (TPM) ponto final da Attestation Azure

[Os registos da plataforma](/azure/azure-monitor/platform/platform-logs-overview) em Azure, incluindo os registos de atividades Azure e registos de recursos, fornecem informações detalhadas de diagnóstico e auditoria para os recursos do Azure e para a plataforma Azure em que dependem. [As métricas da plataforma](/azure/azure-monitor/platform/data-platform-metrics) são recolhidas por padrão e normalmente armazenadas na base de dados de métricas do Azure Monitor. Este artigo fornece detalhes sobre a criação e configuração de configurações de diagnóstico para enviar métricas de plataforma e registos de plataforma para diferentes destinos. 

O serviço de ponto final TPM está ativado com definição de diagnóstico e pode ser utilizado para monitorizar a atividade. Para configurar [o Azure Monitoring](/azure/azure-monitor/overview) para o ponto final de serviço TPM utilizando o PowerShell siga gentilmente os passos abaixo. 

Configurar o serviço Azure Attestation. 

[Configurar a Azure Attestation com a Azure PowerShell](/azure/attestation/quickstart-powershell#:~:text=%20Quickstart%3A%20Set%20up%20Azure%20Attestation%20with%20Azure,Register%20Microsoft.Attestation%20resource%20provider.%20Register%20the...%20More%20)

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```
Os registos de atividade podem ser encontrados na secção de Contentores da conta de armazenamento. Informações detalhadas podem ser encontradas em [registos de recursos de um recurso Azure e analisar com O Azure Monitor - Azure Monitor](/azure/azure-monitor/learn/tutorial-resource-logs)
