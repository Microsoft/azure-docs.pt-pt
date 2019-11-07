---
title: Usar Azure Stream Analytics
description: Dicas para usar Azure Stream Analytics com o SQL Data Warehouse do Azure para desenvolver soluções.
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
ms.openlocfilehash: 63803f3ac477e48d8d1c14a72e2ee9b9d4860047
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685733"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Usar Azure Stream Analytics com SQL Data Warehouse
Azure Stream Analytics é um serviço totalmente gerenciado que fornece baixa latência, alta disponibilidade e processamento de eventos complexos escalonável por meio de streaming de dados na nuvem. Você pode aprender as noções básicas lendo [introdução ao Azure Stream Analytics][Introduction to Azure Stream Analytics]. Em seguida, você pode aprender como criar uma solução de ponta a ponta com Stream Analytics seguindo o tutorial introdução ao [uso de Azure Stream Analytics][Get started using Azure Stream Analytics] .

Neste artigo, você aprenderá a usar o banco de dados SQL Data Warehouse do Azure como um coletor de saída para seus trabalhos de Stream Analytics.

## <a name="prerequisites"></a>Pré-requisitos
Primeiro, execute as etapas a seguir no tutorial introdução ao [uso de Azure Stream Analytics][Get started using Azure Stream Analytics] .  

1. Criar uma entrada do hub de eventos
2. Configurar e iniciar o aplicativo gerador de eventos
3. Provisionar um trabalho de Stream Analytics
4. Especificar entrada e consulta do trabalho

Em seguida, crie um banco de dados SQL Data Warehouse do Azure

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Especificar a saída do trabalho: banco de dados SQL Data Warehouse do Azure
### <a name="step-1"></a>Passo 1
Em seu trabalho de Stream Analytics clique em **saída** na parte superior da página e, em seguida, clique em **Adicionar**.

### <a name="step-2"></a>Passo 2
Selecione banco de dados SQL.

### <a name="step-3"></a>Passo 3
Insira os seguintes valores na próxima página:

* *Alias de saída*: Insira um nome amigável para esta saída de trabalho.
* *Subscrição*:
  * Se o banco de dados do SQL Data Warehouse estiver na mesma assinatura que o trabalho de Stream Analytics, selecione usar banco de dados SQL da assinatura atual.
  * Se o banco de dados estiver em uma assinatura diferente, selecione usar o banco de dados SQL de outra assinatura.
* *Banco de dados*: especifique o nome de um banco de dados de destino.
* *Nome do servidor*: especifique o nome do servidor para o banco de dados que você acabou de especificar. Você pode usar o portal do Azure para encontrá-lo.

![][server-name]

* *Nome de usuário*: especifique o nome de usuário de uma conta que tenha permissões de gravação para o banco de dados.
* *Senha*: forneça a senha para a conta de usuário especificada.
* *Tabela*: especifique o nome da tabela de destino no banco de dados.

![][add-database]

### <a name="step-4"></a>Passo 4
Clique no botão de seleção para adicionar essa saída de trabalho e verificar se Stream Analytics pode se conectar com êxito ao banco de dados.

Quando a conexão com o banco de dados for realizada com sucesso, você verá uma notificação no Portal. Você pode clicar em testar para testar a conexão com o banco de dados.

## <a name="next-steps"></a>Passos seguintes
Para obter uma visão geral da integração, consulte [visão geral da integração do SQL data warehouse][SQL Data Warehouse integration overview].

Para mais sugestões de desenvolvimento, consulte [Descrição geral do desenvolvimento no SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: https://azure.microsoft.com/documentation/services/stream-analytics/
