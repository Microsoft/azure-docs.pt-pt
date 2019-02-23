---
title: Descrição geral do fluxo de dados de mapeamento de fábrica de dados do Azure
description: Uma explicação de descrição geral de mapeamento de fluxos de dados do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 5064f47444b4ca6d9194ed66144938e6e3d51a4e
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732370"
---
# <a name="what-are-mapping-data-flows-in-azure-data-factory"></a>O que são o mapeamento de fluxos de dados do Azure Data Factory?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Fluxos de dados de mapeamento de permitir que os engenheiros de dados desenvolver a lógica de transformação de dados de gráfico sem escrever código. Os fluxos de dados resultantes são executados como atividades dentro de Pipelines de fábrica de dados do Azure com clusters do Azure Databricks de escalamento horizontal.

A intenção do fluxo de dados do Azure Data Factory é fornecer uma experiência visual totalmente sem necessidade de código. Os fluxos de dados será executado em seu próprio cluster em execução para processamento de dados de escalamento horizontal. O Azure Data Factory cuida de tudo a tradução de código, a otimização de caminho e a execução das suas tarefas de fluxo de dados.

Comece por criar fluxos de dados no modo de depuração para que pode validar sua lógica de transformação interativamente. Em seguida, adicione uma atividade de fluxo de dados para o seu pipeline para executar e o teste de seus dados na depuração de pipeline de fluxo ou usar "Acionar agora" no pipeline para testar o fluxo de dados a partir de um pipeline de atividade.

Irá, em seguida, agendar e monitorizar as atividades do fluxo de dados com pipelines do Azure Data Factory que executar a atividade de fluxo de dados.

O comutador de estado do modo de depuração na superfície de design do fluxo de dados permite criação interativa de transformações de dados. Modo de depuração fornece um ambiente de preparação de dados para a construção de fluxo de dados.
