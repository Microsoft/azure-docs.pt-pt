---
title: In-Memory OLTP melhora SQL txn perf
description: Adote o OLTP in-memory para melhorar o desempenho transacional numa base de dados SQL existente.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: 653ed75341d5d56ecbe06cb59f0efafa1e68aa0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067274"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>Utilize o OLTP in-memory para melhorar o desempenho da sua aplicação na Base de Dados SQL

[O OLTP em Memória](sql-database-in-memory.md) pode ser usado para melhorar o desempenho do processamento de transações, ingestão de dados e cenários transitórios de dados, nas bases de dados [premium e business critical](sql-database-service-tiers-vcore.md) sem aumentar o nível de preços. 

> [!NOTE] 
> Saiba como o Quorum duplica a carga de trabalho da base de dados chave [enquanto baixa o DTU em 70% com a Base de Dados SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Siga estes passos para adotar o OLTP in-memory na sua base de dados existente.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Passo 1: Certifique-se de que está a utilizar uma base de dados premium e de nível crítico de negócios

In-Memory OLTP é suportado apenas nas bases de dados premium e business critical tier. In-Memory é suportado se o resultado devolvido for 1 (não 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* significa *processamento de transações extremas*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Passo 2: Identificar objetos para migrar para OLTP em memória
O SSMS inclui um relatório de visão geral de análise de desempenho de **transações** que pode ser executado contra uma base de dados com uma carga de trabalho ativa. O relatório identifica tabelas e procedimentos armazenados que são candidatos à migração para OLTP in-memory.

No SSMS, para gerar o relatório:

* No **Object Explorer,** clique no nó da base de dados.
* Clique em **relatórios** > **padrão relatórios** > de análise de desempenho de**transações**.

Para mais informações, consulte [se uma tabela ou procedimento armazenado deve ser portado para OLTP em memória](https://msdn.microsoft.com/library/dn205133.aspx).

## <a name="step-3-create-a-comparable-test-database"></a>Passo 3: Criar uma base de dados de teste comparável
Suponha que o relatório indique que a sua base de dados tem uma tabela que beneficiaria de ser convertida numa tabela otimizada pela memória. Recomendamos que você primeiro teste para confirmar a indicação por teste.

Precisa de uma cópia de teste da sua base de dados de produção. A base de dados de teste deve estar ao mesmo nível de nível de serviço que a sua base de dados de produção.

Para facilitar os testes, ajuste a sua base de dados de teste da seguinte forma:

1. Ligue-se à base de dados de teste utilizando SSMS.
2. Para evitar a necessidade da opção WITH (SNAPSHOT) em consultas, detete a opção base de dados como mostrado na seguinte declaração T-SQL:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Passo 4: Mesas de migração
Deve criar e povoar uma cópia otimizada pela memória da tabela que pretende testar. Pode criá-lo usando qualquer um dos dois:

* O útil Assistente de Otimização de Memória em SSMS.
* T-SQL manual.

#### <a name="memory-optimization-wizard-in-ssms"></a>Assistente de Otimização de Memória em SSMS
Para utilizar esta opção de migração:

1. Ligue-se à base de dados de teste com SSMS.
2. No **Object Explorer,** clique à direita na tabela e, em seguida, clique em **Memory Optimization Advisor**.
   
   * O assistente **do orientador** de memória de mesa é apresentado.
3. No assistente, clique na **validação da migração** (ou no botão **Seguinte)** para ver se a tabela tem alguma funcionalidade não suportada que não seja suportada em tabelas otimizadas pela memória. Para obter mais informações, consulte:
   
   * A *lista de verificação* de otimização da memória no Aviso de [Otimização](https://msdn.microsoft.com/library/dn284308.aspx)da Memória .
   * [Construções Transact-SQL não suportadas por OLTP em Memória](https://msdn.microsoft.com/library/dn246937.aspx).
   * [Migração para OLTP em Memória.](https://msdn.microsoft.com/library/dn247639.aspx)
4. Se a tabela não tiver funcionalidades não suportadas, o consultor pode realizar o esquema real e a migração de dados para si.

#### <a name="manual-t-sql"></a>Manual T-SQL
Para utilizar esta opção de migração:

1. Ligue-se à sua base de dados de teste utilizando SSMS (ou um utilitário semelhante).
2. Obtenha o script T-SQL completo para a sua tabela e os seus índices.
   
   * No SSMS, clique no nó da mesa.
   * Clique na **tabela de scripts como** > **criar para** > **uma nova janela de consulta**.
3. Na janela do script, adicione COM (MEMORY_OPTIMIZED = ON) à declaração create TABLE.
4. Se houver um índice clustered, altere-o para não agrupada.
5. Mude o nome da tabela existente utilizando SP_RENAME.
6. Crie a nova cópia otimizada pela memória da tabela executando o seu script CREATE TABLE editado.
7. Copie os dados para a sua tabela otimizada pela memória utilizando o INSERT... SELECIONE * EM:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Passo 5 (opcional): Procedimentos armazenados de migração
A função In-Memory também pode modificar um procedimento armazenado para um melhor desempenho.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Considerações com procedimentos armazenados com compilados de forma nativa
Um procedimento armazenado com compilado de forma nativa deve ter as seguintes opções na sua cláusula T-SQL WITH:

* NATIVE_COMPILATION
* SCHEMABINDING: significa ndo tabelas que o procedimento armazenado não pode ter as suas definições de coluna alteradas de qualquer forma que afetem o procedimento armazenado, a menos que deixe cair o procedimento armazenado.

Um módulo nativo deve usar um grande [bloco ATÓMICO](https://msdn.microsoft.com/library/dn452281.aspx) para gestão de transações. Não existe qualquer papel para uma transação de INÍCIO explícita, ou para transações de rollback. Se o seu código detetar uma violação de uma regra de negócio, pode encerrar o bloco atómico com uma declaração de [THROW.](https://msdn.microsoft.com/library/ee677615.aspx)

### <a name="typical-create-procedure-for-natively-compiled"></a>Procedimento típico de CRIAÇÃO para compilado de forma nativa
Tipicamente, o T-SQL para criar um procedimento armazenado com compilado nativo é semelhante ao seguinte modelo:

```
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

* Para o TRANSACTION_ISOLATION_LEVEL, o SNAPSHOT é o valor mais comum para o procedimento armazenado de forma nativa. No entanto, é também apoiado um subconjunto dos outros valores:
  
  * LEITURA REPETÍVEL
  * SERIALIZÁVEL
* O valor linguístico deve estar presente na visão das línguas.

### <a name="how-to-migrate-a-stored-procedure"></a>Como migrar um procedimento armazenado
Os passos de migração são:

1. Obtenha o script CREATE PROCEDURE ao procedimento regular de conservação interpretado.
2. Reescreva o cabeçalho para combinar com o modelo anterior.
3. Verifique se o código T-SQL do procedimento armazenado utiliza quaisquer funcionalidades que não sejam suportadas para procedimentos armazenados com compilados de forma nativa. Implementar suposições, se necessário.
   
   * Para mais detalhes consulte [questões de migração para procedimentos armazenados com compilados de forma nativa.](https://msdn.microsoft.com/library/dn296678.aspx)
4. Mude o nome do antigo procedimento armazenado utilizando SP_RENAME. Ou simplesmente drop-lo.
5. Execute o seu script T-SQL editado CREATE PROCEDURE.

## <a name="step-6-run-your-workload-in-test"></a>Passo 6: Executar a sua carga de trabalho em teste
Execute uma carga de trabalho na sua base de dados de teste semelhante à carga de trabalho que corre na sua base de dados de produção. Isto deve revelar o ganho de desempenho obtido pela utilização da funcionalidade In-Memory para tabelas e procedimentos armazenados.

Os principais atributos da carga de trabalho são:

* Número de ligações simultâneas.
* Relação leitura/escrita.

Para personalizar e executar a carga de trabalho de teste, considere usar a ferramenta ostress.exe útil, que ilustraaqui [.](sql-database-in-memory.md)

Para minimizar a latência da rede, ecorra o seu teste na mesma região geográfica do Azure onde existe a base de dados.

## <a name="step-7-post-implementation-monitoring"></a>Passo 7: Monitorização pós-implementação
Considere monitorizar os efeitos de desempenho das suas implementações em Produção em Memória:

* [Monitorizar o armazenamento em memória](sql-database-in-memory-oltp-monitoring.md).
* [Monitorizar a Base de Dados SQL do Azure com vistas de gestão dinâmica (Monitoring Azure SQL Database using dynamic management views)](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Ligações relacionadas
* [OLTP in-memory (Otimização em Memória)](https://msdn.microsoft.com/library/dn133186.aspx)
* [Introdução aos procedimentos armazenados compilados de forma nativa](https://msdn.microsoft.com/library/dn133184.aspx)
* [Conselheiro de Otimização da Memória](https://msdn.microsoft.com/library/dn284308.aspx)

