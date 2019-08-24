---
title: Dimensionar verticalmente a máquina virtual do Azure com a automação do Azure | Microsoft Docs
description: Como dimensionar verticalmente uma máquina virtual do Linux em resposta a alertas de monitoramento com a automação do Azure
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: gwallace
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
ms.openlocfilehash: f4f49030d479e85b749db28c59fd2e2462ff405f
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013590"
---
# <a name="vertically-scale-azure-linux-virtual-machine-with-azure-automation"></a>Dimensionar verticalmente a máquina virtual Linux do Azure com a automação do Azure
O dimensionamento vertical é o processo de aumentar ou diminuir os recursos de um computador em resposta à carga de trabalho. No Azure, isso pode ser feito alterando o tamanho da máquina virtual. Isso pode ajudar nos seguintes cenários

* Se a máquina virtual não estiver sendo usada com frequência, você poderá redimensioná-la para um tamanho menor para reduzir os custos mensais
* Se a máquina virtual estiver vendo uma carga de pico, ela poderá ser redimensionada para um tamanho maior para aumentar sua capacidade

O contorno das etapas para fazer isso é o seguinte

1. Configurar a automação do Azure para acessar suas máquinas virtuais
2. Importar os runbooks de escala vertical da automação do Azure para sua assinatura
3. Adicionar um webhook ao seu runbook
4. Adicionar um alerta à sua máquina virtual


## <a name="scale-limitations"></a>Limitações de escala

Devido ao tamanho da primeira máquina virtual, os tamanhos que podem ser dimensionados podem ser limitados devido à disponibilidade dos outros tamanhos no cluster em que a máquina virtual atual está implantada. Nos runbooks de automação publicados usados neste artigo, cuidamos desse caso e só dimensionamos nos pares de tamanho de VM abaixo. Isso significa que uma máquina virtual Standard_D1v2 não será expandida repentinamente para Standard_G5 ou reduzida para Basic_A0. Também não há suporte para expansão/redução de tamanhos de máquina virtual restritos. 

Você pode optar por dimensionar entre os seguintes pares de tamanhos:

* [Série a](#a-series)
* [Série B](#b-series)
* [Série D](#d-series)
* [Série E](#e-series)
* [Série F](#f-series)
* [Série G](#g-series)
* [Série H](#h-series)
* [Série L](#l-series)
* [Série M](#m-series)
* [Série N](#n-series)

### <a name="a-series"></a>Série A

| Tamanho inicial | Dimensionar tamanho | 
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

| Tamanho inicial | Dimensionar tamanho | 
| --- | --- |
| Standard_B1s | Standard_B2s |
| Standard_B1ms | Standard_B2ms |
| Standard_B2ms | Standard_B4ms |
| Standard_B4ms | Standard_B8ms |

### <a name="d-series"></a>Série D

| Tamanho inicial | Dimensionar tamanho | 
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

| Tamanho inicial | Dimensionar tamanho | 
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

| Tamanho inicial | Dimensionar tamanho | 
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

| Tamanho inicial | Dimensionar tamanho | 
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

| Tamanho inicial | Dimensionar tamanho | 
| --- | --- |
| Standard_H8 | Standard_H16 |
| Standard_H8m | Standard_H16m |

### <a name="l-series"></a>Série L

| Tamanho inicial | Dimensionar tamanho | 
| --- | --- |
| Standard_L4s | Standard_L8s |
| Standard_L8s | Standard_L16s |
| Standard_L16s | Standard_L32s |
| Standard_L8s_v2 | Standard_L16s_v2 |
| Standard_L16s_v2 | Standard_L32s_v2 |
| Standard_L32s_v2 | Standard_L64s_v2 |
| Standard_L64s_v2 | Standard_L80s_v2 |

### <a name="m-series"></a>Série M

| Tamanho inicial | Dimensionar tamanho | 
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

| Tamanho inicial | Dimensionar tamanho | 
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
| Standard_NV12s_v3 |Standard_NV48s_v3 |


## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Configurar a automação do Azure para acessar suas máquinas virtuais
A primeira coisa que você precisa fazer é criar uma conta de automação do Azure que hospedará os runbooks usados para dimensionar as instâncias do conjunto de dimensionamento de VM. Recentemente, o serviço de automação introduziu o recurso "conta Executar como", que torna a configuração da entidade de serviço para executar automaticamente os runbooks em nome do usuário. Você pode ler mais sobre isso no artigo abaixo:

* [Autenticar Runbooks com a conta Run As do Azure](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importar os runbooks de escala vertical da automação do Azure para sua assinatura
Os runbooks que são necessários para dimensionar verticalmente sua máquina virtual já estão publicados na Galeria de runbook da automação do Azure. Você precisará importá-los para sua assinatura. Você pode aprender a importar runbooks lendo o artigo a seguir.

* [Runbook and module galleries for Azure Automation](../../automation/automation-runbook-gallery.md) (Galerias de runbooks e módulos para a Automatização do Azure)

Os runbooks que precisam ser importados são mostrados na imagem abaixo

![Importar runbooks](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Adicionar um webhook ao seu runbook
Depois de importar os runbooks, você precisará adicionar um webhook ao runbook para que ele possa ser disparado por um alerta de uma máquina virtual. Os detalhes da criação de um webhook para seu runbook podem ser lidos aqui

* [WebHooks de automação do Azure](../../automation/automation-webhooks.md)

Certifique-se de copiar o webhook antes de fechar a caixa de diálogo do webhook, pois você precisará dela na próxima seção.

## <a name="add-an-alert-to-your-virtual-machine"></a>Adicionar um alerta à sua máquina virtual
1. Selecionar configurações de máquina virtual
2. Selecione "regras de alerta"
3. Selecione "adicionar alerta"
4. Selecione uma métrica na qual o alerta será acionado
5. Selecione uma condição que, quando atendida, fará com que o alerta seja acionado
6. Selecione um limite para a condição na etapa 5. para ser atendido
7. Selecione um período no qual o serviço de monitoramento verificará a condição e o limite nas etapas 5 & 6
8. Cole o webhook que você copiou da seção anterior.

![Adicionar alerta à máquina virtual 1](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Adicionar alerta à máquina virtual 2](./media/vertical-scaling-automation/add-alert-webhook-2.png)

