---
title: Recuperação de base de dados acelerada
titleSuffix: Azure SQL
description: A recuperação acelerada da base de dados proporciona uma recuperação rápida e consistente da base de dados, a reversão instantânea da transação e a truncação agressiva de registos para bases de dados no portfólio de serviços Azure SQL.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 05/19/2020
ms.openlocfilehash: c0243ecea778a02238b205f1659d796165f7b316
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84044361"
---
# <a name="accelerated-database-recovery-in-azure-sql"></a>Recuperação acelerada da base de dados em Azure SQL 
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

**Accelerated Database Recovery (ADR)** é uma funcionalidade do motor de base de dados SQL que melhora consideravelmente a disponibilidade de bases de dados, especialmente na presença de transações de longo prazo, reformulando o processo de recuperação do motor de base de dados SQL. ATUALMENTE, o ADR está disponível para a Base de Dados Azure SQL, Pedres-Gerível Azure SQL, SQL Server em VMs Azure e bases de dados em Azure Synapse (atualmente em pré-visualização). Os principais benefícios da ADR são:

- **Recuperação rápida e consistente da base de dados**

  Com o ADR, as transações de longo prazo não afetam o tempo de recuperação global, permitindo uma recuperação rápida e consistente da base de dados, independentemente do número de transações ativas no sistema ou nas suas dimensões.

- **Reversão instantânea da transação**

  Com a ADR, a reversão da transação é instantânea, independentemente do tempo em que a transação tenha estado ativa ou do número de atualizações que tenha sido realizada.

- **Truncation de log agressiva**

  Com a ADR, o registo de transações é agressivamente truncado, mesmo na presença de transações ativas de longo prazo, o que o impede de crescer fora de controlo.

## <a name="standard-database-recovery-process"></a>Processo de recuperação de bases de dados padrão

A recuperação da base de dados segue o modelo de recuperação da [ARIES](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) e consiste em três fases, que são ilustradas no diagrama seguinte e explicadas com mais detalhes após o diagrama.

![processo de recuperação atual](./media/accelerated-database-recovery/current-recovery-process.png)

- **Fase de análise**

  Verificação antecipada do registo de transações desde o início do último ponto de verificação bem sucedido (ou a página suja mais antiga LSN) até ao fim, para determinar o estado de cada transação no momento em que a base de dados parou.

- **Fase de redo**

  A digitalização antecipada do registo de transações da transação mais antiga não comprometida até ao fim, para levar a base de dados ao estado em que estava no momento do acidente, refazendo todas as operações comprometidas.

- **Fase de desfazer**

  Para cada transação que estava ativa a partir da altura do acidente, atravessa o registo para trás, desfazendo as operações que esta transação realizou.

Com base neste design, o tempo que o motor de base de dados SQL demora a recuperar de um reinício inesperado é (aproximadamente) proporcional ao tamanho da transação ativa mais longa do sistema no momento da queda. A recuperação requer uma reversão de todas as transações incompletas. O tempo necessário é proporcional ao trabalho que a transação realizou e ao tempo que tem estado ativo. Por conseguinte, o processo de recuperação pode demorar muito tempo na presença de transações de longo prazo (tais como grandes operações de inserção a granel ou operações de construção de índices contra uma mesa grande).

Além disso, cancelar/reverter uma grande transação com base neste design também pode demorar muito tempo, uma vez que está a utilizar a mesma fase de recuperação do Undo, tal como acima descrito.

Além disso, o motor de base de dados SQL não pode truncar o registo de transações quando há transações de longo prazo porque os seus registos de registo correspondentes são necessários para os processos de recuperação e reversão. Como resultado deste design do motor de base de dados SQL, alguns clientes costumavam enfrentar o problema de que o tamanho do registo de transações cresce muito grande e consome enormes quantidades de espaço de unidade.

## <a name="the-accelerated-database-recovery-process"></a>O processo de recuperação acelerada da base de dados

A DrA aborda os problemas acima referidos reformulando completamente o processo de recuperação do motor de base de dados SQL para:

- Faça-o tempo/instante constante evitando ter de digitalizar o registo de/para o início da transação ativa mais antiga. Com a ADR, o registo de transações é apenas processado a partir do último ponto de verificação bem sucedido (ou número de sequência de registo mais antigo (LSN)). Como resultado, o tempo de recuperação não é afetado por transações de longo prazo.
- Minimize o espaço de registo de transações necessário, uma vez que já não é necessário processar o registo para toda a transação. Como resultado, o registo de transações pode ser truncado agressivamente à medida que os pontos de verificação e cópias de segurança ocorrem.

A um nível elevado, a ADR obtém uma rápida recuperação da base de dados, versonando todas as modificações físicas da base de dados e apenas desfazendo operações lógicas, que são limitadas e podem ser desfeitas quase instantaneamente. Qualquer transação que estivesse ativa a partir do momento de um acidente é marcada como abortada e, portanto, quaisquer versões geradas por estas transações podem ser ignoradas por consultas de utilizador simultâneas.

O processo de recuperação da ADR tem as mesmas três fases do processo de recuperação em curso. A forma como estas fases funcionam com ADR é ilustrada no diagrama seguinte e explicada com mais detalhes após o diagrama.

![Processo de recuperação do ADR](./media/accelerated-database-recovery/adr-recovery-process.png)

- **Fase de análise**

  O processo permanece o mesmo de antes com a adição de sLog de reconstrução e cópia de registos de registos para operações não versonizadas.
  
- **Fase de redo**

  Dividido em duas fases (P)
  - Fase 1

      Refazer do sLog (transação não comprometida mais antiga até ao último ponto de verificação). Redo é uma operação rápida, pois só precisa processar alguns registos do sLog.

  - Fase 2

     Refazer a partir do Registo de Transações começa a partir do último checkpoint (em vez da transação não comprometida mais antiga)

- **Fase de desfazer**

   A fase Desfazer com ADR completa quase instantaneamente usando o sLog para desfazer operações não versonizadas e A Loja de Versão Persistida (PVS) com O Reverlógico para executar o Undo baseado em versão de nível de linha.

## <a name="adr-recovery-components"></a>Componentes de recuperação aDR

Os quatro componentes-chave da ADR são:

- **Loja de Versão Persistida (PVS)**

  A loja de versão persistida é um novo mecanismo de motor de base de dados SQL para persistir as versões de linha geradas na própria base de dados em vez da loja de `tempdb` versão tradicional. O PVS permite o isolamento de recursos, bem como melhora a disponibilidade de secundários legíveis.

- **Reverter lógico**

  O reverso lógico é o processo assíncrono responsável pela realização do Undo baseado em versão de linha - proporcionando reversão de transação instantânea e desfazer para todas as operações versonizadas. O reverso lógico é realizado por:

  - Acompanhar todas as transações abortadas e marcá-las invisíveis a outras transações. 
  - Executar o reversão utilizando PVS para todas as transações do utilizador, em vez de digitalizar fisicamente o registo de transações e desfazer alterações uma de cada vez.
  - Libertando todas as fechaduras imediatamente após a interrupção da transação. Uma vez que o aborto envolve simplesmente marcar mudanças na memória, o processo é muito eficiente e, portanto, as fechaduras não têm de ser mantidas por muito tempo.

- **sLog**

  SLog é um fluxo de registo secundário na memória que armazena registos de registos de operações não versonizadas (tais como invalidação de cache de metadados, aquisições de bloqueio, e assim por diante). O sLog é:

  - Baixo volume e memória
  - Persistiu no disco por ser serializado durante o processo de checkpoint
  - Periodicamente truncado à medida que as transações se comprometem
  - Acelera a redoe e desfazer processando apenas as operações não versonizadas  
  - Permite a truncação agressiva de registo de transações preservando apenas os registos de registo necessários

- **Limpador**

  O aspirador é o processo assíncrono que acorda periodicamente e limpa versões de página que não são necessárias.

## <a name="accelerated-database-recovery-patterns"></a>Padrões de recuperação acelerados da base de dados

Os seguintes tipos de cargas de trabalho beneficiam mais da ADR:

- Cargas de trabalho com transações de longa duração.
- Cargas de trabalho que viram casos em que as transações ativas estão a fazer com que o registo de transações cresça significativamente.  
- Cargas de trabalho que experimentaram longos períodos de indisponibilidade de base de dados devido a uma recuperação de longo prazo (como o reinício inesperado do serviço ou o reversão da transação manual).
