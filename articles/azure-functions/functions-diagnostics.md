---
title: Visão geral do diagnóstico de Azure Functions
description: Saiba como você pode solucionar problemas com seu aplicativo de funções com o Azure Functions Diagnostics.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 29f90508bf0bdd77f02d78cbcba8fdfe7009f0d2
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562056"
---
# <a name="azure-functions-diagnostics-overview"></a>Visão geral do diagnóstico de Azure Functions

Quando você estiver executando um aplicativo de funções, você deseja estar preparado para quaisquer problemas que possam surgir, de erros de 4xx para disparar falhas. O diagnóstico de Azure Functions é uma experiência inteligente e interativa para ajudá-lo a solucionar problemas do seu aplicativo de funções sem nenhuma configuração ou custo adicional. Quando você tiver problemas com seu aplicativo de funções, o diagnóstico de Azure Functions indicará o que há de errado para orientá-lo sobre as informações corretas para solucionar problemas e resolver o problema com mais facilidade e rapidez. Este artigo mostra as noções básicas de como usar o diagnóstico de Azure Functions para diagnosticar e resolver mais rapidamente problemas comuns do aplicativo de funções.

## <a name="start-azure-functions-diagnostics"></a>Iniciar o diagnóstico de Azure Functions

Para acessar o diagnóstico de Azure Functions:

1. Navegue até seu aplicativo de funções no [portal do Azure](https://portal.azure.com).
2. Selecione a guia **recursos da plataforma** .
3. Selecione **diagnosticar e solucionar problemas** no **Gerenciamento de recursos**, que abre o diagnóstico de Azure functions.
4. Escolha uma categoria que melhor descreva o problema do seu aplicativo de funções usando as palavras-chave no bloco Home Page. Você também pode digitar uma palavra-chave que melhor descreva seu problema na barra de pesquisa. Por exemplo, você pode digitar `execution` para ver uma lista de relatórios de diagnóstico relacionados à sua execução de aplicativo de funções e abri-los diretamente da Home Page.

![Home Page](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>Usar a interface interativa

Depois de selecionar uma categoria de Home Page que melhor se alinha com o problema do seu aplicativo de funções, a interface interativa do Azure Functions Diagnostics, o gênio, pode orientá-lo no diagnóstico e na resolução do problema de seu aplicativo. Você pode usar os atalhos de bloco fornecidos pelo gênio para exibir o relatório de diagnóstico completo da categoria do problema em que você está interessado. Os atalhos de bloco fornecem uma maneira direta de acessar suas métricas de diagnóstico.

![Gênio](./media/functions-diagnostics/genie.png)

Depois de selecionar um bloco, você poderá ver uma lista de tópicos relacionados ao problema descrito no bloco. Esses tópicos fornecem trechos de informações notáveis do relatório completo. Você pode selecionar qualquer um desses tópicos para investigar ainda mais os problemas. Além disso, você pode selecionar **Exibir relatório completo** para explorar todos os tópicos em uma única página.

![Visualização do relatório de diagnóstico](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>Exibir um relatório de diagnóstico

Depois de escolher um tópico, você pode exibir um relatório de diagnóstico específico para seu aplicativo de funções. Os relatórios de diagnóstico usam ícones de status para indicar se há problemas específicos com seu aplicativo. Você verá uma descrição detalhada do problema, as ações recomendadas, as métricas relacionadas e os documentos úteis. Relatórios de diagnóstico personalizados são gerados de uma série de verificações executadas em seu aplicativo de funções. Os relatórios de diagnóstico podem ser uma ferramenta útil para identificar problemas em seu aplicativo de funções e orientá-lo para resolver o problema.

## <a name="find-the-problem-code"></a>Localizar o código do problema 

Para funções baseadas em script, você pode usar a **execução de função** e **erros** em **aplicativo de funções ou relatar erros** para restringir a linha de código causando exceções ou erros. Esse recurso pode ser uma ferramenta útil para chegar à causa raiz e corrigir problemas de uma linha de código específica. Essa opção não está disponível para funções pré-compiladas C# e Java.

![Relatório de diagnóstico sobre erros de execução de função](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![Exceção de função](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>Passos seguintes

Você pode fazer perguntas ou fornecer comentários sobre o diagnóstico de Azure Functions no [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Inclua `[Diag]` no título de seus comentários.

> [!div class="nextstepaction"]
> [Monitorar seus aplicativos de funções](functions-monitoring.md)
