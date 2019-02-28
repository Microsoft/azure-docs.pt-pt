---
title: Copiar dados de forma incremental utilizando o Azure Data Factory | Microsoft Docs
description: Estes tutoriais mostram-lhe como copiar dados de forma incremental de um arquivo de dados de origem para um arquivo de dados de destino. O primeiro copia dados a partir de uma tabela.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: cb75e943416c227730589ab5e7feeb7b8ba5e245
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957934"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Carrega dados de forma incremental a partir de um arquivo de dados de origem para um arquivo de dados de destino

Uma solução de integração de dados, que carrega dados incrementalmente (ou delta) após um carregamento de dados completos inicial é um cenário bastante utilizado. Os tutoriais nesta secção mostram formas diferentes de carregar dados de forma incremental através da utilização do Azure Data Factory.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Carregamento da base de dados usando uma marca d'água de dados delta
Neste caso, é possível definir uma marca d'água na base de dados de origem. Uma marca d'água é uma coluna com o último carimbo de data/hora atualizado ou uma chave de incrementação. A solução de carregamento delta carrega os dados alterados entre uma marca d'água antiga e uma nova marca d'água. O fluxo de trabalho para esta abordagem é descrito no diagrama seguinte: 

![Fluxo de trabalho para a utilização de uma marca d'água](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Para obter instruções passo a passo, veja os seguintes tutoriais: 

- [Copiar dados de forma incremental de uma tabela na Base de Dados SQL do Azure para o armazenamento de Blobs do Azure](tutorial-incremental-copy-powershell.md)
- [Copiar dados de forma incremental a partir de várias tabelas no SQL Server no local para a Base de Dados SQL do Azure](tutorial-incremental-copy-multiple-tables-powershell.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Carregamento do SQL DB utilizando a tecnologia de controlo de alterações de dados delta
A tecnologia de Controlo de Alterações é uma solução simples no SQL Server e na Base de Dados SQL do Azure que fornece um mecanismo de controlo de alterações eficiente para aplicações. Permite que uma aplicação facilmente identifique dados que foram inseridos, atualizados ou eliminados. 

O fluxo de trabalho para esta abordagem é descrito no diagrama seguinte:

![Fluxo de trabalho para utilizar o Controlo de Alterações](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Para obter instruções passo a passo, veja o seguinte tutorial: <br/>
[Copiar dados de forma incremental da Base de Dados SQL do Azure para o armazenamento de Blobs do Azure utilizando a tecnologia de Controlo de Alterações](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>A carregar ficheiros de novos e alterados apenas através de LastModifiedDate
Pode obter os metadados (LastModifiedDate) dos seus ficheiros primeiro e, em seguida, copie os ficheiros novos e alterados apenas para o arquivo de destino.

Para obter instruções passo a passo, veja o seguinte tutorial: <br/>
[Copiar ficheiros novos e alterados com base em LastModifiedDate do armazenamento de Blobs do Azure para o armazenamento de Blobs do Azure de forma incremental](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>A carregar novos ficheiros utilizando apenas a hora particionados de nome de ficheiro ou pasta.
Pode copiar ficheiros novos apenas, onde ficheiros ou pastas já foi particionado com informações de timeslice como parte do nome do ficheiro ou pasta (por exemplo, /yyyy/mm/dd/file.csv) de tempo. 

Para obter instruções passo a passo, veja o seguinte tutorial: <br/>
[Copiar novos ficheiros com base na pasta particionada de tempo ou nome de ficheiro do armazenamento de Blobs do Azure para o armazenamento de Blobs do Azure de forma incremental](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>Passos Seguintes
Avançar para o tutorial seguinte: 

> [!div class="nextstepaction"]
>[Copiar dados de forma incremental de uma tabela na Base de Dados SQL do Azure para o armazenamento de Blobs do Azure](tutorial-incremental-copy-powershell.md)
