---
title: Acelerado de recuperação de base de dados - base de dados SQL do Azure | Documentos da Microsoft
description: A base de dados do SQL do Azure tem um novo recurso que fornece a recuperação de base de dados rápida e consistente, reversão de transação de instantâneo e truncamento de log agressivo para bases de dados individuais e bases de dados agrupadas na base de dados do Azure SQL e bases de dados no Azure SQL Data Armazém.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 77bc33747964a5f4ee1a67aba777dc3ed76b9a51
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073454"
---
# <a name="accelerated-database-recovery"></a>Recuperação de base de dados acelerada

**Acelerado de recuperação de base de dados (ADR)** é uma funcionalidade de motor da base de dados nova SQL melhora significativamente a disponibilidade de base de dados, especialmente na presença de longa execução transações, ao ter de redesenhar o processo de recuperação do motor de base de dados SQL. Regras de implementação automática estão atualmente disponível para bases de dados individuais e bases de dados agrupadas na base de dados do Azure SQL e bases de dados no Azure SQL Data Warehouse. Os principais benefícios da ADR são:

- **Recuperação de base de dados rápida e consistente**

  Com a ADR, transações de execução longa não afetam o tempo de recuperação geral, habilitar a recuperação de base de dados rápida e consistente, independentemente do número de transações ativas no sistema ou seus tamanhos.

- **Reversão de transação de instantâneo**

  Com regras de implementação automática, a reversão de transação é instantânea, independentemente do tempo que a transação foi Active Directory ou o número de atualizações que efetuou.

- **Truncamento de log agressivo**

  Com regras de implementação automática, o registo de transações é agressivamente truncado, mesmo na ocorrência de transações ativas de execução longa, que impede crescimento descontrolado.

## <a name="the-current-database-recovery-process"></a>O processo de recuperação de base de dados atual

Recuperação de base de dados no SQL Server segue a [ARIES](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) modelo de recuperação e consiste em três fases, que são ilustradas no diagrama seguinte e explicadas mais detalhadamente o diagrama a seguir.

![processo de recuperação atual](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Fase de análise**

  Reencaminhar análise do registo de transação do início do último ponto de verificação com êxito (ou a página de desatualização mais antiga LSN) até ao fim, para determinar o estado de cada transação em tempo de parada do SQL Server.

- **Refazer fase**

  Reencaminhar análise do registo de transação da transação não consolidada mais antiga até ao fim, para a base de dados para o estado em que estava no momento da falha ao Refazer todas as operações consolidadas.

- **Anular fase**

  Para cada transação que estava ativa no momento da falha, atravessa o registo com versões anteriores, desfazendo as operações que esta transação executada.

Com base nesse projeto, o tempo que demora o motor de base de dados SQL para recuperar a partir de uma reinicialização inesperada é (aproximadamente) proporcional ao tamanho da transação ativa mais longo no sistema no momento da falha. A recuperação de requer uma reversão de todas as transações incompletas. O período de tempo necessário é proporcional ao trabalho que efetuou a transação e o tempo tem sido Active Directory. Por conseguinte, o processo de recuperação do SQL Server pode demorar muito tempo na presença de transações de longa execução (por exemplo, em grande volume inserir operações ou operações de compilação de índice numa grande tabela).

Além disso, a cancelar/reverter uma transação grande, com base nesse design pode também demorar muito tempo à medida que está a utilizar a mesma fase de recuperação de desfazer, tal como descrito acima.

Além disso, o motor de base de dados SQL não é possível truncar o registo de transações quando existem longo executar transações porque seus registros de log correspondentes são necessários para os processos de recuperação e reversão. Como resultado deste design do motor de base de dados SQL, alguns clientes enfrentam o mesmo problema que o tamanho do registo de transações aumenta muito grande e consome enormes quantidades de espaço na unidade.

## <a name="the-accelerated-database-recovery-process"></a>O processo de recuperação de base de dados acelerada

ADR aborda os problemas acima ao redesenhar completamente o processo de recuperação do motor de base de dados SQL para:

- Torná-lo a constante tempo/instantâneas ao evitar ter que verificar o registo do/para o início da transação ativa mais antiga. Com regras de implementação automática, o registo de transações só é processado do último ponto de verificação com êxito (ou mais antiga página desatualização número de sequência de registo (LSN)). Como resultado, o tempo de recuperação não é afetado por longa executar transações.
- Minimize o espaço do registo de transação necessária, uma vez que já não existe uma necessidade para processar o registo para a transação inteira. Como resultado, o log de transação pode ser truncado agressivamente como pontos de verificação e cópias de segurança ocorrerem.

Num alto nível, ADR alcança recuperação de base de dados rápido ao controle de versão, todas as modificações de banco de dados físico e apenas anulação operações lógicas, que são limitados e podem ser anulada quase instantaneamente. Qualquer transação que estava ativa no momento de uma pane são marcados como abortada e, portanto, todas as versões geradas por essas transações podem ser ignoradas por consultas de utilizador em simultâneo.

O processo de recuperação ADR tem as três fases mesmo que o processo de recuperação atual. Como essas fases operam com regras de implementação automática é ilustrado no diagrama seguinte e explicado mais detalhadamente o diagrama a seguir.

![Processo de recuperação de regras de implementação automática](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Fase de análise**

  O processo permanece o mesmo que hoje com a adição de reconstruímos sLog e copiar os registros de log para operações sem versões.
  
- **Refazer** fase

  Dividido em duas fases (P)
  - Fase 1

      Refazer de sLog (transações não consolidadas mais antiga até o último ponto de verificação). Refazer é uma operação rápida porque precisa apenas de processar alguns registos a partir do sLog.
      
  - Fase 2

     Refazer do Log de transação começa a partir do último ponto de verificação (em vez de transações não consolidadas mais antigo)
     
- **Anular fase**

   A fase Undo com regras de implementação automática é concluída quase instantaneamente, usando sLog para anular operações sem versões e persistentes versão Store (PVS) com a lógica de reverter para efetuar desfazer com base na versão de nível de linha.

## <a name="adr-recovery-components"></a>Componentes da recuperação de regras de implementação automática

Os quatro componentes principais de regras de implementação automática são:

- **Store de versão persistente (PVS)**

  O arquivo de versões persistente é um novo mecanismo de motor de base de dados SQL para manter as versões de linha geradas na base de dados em si em vez do tradicional `tempdb` arquivo de versões. PVS permite o isolamento de recursos, bem como melhora a disponibilidade de bases de dados secundárias legíveis.

- **Lógica reverter**

  Reverter de lógica é o processo assíncrono responsável por executar desfazer com base na versão de nível de linha – fornecendo a reversão de transação de instantâneo e desfazer para todas as operações com a versão.

  - Mantém um registro de transações abortadas tudo
  - Efetua a reversão com o PVS para todas as transações de utilizador
  - Abortar de todos os bloqueios imediatamente após a transação de versões

- **sLog**

  sLog é um fluxo de registos de dentro da memória secundário que criar arquivos de registos para operações sem versões (por exemplo, invalidação de cache de metadados, aquisições de bloqueios e assim por diante). O sLog é:

  - Volume baixo e dentro da memória
  - Persistido no disco, pelo que está a ser serializado durante o processo de ponto de verificação
  - Truncados periodicamente como consolidação de transações
  - Acelera Refazer e anular ao processar apenas as operações sem versões  
  - Permite o truncamento do log de transação agressiva, preservando apenas os registros de log necessárias

- **Mais limpo**

  A mais limpa é o processo assíncrono que acorda periodicamente e limpa as versões de página que não são necessários.

## <a name="who-should-consider-accelerated-database-recovery"></a>Quem deve considerar a recuperação de base de dados acelerada

Os seguintes tipos de clientes devem considerar a ativação de ADR:

- Os clientes que tenham cargas de trabalho com longos transações em execução.
- Clientes que já VI casos em que estão a causar o registo de transações aumentar significativamente transações ativas.  
- Clientes que passaram pelos longos períodos de indisponibilidade da base de dados devido à recuperação (por exemplo, inesperado do SQL Server reinício ou manual reversão de transação) de longa execução do SQL Server.

