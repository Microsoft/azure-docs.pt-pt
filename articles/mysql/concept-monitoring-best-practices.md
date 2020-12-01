---
title: Monitorização das melhores práticas - Base de Dados Azure para o MySQL
description: Este artigo descreve as melhores práticas para monitorizar a sua Base de Dados Azure para o MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 59301e26f4d42056322ba5a7cdaff1555c531096
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355052"
---
# <a name="best-practices-for-monitoring-azure-database-for-mysql--single-server"></a>Melhores práticas para monitorizar a Base de Dados Azure para o MySQL -Servidor único

Conheça as melhores práticas que podem ser usadas para monitorizar as suas operações de base de dados e certifique-se de que o desempenho não está comprometido à medida que o tamanho dos dados aumenta. À medida que adicionamos novas capacidades à plataforma, continuaremos a aperfeiçoar as melhores práticas detalhadas nesta secção.

## <a name="layout-of-the-current-monitoring-toolkit"></a>Layout do kit de ferramentas de monitorização atual

A Azure Database for MySQL fornece ferramentas e métodos que pode usar para monitorizar facilmente o uso, adicionar ou remover recursos (como CPU, memória ou I/O), resolver problemas potenciais e ajudar a melhorar o desempenho de uma base de dados. Pode monitorizar as [métricas](concepts-monitoring.md#metrics) de desempenho regularmente para ver os valores médios, máximos e mínimos para uma variedade de intervalos de tempo.

Pode [configurar alertas](howto-alert-on-metric.md#create-an-alert-rule-on-a-metric-from-the-azure-portal) para um limiar métrico, para que seja informado se o servidor atingiu esses limites e tomar as ações apropriadas.  

Monitorize o servidor de base de dados para se certificar de que os recursos atribuídos à base de dados podem lidar com a carga de trabalho da aplicação. Se a base de dados estiver a atingir os limites de recursos, considere:
    * Identificar e otimizar as principais consultas que consomem recursos. 
    * Adicionar mais recursos através da atualização do nível de serviço.

### <a name="cpu-utilization"></a>Utilização da CPU
Monitorize a utilização do CPU e se a base de dados estiver a esgotar os recursos da CPU. Se a utilização do CPU for 90% ou mais do que deve aumentar o seu cálculo aumentando o número de vCores ou escala para o próximo nível de preços.  Certifique-se de que a potência ou a concordância são tão esperadas quanto escalar para cima/para baixo no CPU. 

### <a name="memory"></a>Memória 
A quantidade de memória disponível para o servidor de base de dados é proporcional ao [número de vCores](concepts-pricing-tiers.md). Certifique-se de que a memória é suficiente para a carga de trabalho. O teste de carga da sua aplicação para verificar se a memória é suficiente para operações de leitura e escrita. Se o consumo de memória da base de dados crescer frequentemente para além de um limiar definido, isto indica que deve atualizar o seu caso aumentando vCores ou nível de desempenho mais elevado. Utilize [a Loja de Consultas,](concepts-query-store.md) [Recomendações de Desempenho de Consulta](concepts-performance-recommendations.md) para identificar consultas com a duração mais longa, a maioria executada. Explore oportunidades para otimizar. 

### <a name="storage"></a>Armazenamento 
A [quantidade de armazenamento](howto-create-manage-server-portal.md#scale-compute-and-storage) prevista para o servidor MySQL determina os IOPs para o seu servidor. O armazenamento utilizado pelo serviço inclui os ficheiros de base de dados, registos de transações, registos de servidores e instantâneos de backup. Certifique-se de que o espaço do disco consumido não excede constantemente acima de 85% do espaço total do disco a provisionado. Se for esse o caso, é necessário eliminar ou arquivar dados do servidor de bases de dados para libertar algum espaço. 

### <a name="network-traffic"></a>Tráfego de rede 

**Rede Receber Produção, Transmissão de Transmissão** de Rede – A taxa de tráfego de rede de e para o caso MySQL em megabytes por segundo. É necessário avaliar o requisito de produção para o servidor e monitorizar constantemente o tráfego se a produção for inferior ao esperado. 

### <a name="database-connections"></a>Ligações de base de dados 
**Ligações de dados** – O número de sessões de clientes que estão ligadas à Base de Dados Azure para o MySQL deve ser alinhado com os limites de ligação para o tamanho [SKU selecionado.](concepts-server-parameters.md#max_connections) 


## <a name="next-steps"></a>Passos seguintes

- [Melhores práticas para o desempenho da Base de Dados Azure para o MySQL](concept-performance-best-practices.md)
- [Melhores práticas para operações de servidor usando Azure Database para MySQL](concept-operation-excellence-best-practices.md)
