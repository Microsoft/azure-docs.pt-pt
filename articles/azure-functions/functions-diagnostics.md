---
title: Visão geral do diagnóstico das funções azure
description: Saiba como pode resolver problemas com a sua aplicação de funções com diagnósticos de Funções Azure.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: fb329273d442c023233fee2e7e01aabe5f5bff8c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122375"
---
# <a name="azure-functions-diagnostics-overview"></a>Visão geral do diagnóstico das Funções Azure

Quando estiver a executar uma aplicação de função, pretende estar preparado para quaisquer problemas que possam surgir, desde erros de 4xx até desencadear falhas. O diagnóstico de Funções Azure é uma experiência inteligente e interativa para ajudá-lo a resolver problemas na sua aplicação de funções sem configuração ou custo extra. Quando se depara com problemas com a sua aplicação de funções, o diagnóstico da Azure Functions aponta o que está errado. Guia-o para obter a informação certa para mais facilmente e rapidamente resolver o problema. Este artigo mostra-lhe o básico de como usar diagnósticos de Funções Azure para diagnosticar e resolver problemas de aplicações de funções comuns mais rapidamente.

## <a name="start-azure-functions-diagnostics"></a>Iniciar diagnósticos de funções azure

Para iniciar diagnósticos de funções Azure:

1. Navegue para a sua aplicação de funções no [portal Azure.](https://portal.azure.com)
1. **Selecione Diagnosticar e resolver problemas** para abrir diagnósticos de Funções Azure.
1. Escolha uma categoria que melhor descreva o problema da sua aplicação de função utilizando as palavras-chave no azulejo da página inicial. Também pode escrever uma palavra-chave que melhor descreve o seu problema na barra de pesquisa. Por exemplo, pode escrever `execution` para ver uma lista de relatórios de diagnóstico relacionados com a execução da sua aplicação de função e abri-los diretamente a partir da página inicial.

   :::image type="content" source="./media/functions-diagnostics/functions-app-search-azure-functions-diagnostics.png" alt-text="Pesquisa de diagnósticos de Funções Azure." border="true":::

## <a name="use-the-interactive-interface"></a>Utilize a interface Interativa

Uma vez que selecione uma categoria de página inicial que melhor se alinha com o problema da sua aplicação de funções, a interface interativa de diagnósticos da Azure Functions, chamada Genie, pode guiá-lo através do diagnóstico e resolução de problemas da sua aplicação. Você pode usar os atalhos de azulejos fornecidos por Genie para ver o relatório completo de diagnóstico da categoria de problema que você está interessado. Os atalhos de azulejos proporcionam-lhe uma forma direta de aceder às suas métricas de diagnóstico.

:::image type="content" source="./media/functions-diagnostics/genie.png" alt-text="Genie é interface de diagnóstico da Azure Functions." border="false":::

Depois de selecionar um azulejo, pode ver uma lista de tópicos relacionados com o problema descrito no azulejo. Estes tópicos fornecem fragmentos de informações notáveis a partir do relatório completo. Selecione qualquer um destes tópicos para investigar mais aprofundadamente as questões. Além disso, pode selecionar **O Relatório Completo** do View para explorar todos os tópicos numa única página.

:::image type="content" source="./media/functions-diagnostics/preview-of-diagnostic-report.png" alt-text="Pré-visualização do relatório de diagnóstico" border="false":::

## <a name="view-a-diagnostic-report"></a>Ver um relatório de diagnóstico

Depois de escolher um tópico, pode ver um relatório de diagnóstico específico da sua aplicação de funções. Os relatórios de diagnóstico utilizam ícones de estado para indicar se existem problemas específicos com a sua aplicação. Você vê descrição detalhada do problema, ações recomendadas, métricas relacionadas e documentos úteis. Os relatórios de diagnóstico personalizados são gerados a partir de uma série de verificações executadas na sua aplicação de função. Os relatórios de diagnóstico podem ser uma ferramenta útil para identificar problemas na sua aplicação de funções e guiá-lo para a resolução do problema.

## <a name="find-the-problem-code"></a>Encontre o código de problemas

Para funções baseadas em scripts, pode utilizar a **execução de funções e os erros** no âmbito da aplicação de **funções Para reduzir a** linha de código, causando exceções ou erros. Pode utilizar esta ferramenta para chegar à causa principal e corrigir problemas a partir de uma linha de código específica. Esta opção não está disponível para funções pré-compiladas de C# e Java.

:::image type="content" source="./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png" alt-text="Relatório de diagnóstico sobre erros de execução de funções" border="false":::

:::image type="content" source="./media/functions-diagnostics/function-exception.png" alt-text="Vista de detalhes de exceção." border="false":::

## <a name="next-steps"></a>Passos seguintes

Pode fazer perguntas ou fornecer feedback sobre diagnósticos de Funções Azure no [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Inclua `[Diag]` o título do seu feedback.

> [!div class="nextstepaction"]
> [Monitorize as suas aplicações de função](functions-monitoring.md)
