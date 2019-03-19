---
title: Notas de versão de armazém de dados SQL do Azure, Agosto de 2018 | Documentos da Microsoft
description: Notas de versão do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/13/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 9547380d4636c1a72add9dab7a9eed850abd0307
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57889714"
---
# <a name="whats-new-in-azure-sql-data-warehouse-august-2018"></a>O que há de novo no Azure SQL Data Warehouse? Agosto de 2018
O Azure SQL Data Warehouse recebe melhorias continuamente. Este artigo descreve os novos recursos e alterações que foram introduzidas em Agosto de 2018.

## <a name="automatic-intelligent-insights"></a>Informações inteligentes automática
A Microsoft introduziu [automática informações inteligentes](https://azure.microsoft.com/blog/automatic-intelligent-insights-to-optimize-performance-with-sql-data-warehouse/) para fornecer a promessa de cloud de automatização para o seu armazém de dados. Já não terá de monitorizar o seu armazém de dados de estatísticas de tabela distorção e inferior ao ideal de dados. Sem custos adicionais, o SQL Data Warehouse apresenta informações inteligentes para todas as instâncias de geração 2. Integrando [do Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), automaticamente pode receber recomendações de melhores práticas para melhorar o desempenho das cargas de trabalho ativas. O SQL Data Warehouse analisa suas recomendações de carga de trabalho e superfícies com base na sua utilização. Esta análise ocorre diária que lhe permite monitorizar os relatórios de utilização e recomendações de melhorias à sua carga de trabalho.

Pode ver as recomendações no portal do Assistente do Azure: ![Recomendações de Portal de assistente do Azure para o armazém de dados SQL do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/4e205b6d-df04-48db-8eec-d591f2592cf4.png)

Pode explorar cada categoria para ver as recomendações para o alerta específico: ![Detalhes de recomendação do Portal do Assistente do Azure para o armazém de dados SQL do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/3c42426e-6969-46e3-9025-c34c0755a302.png)


## <a name="bug-fixes"></a>Correções de erros

| Título | Descrição |
|:---|:---|
| **Potenciais falhas de consulta quando a contagem de dividir excede o limite máximo** |Quando o limite de divisão de ficheiro de 1 milhão de limite for excedido uma exceção não tratada causou o motor do SQL Despejar e todas as consultas de falha. Esta correção de resolver o problema ao processar a exceção corretamente e devolver um erro sem causar a falha das consultas. |
| **Valor de predefinição ExternalMoveReadersPerNode maior para melhorar o desempenho de carga** |Este problema foi causado pela definição de propriedade ExternalMoveReadersPerNode a ser sincronizada com o definição do service fabric. Este regressão causado uma geração 2 degradação do desempenho de carga. A correção oferece desempenho de carregamento de geração 2 novamente dentro de parâmetros de design otimizadas.|


## <a name="next-steps"></a>Passos Seguintes
Agora que já sabe um pouco sobre o SQL Data Warehouse, saiba como rapidamente [criar um SQL Data Warehouse][create a SQL Data Warehouse]. Se não estiver familiarizado com o Azure, poderá achar útil o [Glossário do Azure][Azure glossary] quando se deparar com terminologia nova. Em alternativa, veja alguns destes outros Recursos do SQL Data Warehouse.  

* [Histórias de sucesso de clientes]
* [Blogues]
* [Pedidos de funcionalidades]
* [Vídeos]
* [Blogues da Equipa Customer Advisory]
* [Fórum do Stack Overflow]
* [Twitter]


[Blogues]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogues da Equipa Customer Advisory]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Histórias de sucesso de clientes]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Pedidos de funcionalidades]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
