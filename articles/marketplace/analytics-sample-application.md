---
title: Aplicação de amostra para aceder a dados de análise de mercado comercial
description: Utilize a aplicação de amostra para construir a sua própria aplicação de análise de mercado comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8fe2301c4d4ed2a582774c65d5dbe68bab416aa3
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584077"
---
# <a name="sample-application-for-accessing-commercial-marketplace-analytics-data"></a>Aplicação de amostra para aceder a dados de análise de mercado comercial

As aplicações da amostra são criadas em línguas C# e JAVA e estão disponíveis no [GitHub](https://github.com/partneranalytics).

- [C# Aplicação da amostra](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV)
- [Aplicação da amostra JAVA](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV_Java)

Pode optar por inspirar-se na aplicação da amostra e construir a sua própria aplicação em qualquer idioma.

A aplicação da amostra alcança os seguintes objetivos:

- Gera um token Azure Ative Directory (Azure AD).
- Obtém conjuntos de dados disponíveis.
- Cria consultas definidas pelo utilizador.
- Obtém consultas definidas pelo utilizador e sistema.
- Marca um relatório.

A aplicação da amostra não cobre o método de chamar APIs para outras funcionalidades. No entanto, o processo de chamar outras APIs permanece o mesmo que acima descrito.

## <a name="how-to-run-the-application"></a>Como executar a aplicação

1. Clone o repositório para um sistema local usando este comando:

    `git clone https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git`

    > [!NOTE]
    > Para obter mais instruções, consulte o `ProgrammaticExportSampleAppISV/README.md` ficheiro no [repositório](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git)GitHub .

1. Para executar rapidamente a aplicação, atualize o ID do cliente e o segredo do cliente no **appsettings.Development.js**

    :::image type="content" source="./media/analytics-programmatic-access/appsettings-development-json.png" alt-text="Ilustra um corte do appsettings.Development.jsarquivado.":::

A execução da aplicação iniciará um servidor web local e uma página será aberta ( `https://localhost:44365/ProgrammaticExportSampleApp/sample` ).

[![Ilustra a página do relatório da agenda.](./media/analytics-programmatic-access/schedule-report.png)](./media/analytics-programmatic-access/schedule-report.png#lightbox)

Esta página fará chamadas de API para o webserver que está a funcionar na máquina local, o que por sua vez fará as chamadas de acesso programático real API.

## <a name="code-snippets"></a>Fragmentos de código

A estrutura básica do código C# para fazer as chamadas de API de acesso programático é a seguinte:

:::image type="content" source="./media/analytics-programmatic-access/code-snippets.png" alt-text="Screenshot das chamadas da API.":::

## <a name="next-steps"></a>Passos seguintes

- [APIs para aceder a dados de análise de marketplace comercial](analytics-available-apis.md)
