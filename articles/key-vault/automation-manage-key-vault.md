---
title: Gerir o Azure Key Vault com a automatização do Azure - Azure Key Vault | Documentos da Microsoft
description: Saiba mais sobre como o serviço de automatização do Azure pode ser utilizado para gerir o Azure Key Vault.
services: Key-Vault, automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: ''
ms.assetid: 4e780762-19b6-4ca6-b894-ebb44c538f35
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: magoedte
ms.openlocfilehash: ace32968808dfa919e6ca5d5777818d2672249fe
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224878"
---
# <a name="managing-azure-key-vault-using-azure-automation"></a>Gerir o Azure Key Vault com a automatização do Azure

Este guia apresenta-lhe para o serviço de automatização do Azure e como ele pode ser usado para simplificar a gestão das chaves e segredos no Azure Key Vault.

## <a name="what-is-azure-automation"></a>O que é a Automatização do Azure?

[A automatização do Azure](../automation/automation-intro.md) é um serviço do Azure para simplificar a gestão da cloud através de automatização de processos e configuração do estado pretendido. Utilizar a automatização do Azure, manual, repetido, tarefas de execução demorada e propensa a erros podem ser automatizadas para aumentar a confiabilidade, a eficiência e a hora de valor para a sua organização.

A automatização do Azure fornece um motor de execução do fluxo de trabalho altamente confiável de elevada disponibilidade que se dimensiona para atender às suas necessidades. Na automatização do Azure, processos podem ser iniciados manualmente, por sistemas de terceiros 3rd ou em intervalos agendados, de modo a que tarefas acontecem exatamente quando necessário.

Reduzir o overhead operacional e liberar IT e a equipa do DevOps para se dedicar ao trabalho que acrescenta valor ao negócio, movendo as tarefas de gestão na cloud a ser executada automaticamente pela automatização do Azure.

## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Como pode automatização do Azure ajudar a gerir o Azure Key Vault?

Key Vault pode ser gerido na automatização do Azure utilizando o [cmdlets do AzureRM Key Vault](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) e [cmdlets do Cofre de chaves do Azure clássico](https://docs.microsoft.com/powershell/module/servicemanagement/azure). O módulo do Azure para gerir o Cofre de chave clássico está disponível automaticamente na automatização do Azure e pode importar os [módulo do AzureRM KeyVault](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) para a automatização do Azure, para que possam realizar muitas de suas tarefas de gestão do Cofre de chaves no serviço. Para saber mais sobre como importar o módulo para automatização do Azure, veja [gerir módulos na automatização do Azure](../automation/shared-resources/modules.md) também pode emparelhar estes cmdlets na automatização do Azure com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas em Serviços do Azure e o 3º sistemas de terceiros.

Com os cmdlets do Azure Key Vault pode realizar estas tarefas, entre outras: 

* Criar e configurar um cofre de chaves
* Criar ou importar uma chave
* Criar ou atualizar um segredo
* Atributos de atualização de uma chave
* Obter uma chave ou segredo
* Eliminar uma chave ou segredo

Aqui estão alguns exemplos de utilização do PowerShell para gerir o Cofre de chaves:  

* [Cofre de chaves do Azure - passo a passo](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Definir e configurar um cofre de chave do Azure](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu as noções básicas da automatização do Azure e como ele pode ser usado para gerir o Azure Key Vault, siga estas ligações para saber mais sobre a automatização do Azure.

* Consulte a automatização do Azure [Tutorial de introdução](../automation/automation-first-runbook-graphical.md).
* Consulte a [scripts do PowerShell do Azure Key Vault](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).

