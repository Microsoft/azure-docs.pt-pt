---
title: Investigue alertas com Azure Sentinel. Microsoft Docs
description: Aprenda a usar modelos de deteção de ameaças Azure incorporados fora da caixa que o notificam quando algo suspeito acontece.
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
ms.openlocfilehash: 2d1ef885ea36e2eee7fd30268fd7b1f4609a70bc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85560034"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Tutorial: Detetar ameaças fora da caixa


> [!IMPORTANT]
> A deteção de ameaças fora da caixa está atualmente em visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Depois de [ligar as suas fontes de dados](quickstart-onboard.md)   ao Azure Sentinel, pretende ser notificado quando algo suspeito acontece. Para lhe permitir isso, o Azure Sentinel fornece-lhe modelos incorporados fora da caixa. Estes modelos foram desenhados pela equipa de peritos e analistas de segurança da Microsoft com base em ameaças conhecidas, vetores de ataque comuns e cadeias suspeitas de escalada de atividade. Depois de ativar estes modelos, eles procurarão automaticamente qualquer atividade que pareça suspeita em todo o seu ambiente. Muitos dos modelos podem ser personalizados para pesquisar ou filtrar atividades, de acordo com as suas necessidades. Os alertas gerados por estes modelos criarão incidentes que pode atribuir e investigar no seu ambiente.

Este tutorial ajuda-o a detetar ameaças com a Azure Sentinel:

> [!div class="checklist"]
> * Utilizar deteções fora da caixa
> * Automatizar respostas de ameaças

## <a name="about-out-of-the-box-detections"></a>Acerca das deteções completas

Para ver todas as deteções fora da caixa, vá ao **Analytics** e, em seguida, **aos modelos de Regras**. Este separador contém todas as regras incorporadas do Azure Sentinel.

   ![Use deteções incorporadas para encontrar ameaças com Azure Sentinel](media/tutorial-detect-built-in/view-oob-detections.png)

Estão disponíveis os seguintes tipos de modelos:

- **Segurança da Microsoft** - Os modelos de segurança da Microsoft criam automaticamente incidentes do Azure Sentinel a partir dos alertas gerados noutras soluções de segurança da Microsoft, em tempo real. Pode utilizar as regras de segurança da Microsoft como modelo para criar novas regras com lógica semelhante. Para obter mais informações sobre as regras de segurança, consulte [criar automaticamente incidentes a partir de alertas de segurança da Microsoft.](create-incidents-from-alerts.md)
- **Fusion** - Baseado na tecnologia Fusion, a deteção avançada de ataques em vários estágios em Azure Sentinel usa algoritmos de aprendizagem de máquinas escaláveis que podem correlacionar muitos alertas e eventos de baixa fidelidade em vários produtos em incidentes de alta fidelidade e ação. A fusão é ativada por defeito. Como a lógica está escondida, não pode usar isto como modelo para criar mais do que uma regra.
- **Machine learning behavioral analytics** - Estes modelos são baseados em algoritmos de aprendizagem automática da Microsoft proprietários, por isso não pode ver a lógica interna de como funcionam e quando funcionam. Como a lógica está escondida, não pode usar isto como modelo para criar mais do que uma regra.
-   **Agendadas** – As regras analíticas programadas são agendadas consultas escritas por especialistas em segurança da Microsoft. Pode-se ver a lógica da consulta e fazer-lhe alterações. Pode usar as regras programadas como modelo para criar novas regras com lógica semelhante.

## <a name="use-out-of-the-box-detections"></a>Utilizar deteções fora da caixa

1. Para utilizar um modelo incorporado, clique na **regra Criar** para criar uma nova regra ativa baseada nesse modelo. Cada entrada tem uma lista de fontes de dados necessárias que são verificadas automaticamente e isso pode resultar na desativação **da regra Create.**
  
   ![Use deteções incorporadas para encontrar ameaças com Azure Sentinel](media/tutorial-detect-built-in/use-built-in-template.png)
 
1. Isto abre o assistente de criação de regras, com base no modelo selecionado. Todos os detalhes são preenchidos automaticamente, e para **regras agendadas** ou **regras de segurança**da Microsoft, você pode personalizar a lógica para melhor se adequar à sua organização, ou criar regras adicionais com base no modelo incorporado. Depois de seguir os passos no assistente de criação de regras e ter terminado de criar uma regra com base no modelo, a nova regra aparece no separador **regras Ative.**

Para obter mais informações sobre os campos do assistente, consulte [Tutorial: Crie regras analíticas personalizadas para detetar ameaças suspeitas](tutorial-detect-threats-custom.md).



## <a name="next-steps"></a>Próximos passos
Neste tutorial, aprendeu a detetar ameaças usando Azure Sentinel. 

Para aprender a automatizar as suas respostas a ameaças, [Crie respostas automáticas de ameaças em Azure Sentinel](tutorial-respond-threats-playbook.md).

