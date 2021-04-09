---
title: In-Memory OLTP melhora SQL txn perf
description: Adote In-Memory OLTP para melhorar o desempenho transacional numa base de dados existente na Base de Dados Azure SQL e na Azure SQL Managed Instance.
services: sql-database
ms.service: sql-database
ms.custom: sqldbrb=2
ms.subservice: development
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: e17e98e784b7453c87814c5cce5c03568f66b1cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91619751"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-azure-sql-database-and-azure-sql-managed-instance"></a>Utilize In-Memory OLTP para melhorar o desempenho da sua aplicação na Base de Dados Azure SQL e na Azure SQL Gestd Managing Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

[OLTP in-Memory](in-memory-oltp-overview.md) pode ser usado para melhorar o desempenho do processamento de transações, ingestão de dados e cenários transitórios de dados, em bases de dados [de nível Premium e Business Critical](database/service-tiers-vcore.md) sem aumentar o nível de preços.

> [!NOTE]
> Saiba como o [Qurum duplica a carga de trabalho da base de dados chave enquanto baixa a DTU em 70% com a Base de Dados Azure SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

Siga estes passos para adotar In-Memory OLTP na sua base de dados existente.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Passo 1: Certifique-se de que está a utilizar uma base de dados de nível Premium e Business Critical

In-Memory OLTP é suportado apenas em bases de dados de nível Premium e Business Critical. In-Memory é suportado se o resultado devolvido for 1 (não 0):

```sql
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* significa *processamento de transações extremas*

## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Passo 2: Identificar objetos para migrar para In-Memory OLTP

O SSMS inclui um relatório **geral de análise de desempenho de transação** que pode correr contra uma base de dados com uma carga de trabalho ativa. O relatório identifica tabelas e procedimentos armazenados que são candidatos à migração para In-Memory OLTP.

Na SSMS, para gerar o relatório:

* No **Object Explorer,** clique com o botão direito no nó de base de dados.
* Clique **em Relatórios**  >  **Relatórios Relatórios**  >  **Padrão Relatórios Análise de Desempenho de Transação**.

Para obter mais informações, consulte [a determinação de uma tabela ou procedimento armazenado para In-Memory OLTP](/sql/relational-databases/in-memory-oltp/determining-if-a-table-or-stored-procedure-should-be-ported-to-in-memory-oltp).

## <a name="step-3-create-a-comparable-test-database"></a>Passo 3: Criar uma base de dados de teste comparável

Suponha que o relatório indique que a sua base de dados tem uma tabela que beneficiaria de ser convertida para uma tabela otimizada para a memória. Recomendamos que faça o primeiro teste para confirmar a indicação através do teste.

Precisa de uma cópia da sua base de dados de produção. A base de dados de teste deve estar no mesmo nível de serviço que a sua base de dados de produção.

Para facilitar os testes, ajuste a sua base de dados de teste da seguinte forma:

1. Ligue-se à base de dados de teste utilizando SSMS.
2. Para evitar a necessidade da opção COM (SNAPSHOT) em consultas, desa esta medida de trabalho como indicado na seguinte declaração T-SQL:

   ```sql
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Passo 4: Mesas de migração

Tem de criar e preencher uma cópia otimizada da tabela que pretende testar. Pode criá-lo utilizando:

* O assistente de otimização de memória útil em SSMS.
* Manual T-SQL.

### <a name="memory-optimization-wizard-in-ssms"></a>Assistente de otimização de memória em SSMS

Para utilizar esta opção de migração:

1. Ligue-se à base de dados de teste com o SSMS.
2. No **Object Explorer**, clique à direita na tabela e, em seguida, clique no **Aviso de Otimização de Memória**.

   É apresentado o assistente **'Orientador de Optimizador de Memória de Tabela'.**
3. No assistente, clique na **validação de migração** (ou no botão **Seguinte)** para ver se a tabela tem alguma funcionalidade não suportada que não esteja suportada em tabelas otimizadas para a memória. Para obter mais informações, consulte:

   * A *lista de verificação de otimização de memória* no Aviso de [Otimização de Memória](/sql/relational-databases/in-memory-oltp/memory-optimization-advisor).
   * [Construções Transact-SQL Não suportadas por In-Memory OLTP](/sql/relational-databases/in-memory-oltp/transact-sql-constructs-not-supported-by-in-memory-oltp).
   * [Migrando para In-Memory OLTP](/sql/relational-databases/in-memory-oltp/plan-your-adoption-of-in-memory-oltp-features-in-sql-server).
4. Se a tabela não tiver funcionalidades não suportadas, o conselheiro pode realizar o esquema real e a migração de dados para si.

### <a name="manual-t-sql"></a>Manual T-SQL

Para utilizar esta opção de migração:

1. Ligue-se à sua base de dados de teste utilizando SSMS (ou um utilitário semelhante).
2. Obtenha o roteiro T-SQL completo para a sua tabela e os seus índices.

   * Em SSMS, clique com o botão direito do nó de mesa.
   * Clique na **tabela de scripts como** CREATE para  >  **nova** janela de  >  **consulta**.
3. Na janela do script, adicione COM (MEMORY_OPTIMIZED = ON) à declaração DE TABELA CREATE.
4. Se houver um índice CLUSTERED, altere-o para NÃO-DETIDO.
5. Mude o nome da tabela existente utilizando SP_RENAME.
6. Crie a nova cópia otimizada da tabela, executando o seu script DE TABELA CREATE editado.
7. Copie os dados para a sua tabela otimizada para a memória utilizando o INSERT... SELECIONE * EM:

```sql
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```

## <a name="step-5-optional-migrate-stored-procedures"></a>Passo 5 (opcional): Migrar procedimentos armazenados

A funcionalidade In-Memory também pode modificar um procedimento armazenado para melhorar o desempenho.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Considerações com procedimentos armazenados nativamente compilados

Um procedimento armazenado nativamente compilado deve ter as seguintes opções na sua cláusula T-SQL COM:

* NATIVE_COMPILATION
* SCHEMABINDING: ou seja, tabelas que o procedimento armazenado não pode alterar as suas definições de coluna de qualquer forma que afete o procedimento armazenado, a menos que deixe cair o procedimento armazenado.

Um módulo nativo deve usar um grande [bloco ATOMIC](/sql/relational-databases/in-memory-oltp/atomic-blocks-in-native-procedures) para a gestão de transações. Não existe qualquer papel para uma TRANSAÇÃO DE INÍCIO explícita, ou para TRANSAÇÃO ROLLBACK. Se o seu código detetar uma violação de uma regra comercial, pode terminar o bloco atómico com uma declaração [DE LANÇAMENTO.](/sql/t-sql/language-elements/throw-transact-sql)

### <a name="typical-create-procedure-for-natively-compiled"></a>Procedimento típico de CRIA para compilados nativo

Tipicamente, o T-SQL para criar um procedimento armazenado nativamente compilado é semelhante ao seguinte modelo:

```sql
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

* Para o TRANSACTION_ISOLATION_LEVEL, o SNAPSHOT é o valor mais comum para o procedimento armazenado de forma nativa. No entanto, um subconjunto dos outros valores também é suportado:
  
  * LEITURA REPETÍVEL
  * SERIALIZÁVEL
* O valor da LÍNGUA deve estar presente na visão sys.languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Como migrar um procedimento armazenado

As etapas de migração são:

1. Obtenha o script CREATE PROCEDURE para o procedimento regular interpretado armazenado.
2. Reescreva o seu cabeçalho para corresponder ao modelo anterior.
3. Verificar se o procedimento armazenado código T-SQL utiliza quaisquer funcionalidades que não sejam suportadas para procedimentos armazenados de forma nativa. Implementar soluções alternativas, se necessário.

   Para mais detalhes consulte [as questões de migração para procedimentos armazenados de forma nativa.](/sql/relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables)
4. Mude o nome do antigo procedimento armazenado utilizando SP_RENAME. Ou simplesmente deixá-lo.
5. Execute o seu script DE PROCEDIMENTO DE CRIA EDITADO T-SQL.

## <a name="step-6-run-your-workload-in-test"></a>Passo 6: Executar a sua carga de trabalho em teste

Executa uma carga de trabalho na base de dados de testes semelhante à carga de trabalho que funciona na sua base de dados de produção. Isto deve revelar o ganho de desempenho alcançado pela utilização da funcionalidade In-Memory para tabelas e procedimentos armazenados.

Os principais atributos da carga de trabalho são:

* Número de ligações simultâneas.
* Relação de leitura/escrita.

Para personalizar e executar a carga de trabalho de teste, considere usar a `ostress.exe` ferramenta útil, que ilustra neste artigo [de memória.](in-memory-oltp-overview.md)

Para minimizar a latência da rede, escorrida o seu teste na mesma região geográfica do Azure onde existe a base de dados.

## <a name="step-7-post-implementation-monitoring"></a>Passo 7: Monitorização pós-implementação

Considere monitorizar os efeitos de desempenho das suas implementações In-Memory na produção:

* [Monitor In-Memory armazenamento](in-memory-oltp-monitor-space.md).
* [Monitorização utilizando pontos de vista dinâmicos de gestão](database/monitoring-with-dmvs.md)

## <a name="related-links"></a>Ligações relacionadas

* [OLTP in-memory (otimização na memória)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
* [Introdução aos Procedimentos Armazenados Compilados Nativos](/sql/relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables)
* [Consultor de Otimização de Memória](/sql/relational-databases/in-memory-oltp/memory-optimization-advisor)
