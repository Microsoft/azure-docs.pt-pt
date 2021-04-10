---
title: Configurar escala para serviços de nuvem Azure (suporte alargado)
description: Como permitir opções de escala para serviços de nuvem Azure (suporte alargado)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: autoscale
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: dab3411319f40a111144a3e1c1f02f1b67d011b0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935656"
---
# <a name="configure-scaling-options-with-azure-cloud-services-extended-support"></a>Configure opções de escala com Azure Cloud Services (suporte alargado) 

As condições podem ser configuradas para permitir que as implementações dos Serviços cloud (suporte alargado) sejam dimensionadas para dentro e para fora. Estas condições podem basear-se na utilização do CPU, na carga do disco e na carga de rede. 

Considere as seguintes informações ao configurar o dimensionamento das suas implementações do Cloud Service:
- A escala tem impacto no uso do núcleo. Casos de papel maior consomem mais núcleos e só pode escalar dentro do limite central da sua subscrição. Para obter mais informações, veja [Subscrição do Azure e limites de serviço, quotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md).
- O dimensionamento baseado no limiar de mensagens de fila é suportado. Para mais informações, consulte [Começar com o armazenamento da Fila Azure.](../storage/queues/storage-dotnet-how-to-use-queues.md)
- Para garantir uma elevada disponibilidade das suas aplicações cloud Service (suporte alargado), certifique-se de implementar com duas ou mais instâncias de função.
- A autoescala personalizada só pode ocorrer quando todas as funções estão num estado **De Ready.**

## <a name="configure-and-manage-scaling"></a>Configurar e gerir a escala

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Selecione a implementação do Serviço de Nuvem (suporte alargado) que pretende configurar a escala. 
3. Selecione a **lâmina de escala.** 

    :::image type="content" source="media/enable-scaling-1.png" alt-text="A imagem mostra a seleção da opção Remote Desktop no portal Azure":::

4. Uma página apresentará uma lista de todas as funções em que o escalonamento pode ser configurado. Selecione o papel que pretende configurar. 
5. Selecione o tipo de escala que pretende configurar
    - **A escala manual** definirá a contagem absoluta de instâncias.
        1. Selecione **a escala manual**.
        2. Insira o número de casos a que pretende escalar para cima ou para baixo.
        3. Selecione **Guardar**.

        :::image type="content" source="media/enable-scaling-2.png" alt-text="Imagem mostra configuração de escala manual no portal Azure":::

        4. A operação de escala começará imediatamente. 
        
    - **A Autoescala personalizada** permitir-lhe-á definir regras que regem o quanto ou o pouco à escala. 
        1. Selecione **autoescala personalizada**
        2. Escolha escalar com base numa contagem métrica ou de exemplo.

        :::image type="content" source="media/enable-scaling-3.png" alt-text="Imagem mostra configuração de autoescalação personalizada no portal Azure":::

        3. Se a escalar com base numa métrica, adicione as regras necessárias para alcançar os resultados de escalonamento desejados.

        :::image type="content" source="media/enable-scaling-4.png" alt-text="Imagem mostra a criação de regras de autoescalação personalizadas no portal Azure":::

        4. Selecione **Guardar**.
        5. As operações de escala começarão assim que uma regra for desencadeada.
        
6. Pode visualizar ou ajustar as regras de escala existentes aplicadas às suas implementações selecionando o separador **Escala.**

    :::image type="content" source="media/enable-scaling-5.png" alt-text="A imagem mostra o ajuste de uma regra de escala existente no portal Azure":::

## <a name="next-steps"></a>Passos seguintes 
- Reveja os [pré-requisitos](deploy-prerequisite.md) de implantação para serviços em nuvem (suporte alargado).
- Reveja [perguntas frequentes](faq.md) para serviços cloud (suporte alargado).
- Implementar um Serviço de Cloud (suporte alargado) utilizando o [portal Azure](deploy-portal.md), [PowerShell,](deploy-powershell.md) [Modelo](deploy-template.md) ou [Estúdio Visual](deploy-visual-studio.md).