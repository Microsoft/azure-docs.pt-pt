---
title: O OLTP na memória melhora o desempenho do SQL TXN | Microsoft Docs
description: Adote o OLTP na memória para melhorar o desempenho transacional em um banco de dados SQL existente.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: e869b2bba3bd64b58d9063e9445889ef709efdc3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567952"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>Usar o OLTP na memória para melhorar o desempenho do aplicativo no banco de dados SQL

O [OLTP na memória](sql-database-in-memory.md) pode ser usado para aprimorar o desempenho de processamento de transações, ingestão de dados e cenários de dados transitórios, em bancos de dado de [camada Premium e comercialmente crítico](sql-database-service-tiers-vcore.md) sem aumentar o tipo de preço. 

> [!NOTE] 
> Saiba como o [Quorum duplica a carga de trabalho do banco de dados principal ao reduzir a DTU por 70% com o banco de dados SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Siga estas etapas para adotar o OLTP na memória em seu banco de dados existente.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Passo 1: Verifique se você está usando um banco de dados da camada Premium e Comercialmente Crítico

O OLTP na memória tem suporte apenas em bancos de dados da camada Premium e Comercialmente Crítico. Na memória, haverá suporte se o resultado retornado for 1 (não 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* significa *processamento de transações* extremas



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Passo 2: Identificar objetos para migrar para o OLTP na memória
O SSMS inclui um relatório de **visão geral da análise de desempenho de transação** que você pode executar em um banco de dados com uma carga de trabalho ativa. O relatório identifica tabelas e procedimentos armazenados que são candidatos à migração para o OLTP na memória.

No SSMS, para gerar o relatório:

* No Pesquisador de **objetos**, clique com o botão direito do mouse no nó do banco de dados.
* Clique em **relatórios** > **padrão relatórios** > **análise de desempenho da transação visão geral**.

Para obter mais informações, consulte [determinando se uma tabela ou um procedimento armazenado deve ser movido para o OLTP na memória](https://msdn.microsoft.com/library/dn205133.aspx).

## <a name="step-3-create-a-comparable-test-database"></a>Passo 3: Criar um banco de dados de teste comparável
Suponha que o relatório indique que seu banco de dados tem uma tabela que se beneficiaria de ser convertido em uma tabela com otimização de memória. É recomendável que você primeiro teste para confirmar a indicação testando.

Você precisa de uma cópia de teste do seu banco de dados de produção. O banco de dados de teste deve estar no mesmo nível de camada de serviço que o banco de dados de produção.

Para facilitar o teste, ajuste o banco de dados de teste da seguinte maneira:

1. Conecte-se ao banco de dados de teste usando o SSMS.
2. Para evitar a necessidade da opção WITH (SNAPSHOT) em consultas, defina a opção Database, conforme mostrado na seguinte instrução T-SQL:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Passo 4: Migrar tabelas
Você deve criar e preencher uma cópia com otimização de memória da tabela que deseja testar. Você pode criá-lo usando:

* O prático assistente de otimização de memória no SSMS.
* T-SQL manual.

#### <a name="memory-optimization-wizard-in-ssms"></a>Assistente de otimização de memória no SSMS
Para usar essa opção de migração:

1. Conecte-se ao banco de dados de teste com o SSMS.
2. No Pesquisador de **objetos**, clique com o botão direito do mouse na tabela e clique em **supervisor de otimização de memória**.
   
   * O assistente do **supervisor de otimização de memória da tabela** é exibido.
3. No assistente, clique em **validação de migração** (ou no botão **Avançar** ) para ver se a tabela tem algum recurso sem suporte que não tenha suporte em tabelas com otimização de memória. Para obter mais informações, consulte:
   
   * A *lista de verificação de otimização de memória* no Orientador de [otimização de memória](https://msdn.microsoft.com/library/dn284308.aspx).
   * [Construções Transact-SQL sem suporte pelo OLTP na memória](https://msdn.microsoft.com/library/dn246937.aspx).
   * [Migrando para o OLTP na memória](https://msdn.microsoft.com/library/dn247639.aspx).
4. Se a tabela não tiver recursos sem suporte, o supervisor poderá executar o esquema real e a migração de dados para você.

#### <a name="manual-t-sql"></a>T-SQL manual
Para usar essa opção de migração:

1. Conecte-se ao banco de dados de teste usando o SSMS (ou um utilitário semelhante).
2. Obtenha o script T-SQL completo para sua tabela e seus índices.
   
   * No SSMS, clique com o botão direito do mouse no nó da tabela.
   * Clique em **tabela de script como** > **criar para a** > **nova janela de consulta**.
3. Na janela de script, adicione WITH (MEMORY_OPTIMIZED = ON) à instrução CREATE TABLE.
4. Se houver um índice CLUSTERIZAdo, altere-o para não CLUSTERIZAdo.
5. Renomeie a tabela existente usando SP_RENAME.
6. Crie a nova cópia com otimização de memória da tabela executando o script de CREATE TABLE editado.
7. Copie os dados para a tabela com otimização de memória usando INSERT... SELECIONE * EM:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Etapa 5 (opcional): Migrar procedimentos armazenados
O recurso na memória também pode modificar um procedimento armazenado para melhorar o desempenho.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Considerações com procedimentos armazenados compilados nativamente
Um procedimento armazenado compilado nativamente deve ter as seguintes opções em seu T-SQL com a cláusula:

* NATIVE_COMPILATION
* SCHEMAbinding: significa que as tabelas para as quais o procedimento armazenado não pode ter suas definições de coluna foram alteradas de forma que afetem o procedimento armazenado, a menos que você remova o procedimento armazenado.

Um módulo nativo deve usar um grande [bloco atômico](https://msdn.microsoft.com/library/dn452281.aspx) para gerenciamento de transações. Não há nenhuma função para um BEGIN TRANSACTION explícito ou para a transação de reversão. Se o seu código detectar uma violação de uma regra de negócio, ele poderá encerrar o bloco atômico com uma instrução [throw](https://msdn.microsoft.com/library/ee677615.aspx) .

### <a name="typical-create-procedure-for-natively-compiled"></a>PROCEDIMENTO CREATE típico para compilação nativa
Normalmente, o T-SQL para criar um procedimento armazenado compilado nativamente é semelhante ao seguinte modelo:

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

* Para o TRANSACTION_ISOLATION_LEVEL, o instantâneo é o valor mais comum para o procedimento armazenado compilado nativamente. No entanto, também há suporte para um subconjunto dos outros valores:
  
  * LEITURA REPETIDA
  * SERIALIZÁVEL
* O valor de idioma deve estar presente na exibição sys. Languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Como migrar um procedimento armazenado
As etapas de migração são:

1. Obtenha o script criar procedimento para o procedimento armazenado regular interpretado.
2. Reescreva seu cabeçalho para corresponder ao modelo anterior.
3. Verifique se o código T-SQL do procedimento armazenado usa qualquer recurso que não tenha suporte para procedimentos armazenados compilados nativamente. Implemente soluções alternativas, se necessário.
   
   * Para obter detalhes, consulte [problemas de migração para procedimentos armazenados compilados nativamente](https://msdn.microsoft.com/library/dn296678.aspx).
4. Renomeie o antigo procedimento armazenado usando SP_RENAME. Ou simplesmente solte-o.
5. Execute o script T-SQL de criar procedimento editado.

## <a name="step-6-run-your-workload-in-test"></a>Passo 6: Executar sua carga de trabalho em teste
Execute uma carga de trabalho em seu banco de dados de teste que seja semelhante à carga de trabalho que é executada em seu banco de dados de produção. Isso deve revelar o benefício de desempenho obtido pelo uso do recurso na memória para tabelas e procedimentos armazenados.

Os principais atributos da carga de trabalho são:

* Número de conexões simultâneas.
* Taxa de leitura/gravação.

Para adaptar e executar a carga de trabalho de teste, considere o uso da ferramenta conveniente ostress. exe, ilustrada [aqui](sql-database-in-memory.md).

Para minimizar a latência de rede, execute o teste na mesma região geográfica do Azure em que o banco de dados existe.

## <a name="step-7-post-implementation-monitoring"></a>Passo 7: Monitoramento pós-implementação
Considere monitorar os efeitos de desempenho de suas implementações na memória em produção:

* [Monitorar o armazenamento na memória](sql-database-in-memory-oltp-monitoring.md).
* [Monitorizar a Base de Dados SQL do Azure com vistas de gestão dinâmica (Monitoring Azure SQL Database using dynamic management views)](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Ligações relacionadas
* [OLTP na memória (otimização na memória)](https://msdn.microsoft.com/library/dn133186.aspx)
* [Introdução aos procedimentos armazenados compilados nativamente](https://msdn.microsoft.com/library/dn133184.aspx)
* [Orientador de otimização de memória](https://msdn.microsoft.com/library/dn284308.aspx)

