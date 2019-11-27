---
title: 'Copiar dados incrementalmente usando Azure Data Factory '
description: Estes tutoriais mostram-lhe como copiar dados de forma incremental de um arquivo de dados de origem para um arquivo de dados de destino. O primeiro copia dados a partir de uma tabela.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: 42dcd6ecc6df1c9877581d89bf22724054e305d0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217276"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Carrega dados de forma incremental a partir de um arquivo de dados de origem para um arquivo de dados de destino

Uma solução de integração de dados, que carrega dados incrementalmente (ou delta) após um carregamento de dados completos inicial é um cenário bastante utilizado. Os tutoriais nesta secção mostram formas diferentes de carregar dados de forma incremental através da utilização do Azure Data Factory.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Carregamento de dados Delta do banco usando uma marca d' água
Neste caso, é possível definir uma marca d'água na base de dados de origem. Uma marca d'água é uma coluna com o último carimbo de data/hora atualizado ou uma chave de incrementação. A solução de carregamento delta carrega os dados alterados entre uma marca d'água antiga e uma nova marca d'água. O fluxo de trabalho para esta abordagem é descrito no diagrama seguinte: 

![Fluxo de trabalho para a utilização de uma marca d'água](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Para obter instruções passo a passo, veja os seguintes tutoriais: 
- [Copiar dados de forma incremental de uma tabela na Base de Dados SQL do Azure para o armazenamento de Blobs do Azure](tutorial-incremental-copy-powershell.md)
- [Copiar dados de forma incremental a partir de várias tabelas no SQL Server no local para a Base de Dados SQL do Azure](tutorial-incremental-copy-multiple-tables-powershell.md)

Para modelos, consulte o seguinte:
- [Cópia Delta com tabela de controle](solution-template-delta-copy-with-control-table.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Carregamento de dados Delta do BD SQL usando a tecnologia Controle de Alterações
A tecnologia de Controlo de Alterações é uma solução simples no SQL Server e na Base de Dados SQL do Azure que fornece um mecanismo de controlo de alterações eficiente para aplicações. Permite que uma aplicação facilmente identifique dados que foram inseridos, atualizados ou eliminados. 

O fluxo de trabalho para esta abordagem é descrito no diagrama seguinte:

![Fluxo de trabalho para utilizar o Controlo de Alterações](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Para obter instruções passo a passo, veja o seguinte tutorial: <br/>
- [Copiar dados de forma incremental da Base de Dados SQL do Azure para o armazenamento de Blobs do Azure utilizando a tecnologia de Controlo de Alterações](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Carregando somente arquivos novos e alterados usando LastModifiedDate
Você pode copiar os arquivos novos e alterados somente usando LastModifiedDate para o repositório de destino. O ADF examinará todos os arquivos do repositório de origem, aplicará o filtro de arquivo por seus LastModifiedDate e copiará apenas o arquivo novo e atualizado desde a última vez para o repositório de destino.  Lembre-se de que, se você deixar o ADF digitalizar grandes quantidades de arquivos, mas copiar apenas alguns arquivos para o destino, você ainda esperará que a longa duração devido à verificação de arquivos também seja demorada.   

Para obter instruções passo a passo, veja o seguinte tutorial: <br/>
- [Copiar arquivos novos e alterados incrementalmente com base em LastModifiedDate do armazenamento de BLOBs do Azure para o armazenamento de BLOBs do Azure](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

Para modelos, consulte o seguinte:
- [Copiar novos arquivos por LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Carregar novos arquivos somente usando a pasta ou o nome do arquivo particionado.
Você só pode copiar novos arquivos, onde os arquivos ou pastas já foram particionados com informações da fatia do tempo como parte do nome do arquivo ou da pasta (por exemplo,/yyyy/mm/dd/File.csv). É a abordagem de melhor desempenho para carregar incrementalmente novos arquivos. 

Para obter instruções passo a passo, veja o seguinte tutorial: <br/>
- [Copiar incrementalmente novos arquivos com base na pasta particionada do tempo ou no nome do arquivo do armazenamento de BLOBs do Azure para o armazenamento de BLOBs do Azure](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>Passos seguintes
Avançar para o tutorial seguinte: 

> [!div class="nextstepaction"]
>[Copiar dados de forma incremental de uma tabela na Base de Dados SQL do Azure para o armazenamento de Blobs do Azure](tutorial-incremental-copy-powershell.md)
