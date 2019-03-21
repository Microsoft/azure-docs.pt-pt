---
title: Notas de versão de armazém de dados SQL do Azure, Setembro de 2018 | Documentos da Microsoft
description: Notas de versão do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/08/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: bc559a1224aace2ee599c24c8dce07a6d55173fd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863717"
---
# <a name="whats-new-in-azure-sql-data-warehouse-september-2018"></a>O que há de novo no Azure SQL Data Warehouse? Setembro de 2018
O Azure SQL Data Warehouse recebe melhorias continuamente. Este artigo descreve os novos recursos e alterações que foram introduzidas em Setembro de 2018.

## <a name="new-lower-entry-point-for-sql-data-warehouse-gen2"></a>Novo ponto de entrada inferior para o SQL Data Warehouse Gen2
Em Abril de 2018 [Microsoft anunciada](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) Azure SQL Data Warehouse Gen2 que oferece 5 vezes o desempenho, 5x a escala de computação, 4 x a simultaneidade e o armazenamento ilimitado. Conforme observado na [armazém de dados na cloud Benchmark](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) por Gigaom, o SQL Data Warehouse Gen2 **supera o Amazon Redshift em 42%**.

Geração 2 agora está disponível em geral num menor entrada ponto de DWU500c permitindo-lhe gerir um ambientes de menor tamanho data warehouse ou de programador/teste com todos os aprimoramentos mais recentes do serviço. Ponto de entrada novo retém todos os recursos de geração 2, incluindo [colocação em cache adaptável](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/), [iluminação rapidamente dados descarte](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)e suporte para [armazém de dados em tempo real](https://azure.microsoft.com/blog/enabling-real-time-data-warehousing-with-azure-sql-data-warehouse/).

## <a name="sql-vulnerability-assessment"></a>Avaliação de Vulnerabilidades do SQL
[Avaliação de vulnerabilidade de SQL (VA)](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/25/sql-vulnerability-assessment-now-supports-azure-sql-data-warehouse-and-azure-sql-database-managed-instance/) é um serviço de fácil de usar que monitoriza continuamente o seu armazém de dados. Ele ajuda a garantir um elevado nível de segurança durante todo o tempo e que as políticas organizacionais são cumpridas. Ele fornece um relatório de segurança abrangente, juntamente com passos de remediação passíveis de ação para cada problema encontrado. Este relatório torna mais fácil para que possa gerir o escritor de segurança da base de dados e concentrar sua atenção as ações de impacto mais elevadas, de forma proativa, mesmo se não tiver uma segurança especializada. Para ambientes dinâmicos em que as alterações são frequentes e difícil de controlar, VA é imprescindível detetar as definições que podem deixar o seu armazém de dados vulnerável a ataques.

## <a name="improved-availability-with-query-restartability"></a>Maior disponibilidade, com capacidade de reinicialização de consulta
Durante a execução da consulta, pode ocorrer qualquer número de problemas que pode fazer com que uma consulta a falhar. Uma interrupção na rede, uma falha de hardware ou outra desativação pode causar uma interrupção. O SQL Data Warehouse suporta agora a capacidade de reinicialização de consulta para o passo ou consultas de nível de instrução SELECT. 

Com capacidade de reinicialização de consulta, uma consulta que já em andamento é interrompido devido a uma falha será reiniciado automaticamente. Dependendo do número de passos, a forma da consulta, ou quando a consulta foi interrompida durante a execução, o motor do SQL Data Warehouse também irá reiniciar a consulta completa ou será retomada a partir do último passo de consulta concluída. Do ponto de vista do utilizador final, a consulta apenas é concluída. 

## <a name="maintenance-scheduling-preview"></a>Manutenção agendamento (pré-visualização)
O Azure SQL Data Warehouse manutenção de agendamento está agora em pré-visualização. Esta nova funcionalidade de forma totalmente integrada integra-se as notificações de manutenção planeada do serviço de estado de funcionamento do Monitor de verificação de estado de funcionamento de recursos e o serviço de agendamento de manutenção do Azure SQL Data Warehouse. Agendamento de manutenção permite agendar uma janela de tempo quando é conveniente receber as novas funcionalidades, atualizações e patches.

Agendamento de manutenção tira partido do Azure Monitor e permite aos clientes determinar como quiser ser notificado de uma iminente eventos de manutenção e deverá ser acionada automatizada que fluxos para gerir o tempo de inatividade e minimizar o impacto de suas operações. As notificações podem incluir um e-mail ou texto. 

## <a name="wide-row-support-in-polybase"></a>Suporte de linha ampla no Polybase
Ao carregar os dados para o SQL Data Warehouse, a orientação geral é usar [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading#options-for-loading-with-polybase) com é o suporte para o carregamento de dados paralelos. Esta versão ativa o suporte para colunas mais amplas de 32K para 1MB - permitindo-lhe carregar tabelas com tamanho grande de linha. O suporte para linhas ampla simplifica o processo para tabelas com grande de linhas de carregamento de dados.

> [!Note]
> O tamanho total de linhas não pode exceder 1 MB de tamanho.

## <a name="additional-language-support"></a>Suporte a idiomas adicionais
Esta versão introduz o suporte para os elementos de linguagem T-SQL seguintes:

### <a name="stringsplit"></a>STRING_SPLIT
O [STRING_SPLIT](https://docs.microsoft.com/sql/t-sql/functions/string-split-transact-sql) função divide uma cadeia de caracteres com o separador especificado. STRING_SPLIT é útil em cenários em que uma coluna pode ter vários valores para analisar e inserir em outra tabela de carregamento de dados.

#### <a name="example"></a>Exemplo
```sql
DECLARE @tags NVARCHAR(400) = 'clothing,road,,touring,bike';

SELECT
    value
FROM
    STRING_SPLIT(@tags, ',')
WHERE
    RTRIM(value) <> '';
```

### <a name="compressdecompress-functions"></a>Funções COMPRESS/execução
O [COMPRIMIR](https://docs.microsoft.com/sql/t-sql/functions/compress-transact-sql) / [sob](https://docs.microsoft.com/sql/t-sql/functions/decompress-transact-sql) funções permitem-lhe comprimir ou descomprimir uma cadeia de caracteres de entrada com o algoritmo GZIP.

#### <a name="example"></a>Exemplo

```sql
SELECT
    name [name_original]
    , COMPRESS(name) [name_compressed]
    , CAST(DECOMPRESS(COMPRESS(name)) AS NVARCHAR(MAX)) [name_decompressed]
FROM
    sys.objects;
```

### <a name="if-exists-clause-for-dropping-views"></a>Cláusula EXISTS IF para descartar a vistas
A adição da cláusula IF existe no [LARGAR vista](https://docs.microsoft.com/sql/t-sql/statements/drop-view-transact-sql) instrução simplifica o código de T-SQL necessário para remover uma vista do armazém de dados. A sintaxe se existe, quando aplicado a uma instrução DROP vista irá diminuir a exibição se existe ou se será ignorada, a instrução se a vista não existe.

#### <a name="example"></a>Exemplo
```sql
DROP VIEW IF EXISTS dbo.TestView;
```
```
Message
--------------------------------------------------
Commands completed successfully.

```

## <a name="improved-compilation-time-for-singleton-inserts-and-ddl-statements"></a>Tempo de compilação melhorada para inserções de Singleton e instruções DDL 
Seguindo um modelo de extração, transformação e carregamento (ETL) tradicional para inserção de dados, muitas vezes, leva para executar uma inserção de singleton ([inserir valores](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql)) numa tabela na base de dados. Esta versão melhora o desempenho de operações de inserção de singleton ao reduzir o tempo de compilação necessário para executar este tipo de instrução. Em alguns casos, a melhoria de compilação observado é até 3 vezes mais rápido. Este melhoramento irá diminuir o tempo necessário para executar uma instrução singleton insert. 

A melhoria também beneficia armazéns com um grande número de objetos, da mesma forma reduzindo o tempo de compilação de consulta para instruções de linguagem DDL (Data Definition), incluindo CREATE, ALTER e operações de eliminação de dados. 

Por fim, a melhoria reduz a execução geral de instruções executadas através de tabelas largas - tabelas que têm um grande número de colunas. O aprimoramento é uma redução no tempo em que estejam a etapa de compilação de consulta que reduz o tempo de execução geral para consultas.

## <a name="bug-fixes"></a>Correções de erros

| Título | Descrição |
|:---|:---|
| **Corrigir ao criar estatísticas em distribuições para as restrições exclusivas** | Esta correção resolve um erro que os utilizadores encontram quando em execução de ATUALIZAÇÃO de estatísticas, com apenas a tabela especificado, quando a tabela tivesse uma restrição exclusiva definida. |
| **Corrigir ao compilar a consultas em tabelas externas** | Esta correção resolve um defeito que afetados tempo de compilação para consultas que envolvem tabelas externas.|
| **Corrigir ao executar uma instrução com tipos grandes** | Resolver um defeito na compilação de instrução preparado com parâmetros declarados como um da *grandes* tipos (nvarchar (Max), varchar (Max) e varbinary(max)). |
| **Corrigir quando ocorre um erro para consultas profundamente aninhadas** | Fornece uma mensagem de erro claras quando uma consulta aninhada profundamente excede os limites de sistema.|
| **Corrigir erros de tempo de compilação quando uma instrução contém uma subconsulta correlacionada e uma constante de tempo de execução** |Aborda o erro de tempo de compilação para consultas com uma combinação específica de subconsultas correlacionadas e constantes de tempo de execução (como GETDATE()).|
| **Tempo limite de endereço para adquirir os bloqueios de objeto PDW e a ranhura de simultaneidade para autostats** |Correção adiciona o tempo limite de bloqueio para impedir que pedidos autostats bloquear os pedidos de origem durante muito tempo.|

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
