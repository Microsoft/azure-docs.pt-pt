---
title: Mapear campos de dados para entidades do Azure Sentinel | Microsoft Docs
description: Mapear campos de dados em tabelas para entidades do Azure Sentinel em regras de análise, para uma melhor informação sobre incidentes
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
ms.openlocfilehash: cb91d269f6b166510db54637d17d776e71137408
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102456292"
---
# <a name="map-data-fields-to-entities-in-azure-sentinel"></a>Mapear campos de dados para entidades em Azure Sentinel 

> [!IMPORTANT]
>
> - A nova versão da funcionalidade de mapeamento da entidade está em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

> [!IMPORTANT]
>
> - Consulte [notas sobre a nova versão](#notes-on-the-new-version) no final deste documento para obter informações importantes sobre compatibilidade retrógrada e diferenças entre as versões novas e antigas do mapeamento da entidade.

## <a name="introduction"></a>Introdução

O mapeamento de entidades é parte integrante da configuração das [regras de análise](tutorial-detect-threats-custom.md)de consultas programadas . Enriquece a produção das regras (alertas e incidentes) com informação essencial que serve de blocos de construção de quaisquer processos de investigação e ações corretivas que se seguem.

O procedimento detalhado abaixo faz parte do assistente de criação de regras de análise. É tratado aqui de forma independente para abordar o cenário de adicionar ou alterar mapeamentos de entidades numa regra de análise existente.

## <a name="how-to-map-entities"></a>Como mapear entidades

1. A partir do menu de navegação Azure Sentinel, **selecione Analytics**.

1. Selecione uma regra de consulta agendada e clique em **Editar.** Ou criar uma nova regra clicando em **Criar &#10132; regra** de consulta agendada no topo do ecrã.

1. Clique no separador **lógica de regra definida.**

    :::image type="content" source="media/map-data-fields-to-entities/map-entities.png" alt-text="Map fields para entidades":::

1. Na secção **de melhoramento de alerta,** no **mapeamento da Entidade,** selecione um tipo de entidade a partir da lista de drop-down **tipo Entity.**

1. Selecione um **identificador** para a entidade. Os identificadores são atributos de uma entidade que pode identificá-lo o suficiente. Escolha um da lista de drop-down do **Identificador** e, em seguida, escolha um campo de dados **da** lista de drop-down value que corresponda ao identificador. Com algumas exceções, a lista **de Valores** é povoada pelos campos de dados na tabela definida como o assunto da consulta de regras.

    Pode definir **até três identificadores** para uma determinada entidade. Alguns identificadores são necessários, outros são opcionais. Deve escolher pelo menos um identificador necessário. Se não o fizer, uma mensagem de aviso instrui-o-á sobre quais os identificadores necessários. Para obter melhores resultados - para uma identificação única máxima - deve utilizar **identificadores fortes** sempre que possível, e a utilização de múltiplos identificadores fortes permitirá uma maior correlação entre fontes de dados. Consulte a lista completa das [entidades e identificadores](entities-reference.md)disponíveis.

1. Clique **em Adicionar nova entidade** para mapear mais entidades. Pode **mapear até cinco entidades** numa única regra de análise. Também pode mapear mais do que um do mesmo tipo. Por exemplo, pode mapear duas entidades **IP,** uma a partir de um campo de endereço IP de *origem* e outra de um campo de *endereços IP* de destino. Assim, podes localizar os dois.

    Se mudar de ideia, ou se cometeu um erro, pode remover um mapeamento de entidade clicando no ícone do caixote do lixo ao lado da lista de down-down da entidade.

1. Quando terminar de mapear entidades, clique no **separador 'Rever'.** Assim que a validação da regra for bem sucedida, clique em **Guardar**.

## <a name="notes-on-the-new-version"></a>Notas sobre a nova versão

- Se já tinha definido os mapeamentos de entidades para esta regra de análise utilizando a versão antiga, esses mapeamentos aparecem no código de consulta. Os mapeamentos de entidades definidos na nova versão **não aparecem no código de consulta**. As regras de análise só podem suportar uma versão de mapeamentos de entidades de cada vez, e a nova versão tem precedência. Portanto, qualquer mapeamento que você define aqui fará com que **todos e todos os** mapeamentos definidos no código de consulta sejam **ignorados** quando a consulta é executado. 

- Se ainda precisar de utilizar a **versão antiga** do mapeamento da entidade (desde que a nova versão ainda esteja em pré-visualização), ainda pode aceder a ela utilizando uma bandeira de recurso no URL. Coloque o cursor entre `https://portal.azure.com/` `#blade` e , e insira o texto `?feature.EntityMapping=false` .

  - Os limites da versão antiga continuarão a aplicar-se. Pode mapear apenas as entidades de utilizador, anfitrião, endereço IP, URL e hash de ficheiro, e apenas uma de cada.

  - Tem de **remover** quaisquer mapeamentos de entidades criados utilizando a nova versão **antes** de regressar à versão antiga, caso contrário qualquer mapeamento de entidade que utilize a versão antiga **não funcionará**.

- Uma vez que a nova versão do mapeamento da entidade esteja em Disponibilidade Geral, deixará de ser possível utilizar a versão antiga. É altamente recomendável que emigres os teus antigos mapeamentos de entidade para a nova versão.


## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a mapear campos de dados para entidades nas regras de análise do Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Obtenha a imagem completa nas [regras de análise de consultas programadas](tutorial-detect-threats-custom.md).
- Saiba mais sobre [as entidades em Azure Sentinel.](entities-in-azure-sentinel.md)
