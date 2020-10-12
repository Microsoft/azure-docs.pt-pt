---
title: Recuperação de base de dados acelerada
titleSuffix: Azure SQL
description: A recuperação acelerada da base de dados proporciona uma recuperação rápida e consistente da base de dados, o revés instantâneo de transações e a truncação agressiva de registos para bases de dados no portfólio Azure SQL.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 4c679b6bb0f5645ea7a972be03ba3621b824a501
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327634"
---
# <a name="accelerated-database-recovery-in-azure-sql"></a>Recuperação acelerada da base de dados em Azure SQL 
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

**Accelerated Database Recovery (ADR)** é uma funcionalidade de motor de base de dados SQL Server que melhora consideravelmente a disponibilidade da base de dados, especialmente na presença de transações de longa duração, redesenhando o processo de recuperação do motor de base de dados sql Server. 

ADR está atualmente disponível para Azure SQL Database, Azure SQL Managed Instance, bases de dados em Azure Synapse Analytics (atualmente em pré-visualização) e SQL Server em VMs Azure a partir do SQL Server 2019. 

> [!NOTE] 
> O ADR é ativado por padrão na Base de Dados Azure SQL e na Azure SQL Gerenciada Instância e a desativação do ADR para qualquer um dos produtos não é suportado. 

## <a name="overview"></a>Descrição geral

Os principais benefícios da ADR são:

- **Recuperação rápida e consistente da base de dados**

  Com o ADR, as transações de longa duração não têm impacto no tempo global de recuperação, permitindo uma recuperação rápida e consistente da base de dados, independentemente do número de transações ativas no sistema ou das suas dimensões.

- **Reversão instantânea da transação**

  Com o ADR, o revés de transações é instantâneo, independentemente do momento em que a transação tenha estado ativa ou do número de atualizações realizadas.

- **Truncação de log agressivo**

  Com o ADR, o registo de transações é agressivamente truncado, mesmo na presença de transações ativas de longa duração, o que o impede de crescer fora de controlo.

## <a name="standard-database-recovery-process"></a>Processo padrão de recuperação da base de dados

A recuperação da base de dados segue o modelo de recuperação da [ARIES](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) e consiste em três fases, que são ilustradas no diagrama seguinte e explicadas mais detalhadamente após o diagrama.

![processo de recuperação atual](./media/accelerated-database-recovery/current-recovery-process.png)

- **Fase de análise**

  Avante a verificação do registo de transações desde o início do último ponto de verificação bem sucedido (ou a página suja mais antiga LSN) até ao fim, para determinar o estado de cada transação no momento em que a base de dados parou.

- **Fase de redo**

  Avante a verificação do registo de transações da transação mais antiga não comprometida até ao final, para trazer a base de dados para o estado em que estava no momento do acidente, refaçando todas as operações comprometidas.

- **Fase de desfazer**

  Para cada transação que estava ativa a partir da hora do acidente, atravessa o log para trás, desfazendo as operações que esta transação efetuou.

Com base neste design, o tempo que o motor de base de dados sql Server leva para recuperar de um reinício inesperado é (aproximadamente) proporcional ao tamanho da transação ativa mais longa do sistema no momento da falha. A recuperação requer uma reversão de todas as transações incompletas. O tempo necessário é proporcional ao trabalho que a transação realizou e ao tempo em que esteve ativa. Por conseguinte, o processo de recuperação pode demorar muito tempo na presença de transações de longa duração (tais como grandes operações de inserção a granel ou operações de construção de índices contra uma grande tabela).

Além disso, cancelar/reverter uma grande transação baseada neste design também pode demorar muito tempo, uma vez que está a usar a mesma fase de recuperação do Undo como descrito acima.

Além disso, o motor de base de dados SQL Server não pode truncar o registo de transações quando há transações de longa duração porque os registos de registo correspondentes são necessários para os processos de recuperação e reversão. Como resultado deste design do motor de base de dados SQL Server, alguns clientes costumavam enfrentar o problema de que o tamanho do registo de transações cresce muito e consome enormes quantidades de espaço de unidade.

## <a name="the-accelerated-database-recovery-process"></a>O processo acelerado de recuperação da base de dados

O ADR aborda os problemas acima, redesenhando completamente o processo de recuperação do motor de base de dados do SQL Server para:

- Torne-o tempo/instante constante evitando ter de digitalizar o registo de/para o início da transação ativa mais antiga. Com o ADR, o registo de transações só é processado a partir do último ponto de verificação bem sucedido (ou número de sequência de registo de página suja mais antigo (LSN)). Como resultado, o tempo de recuperação não é afetado por transações de longa duração.
- Minimize o espaço de registo de transações necessário, uma vez que já não existe necessidade de processar o registo de toda a transação. Como resultado, o registo de transações pode ser truncado de forma agressiva à medida que os pontos de verificação e backups ocorrem.

A um nível elevado, o ADR consegue uma rápida recuperação da base de dados através da versão de todas as modificações físicas da base de dados e apenas desfazendo operações lógicas, que são limitadas e podem ser desfeitas quase instantaneamente. Qualquer transação que estivesse ativa a partir do momento da queda é marcada como abortada e, portanto, quaisquer versões geradas por estas transações podem ser ignoradas por consultas de utilizador simultâneas.

O processo de recuperação do ADR tem as mesmas três fases do processo de recuperação em curso. A forma como estas fases funcionam com ADR é ilustrada no diagrama seguinte e explica-se mais detalhadamente após o diagrama.

![Processo de recuperação do ADR](./media/accelerated-database-recovery/adr-recovery-process.png)

- **Fase de análise**

  O processo permanece o mesmo de antes com a adição de sLog de reconstrução e cópia de registos de registos para operações não versadas.
  
- **Fase de redo**

  Dividido em duas fases (P)
  - Fase 1

      Redo de sLog (a mais antiga transação não comprometida até ao último ponto de verificação). O Redo é uma operação rápida, pois só precisa de processar alguns registos do sLog.

  - Fase 2

     Redo do Transaction Log começa a partir do último ponto de verificação (em vez da transação não comprometida mais antiga)

- **Fase de desfazer**

   A fase de desfazer com ODR completa-se quase instantaneamente utilizando o sLog para desfazer operações não versadas e a Loja de Versão Persistente (PVS) com o Logical Revert para executar o Undo baseado em versão de linha.

## <a name="adr-recovery-components"></a>Componentes de recuperação de ADR

Os quatro componentes-chave do ADR são:

- **Loja de versão persistiu (PVS)**

  A loja de versão persistiu é um novo mecanismo de motor de base de dados SQL Server para persistir nas versões de linha geradas na própria base de dados em vez da loja de `tempdb` versão tradicional. O PVS permite o isolamento dos recursos, bem como melhora a disponibilidade de secundários legíveis.

- **Reverter lógico**

  O reverte lógico é o processo assíncrona responsável pela realização de undo baseado em versão de linha - fornecendo reversão de transações instantâneas e desfazer para todas as operações versadas. O reverte lógico é realizado por:

  - Acompanhar todas as transações abortadas e marcá-las invisíveis para outras transações. 
  - Efetuar o rollback utilizando PVS para todas as transações de utilizador, em vez de digitalizar fisicamente o registo de transações e desfazer alterações uma de cada vez.
  - Libertando todas as fechaduras imediatamente após a abortar a transação. Uma vez que o aborto envolve simplesmente marcar alterações na memória, o processo é muito eficiente e, portanto, os bloqueios não têm de ser mantidos por muito tempo.

- **sLog**

  sLog é um fluxo de registo secundário de registo de memória que armazena registos de registos para operações não versadas (tais como invalidação de cache de metadados, aquisições de bloqueio, e assim por diante). O sLog é:

  - Baixo volume e memória
  - Persistiu no disco por ser serializado durante o processo de verificação
  - Truncado periodicamente à medida que as transações se comprometem
  - Acelera o redo e desfazer através do processamento apenas das operações não versadas  
  - Permite a truncação de registo de transações agressiva preservando apenas os registos de registos necessários

- **Limpador**

  O aspirador é o processo assíncronos que acorda periodicamente e limpa versões de página que não são necessárias.

## <a name="accelerated-database-recovery-patterns"></a>Padrões acelerados de recuperação da base de dados

Os seguintes tipos de cargas de trabalho são os que mais beneficiam do ADR:

- Cargas de trabalho com transações de longa duração.
- Cargas de trabalho que viram casos em que as transações ativas estão a fazer com que o registo de transações cresça significativamente.  
- Cargas de trabalho que tenham experimentado longos períodos de indisponibilidade de base de dados devido a uma recuperação prolongada (como o reinício inesperado do serviço ou o revés de transações manuais).
