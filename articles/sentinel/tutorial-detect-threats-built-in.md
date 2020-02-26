---
title: Investigar alertas com O Sentinela Microsoft Docs
description: Use este tutorial para aprender a investigar alertas com O Sentinela.
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
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: df855aa768fdeb279482b8407259be1a644322d9
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585208"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Tutorial: Detetar ameaças fora da caixa


> [!IMPORTANT]
> A deteção de ameaças fora da caixa está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Depois de [ligar as suas fontes](quickstart-onboard.md) de dados ao Azure Sentinel, pretende ser notificado quando algo suspeito acontecer. Para permitir-lhe fazê-lo, o Azure Sentinel fornece-lhe modelos embutidos fora da caixa. Estes modelos foram projetados pela equipa de especialistas e analistas de segurança da Microsoft com base em ameaças conhecidas, vetores comuns de ataque e cadeias de escalada de atividades suspeitas. Depois de ativar estes modelos, eles vão automaticamente procurar qualquer atividade que pareça suspeita em todo o seu ambiente. Muitos dos modelos podem ser personalizados para procurar, ou filtrar, atividades, de acordo com as suas necessidades. Os alertas gerados por estes modelos criarão incidentes que pode atribuir e investigar no seu ambiente.

Este tutorial ajuda-o a detetar ameaças com o Azure Sentinel:

> [!div class="checklist"]
> * Utilizar deteções fora da caixa
> * Automatizar respostas de ameaças

## <a name="about-out-of-the-box-detections"></a>Sobre deteções fora da caixa

Para ver todas as deteções fora da caixa, vá ao **Analytics** e, em seguida, **reine os modelos**. Este separador contém todas as regras incorporadas do Azure Sentinel.

   ![Utilize deteções incorporadas para encontrar ameaças com o Azure Sentinel](media/tutorial-detect-built-in/view-oob-detections.png)

Os seguintes tipos de modelos estão disponíveis:

- **Segurança** da Microsoft - Os modelos de segurança da Microsoft criam automaticamente incidentes do Azure Sentinel a partir dos alertas gerados noutras soluções de segurança da Microsoft, em tempo real. Pode usar as regras de segurança da Microsoft como modelo para criar novas regras com lógica semelhante. Para obter mais informações sobre as regras de segurança, consulte [automaticamente a criação de incidentes a partir de alertas de segurança da Microsoft](create-incidents-from-alerts.md).
- **Fusion** - Baseado na tecnologia Fusion, a deteção avançada de ataques multiestágios em Azure Sentinel utiliza algoritmos de aprendizagem automática escaláveis que podem correlacionar muitos alertas e eventos de baixa fidelidade em vários produtos em incidentes de alta fidelidade e ação. A fusão é ativada por defeito. Como a lógica está escondida, não pode usar isto como modelo para criar mais do que uma regra.
- Análise comportamental de **aprendizagem automática** - Estes modelos são baseados em algoritmos de aprendizagem automática da Microsoft, por isso não se consegue ver a lógica interna de como funcionam e quando funcionam. Como a lógica está escondida, não pode usar isto como modelo para criar mais do que uma regra.
-   **Agendados** – As regras analíticas programadas são consultas agendadas escritas por especialistas em segurança da Microsoft. Pode-se ver a lógica da consulta e fazer alterações. Você pode usar as regras programadas como um modelo para criar novas regras com lógica semelhante.

## <a name="use-out-of-the-box-detections"></a>Utilizar deteções fora da caixa

1. Para utilizar um modelo incorporado, clique na **regra Criar** para criar uma nova regra ativa baseada nesse modelo. Cada entrada tem uma lista de fontes de dados necessárias que são verificadas automaticamente e isso pode resultar na desativação da **regra Create.**
  
   ![Utilize deteções incorporadas para encontrar ameaças com o Azure Sentinel](media/tutorial-detect-built-in/use-built-in-template.png)
 
1. Isto abre o assistente de criação de regras, com base no modelo selecionado. Todos os detalhes são preenchidos automaticamente, e para **regras agendadas** ou regras de **segurança da Microsoft,** você pode personalizar a lógica para melhor se adequar à sua organização, ou criar regras adicionais com base no modelo incorporado. Depois de seguir os passos no assistente de criação de regras e terminar de criar uma regra baseada no modelo, a nova regra aparece no separador **de regras Ativas.**

Para obter mais informações sobre os campos do assistente, consulte [Tutorial: Crie regras analíticas personalizadas para detetar ameaças suspeitas](tutorial-detect-threats-custom.md).



## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a detetar ameaças usando o Azure Sentinel. 

Para aprender a automatizar as suas respostas a ameaças, [instale respostas de ameaça automatizadas no Azure Sentinel](tutorial-respond-threats-playbook.md).

