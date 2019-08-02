---
title: Recuperação de banco de dados acelerada-banco de dados SQL do Azure | Microsoft Docs
description: O banco de dados SQL do Azure tem um novo recurso que fornece recuperação rápida e consistente de banco de dados, reversão instantânea de transação e truncamento agressivo de log para bancos de dados individuais e bancos de dado em pool no banco de dados SQL do Azure e bancos de dados em SQL data do Azure Armazén.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: d516dc51a25cbef92ff9fa22012773507b528a99
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569630"
---
# <a name="accelerated-database-recovery"></a>Recuperação de banco de dados acelerada

A **ADR (recuperação de banco de dados acelerada)** é um novo recurso do mecanismo de banco de dados SQL que melhora muito a disponibilidade do banco de dados, especialmente na presença de transações de longa execução, remodelando o processo de recuperação do mecanismo de banco de dados SQL. O ADR está disponível no momento para bancos de dados individuais e bancos de dados em pool no banco de dados SQL do Azure, e os bancos de dados no Azure SQL Data Warehouse (atualmente em visualização pública). Os principais benefícios do ADR são:

- **Recuperação de banco de dados rápida e consistente**

  Com a ADR, as transações de execução longa não afetam o tempo de recuperação geral, permitindo uma recuperação de banco de dados rápida e consistente, independentemente do número de transações ativas no sistema ou de seus tamanhos.

- **Reversão de transação instantânea**

  Com ADR, a reversão de transação é instantânea, independentemente do tempo em que a transação esteve ativa ou do número de atualizações que foi realizado.

- **Truncamento agressivo de log**

  Com ADR, o log de transações é truncado agressivamente, mesmo na presença de transações de longa execução ativas, o que impede o crescimento fora do controle.

## <a name="the-current-database-recovery-process"></a>O processo de recuperação de banco de dados atual

A recuperação de banco de dados no SQL Server segue o modelo de recuperação [Aries](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) e consiste em três fases, que são ilustradas no diagrama a seguir e explicadas em mais detalhes após o diagrama.

![processo de recuperação atual](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Fase de análise**

  Encaminhe a verificação do log de transações desde o início do último ponto de verificação bem-sucedido (ou o LSN de página sujo mais antigo) até o final, para determinar o estado de cada transação no momento SQL Server parado.

- **Fase de refazer**

  Encaminhe a varredura do log de transações da transação não confirmada mais antiga até o fim, para colocar o banco de dados no estado em que estava no momento da falha, refazendo todas as operações confirmadas.

- **Fase desfazer**

  Para cada transação que estava ativa no momento da falha, percorre o log para trás, desfazendo as operações executadas por essa transação.

Com base nesse design, o tempo necessário para que o mecanismo de banco de dados SQL se recupere de uma reinicialização inesperada é (aproximadamente) proporcional ao tamanho da transação ativa mais longa no sistema no momento da falha. A recuperação requer uma reversão de todas as transações incompletas. O período de tempo necessário é proporcional ao trabalho que a transação realizou e da hora em que ela esteve ativa. Portanto, o processo de recuperação de SQL Server pode levar muito tempo na presença de transações de longa execução (como operações de inserção em massa grandes ou operações de compilação de índice em uma tabela grande).

Além disso, o cancelamento/reversão de uma transação grande baseada nesse design também pode levar muito tempo, pois está usando a mesma fase de desfazer recuperação, conforme descrito acima.

Além disso, o mecanismo do banco de dados SQL não pode truncar o log de transações quando há transações de longa duração porque seus registros de log correspondentes são necessários para os processos de recuperação e reversão. Como resultado desse design do mecanismo de banco de dados SQL, alguns clientes enfrentam o problema de que o tamanho do log de transações cresce muito grande e consome enormes quantidades de espaço na unidade.

## <a name="the-accelerated-database-recovery-process"></a>O processo de recuperação de banco de dados acelerado

ADR aborda os problemas acima remodelando completamente o processo de recuperação do mecanismo de banco de dados SQL para:

- Torne o tempo constante/instantâneo, evitando ter que verificar o log de/para o início da transação ativa mais antiga. Com ADR, o log de transações só é processado a partir do último ponto de verificação bem-sucedido (ou LSN (número de sequência de log) de página sujo mais antigo). Como resultado, o tempo de recuperação não é afetado por transações de longa execução.
- Minimize o espaço de log de transações necessário, pois não há mais necessidade de processar o log para toda a transação. Como resultado, o log de transações pode ser truncado agressivamente, pois ocorrem pontos de verificação e backups.

Em um nível alto, ADR alcança a recuperação rápida do banco de dados ao realizar o controle de versão de todas as modificações físicas do banco de dados e desfazer apenas operações lógicas, que são limitadas e podem ser desfeitas quase instantaneamente. Qualquer transação que estava ativa no momento de uma falha é marcada como anulada e, portanto, todas as versões geradas por essas transações podem ser ignoradas por consultas de usuário simultâneas.

O processo de recuperação ADR tem as mesmas três fases que o processo de recuperação atual. A forma como essas fases operam com ADR é ilustrada no diagrama a seguir e explicada em mais detalhes após o diagrama.

![Processo de recuperação ADR](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Fase de análise**

  O processo permanece o mesmo que hoje com a adição da recriação de sLog e a cópia de registros de log para operações sem controle de versão.
  
- Fase de refazer

  Dividido em duas fases (P)
  - Fase 1

      Refazer de sLog (transação não confirmada mais antiga até o último ponto de verificação). Refazer é uma operação rápida, pois ela só precisa processar alguns registros do sLog.
      
  - Fase 2

     Refazer do log de transações começa do último ponto de verificação (em vez da transação não confirmada mais antiga)
     
- **Fase desfazer**

   A fase de desfazer com ADR é concluída quase instantaneamente usando sLog para desfazer operações sem controle de versão e o PVS (repositório de versões persistentes) com a reversão lógica para executar desfazer baseado em versão de nível de linha.

## <a name="adr-recovery-components"></a>Componentes de recuperação ADR

Os quatro principais componentes de ADR são:

- **Repositório de versão persistente (PVS)**

  O repositório de versão persistente é um novo mecanismo do mecanismo de banco de dados SQL para persistir as versões de linha geradas no próprio `tempdb` banco de dados, em vez do armazenamento de versão tradicional. O PVS habilita o isolamento de recursos, bem como melhora a disponibilidade de secundários legíveis.

- **Reverter lógica**

  A reversão lógica é o processo assíncrono responsável por executar desfazer baseado em versão de linha, fornecendo reversão de transação instantânea e desfazer para todas as operações com controle de versão.

  - Mantém o controle de todas as transações anuladas
  - Executa a reversão usando PVS para todas as transações de usuário
  - Libera todos os bloqueios imediatamente após a anulação da transação

- **sLog**

  sLog é um fluxo de log na memória secundário que armazena registros de log para operações sem controle de versão (como invalidação de cache de metadados, aquisições de bloqueio e assim por diante). O sLog é:

  - Baixo volume e na memória
  - Persistido no disco ao ser serializado durante o processo de ponto de verificação
  - Truncado periodicamente conforme as transações são confirmadas
  - Acelera a refazer e desfazer processando apenas as operações sem controle de versão  
  - Habilita o truncamento agressivo de log de transações preservando apenas os registros de log necessários

- **Limpeza**

  O mais limpo é o processo assíncrono que é ativado periodicamente e limpa as versões de página que não são necessárias.

## <a name="who-should-consider-accelerated-database-recovery"></a>Quem deve considerar a recuperação de banco de dados acelerada

Os seguintes tipos de clientes devem considerar habilitar ADR:

- Clientes que têm cargas de trabalho com transações de longa execução.
- Clientes que viram casos em que as transações ativas estão fazendo com que o log de transações cresça significativamente.  
- Clientes que tiveram longos períodos de indisponibilidade de banco de dados devido a SQL Server recuperação de longa execução (como reinicialização SQL Server inesperada ou reversão de transação manual).

