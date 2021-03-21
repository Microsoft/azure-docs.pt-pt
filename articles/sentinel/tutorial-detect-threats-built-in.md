---
title: Detetar ameaças com regras de análise incorporadas em Azure Sentinel | Microsoft Docs
description: Aprenda a usar regras de deteção de ameaças fora da caixa, com base em modelos incorporados, que o notificam quando algo suspeito acontece.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2020
ms.author: yelevin
ms.openlocfilehash: b8a304d523052f1fe839f94608806df51d35fdf9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589927"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Tutorial: Detetar ameaças com modelos prontos a utilizar

Uma vez que tenha [ligado as suas fontes de dados](quickstart-onboard.md) ao Azure Sentinel, deverá ser notificado quando ocorrer algo suspeito. É por isso que o Azure Sentinel fornece modelos fora da caixa, incorporados para ajudá-lo a criar regras de deteção de ameaças. Estes modelos foram desenhados pela equipa de peritos e analistas de segurança da Microsoft com base em ameaças conhecidas, vetores de ataque comuns e cadeias suspeitas de escalada de atividade. As regras criadas a partir destes modelos irão automaticamente pesquisar em todo o seu ambiente qualquer atividade que pareça suspeita. Muitos dos modelos podem ser personalizados para procurar atividades, ou filtrar, de acordo com as suas necessidades. Os alertas gerados por estas regras vão criar incidentes que pode atribuir e investigar no seu ambiente.

Este tutorial ajuda-o a detetar ameaças com a Azure Sentinel:

> [!div class="checklist"]
> * Utilizar deteções de ameaças fora da caixa
> * Automatizar respostas de ameaças

## <a name="about-out-of-the-box-detections"></a>Acerca das deteções completas

Para ver todas as deteções fora da caixa, vá ao **Analytics** e, em seguida, **aos modelos de Regras**. Este separador contém todas as regras incorporadas do Azure Sentinel.

   :::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="Use deteções incorporadas para encontrar ameaças com Azure Sentinel":::

Estão disponíveis os seguintes tipos de modelos:

- **Segurança da Microsoft**
   
   Os modelos de segurança da Microsoft criam automaticamente incidentes do Azure Sentinel a partir dos alertas gerados noutras soluções de segurança da Microsoft, em tempo real. Pode utilizar as regras de segurança da Microsoft como modelo para criar novas regras com lógica semelhante. Para obter mais informações sobre as regras de segurança, consulte [criar automaticamente incidentes a partir de alertas de segurança da Microsoft.](create-incidents-from-alerts.md)

- **Fusão** 

    Com base na tecnologia Fusion, a deteção avançada de ataques em vários estágios em Azure Sentinel usa algoritmos de aprendizagem de máquinas escaláveis que podem correlacionar muitos alertas e eventos de baixa fidelidade em vários produtos em incidentes de alta fidelidade e ação. A fusão é ativada por defeito. Como a lógica é oculta e, portanto, não personalizável, só pode criar uma regra com este modelo.

    > [!IMPORTANT]
    > Algumas das deteções no modelo de regra de fusão estão atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.
    >
    > Para ver quais as deteções em pré-visualização, consulte [a deteção avançada de ataques em vários estágios em Azure Sentinel](fusion.md).

- **Análise comportamental de aprendizagem automática**

    Estes modelos são baseados em algoritmos de aprendizagem automática da Microsoft proprietários, por isso não é possível ver a lógica interna de como funcionam e quando funcionam. Como a lógica é oculta e, portanto, não personalizável, só pode criar uma regra com cada modelo deste tipo.

    > [!IMPORTANT]
    > Os modelos de regras de análise comportamental de aprendizagem automática estão atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

- **Agendado**

    As regras de análise programadas baseiam-se em consultas incorporadas escritas por especialistas em segurança da Microsoft. Pode-se ver a lógica da consulta e fazer-lhe alterações. Pode utilizar o modelo de regras programados e personalizar a lógica de consulta e as definições de agendamento para criar novas regras.

## <a name="use-out-of-the-box-detections"></a>Utilizar deteções fora da caixa

1. Para utilizar um modelo incorporado, clique no nome do modelo e, em seguida, clique no botão **'Criar regra'** no painel de detalhes para criar uma nova regra ativa baseada nesse modelo. Cada modelo tem uma lista de fontes de dados necessárias. Quando abre o modelo, as fontes de dados são automaticamente verificadas para obter disponibilidade. Se houver um problema de disponibilidade, o botão **de regra Criar** pode ser desativado, ou poderá ver um aviso nesse sentido.
  
    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="Painel de pré-visualização de regras de deteção":::
 
1. Clicar no botão **'Criar'** abre o assistente de criação de regras com base no modelo selecionado. Todos os detalhes são preenchidos automaticamente, e com os modelos de segurança **Agendados** ou **Microsoft,** pode personalizar a lógica e outras definições de regras para melhor atender às suas necessidades específicas. Pode repetir este processo para criar regras adicionais baseadas no modelo incorporado. Depois de seguir os passos no assistente de criação de regras até ao fim, terá terminado de criar uma regra baseada no modelo. As novas regras aparecerão no separador **regras Ative.**

    Para obter mais detalhes sobre como personalizar as suas regras no assistente de criação de [regras, consulte Tutorial: Crie regras de análise personalizadas para detetar ameaças.](tutorial-detect-threats-custom.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a detetar ameaças usando Azure Sentinel. 

Para aprender a automatizar as suas respostas a ameaças, [Crie respostas automáticas de ameaças em Azure Sentinel](tutorial-respond-threats-playbook.md).
