---
title: Copiar dados incrementalmente por
description: Estes tutoriais mostram-lhe como copiar dados de forma incremental de um arquivo de dados de origem para um arquivo de dados de destino. O primeiro copia dados a partir de uma tabela.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/22/2018
ms.openlocfilehash: 00d54f9adcab93152881852b6fcac417e9d7c7d9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81409909"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Carrega dados de forma incremental a partir de um arquivo de dados de origem para um arquivo de dados de destino

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Uma solução de integração de dados, que carrega dados incrementalmente (ou delta) após um carregamento de dados completos inicial é um cenário bastante utilizado. Os tutoriais nesta secção mostram formas diferentes de carregar dados de forma incremental através da utilização do Azure Data Factory.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Delta carregamento de dados da base de dados usando uma marca de água
Neste caso, é possível definir uma marca d'água na base de dados de origem. Uma marca d'água é uma coluna com o último carimbo de data/hora atualizado ou uma chave de incrementação. A solução de carregamento delta carrega os dados alterados entre uma marca d'água antiga e uma nova marca d'água. O fluxo de trabalho para esta abordagem é descrito no diagrama seguinte: 

![Fluxo de trabalho para a utilização de uma marca d'água](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Para obter instruções passo a passo, veja os seguintes tutoriais: 
- [Copiar dados de forma incremental de uma tabela na Base de Dados SQL do Azure para o armazenamento de Blobs do Azure](tutorial-incremental-copy-powershell.md)
- [Copiar dados de forma incremental a partir de várias tabelas no SQL Server no local para a Base de Dados SQL do Azure](tutorial-incremental-copy-multiple-tables-powershell.md)

Para os modelos, consulte o seguinte:
- [Cópia delta com a tabela de controlo](solution-template-delta-copy-with-control-table.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Carregamento de dados delta a partir de SQL DB utilizando a tecnologia Change Tracking
A tecnologia de Controlo de Alterações é uma solução simples no SQL Server e na Base de Dados SQL do Azure que fornece um mecanismo de controlo de alterações eficiente para aplicações. Permite que uma aplicação facilmente identifique dados que foram inseridos, atualizados ou eliminados. 

O fluxo de trabalho para esta abordagem é descrito no diagrama seguinte:

![Fluxo de trabalho para utilizar o Controlo de Alterações](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Para obter instruções passo a passo, veja o seguinte tutorial: <br/>
- [Copiar dados de forma incremental da Base de Dados SQL do Azure para o armazenamento de Blobs do Azure utilizando a tecnologia de Controlo de Alterações](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Carregar ficheiros novos e alterados apenas utilizando o LastModifiedDate
Só pode copiar os novos ficheiros e alterar utilizando o LastModifieDDate para a loja de destino. A ADF digitalizará todos os ficheiros da loja de origem, aplicará o filtro de ficheiros pelo seu LastModifiedDate e apenas copiará o novo e atualizado ficheiro desde a última vez na loja de destino.  Tenha em atenção que se deixar a ADF digitalizar enormes quantidades de ficheiros, mas apenas copiar alguns ficheiros para o destino, ainda assim esperaria que a longa duração devido à verificação de ficheiros também fosse morosa.   

Para obter instruções passo a passo, veja o seguinte tutorial: <br/>
- [Copie gradualmente novos e alterados ficheiros com base no LastModifieDDate do armazenamento Azure Blob para o armazenamento da Blob Azure](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

Para os modelos, consulte o seguinte:
- [Copiar novos ficheiros por DataÚltimaModificação](solution-template-copy-new-files-lastmodifieddate.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Carregar novos ficheiros apenas utilizando pasta ou nome de ficheiro dividido seleção.
Só pode copiar novos ficheiros, quando os ficheiros ou pastas já foram divididos com informações sobre o intervalo de tempo como parte do nome do ficheiro ou da pasta (por exemplo, /yyyy/mm/dd/file.csv). É a abordagem mais performante para carregar gradualmente novos ficheiros. 

Para obter instruções passo a passo, veja o seguinte tutorial: <br/>
- [Copie gradualmente novos ficheiros com base na pasta divisória temporal ou no nome do ficheiro do armazenamento Azure Blob para o armazenamento da Blob Azure](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>Passos seguintes
Avançar para o tutorial seguinte: 

> [!div class="nextstepaction"]
>[Copiar dados de forma incremental de uma tabela na Base de Dados SQL do Azure para o armazenamento de Blobs do Azure](tutorial-incremental-copy-powershell.md)
