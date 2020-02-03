---
title: Dados da amostra em diferentes localizações de Armazenamento Azure - Processo de Ciência de Dados da Equipa
description: Dados de exemplo no Azure, contentores, SQL Server, de BLOBs e tabelas para reduzi-lo para um tamanho mais pequeno, mas representativo e mais gerenciável do Hive.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718608"
---
# <a name="heading"></a>Dados da amostra em recipientes de blob Azure, Servidor SQL e tabelas de colmeias

Os seguintes artigos descrevem como dados de exemplo que são armazenados em um dos três diferentes localizações do Azure:

* Os dados do [**contentor de blob azure**](sample-data-blob.md) são amostrados descarregando-os programáticamente e, em seguida, amostrando-os com o código Python da amostra.
* [**Os dados do SQL Server**](sample-data-sql-server.md) são amostrados utilizando tanto o SQL como o Python Programming Language. 
* [**Os dados da tabela da colmeia**](sample-data-hive.md) são amostrados usando consultas da Colmeia.

Esta tarefa de amostragem é um passo no Processo de Ciência de [Dados da Equipa (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

**Por que dados de amostra?**

Se o conjunto de dados que pretende analisar for grande, normalmente, é uma boa idéia para dimensionar os dados para reduzi-lo para um tamanho mais pequeno, mas representativo e mais gerenciável. A redução do tamanho pode facilitar a compreensão de dados, a exploração e a engenharia de recursos. Esta função de amostragem no Processo de Análise cortana é permitir a prototipagem rápida das funções de processamento de dados e modelos de machine learning.

