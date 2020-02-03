---
title: Utilizar a Análise de Fluxo Azure
description: Dicas para utilizar o Azure Stream Analytics com o Azure SQL Data Warehouse para o desenvolvimento de soluções.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a655ada93cd9db9db95295d445c0b4f27d772148
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721205"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Use Azure Stream Analytics com Azure Synapse Analytics
O Azure Stream Analytics é um serviço totalmente gerido que fornece um processamento complexo de eventos altamente disponível, altamente disponível e escalável sobre dados de streaming na nuvem. Pode aprender o básico lendo [Introdução ao Azure Stream Analytics.](../stream-analytics/stream-analytics-introduction.md) Em seguida, pode aprender a criar uma solução de ponta a ponta com o Stream Analytics seguindo o Get iniciado usando o tutorial [Azure Stream Analytics.](../stream-analytics/stream-analytics-real-time-fraud-detection.md)

Neste artigo, você aprenderá a usar a sua base de dados de armazém de dados como um afundado de saída para os seus trabalhos stream analytics.

## <a name="prerequisites"></a>Pré-requisitos
Primeiro, passe pelos seguintes passos no Get começou a usar o tutorial [Azure Stream Analytics.](../stream-analytics/stream-analytics-real-time-fraud-detection.md)  

1. Criar uma entrada do Hub de Eventos
2. Configurar e iniciar aplicação geradora de eventos
3. Provision a Stream Analytics
4. Especificar a entrada e consulta de trabalho

Em seguida, criar uma base de dados azure SQL Data Warehouse

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Especificar saída de trabalho: Base de dados azure SQL Data Warehouse
### <a name="step-1"></a>Passo 1
No seu trabalho stream Analytics clique em **OUTPUT** a partir do topo da página e, em seguida, clique em **ADD**.

### <a name="step-2"></a>Passo 2
Selecione Base de Dados SQL.

### <a name="step-3"></a>Passo 3
Introduza os seguintes valores na página seguinte:

* *Alias de saída*: Introduza um nome amigável para esta saída de trabalho.
* *Subscrição*:
  * Se a sua base de dados sQL Data Warehouse estiver na mesma subscrição que o trabalho do Stream Analytics, selecione Use a Base de Dados SQL da Subscrição Atual.
  * Se a sua base de dados estiver numa subscrição diferente, selecione Use a Base de Dados SQL de Outra Subscrição.
* *Base de dados*: Especifique o nome de uma base de dados de destino.
* *Nome*do servidor : Especifique o nome do servidor para a base de dados que acaba de especificar. Pode usar o portal Azure para encontrar isto.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png)

* *Nome*do utilizador : Especifique o nome de utilizador de uma conta que tenha permissões de escrita para a base de dados.
* *Palavra-passe*: Forneça a palavra-passe para a conta de utilizador especificada.
* *Quadro*: Especifique o nome da tabela-alvo na base de dados.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png)

### <a name="step-4"></a>Passo 4
Clique no botão de verificação para adicionar esta saída de trabalho e para verificar se o Stream Analytics pode ligar-se com sucesso à base de dados.

Quando a ligação à base de dados for bem sucedida, verá uma notificação no portal. Pode clicar em Testar para testar a ligação à base de dados.

## <a name="next-steps"></a>Passos Seguintes
Para uma visão geral da integração, consulte [Integrar outros serviços.](sql-data-warehouse-overview-integrate.md)
Para obter mais dicas de desenvolvimento, consulte decisões de [design e técnicas de codificação para armazéns](sql-data-warehouse-overview-develop.md)de dados.

