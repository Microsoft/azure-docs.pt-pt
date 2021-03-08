---
title: Detalhes personalizados da superfície em Azure Sentinel alerta | Microsoft Docs
description: Extrato e detalhes do evento personalizado de superfície em alertas nas regras de análise do Azure Sentinel, para obter informações melhores e mais completas sobre incidentes
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 45f0ef5366d97c275c40d4d436020dbaf3501d42
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456259"
---
# <a name="surface-custom-event-details-in-alerts-in-azure-sentinel"></a>Detalhes do evento personalizado de superfície em alertas em Azure Sentinel 

> [!IMPORTANT]
>
> - A funcionalidade de detalhes personalizados está em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

## <a name="introduction"></a>Introdução

[As regras de análise de consultas programadas](tutorial-detect-threats-custom.md) analisam **eventos** de fontes de dados ligadas ao Azure Sentinel, e produzem **alertas** quando o conteúdo destes eventos é significativo do ponto de vista de segurança. Estes alertas são analisados, agrupados e filtrados pelos vários motores de Azure Sentinel e destilados em **incidentes** que merecem a atenção de um analista do SOC. No entanto, quando o analista vê o incidente, apenas as propriedades dos próprios alertas do componente são imediatamente visíveis. Chegar ao conteúdo real - a informação contida nos eventos - requer alguma escavação.

Utilizando os **detalhes personalizados** no assistente de regras de **análise,** pode apresentar dados de eventos nos alertas que são construídos a partir desses eventos, fazendo com que os dados do evento sejam parte das propriedades de alerta. Com efeito, isto dá-lhe uma visibilidade imediata do conteúdo do evento nos seus incidentes, permitindo-lhe triagem, investigação, tirar conclusões e responder com muito maior rapidez e eficiência.

O procedimento detalhado abaixo faz parte do assistente de criação de regras de análise. É tratado aqui de forma independente para abordar o cenário de adicionar ou alterar detalhes personalizados numa regra de análise existente.

## <a name="how-to-surface-custom-event-details"></a>Como superfície detalhes do evento personalizado

1. A partir do menu de navegação Azure Sentinel, **selecione Analytics**.

1. Selecione uma regra de consulta agendada e clique em **Editar.** Ou criar uma nova regra clicando em **Criar &#10132; regra** de consulta agendada no topo do ecrã.

1. Clique no separador **lógica de regra definida.**

1. Na secção **de melhoramentos de alerta,** selecione **Detalhes personalizados**.

    :::image type="content" source="media/surface-custom-details-in-alerts/alert-enhancement.png" alt-text="Localizar e selecionar detalhes personalizados":::

1. Na secção de **detalhes personalizados** agora expandidos, adicione pares de valor-chave correspondentes aos detalhes que pretende emergir:

    1. No campo **Chave,** insira um nome à sua escolha que aparecerá como o nome de campo em alertas.

    1. No campo **Valor,** escolha o parâmetro de evento que deseja emergir nos alertas da lista de drop-down. Esta lista será povoada por valores correspondentes aos campos nas tabelas que são objeto da consulta de regras.
    
        :::image type="content" source="media/surface-custom-details-in-alerts/custom-details.png" alt-text="Adicionar detalhes personalizados":::

1. Clique **Em Adicionar novos** detalhes para emergir mais detalhes, repetindo os últimos passos para definir pares de valor-chave. 

    Se mudar de ideia, ou se cometeu um erro, pode remover um detalhe personalizado clicando no ícone do caixote do lixo ao lado da lista de drop-down **Value** para esse detalhe.

1. Quando terminar de definir detalhes personalizados, clique no **separador 'Rever', e crie** o separador's. Assim que a validação da regra for bem sucedida, clique em **Guardar**.

    > [!NOTE]
    > 
    >**Limites do serviço**
    > - Pode definir **até 20 detalhes personalizados** numa única regra de análise.
    >
    > - O limite de tamanho para todos os detalhes personalizados, coletivamente, é **de 2 KB**.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a apresentar detalhes personalizados em alertas utilizando as regras de análise do Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Obtenha a imagem completa nas [regras de análise de consultas programadas](tutorial-detect-threats-custom.md).
- Saiba mais sobre [as entidades em Azure Sentinel.](entities-in-azure-sentinel.md)
