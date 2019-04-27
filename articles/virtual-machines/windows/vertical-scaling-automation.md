---
title: Utilizar a automatização do Azure para aumentar verticalmente as máquinas virtuais do Windows | Documentos da Microsoft
description: Aumentar verticalmente uma Máquina Virtual do Windows em resposta a alertas com a automatização do Azure de monitorização
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4f964713-fb67-4bcc-8246-3431452ddf7d
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 44243f97b6c431578185fcdeb1ddcabc548d927f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60581012"
---
# <a name="vertically-scale-windows-vms-with-azure-automation"></a>Aumentar verticalmente as VMs do Windows com a automatização do Azure

Dimensionamento vertical é o processo de aumentar ou diminuir os recursos de uma máquina em resposta à carga de trabalho. No Azure pode fazê-lo ao alterar o tamanho da Máquina Virtual. Isto pode ajudar a nos seguintes cenários

* Se a Máquina Virtual não está a ser utilizado com freqüência, pode redimensioná-la para baixo para um tamanho mais pequeno para reduzir os custos mensais
* Se a Máquina Virtual está a ver um pico de carga, podem ser redimensionado para um tamanho maior para aumentar sua capacidade

A estrutura de tópicos para obter os passos realizar isso é como abaixo

1. Configurar a automatização do Azure para aceder às suas máquinas virtuais
2. Importar os runbooks do dimensionamento Vertical de automatização do Azure para a sua subscrição
3. Adicionar um webhook ao runbook
4. Adicionar um alerta à máquina Virtual

> [!NOTE]
> Devido ao tamanho da primeira Máquina Virtual, os tamanhos podem ser dimensionado, poderá ser limitado devido à disponibilidade, os outros tamanhos no cluster de que Máquina Virtual atual for implementado num. Os runbooks de automatização publicados usados neste artigo vamos lidar com esse caso e só pode reduzir dentro do abaixo pares de tamanho VM. Isso significa que uma máquina de Virtual Standard_D1v2 será não, de repente, aumentar verticalmente até Standard_G5 ou reduzidos verticalmente para Basic_A0. Também restrita máquina de Virtual tamanhos aumentar/reduzir verticalmente não é suportada. Pode optar por dimensionar entre os seguintes pares de tamanhos:
> 
> | Dimensionar o par de tamanhos de VM |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2v3 |Standard_E64v3 |
> | Standard_E2sv3 |Standard_E64sv3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> 
> 

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Configurar a automatização do Azure para aceder às suas máquinas virtuais
A primeira coisa que precisa fazer é criar uma conta de automatização do Azure que irá alojar os runbooks utilizados para aumentar uma Máquina Virtual. Recentemente, o serviço de automatização introduziu a funcionalidade de "Conta Run As" que torna a definição de cópia de segurança Principal de serviço para executar automaticamente os runbooks em nome do usuário muito fácil. Pode ler mais sobre isso no artigo abaixo:

* [Autenticar Runbooks com a conta Run As do Azure](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importar os runbooks do dimensionamento Vertical de automatização do Azure para a sua subscrição
Os runbooks que são necessários para aumentar verticalmente a sua máquina Virtual já estão a ser publicados na Galeria de Runbook da automatização do Azure. Terá de importá-los na sua subscrição. Pode aprender a importar runbooks ao ler o artigo seguinte.

* [Runbook and module galleries for Azure Automation](../../automation/automation-runbook-gallery.md) (Galerias de runbooks e módulos para a Automatização do Azure)

Os runbooks que precisavam ser importados são apresentados na imagem abaixo

![Importar runbooks](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Adicionar um webhook ao runbook
Depois de ter importado os runbooks que precisará adicionar um webhook para o runbook para que ele pode ser acionado por um alerta a partir de uma Máquina Virtual. Os detalhes da criação de um webhook para o Runbook podem ser lido aqui

* [Webhooks de automatização do Azure](../../automation/automation-webhooks.md)

Certifique-se de que copiar o webhook antes de fechar a caixa de diálogo do webhook, pois irá precisar na próxima seção.

## <a name="add-an-alert-to-your-virtual-machine"></a>Adicionar um alerta à máquina Virtual
1. Selecionar definições da Máquina Virtual
2. Selecione "Regras de alerta"
3. Selecione "Adicionar alerta de"
4. Selecione uma métrica para disparar o alerta no
5. Selecione uma condição, que, quando concluído irá fazer com que o alerta seja acionado
6. Selecione um limite para a condição no passo 5. para ser concluído
7. Selecione um período durante o qual irá verificar o serviço de monitoramento para a condição e o limiar em passos 5 e 6
8. Cole o webhook que copiou na secção anterior.

![Adicionar alerta da Máquina Virtual 1](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Adicionar alerta para a Máquina Virtual 2](./media/vertical-scaling-automation/add-alert-webhook-2.png)

