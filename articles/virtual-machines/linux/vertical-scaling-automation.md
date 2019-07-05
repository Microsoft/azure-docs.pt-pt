---
title: Aumentar verticalmente a máquina virtual do Azure com a automatização do Azure | Documentos da Microsoft
description: Como aumentar verticalmente a Máquina Virtual do Linux em resposta a alertas com a automatização do Azure de monitorização
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: dcee199e-fa25-44d5-9b25-df564cee9b45
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e7190f13f9d41afffcbbc1104f533b0d0586a0d6
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486040"
---
# <a name="vertically-scale-azure-linux-virtual-machine-with-azure-automation"></a>Aumentar verticalmente a máquina de virtual de Linux do Azure com a automatização do Azure
Dimensionamento vertical é o processo de aumentar ou diminuir os recursos de uma máquina em resposta à carga de trabalho. No Azure pode fazê-lo ao alterar o tamanho da Máquina Virtual. Isto pode ajudar a nos seguintes cenários

* Se a Máquina Virtual não está a ser utilizado com freqüência, pode redimensioná-la para baixo para um tamanho mais pequeno para reduzir os custos mensais
* Se a Máquina Virtual está a ver um pico de carga, podem ser redimensionado para um tamanho maior para aumentar sua capacidade

A estrutura de tópicos para obter os passos realizar isso é como abaixo

1. Configurar a automatização do Azure para aceder às suas máquinas virtuais
2. Importar os runbooks do dimensionamento Vertical de automatização do Azure para a sua subscrição
3. Adicionar um webhook ao runbook
4. Adicionar um alerta à máquina Virtual

## <a name="scale-limitations"></a>Limitações de dimensionamento

Devido ao tamanho da primeira Máquina Virtual, os tamanhos podem ser dimensionado, poderá ser limitado devido à disponibilidade, os outros tamanhos no cluster de que Máquina Virtual atual for implementado num. Os runbooks de automatização publicados usados neste artigo vamos lidar com esse caso e só pode reduzir dentro do abaixo pares de tamanho VM. Isso significa que uma máquina de Virtual Standard_D1v2 será não, de repente, aumentar verticalmente até Standard_G5 ou reduzidos verticalmente para Basic_A0. Também restrita máquina de Virtual tamanhos aumentar/reduzir verticalmente não é suportada. 

Pode optar por dimensionar entre os seguintes pares de tamanhos:

* [Série A](#a-series)
* [Série B](#b-series)
* [Série D](#d-series)
* [Série E](#e-series)
* [Série F](#f-series)
* [G-Series](#g-series)
* [Série H](#h-series)
* [L-Series](#l-series)
* [Série M](#m-series)
* [Série N](#n-series)

### <a name="a-series"></a>Série A

| Tamanho inicial | Aumentar verticalmente o tamanho | 
| --- | --- |
| Basic_A0 | Basic_A1 |
| Basic_A1 | Basic_A2 |
| Basic_A2 | Basic_A3 |
| Basic_A3 | Basic_A4 |
| Standard_A0 | Standard_A1 |
| Standard_A1 | Standard_A2 |
| Standard_A2 | Standard_A3 |
| Standard_A3 | Standard_A4 |
| Standard_A5 | Standard_A6 |
| Standard_A6 | Standard_A7 |
| Standard_A8 | Standard_A9 |
| Standard_A10 | Standard_A11 |
| Standard_A1_v2 | Standard_A2_v2 |
| Standard_A2_v2 | Standard_A4_v2 |
| Standard_A4_v2 | Standard_A8_v2 |
| Standard_A2m_v2 | Standard_A4m_v2 |
| Standard_A4m_v2 | Standard_A8m_v2 |

### <a name="b-series"></a>Série B

| Tamanho inicial | Aumentar verticalmente o tamanho | 
| --- | --- |
| Standard_B1s | Standard_B2s |
| Standard_B1ms | Standard_B2ms |
| Standard_B2ms | Standard_B4ms |
| Standard_B4ms | Standard_B8ms |

### <a name="d-series"></a>Série D

| Tamanho inicial | Aumentar verticalmente o tamanho | 
| --- | --- |
| Standard_D1 | Standard_D2 |
| Standard_D2 | Standard_D3 |
| Standard_D3 | Standard_D4 |
| Standard_D11 | Standard_D12 |
| Standard_D12 | Standard_D13 |
| Standard_D13 | Standard_D14 |
| Standard_DS1 | Standard_DS2 |
| Standard_DS2 | Standard_DS3 |
| Standard_DS3 | Standard_DS4 |
| Standard_DS11 | Standard_DS12 |
| Standard_DS12 | Standard_DS13 |
| Standard_DS13 | Standard_DS14 |
| Standard_D1_v2 | Standard_D2_v2 |
| Standard_D2_v2 | Standard_D3_v2 |
| Standard_D3_v2 | Standard_D4_v2 |
| Standard_D4_v2 | Standard_D5_v2 |
| Standard_D11_v2 | Standard_D12_v2 |
| Standard_D12_v2 | Standard_D13_v2 |
| Standard_D13_v2 | Standard_D14_v2 |
| Standard_DS1_v2 | Standard_DS2_v2 |
| Standard_DS2_v2 | Standard_DS3_v2 |
| Standard_DS3_v2 | Standard_DS4_v2 |
| Standard_DS4_v2 | Standard_DS5_v2 |
| Standard_DS11_v2 | Standard_DS12_v2 |
| Standard_DS12_v2 | Standard_DS13_v2 |
| Standard_DS13_v2 | Standard_DS14_v2 |
| Standard_D2_v3 | Standard_D4_v3 |
| Standard_D4_v3 | Standard_D8_v3 |
| Standard_D8_v3 | Standard_D16_v3 |
| Standard_D16_v3 | Standard_D32_v3 |
| Standard_D32_v3 | Standard_D64_v3 |
| Standard_D2s_v3 | Standard_D4s_v3 |
| Standard_D4s_v3 | Standard_D8s_v3 |
| Standard_D8s_v3 | Standard_D16s_v3 |
| Standard_D16s_v3 | Standard_D32s_v3 |
| Standard_D32s_v3 | Standard_D64s_v3 |
| Standard_DC2s | Standard_DC4s |

### <a name="e-series"></a>Série E

| Tamanho inicial | Aumentar verticalmente o tamanho | 
| --- | --- |
| Standard_E2_v3 | Standard_E4_v3 |
| Standard_E4_v3 | Standard_E8_v3 |
| Standard_E8_v3 | Standard_E16_v3 |
| Standard_E16_v3 | Standard_E20_v3 |
| Standard_E20_v3 | Standard_E32_v3 |
| Standard_E32_v3 | Standard_E64_v3 |
| Standard_E2s_v3 | Standard_E4s_v3 |
| Standard_E4s_v3 | Standard_E8s_v3 |
| Standard_E8s_v3 | Standard_E16s_v3 |
| Standard_E16s_v3 | Standard_E20s_v3 |
| Standard_E20s_v3 | Standard_E32s_v3 |
| Standard_E32s_v3 | Standard_E64s_v3 |

### <a name="f-series"></a>Série F

| Tamanho inicial | Aumentar verticalmente o tamanho | 
| --- | --- |
| Standard_F1 | Standard_F2 |
| Standard_F2 | Standard_F4 |
| Standard_F4 | Standard_F8 |
| Standard_F8 | Standard_F16 |
| Standard_F1s | Standard_F2s |
| Standard_F2s | Standard_F4s |
| Standard_F4s | Standard_F8s |
| Standard_F8s | Standard_F16s |
| Standard_F2s_v2 | Standard_F4s_v2 |
| Standard_F4s_v2 | Standard_F8s_v2 |
| Standard_F8s_v2 | Standard_F16s_v2 |
| Standard_F16s_v2 | Standard_F32s_v2 |
| Standard_F32s_v2 | Standard_F64s_v2 |
| Standard_F64s_v2 | Standard_F7s_v2 |

### <a name="g-series"></a>Série G

| Tamanho inicial | Aumentar verticalmente o tamanho | 
| --- | --- |
| Standard_G1 | Standard_G2 |
| Standard_G2 | Standard_G3 |
| Standard_G3 | Standard_G4 |
| Standard_G4 | Standard_G5 |
| Standard_GS1 | Standard_GS2 |
| Standard_GS2 | Standard_GS3 |
| Standard_GS3 | Standard_GS4 |
| Standard_GS4 | Standard_GS5 |

### <a name="h-series"></a>Série H

| Tamanho inicial | Aumentar verticalmente o tamanho | 
| --- | --- |
| Standard_H8 | Standard_H16 |
| Standard_H8m | Standard_H16m |

### <a name="l-series"></a>Série L

| Tamanho inicial | Aumentar verticalmente o tamanho | 
| --- | --- |
| Standard_L4s | Standard_L8s |
| Standard_L8s | Standard_L16s |
| Standard_L16s | Standard_L32s |
| Standard_L8s_v2 | Standard_L16s_v2 |
| Standard_L16s_v2 | Standard_L32s_v2 |
| Standard_L32s_v2 | Standard_L64s_v2 |
| Standard_L64s_v2 | Standard_L80s_v2 |

### <a name="m-series"></a>Série M

| Tamanho inicial | Aumentar verticalmente o tamanho | 
| --- | --- |
| Standard_M8ms | Standard_M16ms |
| Standard_M16ms | Standard_M32ms |
| Standard_M32ms | Standard_M64ms |
| Standard_M64ms | Standard_M128ms |
| Standard_M32ls | Standard_M64ls |
| Standard_M64s | Standard_M128s |
| Standard_M64 | Standard_M128 |
| Standard_M64m | Standard_M128m |

### <a name="n-series"></a>Série N

| Tamanho inicial | Aumentar verticalmente o tamanho | 
| --- | --- |
| Standard_NC6 | Standard_NC12 |
| Standard_NC12 | Standard_NC24 |
| Standard_NC6s_v2 | Standard_NC12s_v2 |
| Standard_NC12s_v2 | Standard_NC24s_v2 |
| Standard_NC6s_v3 | Standard_NC12s_v3 |
| Standard_NC12s_v3 | Standard_NC24s_v3 |
| Standard_ND6 | Standard_ND12 |
| Standard_ND12 | Standard_ND24 |
| Standard_NV6 | Standard_NV12 |
| Standard_NV12 | Standard_NV24 |
| Standard_NV6s_v2 | Standard_NV12s_v2 |
| Standard_NV12s_v2 | Standard_NV24s_v2 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Configurar a automatização do Azure para aceder às suas máquinas virtuais
A primeira coisa que precisa fazer é criar uma conta de automatização do Azure que irá alojar os runbooks utilizados para dimensionar as instâncias de conjunto de dimensionamento de VM. Recentemente, o serviço de automatização introduziu a funcionalidade de "Conta Run As" que torna a definição de cópia de segurança Principal de serviço para executar automaticamente os runbooks em nome do usuário muito fácil. Pode ler mais sobre isso no artigo abaixo:

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

