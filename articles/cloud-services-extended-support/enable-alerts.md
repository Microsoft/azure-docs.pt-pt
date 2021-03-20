---
title: Ativar a monitorização em serviços em nuvem (suporte alargado) utilizando o portal Azure
description: Permitir a monitorização de instâncias de Serviços cloud (suporte alargado) utilizando o portal Azure
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4591253e1a305632b7f41afe692f297d71366382
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98744854"
---
# <a name="enable-monitoring-for-cloud-services-extended-support-using-the-azure-portal"></a>Permitir a monitorização dos Serviços cloud (suporte alargado) utilizando o portal Azure

Este artigo explica como permitir alertas sobre as implementações existentes do Cloud Service (suporte alargado). 

## <a name="add-monitoring-rules"></a>Adicionar regras de monitorização
1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Selecione a implementação do Serviço de Nuvem (suporte alargado) para ativar alertas. 
3. Selecione a lâmina **alertas.** 

    :::image type="content" source="media/enable-alerts-1.png" alt-text="A imagem mostra a seleção do separador Alertas no portal Azure.":::

4. Selecione o ícone **New Alert.**
     :::image type="content" source="media/enable-alerts-2.png" alt-text="A imagem mostra a seleção da nova opção de alerta.":::

5. Insira as condições desejadas e as ações necessárias com base nas métricas que está interessado em rastrear. Pode definir as regras com base em métricas individuais ou no registo de atividades. 

     :::image type="content" source="media/enable-alerts-3.png" alt-text="A imagem mostra onde adicionar condições aos alertas.":::

     :::image type="content" source="media/enable-alerts-4.png" alt-text="A imagem mostra a lógica de sinal configurante.":::

     :::image type="content" source="media/enable-alerts-5.png" alt-text="A imagem mostra a lógica do grupo de ação configurada.":::

6. Quando terminar de configurar alertas, guarde as alterações e com base nas métricas configuradas começará a ver a lâmina **de Alerta** a povoar ao longo do tempo.

## <a name="next-steps"></a>Passos seguintes 
- Reveja [perguntas frequentes](faq.md) para serviços cloud (suporte alargado).
- Implementar um Serviço de Cloud (suporte alargado) utilizando o [portal Azure](deploy-portal.md), [PowerShell,](deploy-powershell.md) [Modelo](deploy-template.md) ou [Estúdio Visual](deploy-visual-studio.md).