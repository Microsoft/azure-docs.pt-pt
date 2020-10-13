---
title: Use Azure Stream Analytics
description: Dicas para utilizar o Azure Stream Analytics com o seu armazém de dados em Azure Synapse para desenvolver soluções em tempo real.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 60fb258fe2c6063b9b9a3ced0f4ba5f71ffd9d7c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449500"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Utilizar o Azure Stream Analytics com o Azure Synapse Analytics

O Azure Stream Analytics é um serviço totalmente gerido que fornece um processamento complexo complexo de baixa latência, altamente disponível e complexo sobre o streaming de dados na nuvem. Pode aprender o básico lendo [Introdução ao Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Em seguida, pode aprender a criar uma solução de ponta a ponta com stream Analytics, seguindo o Get começou a usar o tutorial [Azure Stream Analytics.](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

Neste artigo, você aprenderá a usar o seu armazém de dados como um lavatório de saída para ingestão de dados de alta produção com empregos Azure Stream Analytics.

## <a name="prerequisites"></a>Pré-requisitos

* Azure Stream Analytics Job - Para criar um trabalho Azure Stream Analytics, siga os passos no [Get start using Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) tutorial para:  

    1. Criar uma entrada do Centro de Eventos
    2. Configurar e iniciar a aplicação do gerador de eventos
    3. Provisionamento de um trabalho stream analytics
    4. Especificar entrada de trabalho e consulta
* Azure Synapse SQL pool para o seu armazém de dados - Para criar um novo armazém de dados, siga os passos no [Quickstart para criar um novo armazém de dados.](create-data-warehouse-portal.md)

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>Especifique a saída de streaming para apontar para o seu armazém de dados

### <a name="step-1"></a>Passo 1

A partir do portal Azure, vá ao seu trabalho stream Analytics e clique em **Saídas** no menu **de topologia job.**

### <a name="step-2"></a>Passo 2

Clique no botão **Adicionar** e escolha **Azure Synapse Analytics** no menu drop down.

![Escolha Azure Synapse Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>Passo 3

Introduza os seguintes valores:

* *Alias de saída : Insira*um nome amigável para esta saída de trabalho.
* *Assinatura*:
  * Se o seu armazém de dados estiver na mesma subscrição que o trabalho stream Analytics, clique em ***Select Azure Synapse Analytics a partir das suas subscrições***.
  * Se o seu armazém de dados estiver numa subscrição diferente, clique nas definições de Provide Azure Synapse Analytics manualmente.
* *Base de dados*: Selecione a base de dados de destino da lista de drop down.
* *Nome do utilizador*: Especifique o nome de utilizador de uma conta que tenha permissões de escrita para a base de dados.
* *Senha*: Forneça a palavra-passe para a conta de utilizador especificada.
* *Quadro*: Especificar o nome da tabela-alvo na base de dados.
* clique no botão **Guardar**

![Formulário Azure Synapse Analytics concluído](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

### <a name="step-4"></a>Passo 4

Antes de poder fazer um teste, terá de criar a tabela no seu armazém de dados.  Execute o seguinte script de criação de mesa utilizando o SQL Server Management Studio (SSMS) ou a sua escolha de ferramenta de consulta.

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

No portal Azure para o trabalho de Stream Analytics, clique no nome do seu trabalho.  Clique no botão ***Teste*** no painel de detalhes da ***saída.***

![Botão de teste em detalhes outpout ](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) Quando a ligação à base de dados tiver sucesso, verá uma notificação no portal.

### <a name="step-6"></a>Passo 6

Clique no menu ***de consulta*** em ***topologia de Trabalho*** e altere a consulta para inserir dados na saída Stream que criou.  Clique no botão ***de consulta selecionado*** para testar a sua consulta.  Clique no botão Guardar o botão ***'Guardar consulta'*** quando o teste de consulta for bem sucedido.

![Salvar consulta](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Passo 7

Inicie o trabalho do Azure Stream Analytics.  Clique no botão ***Iniciar*** no menu ***'Vista Geral'.***

![Iniciar tarefa do Stream Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Clique no botão ***Iniciar*** no painel de trabalho inicial.

![Clique em Iniciar](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Passos seguintes

Para uma visão geral da integração, consulte [Integrar outros serviços.](sql-data-warehouse-overview-integrate.md)
Para obter mais dicas de desenvolvimento, consulte [decisões de design e técnicas de codificação para armazéns de dados.](sql-data-warehouse-overview-develop.md)
