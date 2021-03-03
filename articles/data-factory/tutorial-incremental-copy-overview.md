---
title: Copie gradualmente os dados de uma loja de dados de origem para uma loja de dados de destino
description: Estes tutoriais mostram-lhe como copiar dados de forma incremental de um arquivo de dados de origem para um arquivo de dados de destino. O primeiro copia dados a partir de uma tabela.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 7161fb30c8b445681b4cd577d8f8ac9fff5106df
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739250"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Carrega dados de forma incremental a partir de um arquivo de dados de origem para um arquivo de dados de destino

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Uma solução de integração de dados, que carrega dados incrementalmente (ou delta) após um carregamento de dados completos inicial é um cenário bastante utilizado. Os tutoriais nesta secção mostram formas diferentes de carregar dados de forma incremental através da utilização do Azure Data Factory.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Carregamento de dados delta da base de dados usando uma marca de água
Neste caso, é possível definir uma marca d'água na base de dados de origem. Uma marca d'água é uma coluna com o último carimbo de data/hora atualizado ou uma chave de incrementação. A solução de carregamento delta carrega os dados alterados entre uma marca d'água antiga e uma nova marca d'água. O fluxo de trabalho para esta abordagem é descrito no diagrama seguinte: 

![Fluxo de trabalho para a utilização de uma marca d'água](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Para obter instruções passo a passo, veja os seguintes tutoriais: 
- [Copiar dados de forma incremental de uma tabela na Base de Dados SQL do Azure para o armazenamento de Blobs do Azure](tutorial-incremental-copy-powershell.md)
- [Copie gradualmente dados de várias tabelas numa instância do SQL Server para Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)

Para modelos, consulte o seguinte:
- [Cópia delta com a tabela de controlo](solution-template-delta-copy-with-control-table.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Carregamento de dados delta da SQL DB utilizando a tecnologia Change Tracking
A tecnologia de Controlo de Alterações é uma solução simples no SQL Server e na Base de Dados SQL do Azure que fornece um mecanismo de controlo de alterações eficiente para aplicações. Permite que uma aplicação facilmente identifique dados que foram inseridos, atualizados ou eliminados. 

O fluxo de trabalho para esta abordagem é descrito no diagrama seguinte:

![Fluxo de trabalho para utilizar o Controlo de Alterações](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Para obter instruções passo a passo, veja o seguinte tutorial: <br/>
- [Copiar dados de forma incremental da Base de Dados SQL do Azure para o armazenamento de Blobs do Azure utilizando a tecnologia de Controlo de Alterações](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Carregar ficheiros novos e alterados apenas utilizando o LastModifiedDate
Só pode copiar os ficheiros novos e alterados utilizando o LastModifiedDate para a loja de destino. A ADF irá digitalizar todos os ficheiros da loja de origem, aplicar o filtro de ficheiros através do seu Último Instante Modificado, e apenas copiar o novo e atualizado ficheiro desde a última vez na loja de destino.  Por favor, esteja ciente se deixar a ADF digitalizar grandes quantidades de ficheiros mas apenas copiar alguns ficheiros para o destino, ainda esperaria que a longa duração devido à digitalização do ficheiro também fosse demorada.   

Para obter instruções passo a passo, veja o seguinte tutorial: <br/>
- [Copie gradualmente ficheiros novos e alterados com base no LastModifiedDate do armazenamento Azure Blob para o armazenamento Azure Blob](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

Para modelos, consulte o seguinte:
- [Copiar novos ficheiros por DataÚltimaModificação](solution-template-copy-new-files-lastmodifieddate.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Carregar novos ficheiros apenas utilizando a pasta divisória do tempo ou o nome do ficheiro.
Só pode copiar novos ficheiros, onde os ficheiros ou pastas já foram divididos com informações de cronometrs como parte do ficheiro ou nome da pasta (por exemplo, /yyyy/mm/dd/file.csv). É a abordagem mais performante para carregar gradualmente novos ficheiros. 

Para obter instruções passo a passo, veja o seguinte tutorial: <br/>
- [Copie gradualmente novos ficheiros com base na pasta ou nome de ficheiros partitionados de tempo do armazenamento de Azure Blob para o armazenamento de Azure Blob](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>Passos seguintes
Avançar para o tutorial seguinte: 

> [!div class="nextstepaction"]
>[Copiar dados de forma incremental de uma tabela na Base de Dados SQL do Azure para o armazenamento de Blobs do Azure](tutorial-incremental-copy-powershell.md)
