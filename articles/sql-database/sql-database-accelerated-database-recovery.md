---
title: Recuperação de base de dados acelerada
description: A Base de Dados Azure SQL tem uma nova funcionalidade que fornece recuperação rápida e consistente de bases de dados, reversão instantânea de transações e truncação de registo agressiva para bases de dados individuais e bases de dados agruparadas na Base de Dados Azure SQL, e bases de dados em Dados Azure SQL O armazém.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/24/2020
ms.openlocfilehash: 57ca594dd067d15009de5e3abf7276fae48720d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238664"
---
# <a name="accelerated-database-recovery"></a>Recuperação acelerada da base de dados

**Accelerated Database Recovery (ADR)** é uma funcionalidade do motor de base de dados SQL que melhora consideravelmente a disponibilidade de bases de dados, especialmente na presença de transações de longo prazo, reformulando o processo de recuperação do motor de base de dados SQL. ADR está atualmente disponível para a Base de Dados Azure SQL única, piscina elástica e instância gerida, e bases de dados no Armazém de Dados Azure SQL (atualmente em pré-visualização). Os principais benefícios da ADR são:

- **Recuperação rápida e consistente da base de dados**

  Com o ADR, as transações de longo prazo não afetam o tempo de recuperação global, permitindo uma recuperação rápida e consistente da base de dados, independentemente do número de transações ativas no sistema ou nas suas dimensões.

- **Reversão instantânea da transação**

  Com a ADR, a reversão da transação é instantânea, independentemente do tempo em que a transação tenha estado ativa ou do número de atualizações que tenha sido realizada.

- **Truncation de log agressiva**

  Com a ADR, o registo de transações é agressivamente truncado, mesmo na presença de transações ativas de longo prazo, o que o impede de crescer fora de controlo.

## <a name="the-current-database-recovery-process"></a>O atual processo de recuperação da base de dados

A recuperação da base de dados no SQL Server segue o modelo de recuperação [da ARIES](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) e consiste em três fases, que são ilustradas no diagrama seguinte e explicadas com mais detalhes após o diagrama.

![processo de recuperação atual](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Fase de análise**

  Verificação antecipada do registo de transações desde o início do último checkpoint bem sucedido (ou a página suja mais antiga LSN) até ao fim, para determinar o estado de cada transação no momento em que o SQL Server parou.

- **Fase de redo**

  A digitalização antecipada do registo de transações da transação mais antiga não comprometida até ao fim, para levar a base de dados ao estado em que estava no momento do acidente, refazendo todas as operações comprometidas.

- **Fase de desfazer**

  Para cada transação que estava ativa a partir da altura do acidente, atravessa o registo para trás, desfazendo as operações que esta transação realizou.

Com base neste design, o tempo que o motor de base de dados SQL demora a recuperar de um reinício inesperado é (aproximadamente) proporcional ao tamanho da transação ativa mais longa do sistema no momento da queda. A recuperação requer uma reversão de todas as transações incompletas. O tempo necessário é proporcional ao trabalho que a transação realizou e ao tempo que tem estado ativo. Por isso, o processo de recuperação do SQL Server pode demorar muito tempo na presença de transações de longo prazo (tais como grandes operações de inserção a granel ou operações de construção de índices contra uma grande tabela).

Além disso, cancelar/reverter uma grande transação com base neste design também pode demorar muito tempo, uma vez que está a utilizar a mesma fase de recuperação do Undo, tal como acima descrito.

Além disso, o motor de base de dados SQL não pode truncar o registo de transações quando há transações de longo prazo porque os seus registos de registo correspondentes são necessários para os processos de recuperação e reversão. Como resultado deste design do motor de base de dados SQL, alguns clientes costumavam enfrentar o problema de que o tamanho do registo de transações cresce muito grande e consome enormes quantidades de espaço de unidade.

## <a name="the-accelerated-database-recovery-process"></a>O processo de recuperação acelerada da base de dados

A DrA aborda os problemas acima referidos reformulando completamente o processo de recuperação do motor de base de dados SQL para:

- Faça-o tempo/instante constante evitando ter de digitalizar o registo de/para o início da transação ativa mais antiga. Com a ADR, o registo de transações é apenas processado a partir do último ponto de verificação bem sucedido (ou número de sequência de registo mais antigo (LSN)). Como resultado, o tempo de recuperação não é afetado por transações de longo prazo.
- Minimize o espaço de registo de transações necessário, uma vez que já não é necessário processar o registo para toda a transação. Como resultado, o registo de transações pode ser truncado agressivamente à medida que os pontos de verificação e cópias de segurança ocorrem.

A um nível elevado, a ADR obtém uma rápida recuperação da base de dados, versonando todas as modificações físicas da base de dados e apenas desfazendo operações lógicas, que são limitadas e podem ser desfeitas quase instantaneamente. Qualquer transação que estivesse ativa a partir do momento de um acidente é marcada como abortada e, portanto, quaisquer versões geradas por estas transações podem ser ignoradas por consultas de utilizador simultâneas.

O processo de recuperação da ADR tem as mesmas três fases do processo de recuperação em curso. A forma como estas fases funcionam com ADR é ilustrada no diagrama seguinte e explicada com mais detalhes após o diagrama.

![Processo de recuperação do ADR](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

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

  A loja de versão persistida é um novo mecanismo de motor de base de dados `tempdb` SQL para persistir as versões de linha geradas na própria base de dados em vez da loja de versão tradicional. O PVS permite o isolamento de recursos, bem como melhora a disponibilidade de secundários legíveis.

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
- Cargas de trabalho que experimentaram longos períodos de indisponibilidade de base de dados devido à recuperação de longo prazo do SQL Server (como o reinício inesperado do Servidor SQL ou a reversão manual da transação).

