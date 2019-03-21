---
title: OLTP dentro da memória melhora o desempenho de transações do SQL | Documentos da Microsoft
description: Adopt o OLTP na memória para melhorar o desempenho transacional na base de dados SQL existente.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: MightyPen
manager: craigg
ms.date: 11/07/2018
ms.openlocfilehash: ad66253d33b2e99f0be79bfaddc86b3274f5cab0
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258926"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>Utilize o OLTP na memória para melhorar o desempenho da aplicação na base de dados SQL

[OLTP dentro da memória](sql-database-in-memory.md) podem ser utilizadas para melhorar o desempenho de processamento de transações e ingestão de dados e cenários de dados transitório, no [escalão Premium e crítico para a empresa](sql-database-service-tiers-vcore.md) bases de dados sem aumentar o escalão de preço. 

> [!NOTE] 
> Saiba como [quórum duplica a carga de trabalho de chave da base de dados, reduzindo assim a DTU por 70% com a base de dados SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Siga estes passos para adotar o OLTP dentro da memória da base de dados existente.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Passo 1: Certifique-se de que está a utilizar uma base de dados de escalão Premium e crítico para a empresa

OLTP dentro da memória só é suportado em bases de dados do escalão Premium e crítico para a empresa. Dentro da memória é suportada se o resultado retornado é 1 (não 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* significa *Extreme processamento de transação*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Passo 2: Identificam os objetos a migrar para o OLTP dentro da memória
SSMS inclui um **descrição geral da análise de desempenho de transação** relatório que pode ser executado numa base de dados com uma carga de trabalho do Active Directory. O relatório identifica as tabelas e procedimentos armazenados que são candidatos à migração para o OLTP dentro da memória.

No SSMS, para gerar o relatório:

* Na **Object Explorer**, com o botão direito do nó de base de dados.
* Clique em **relatórios** > **relatórios padrão** > **descrição geral da análise de desempenho de transação**.

Para obter mais informações, consulte [determinando se uma tabela ou, em seguida, armazenados procedimento deve ser transportado para OLTP dentro da memória](https://msdn.microsoft.com/library/dn205133.aspx).

## <a name="step-3-create-a-comparable-test-database"></a>Passo 3: Criar uma base de dados de teste comparável
Suponha que o relatório indica a que sua base de dados tem uma tabela que se beneficiaria a ser convertido numa tabela com otimização de memória. Recomendamos que teste primeiro para confirmar a indicação ao testar.

Precisa de uma cópia de teste do seu banco de dados de produção. A base de dados de teste deve estar no mesmo nível de camada de serviço da base de dados de produção.

Para facilitar a testar, Otimize a base de dados de teste da seguinte forma:

1. Ligar à base de dados de teste com o SSMS.
2. Para evitar a ter a opção WITH (SNAPSHOT) em consultas, defina a opção de base de dados, como mostra a seguinte instrução T-SQL:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Passo 4: Migrar as tabelas
Tem de criar e preencher uma cópia com otimização de memória da tabela que pretende testar. Pode criá-la através de um:

* O Assistente de otimização de memória útil no SSMS.
* Manual do T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>Assistente de otimização de memória no SSMS
Para utilizar esta opção de migração:

1. Ligar à base de dados de teste com o SSMS.
2. Na **Object Explorer**, clique com o botão direito na tabela e, em seguida, clique em **Assistente de otimização de memória**.
   
   * O **Advisor de otimizador de memória de tabela** assistente é exibido.
3. No assistente, clique em **validação de migração** (ou o **próxima** botão) para ver se a tabela contém quaisquer funcionalidades não suportadas que não são suportadas em tabelas com otimização de memória. Para obter mais informações, consulte:
   
   * O *lista de verificação de otimização de memória* na [Assistente de otimização de memória](https://msdn.microsoft.com/library/dn284308.aspx).
   * [Construções de Transact-SQL não suportadas pelo OLTP dentro da memória](https://msdn.microsoft.com/library/dn246937.aspx).
   * [Migrar para o OLTP dentro da memória](https://msdn.microsoft.com/library/dn247639.aspx).
4. Se a tabela tem não existem funcionalidades não suportadas, o assistente pode executar o esquema real e a migração de dados para.

#### <a name="manual-t-sql"></a>Manual de T-SQL
Para utilizar esta opção de migração:

1. Ligar à sua base de dados de teste com o SSMS (ou um utilitário semelhante).
2. Obter o script T-SQL completo para a sua tabela e os seus índices.
   
   * No SSMS, clique no seu nó de tabela.
   * Clique em **tabela como de Script** > **criar para** > **nova janela de consulta**.
3. Na janela do script, adicionar WITH (MEMORY_OPTIMIZED = ON) para a instrução CREATE TABLE.
4. Se existir um índice em cluster, alterá-lo para NONCLUSTERED.
5. Mudar o nome da tabela existente, utilizando SP_RENAME.
6. Crie a nova cópia com otimização de memória da tabela ao executar o script CREATE TABLE editado.
7. Copie os dados para a tabela com otimização de memória com INSERT... SELECIONE * EM:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Passo 5 (opcional): Migrar de procedimentos armazenados
A funcionalidade de dentro da memória também pode modificar um procedimento armazenado para um melhor desempenho.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Considerações sobre com procedimentos armazenados compilados nativamente
Um procedimento armazenado compilado nativamente tem de ter as seguintes opções no sua cláusula com o T-SQL:

* NATIVE_COMPILATION
* SCHEMABINDING: ou seja, as tabelas que o procedimento armazenado não pode ter as respetivas definições de coluna alteradas de qualquer forma que iria afetar o procedimento armazenado, a menos que a elimine o procedimento armazenado.

Um módulo nativo tem de utilizar uma grande [blocos ATOMIC](https://msdn.microsoft.com/library/dn452281.aspx) para gerenciamento de transações. Não existe nenhuma função para uma transação explícita de começar, ou para a transação de reversão. Se o seu código detetar uma violação de uma regra de negócio, ele pode encerrar o bloco atomic com um [LANÇAR](https://msdn.microsoft.com/library/ee677615.aspx) instrução.

### <a name="typical-create-procedure-for-natively-compiled"></a>CREATE PROCEDURE típico para compilados nativamente
Normalmente, o T-SQL para criar um procedimento armazenado compilado nativamente é semelhante ao modelo de seguinte:

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

* Para o TRANSACTION_ISOLATION_LEVEL, o INSTANTÂNEO é o valor mais comuns para o procedimento armazenado compilado nativamente. No entanto, um subconjunto dos outros valores também são suportados:
  
  * LER PASSÍVEL DE REPETIÇÃO
  * SERIALIZÁVEL
* O valor de idioma tem de estar presente na vista de sys.languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Como migrar um procedimento armazenado
Os passos de migração são:

1. Obter o script do procedimento de criar para o procedimento armazenado normal interpretado.
2. Reescreva seu cabeçalho de acordo com o modelo anterior.
3. Determine se o procedimento armazenado código T-SQL utiliza quaisquer funcionalidades que não são suportadas para procedimentos armazenados compilados nativamente. Implementar soluções alternativas, se necessário.
   
   * Para obter detalhes, consulte [problemas de migração para compilados nativamente procedimentos armazenados](https://msdn.microsoft.com/library/dn296678.aspx).
4. Mudar o nome do procedimento armazenado antigo utilizando SP_RENAME. Ou basta SOLTÁ-lo.
5. Execute o script de T-SQL de procedimento de criar editado.

## <a name="step-6-run-your-workload-in-test"></a>Passo 6: Executar sua carga de trabalho no teste
Execute uma carga de trabalho na sua base de dados de teste é semelhante à carga de trabalho que é executado na base de dados de produção. Isso deve ficar a saber o ganho de desempenho obtido pela sua utilização da funcionalidade dentro da memória para tabelas e procedimentos armazenados.

Atributos principais da carga de trabalho são:

* Número de ligações simultâneas.
* Rácio de leitura/escrita.

Para adaptar e executar a carga de trabalho de teste, considere utilizar a ferramenta de ostress.exe útil, que ilustra [aqui](sql-database-in-memory.md).

Para minimizar a latência de rede, execute o teste na mesma região geográfica do Azure onde a base de dados existe.

## <a name="step-7-post-implementation-monitoring"></a>Passo 7: Após a implementação de monitorização
Considere os efeitos de desempenho das suas implementações de dentro da memória na produção de monitorização:

* [Monitorizar o armazenamento na memória](sql-database-in-memory-oltp-monitoring.md).
* [Monitorizar a Base de Dados SQL do Azure com vistas de gestão dinâmica (Monitoring Azure SQL Database using dynamic management views)](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Ligações relacionadas
* [(Otimização de memória) de OLTP na memória](https://msdn.microsoft.com/library/dn133186.aspx)
* [Introdução aos procedimentos armazenados compilados nativamente](https://msdn.microsoft.com/library/dn133184.aspx)
* [Assistente de otimização de memória](https://msdn.microsoft.com/library/dn284308.aspx)

