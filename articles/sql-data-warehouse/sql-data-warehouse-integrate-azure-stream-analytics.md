---
title: Utilizar o Azure Stream Analytics com o SQL Data Warehouse | Documentos da Microsoft
description: Sugestões para utilizar o Azure Stream Analytics com o Azure SQL Data Warehouse para o desenvolvimento de soluções.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 605598d1470cbb535d626c15a5e8e4e08aa4d571
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57883819"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Utilizar o Azure Stream Analytics com o SQL Data Warehouse
O Azure Stream Analytics é um serviço totalmente gerido, fornecendo o processamento de eventos complexos de baixa latência, altamente disponível e dimensionável através de transmissão em fluxo de dados na cloud. Pode aprender as noções básicas, lendo [introdução ao Azure Stream Analytics][Introduction to Azure Stream Analytics]. Em seguida, pode saber como criar uma solução ponto a ponto com o Stream Analytics ao seguir a [começar a utilizar o Azure Stream Analytics] [ Get started using Azure Stream Analytics] tutorial.

Neste artigo, irá aprender a utilizar a base de dados do Azure SQL Data Warehouse como um sink de saída para as tarefas do Stream Analytics.

## <a name="prerequisites"></a>Pré-requisitos
Primeiro, execute os seguintes passos no [começar a utilizar o Azure Stream Analytics] [ Get started using Azure Stream Analytics] tutorial.  

1. Criar uma entrada do Hub de eventos
2. Configurar e iniciar a aplicação geradora de eventos
3. Aprovisionar uma tarefa do Stream Analytics
4. Especifique a consulta e de entrada da tarefa

Em seguida, crie uma base de dados do Azure SQL Data Warehouse

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Especifique o resultado da tarefa: Base de dados de armazém de dados SQL do Azure
### <a name="step-1"></a>Passo 1
Na sua tarefa do Stream Analytics, clique em **saída** na parte superior da página e, em seguida, clique **adicionar saída**.

### <a name="step-2"></a>Passo 2
Selecione a base de dados SQL e clique em seguinte.

![][add-output]

### <a name="step-3"></a>Passo 3
Introduza os seguintes valores na página seguinte:

* *Alias de saída*: Introduza um nome amigável para este resultado da tarefa.
* *Subscrição*:
  * Se a sua base de dados do SQL Data Warehouse está na mesma subscrição que a tarefa do Stream Analytics, selecione a base de dados de SQL de utilização da subscrição atual.
  * Se a sua base de dados está numa subscrição diferente, selecione a base de dados de SQL de utilização de outra subscrição.
* *Base de dados*: Especifique o nome de uma base de dados de destino.
* *Nome do servidor*: Especifique o nome do servidor da base de dados que acabou de especificar. Pode utilizar o portal do Azure para encontrá-lo.

![][server-name]

* *Nome de utilizador*: Especifique o nome de utilizador de uma conta que tenha permissões para a base de dados de escrita.
* *palavra-passe*: Forneça a palavra-passe da conta de utilizador especificado.
* *tabela*: Especifique o nome da tabela de destino na base de dados.

![][add-database]

### <a name="step-4"></a>Passo 4
Clique no botão de verificação para adicionar esta saída de tarefa e certifique-se de que o Stream Analytics consegue ligar com êxito à base de dados.

![][test-connection]

Quando a ligação à base de dados tiver êxito, verá uma notificação na parte inferior do portal. Pode clicar em Testar ligação na parte inferior para testar a ligação à base de dados.

## <a name="next-steps"></a>Passos Seguintes
Para uma descrição geral de integração, consulte [descrição geral da integração do SQL Data Warehouse][SQL Data Warehouse integration overview].

Para obter mais sugestões de desenvolvimento, veja [SQL Data Warehouse development overview (Descrição geral do desenvolvimento no SQL Data Warehouse)][SQL Data Warehouse development overview].

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
