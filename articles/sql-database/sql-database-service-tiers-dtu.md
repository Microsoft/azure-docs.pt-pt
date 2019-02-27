---
title: Escalões de serviço de base de dados SQL do Azure - modelo de compra baseado em DTU | Documentos da Microsoft
description: Saiba mais sobre escalões de serviço no modelo de compra baseado em DTU para bases de dados individuais e em pool fornecer os tamanhos de computação e armazenamento.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 02/25/2019
ms.openlocfilehash: 2c7969ee2b48d22189229af8a5662d0ae769f186
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872714"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>Escalões de serviço no modelo de compra baseado em DTU

Escalões de serviço no modelo de compra baseado em DTU são diferenciadas por uma variedade de tamanhos de computação com uma quantidade fixa de armazenamento incluído, foi corrigido o período de retenção para cópias de segurança e o preço fixo. Todos os escalões de serviço no modelo de compra baseado em DTU oferecem a flexibilidade de alterar os tamanhos de computação sem tempo de inatividade. Conjuntos elásticos e bases de dados individuais são faturados por hora com base no escalão de serviço e o tamanho de computação.

> [!IMPORTANT]
> Instância gerida da base de dados SQL não suporta um modelo de compra baseado em DTU. Para obter mais informações, consulte [instância gerida da base de dados SQL do Azure](sql-database-managed-instance.md).
> [!NOTE]
> Para obter informações sobre escalões de serviço baseado em vCore, consulte [escalões de serviço baseado em vCore](sql-database-service-tiers-vcore.md). Para obter informações sobre a diferenciar os escalões de serviço baseado em vCore e de escalões de serviço baseado em DTU, consulte [modelos de compra do Azure SQL Database](sql-database-purchase-models.md).

## <a name="compare-the-dtu-based-service-tiers"></a>Compare os escalões de serviço baseado em DTU

Escolher uma camada de serviço depende principalmente continuidade do negócio, armazenamento e requisitos de desempenho.

||Básica|Standard|Premium|
| :-- | --: |--:| --:| --:|
|Carga de trabalho de destino|Desenvolvimento e produção|Desenvolvimento e produção|Desenvolvimento e produção|
|SLA de Tempo de Atividade|99,99%|99,99%|99,99%|
|Retenção da cópia de segurança|7 dias|35 dias|35 dias|
|CPU|Baixa|Baixa, média, alta|Médio, alto|
|Débito de e/s (aproximado) |2,5 IOPS de por DTU| 2,5 IOPS de por DTU | 48 IOPS por DTU|
|Latência de e/s (aproximada)|5 ms (ler), 10 ms (escrita)|5 ms (ler), 10 ms (escrita)|2 ms (leitura/escrita)|
|Indexação Columnstore |N/A|S3 e posterior|Suportadas|
|OLTP dentro da memória|N/A|N/A|Suportadas|
|||||

> [!NOTE]
> Pode obter uma base de dados SQL do Azure gratuita na camada de serviço básico em conjunto com uma conta gratuita do Azure para explorar o Azure. Para obter informações, consulte [criar uma base de dados gerida na cloud com a sua conta gratuita do Azure](https://azure.microsoft.com/free/services/sql-database/).

## <a name="single-database-dtu-and-storage-limits"></a>Base de dados DTUS e limites de armazenamento

Computação tamanhos são expressos em termos de unidades de transação de base de dados (DTUs) para bases de dados únicas e unidades de transação da base de dados elástica (eDTUs) para conjuntos elásticos. Para obter mais informações sobre DTUs e eDTUs, veja [modelo de compra baseado em DTU](sql-database-purchase-models.md#dtu-based-purchasing-model)?

||Básica|Standard|Premium|
| :-- | --: | --: | --: | --: |
| Tamanho máximo de armazenamento | 2 GB | 1 TB | 4 TB  |
| Limite máximo de DTUs | 5 | 3000 | 4000 | 
||||||

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>EDTU do conjunto elástico, armazenamento e limites de bases de dados agrupadas

| | **Básica** | **Standard** | **Premium** |
| :-- | --: | --: | --: | --: |
| Tamanho de armazenamento máximo por base de dados  | 2 GB | 1 TB | 1 TB |
| Tamanho de armazenamento máximo por conjunto | 156 GB | 4 TB | 4 TB |
| Máximo eDTUs por base de dados | 5 | 3000 | 4000 |
| Máximo eDTUs por conjunto | 1600 | 3000 | 4000 |
| Número máximo de bases de dados por conjunto | 500  | 500 | 100 |
||||||

> [!IMPORTANT]
> Mais de 1 TB de armazenamento no escalão Premium está atualmente disponível em todas as regiões, exceto: Leste da China, Norte da China, Alemanha Central, Nordeste da Alemanha, e.u.a. centro-oeste, US DoD regiões e Governo dos E.u. a centro. Noutras regiões, o armazenamento máximo no escalão Premium está limitado a 1 TB.  Para obter mais informações, consulte [limitações atuais do P11-P15](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md).

## <a name="dtu-benchmark"></a>Referência da DTU

Características físicas (CPU, memória, e/s) associadas a cada medida DTU são calibradas usando um parâmetro de comparação que simula a carga de trabalho de base de dados do mundo real.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Correlacionando os resultados do benchmark do desempenho de base de dados do mundo real

É importante compreender que todos os benchmarks são representativas e indicativos apenas. As taxas de transação obtidas com a aplicação de parâmetro de comparação não serão iguais aos que pode ser alcançado com outros aplicativos. O parâmetro de comparação é composto por uma coleção de transação diferentes tipos de executam num esquema que contém uma série de tabelas e os tipos de dados. Embora o benchmark exercita as mesmas operações básicas que são comuns a todas as cargas de trabalho OLTP, não representam qualquer classe específica de banco de dados ou aplicação. O objetivo do parâmetro de comparação é fornecer um guia razoável para o desempenho relativo de uma base de dados que pode ser esperado quando aumentando ou reduzindo entre tamanhos de computação. Na realidade, bases de dados são de tamanhos diferentes e a complexidade, encontram mixes diferentes de cargas de trabalho e irão responder de formas diferentes. Por exemplo, um aplicativo de e/s intensiva pode atingir os limites de e/s mais cedo ou um aplicativo de uso intenso da CPU pode atingir os limites de CPU mais cedo. Não é garantido que qualquer banco de dados específico dimensionará da mesma forma como o parâmetro de comparação em aumento de carga.

O parâmetro de comparação e a sua metodologia são descritas em mais detalhes abaixo.

### <a name="benchmark-summary"></a>Resumo de benchmark

O parâmetro de comparação mede o desempenho de uma combinação de operações de base de dados básica que ocorrem mais frequentemente em cargas de trabalho (OLTP) de processamento de transações online. Embora o benchmark destina-se com computação de nuvem em mente, o esquema de base de dados, a população de dados e transações foram concebidas para ser amplamente representa os elementos básicos, geralmente utilizados em cargas de trabalho OLTP.

### <a name="schema"></a>Esquema

O esquema foi concebido para ter suficiente variedade e complexidade para oferecer suporte a uma ampla gama de operações. O parâmetro de comparação é executado numa base de dados composto por seis tabelas. As tabelas caem em três categorias: tamanho fixo, dimensionar e crescer. Existem duas tabelas de tamanho fixo; três tabelas dimensionamento; e uma tabela de cada vez maior. Tabelas de tamanho fixo têm um número constante de linhas. Dimensionamento tabelas têm uma cardinalidade que é proporcional ao desempenho da base de dados, mas não muda durante o benchmark. A tabela de cada vez maior é o tamanho como uma tabela de dimensionamento no carregamento inicial, mas, em seguida, as alterações de cardinalidade no decorrer de executar o benchmark como linhas são inseridas e eliminadas.

O esquema inclui uma combinação de tipos de dados, incluindo o número inteiro, numéricos, de caracteres e de data/hora. O esquema inclui chaves primárias e secundárias, mas não quaisquer chaves externas - ou seja, existem sem restrições de integridade referencial entre tabelas.

Um programa de geração de dados gera os dados da base de dados inicial. Dados de número inteiro e numéricos são gerados com várias estratégias. Em alguns casos, os valores são distribuídos aleatoriamente ao longo de um intervalo. Em outros casos, um conjunto de valores aleatoriamente é permuted para garantir que uma distribuição específica é mantida. Campos de texto são gerados a partir de uma lista ponderada de palavras para produzir os dados de aparência realistas.

A base de dados tem o tamanho com base no "fator de escala". O fator de dimensionamento (abreviado como SF) determina a cardinalidade da dimensionar e crescer tabelas. Conforme descrito abaixo na secção utilizadores e Pacing, o tamanho da base de dados, número de utilizadores e o desempenho máximo todos Dimensionar em proporção entre si.

### <a name="transactions"></a>Transações

A carga de trabalho consiste em nove tipos de transação, conforme mostrado na tabela abaixo. Cada transação foi concebida para destacar um determinado conjunto de características de sistema na base de dados sistema e de motor de hardware, com alto contraste das outras transações. Essa abordagem torna mais fácil avaliar o impacto de diferentes componentes para o desempenho geral. Por exemplo, a transação "Leitura pesado" produz um número significativo de operações de leitura do disco.

| Tipo de Transação | Descrição |
| --- | --- |
| Ler Lite |SELECIONAR; dentro da memória; só de leitura |
| Média de leitura |SELECIONAR; principalmente na memória; só de leitura |
| Pesado de leitura |SELECIONAR; não principalmente na memória; só de leitura |
| Atualização Lite |ATUALIZAR; dentro da memória; leitura-escrita |
| Atualizar pesado |ATUALIZAR; não principalmente na memória; leitura-escrita |
| INSERT Lite |INSERIR; dentro da memória; leitura-escrita |
| Inserir pesado |INSERIR; não principalmente na memória; leitura-escrita |
| Eliminar |ELIMINAR; combinação de dentro da memória e não na memória; leitura-escrita |
| Pesado de CPU |SELECIONAR; dentro da memória; carga de CPU relativamente pesada; só de leitura |

### <a name="workload-mix"></a>Combinação de carga de trabalho

Transações são selecionadas aleatoriamente entre uma distribuição ponderada com a mistura geral seguinte. A mistura geral tem um rácio de leitura/escrita de aproximadamente 2:1.

| Tipo de Transação | % de combinação |
| --- | --- |
| Ler Lite |35 |
| Média de leitura |20 |
| Pesado de leitura |5 |
| Atualização Lite |20 |
| Atualizar pesado |3 |
| INSERT Lite |3 |
| Inserir pesado |2 |
| Eliminar |2 |
| Pesado de CPU |10 |

### <a name="users-and-pacing"></a>Os utilizadores e o ritmo de processamento

A carga de trabalho de parâmetro de comparação é orientada a partir de uma ferramenta que submete transações num conjunto de ligações para simular o comportamento de um número de utilizadores em simultâneo. Embora todas as ligações e transações sejam máquina gerada, para manter a simplicidade nos Referimos a estas ligações como "utilizadores". Embora cada usuário funciona independentemente todos os outros utilizadores, todos os utilizadores executam o mesmo ciclo de passos abaixo:

1. Estabelece uma ligação de base de dados.
2. Repita até que o indicado para sair:
   - Selecione uma transação aleatoriamente (a partir de uma distribuição ponderada).
   - Executar a transação selecionada e medir o tempo de resposta.
   - Aguarde um atraso de ritmo de processamento.
3. Feche a ligação de base de dados.
4. Saída.

O atraso ritmo de processamento (no passo c de 2) está selecionado aleatoriamente, mas com uma distribuição que tenha uma média de segundo 1.0. Assim, a cada utilizador pode, a gerar em média, no máximo uma transação por segundo.

### <a name="scaling-rules"></a>Regras de dimensionamento

O número de utilizadores é determinado pelo tamanho da base de dados (em unidades de fator de escala). Existe um utilizador para todos os cinco unidades de fator de escala. Devido a atraso o ritmo de processamento, um usuário pode gerar um máximo de uma transação por segundo, em média.

Por exemplo, um-fator de escala de 500 (SF = 500) base de dados terão de 100 utilizadores e pode chegar um ritmo máximo de 100 TPS. Para orientar um TPS maior taxa requer mais usuários e uma base de dados maior.

### <a name="measurement-duration"></a>Duração de medida

Um parâmetro de comparação válido executar requer uma duração de medição do estado estável de, pelo menos, uma hora.

### <a name="metrics"></a>Métricas

As métricas chave no benchmark são débito e tempo de resposta.

- O débito é a medida de desempenho essenciais no benchmark. Taxa de transferência é reportada no transações por unidade-de-time, contagem de todos os tipos de transação.
- Tempo de resposta é uma medida de previsibilidade do desempenho. A restrição de tempo de resposta varia de acordo com a classe de serviço, com mais classes de serviço com um requisito de tempo de resposta mais rigoroso, conforme mostrado abaixo.

| Classe de serviço | Medida de débito | Requisito de tempo de resposta |
| --- | --- | --- |
| Premium |Transações por segundo |é o percentil 95 0,5 segundos |
| Standard |Transações por minuto |percentil 90th em segundos 1.0 |
| Básica |Transações por hora |percentil 80th em segundos 2.0 |

## <a name="next-steps"></a>Passos Seguintes

- Para obter detalhes sobre específicas de computação tamanhos e opções de tamanho de armazenamento disponíveis para bases de dados individuais, consulte [limites de recursos baseados em DTU de base de dados SQL para bases de dados individuais](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Para obter detalhes sobre específicas de computação tamanhos e opções de tamanho de armazenamento disponíveis para conjuntos elásticos, veja [limites dos recursos baseados em DTU da base de dados de SQL](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
