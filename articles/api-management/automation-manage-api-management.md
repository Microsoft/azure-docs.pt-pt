---
title: Gerir a Azure API Management utilizando a Azure Automation
description: Saiba como o serviço Azure Automation pode ser usado para gerir a Azure API Management.
services: api-management, automation
documentationcenter: ''
author: vladvino
manager: eamono
editor: ''
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: a472e00f9ecab8a5ffa6b19e4fe9a5f8b5ee5b95
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "70072050"
---
# <a name="managing-azure-api-management-using-azure-automation"></a>Gerir a Gestão de API do Azure com a Automatização do Azure
Este guia apresenta-lhe o serviço Azure Automation e como pode ser usado para simplificar a gestão da Azure API Management.

## <a name="what-is-azure-automation"></a>O que é a Automatização do Azure?
[A Azure Automation](https://azure.microsoft.com/services/automation/) é um serviço Azure para simplificar a gestão de nuvem através da automatização de processos. A utilização de tarefas de automação, manual, repetidas, de longa duração e propensas a erros pode ser automatizada para aumentar a fiabilidade, eficiência e tempo de valor para a sua organização.

A Azure Automation fornece um motor de execução de fluxo de trabalho altamente fiável e altamente disponível que escala para atender às suas necessidades. Na Azure Automation, os processos podem ser iniciados manualmente, por sistemas de terceiros ou em intervalos programados para que as tarefas ocorram exatamente quando necessário.

Reduza a sobrecarga operacional e liberte o pessoal de TI e DevOps para se concentrar no trabalho que acrescente valor do negócio, movendo as suas tarefas de gestão de nuvem para ser executado automaticamente pela Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Como pode a Azure Automation ajudar a gerir a Azure API Management?
A API Management pode ser gerida na Azure Automation utilizando os [cmdlets Windows PowerShell para Azure API Management API](https://docs.microsoft.com/powershell/module/az.apimanagement). Dentro da Azure Automation, pode escrever scripts de fluxo de trabalho PowerShell para executar muitas das suas tarefas de Gestão de API utilizando os cmdlets. Pode ainda emparelhar estes cmdlets na Azure Automation com os cmdlets para outros serviços Azure, para automatizar tarefas complexas através dos serviços Azure e sistemas de terceiros.

Aqui estão alguns exemplos de utilização da Gestão da API com a Powershell:

* [Exemplos do Azure PowerShell para Gestão de API](https://docs.microsoft.com/azure/api-management/powershell-samples)

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu os fundamentos da Azure Automation e como pode ser usado para gerir a Azure API Management, siga estes links para saber mais.

* Veja a Azure Automation [a começar tutorial.](../automation/automation-first-runbook-graphical.md)

