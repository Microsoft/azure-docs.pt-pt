---
title: Dados da amostra em diferentes locais de armazenamento do Azure - Processo de Ciência de Dados de Equipa
description: Os dados da amostra em recipientes blob Azure, SQL Server e Hive para reduzi-lo a um tamanho menor, mas representativo e mais manejável.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 22e91d50227fcb44c7b90478d76379c14161ae05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76718608"
---
# <a name="sample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>Dados de exemplo em contentores de blobs do Azure, no SQL Server e em tabelas do Hive

Os seguintes artigos descrevem como recolher dados que são armazenados num dos três locais Azure diferentes:

* [**Os dados do recipiente azure blob**](sample-data-blob.md) são amostrados descarregando-os programáticamente e, em seguida, amostrando-os com o código Python da amostra.
* [**Os dados do SQL Server**](sample-data-sql-server.md) são recolhidos utilizando tanto o SQL como a Linguagem de Programação Python. 
* [**Os dados da tabela da colmeia**](sample-data-hive.md) são recolhidos usando consultas de Colmeia.

Esta tarefa de amostragem é um passo no Processo de Ciência de Dados de [Equipa (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

**Por que amostra de dados?**

Se o conjunto de dados que pretende analisar é grande, é geralmente uma boa ideia reduzir a amostragem dos dados para reduzi-los a um tamanho menor, mas representativo e mais manejável. A redução pode facilitar a compreensão de dados, a exploração e a engenharia de recursos. Esta função de amostragem no Processo de Análise cortana é para permitir uma prototipagem rápida das funções de processamento de dados e modelos de machine learning.

