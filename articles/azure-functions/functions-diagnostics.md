---
title: Visão geral de diagnósticos de funções Azure
description: Saiba como pode resolver problemas com a sua aplicação de função com diagnósticos de Funções Azure.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: fb329273d442c023233fee2e7e01aabe5f5bff8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "83122375"
---
# <a name="azure-functions-diagnostics-overview"></a>Descrição geral dos diagnósticos das Funções do Azure

Quando estiver a executar uma aplicação de função, pretende estar preparado para quaisquer problemas que possam surgir, desde erros de 4xx até falhas de desencadeamento. O diagnóstico de Funções Azure é uma experiência inteligente e interativa para ajudá-lo a resolver problemas na sua aplicação de função sem configuração ou custo extra. Quando se depara com problemas com a sua aplicação de função, o diagnóstico do Azure Functions aponta o que está errado. Guia-o para a informação certa para resolver problemas mais facilmente e rapidamente e resolver o problema. Este artigo mostra-lhe o básico de como usar diagnósticos de Funções Azure para diagnosticar e resolver problemas de aplicações de função comum mais rapidamente.

## <a name="start-azure-functions-diagnostics"></a>Iniciar diagnósticos de funções Azure

Para iniciar diagnósticos de funções Azure:

1. Navegue para a sua aplicação de função no [portal Azure.](https://portal.azure.com)
1. Selecione **diagnosticar e resolver problemas** para abrir diagnósticos de Funções Azure.
1. Escolha uma categoria que melhor descreva a emissão da sua aplicação de função utilizando as palavras-chave no azulejo inicial. Também pode escrever uma palavra-chave que melhor descreve o seu problema na barra de pesquisa. Por exemplo, pode escrever `execution` para ver uma lista de relatórios de diagnóstico relacionados com a execução da aplicação de função e abri-los diretamente a partir da página inicial.

   :::image type="content" source="./media/functions-diagnostics/functions-app-search-azure-functions-diagnostics.png" alt-text="Procure diagnósticos de Funções Azure." border="true":::

## <a name="use-the-interactive-interface"></a>Utilize a interface Interativa

Uma vez selecionada uma categoria de página inicial que melhor se alinha com o problema da sua aplicação de função, a interface interativa de diagnósticos Azure Functions, denominada Genie, pode guiá-lo através do diagnóstico e resolução de problemas da sua aplicação. Você pode usar os atalhos de azulejos fornecidos por Genie para ver o relatório completo de diagnóstico da categoria de problemas que você está interessado. Os atalhos de azulejos proporcionam-lhe uma forma direta de aceder às suas métricas de diagnóstico.

:::image type="content" source="./media/functions-diagnostics/genie.png" alt-text="Genie é a interface de diagnóstico de Azure Functions." border="false":::

Depois de selecionar um azulejo, pode ver uma lista de tópicos relacionados com a questão descrita no azulejo. Estes tópicos fornecem fragmentos de informações notáveis do relatório completo. Selecione qualquer um destes tópicos para investigar mais aprofundadamente as questões. Além disso, pode selecionar **Ver Relatório Completo** para explorar todos os tópicos numa única página.

:::image type="content" source="./media/functions-diagnostics/preview-of-diagnostic-report.png" alt-text="Pré-visualização do relatório de diagnóstico" border="false":::

## <a name="view-a-diagnostic-report"></a>Ver um relatório de diagnóstico

Depois de escolher um tópico, pode ver um relatório de diagnóstico específico da sua aplicação de função. Os relatórios de diagnóstico utilizam ícones de estado para indicar se existem problemas específicos com a sua aplicação. Você vê descrição detalhada do problema, ações recomendadas, métricas relacionadas e documentos úteis. Os relatórios de diagnóstico personalizados são gerados a partir de uma série de verificações executadas na sua aplicação de função. Os relatórios de diagnóstico podem ser uma ferramenta útil para identificar problemas na sua aplicação de função e guiá-lo para a resolução do problema.

## <a name="find-the-problem-code"></a>Encontre o código de problemas

Para funções baseadas em scripts, pode utilizar **execução de funções e erros** em **relatórios de aplicação para** reduzir a linha de código causando exceções ou erros. Pode utilizar esta ferramenta para chegar à causa raiz e corrigir problemas a partir de uma linha de código específica. Esta opção não está disponível para funções pré-recompensadas C# e Java.

:::image type="content" source="./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png" alt-text="Relatório de diagnóstico sobre erros de execução de funções" border="false":::

:::image type="content" source="./media/functions-diagnostics/function-exception.png" alt-text="Vista para detalhes da exceção." border="false":::

## <a name="next-steps"></a>Passos seguintes

Pode fazer perguntas ou fornecer feedback sobre diagnósticos de funções Azure no [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Inclua `[Diag]` no título do seu feedback.

> [!div class="nextstepaction"]
> [Monitorize as suas aplicações de função](functions-monitoring.md)
