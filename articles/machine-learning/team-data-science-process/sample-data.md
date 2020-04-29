---
title: Dados da amostra em diferentes localizações de Armazenamento Azure - Processo de Ciência de Dados da Equipa
description: Amostra de dados em recipientes de blob Azure, SQL Server e tabelas hive para reduzi-lo a um tamanho menor, mas representativo e mais manejável.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76718608"
---
# <a name="sample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>Dados de exemplo em contentores de blobs do Azure, no SQL Server e em tabelas do Hive

Os seguintes artigos descrevem como recolher dados armazenados num dos três locais diferentes do Azure:

* Os dados do [**contentor de blob azure**](sample-data-blob.md) são amostrados descarregando-os programáticamente e, em seguida, amostrando-os com o código Python da amostra.
* [**Os dados do SQL Server**](sample-data-sql-server.md) são amostrados utilizando tanto o SQL como o Python Programming Language. 
* [**Os dados da tabela da colmeia**](sample-data-hive.md) são amostrados usando consultas da Colmeia.

Esta tarefa de amostragem é um passo no Processo de Ciência de [Dados da Equipa (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

**Por que dados de amostra?**

Se o conjunto de dados que planeia analisar é grande, normalmente é uma boa ideia recolher os dados para reduzi-los a um tamanho menor, mas representativo e mais manejável. A redução do tamanho pode facilitar a compreensão de dados, a exploração e a engenharia de recursos. Esta função de amostragem no Processo de Análise cortana é permitir a prototipagem rápida das funções de processamento de dados e modelos de machine learning.

