---
title: Utilizar a Análise de Fluxo Azure
description: Dicas para utilizar o Azure Stream Analytics com o seu armazém de dados em Azure Synapse para desenvolver soluções em tempo real.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 2/5/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e281f8a1fb3959256d836134b4c59f5399deb9bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633290"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Use Azure Stream Analytics com Azure Synapse Analytics

O Azure Stream Analytics é um serviço totalmente gerido que fornece um processamento complexo de eventos altamente disponível, altamente disponível e escalável sobre dados de streaming na nuvem. Pode aprender o básico lendo [Introdução ao Azure Stream Analytics.](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Em seguida, pode aprender a criar uma solução de ponta a ponta com o Stream Analytics seguindo o Get iniciado usando o tutorial [Azure Stream Analytics.](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

Neste artigo, você aprenderá a usar o seu armazém de dados como um afundado de saída para os seus trabalhos de Azure Stream Analytics.

## <a name="prerequisites"></a>Pré-requisitos

* Azure Stream Analytics Job - Para criar um trabalho de Azure Stream Analytics, siga os passos no Get iniciado usando o tutorial [Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) para:  

    1. Criar uma entrada do Hub de Eventos
    2. Configurar e iniciar aplicação geradora de eventos
    3. Provision a Stream Analytics
    4. Especificar a entrada e consulta de trabalho
* Armazém de dados da piscina Azure Synapse SQL - Para criar um novo armazém de dados, siga os passos no [Quickstart para criar um novo armazém de dados](create-data-warehouse-portal.md).

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>Especifique a saída de streaming para apontar para o seu armazém de dados

### <a name="step-1"></a>Passo 1

A partir do portal Azure, vá ao seu trabalho de Stream Analytics e clique em **Saídas** sob o menu de **topologia de trabalho.**

### <a name="step-2"></a>Passo 2

Clique no botão **Adicionar** e escolha a Base de **Dados SQL** a partir do menu de descida.

![Escolha base de dados SQL](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutput.png)

### <a name="step-3"></a>Passo 3

Introduza os seguintes valores:

* *Alias de saída*: Introduza um nome amigável para esta saída de trabalho.
* *Assinatura:*
  * Se o seu armazém de dados estiver na mesma subscrição que o trabalho do Stream Analytics, clique em ***Select SQL Database a partir das suas subscrições***.
  * Se a sua base de dados estiver numa subscrição diferente, clique em fornecer as definições de Base de Dados SQL manualmente.
* *Base de dados*: Selecione a base de dados de destino da lista de descidas.
* *Nome*do utilizador : Especifique o nome de utilizador de uma conta que tenha permissões de escrita para a base de dados.
* *Palavra-passe*: Forneça a palavra-passe para a conta de utilizador especificada.
* *Quadro*: Especifique o nome da tabela-alvo na base de dados.
* clique no botão **Guardar**

![Formulário de Base de Dados SQL concluído](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutputdbsettings.png)

### <a name="step-4"></a>Passo 4

Antes de poder fazer um teste, terá de criar a tabela no seu armazém de dados.  Execute o seguinte script de criação de tabela usando o SQL Server Management Studio (SSMS) ou a sua escolha de ferramenta de consulta.

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>Passo 5

No portal Azure para trabalho stream analytics, clique no seu nome de trabalho.  Clique no botão ***Teste*** no painel de ***detalhes da saída.***

![Botão de teste](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) em detalhes outpout Quando a ligação à base de dados for bem sucedida, verá uma notificação no portal.

### <a name="step-6"></a>Passo 6

Clique no menu ***Consulta*** em ***topologia de trabalho*** e altere a consulta para inserir dados na saída do Stream que criou.  Clique no botão de ***consulta selecionado*** para testar a sua consulta.  Clique no botão ***Guardar Consulta*** quando o teste de consulta for bem sucedido.

![Guardar consulta](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Passo 7

Inicie o trabalho de Azure Stream Analytics.  Clique no botão ***Iniciar*** no menu ***'Visão Geral'.***

![Iniciar tarefa do Stream Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Clique no botão ***Iniciar*** no painel de trabalho inicial.

![Clique em Iniciar](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Passos seguintes

Para uma visão geral da integração, consulte [Integrar outros serviços.](sql-data-warehouse-overview-integrate.md)
Para obter mais dicas de desenvolvimento, consulte decisões de [design e técnicas de codificação para armazéns](sql-data-warehouse-overview-develop.md)de dados.
