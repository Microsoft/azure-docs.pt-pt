---
title: Melhores práticas de desempenho - Azure Database for MySQL
description: Este artigo descreve algumas recomendações para monitorizar e sintonizar o desempenho da sua Base de Dados Azure para o MySQL.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/28/2021
ms.openlocfilehash: 7b5223bc08c470a0e8722b76b80473aaa235b51a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727163"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>Melhores práticas para um melhor desempenho da sua Base de Dados Azure para MySQL - Servidor único

Saiba como obter o melhor desempenho enquanto trabalha com a sua Base de Dados Azure para o MySQL - Servidor Único. À medida que adicionamos novas capacidades à plataforma, continuaremos a refinar as nossas recomendações nesta secção.

## <a name="physical-proximity"></a>Proximidade Física

 Certifique-se de que implementa uma aplicação e a base de dados na mesma região. Uma verificação rápida antes de iniciar qualquer corrida de benchmarking de desempenho é determinar a latência da rede entre o cliente e a base de dados usando uma consulta select 1 simples. 

## <a name="accelerated-networking"></a>Redes Aceleradas

Utilize rede acelerada para o servidor de aplicações se estiver a utilizar a máquina virtual Azure, Azure Kubernetes ou Serviços de Aplicação. O Networking Acelerado permite uma virtualização de E/S de raiz única (SR-IOV) a um VM, melhorando consideravelmente o seu desempenho em rede. Este percurso de alto desempenho contorna o hospedeiro do datapath, reduzindo a latência, o nervosismo e a utilização do CPU, para utilização com as cargas de trabalho de rede mais exigentes em tipos de VM suportados.

## <a name="connection-efficiency"></a>Eficiência da Ligação

Estabelecer uma nova ligação é sempre uma tarefa dispendiosa e morosa. Quando uma aplicação solicita uma ligação de base de dados, prioriza a atribuição de ligações de base de dados inativas existentes em vez de criar uma nova.  Aqui ficam algumas opções para boas práticas de ligação:

- **ProxySQL**: Utilize [o ProxySQL](https://proxysql.com/) que fornece pooling de ligação incorporada e equilíbrio de carga a [sua carga de trabalho](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) para múltiplas réplicas de leitura, conforme exigido a pedido, com quaisquer alterações no código de aplicação.

- **Heimdall Data Proxy**: Em alternativa, também pode alavancar a Proxy Heimdall Data Proxy, uma solução de procuração de propriedade neutra em fornecedor. Suporta a consulta e a leitura/escrita dividida com deteção de lag de replicação. Também pode consultar como acelerar o [Desempenho mySQL com o proxy Heimdall](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/accelerate-mysql-performance-with-the-heimdall-proxy/ba-p/1063349).  

- **Ligação persistente ou Long-Lived**: Se a sua aplicação tiver transações curtas ou consultas normalmente com tempo de execução < 5-10 ms, em seguida, substitua as ligações curtas por ligações persistentes. Substituir ligações curtas por ligações persistentes requer apenas pequenas alterações ao código, mas tem um grande efeito em termos de melhorar o desempenho em muitos cenários típicos de aplicação. Certifique-se de que define o tempo limite ou fecha a ligação quando a transação estiver concluída.

- **Replica**: Se estiver a utilizar réplica, utilize [o ProxySQL](https://proxysql.com/) para equilibrar a carga entre o servidor primário e o servidor de réplicas secundárias legíveis. Saiba [como configurar o ProxySQL.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847)

## <a name="data-import-configurations"></a>Configurações de Importação de Dados

- Pode escalar temporariamente o seu caso para um tamanho SKU mais elevado antes de iniciar uma operação de importação de dados e, em seguida, reduzi-lo quando a importação for bem sucedida.
- Pode importar os seus dados com o mínimo de tempo de inatividade utilizando [o Azure Database Migration Service (DMS)](https://datamigration.microsoft.com/) para migrações online ou offline. 

## <a name="azure-database-for-mysql-memory-recommendations"></a>Base de Dados Azure para Recomendações de Memória MySQL

Uma Base de Dados Azure para as melhores práticas de desempenho do MySQL é atribuir RAM suficiente para que você esteja trabalhando conjunto reside quase completamente na memória. 

- Verifique se a percentagem de memória utilizada para atingir os [limites](./concepts-pricing-tiers.md) utilizando as [métricas do servidor MySQL](./concepts-monitoring.md). 
- Configurar alertas sobre estes números para garantir que à medida que os servidores atingem limites, pode tomar ações rápidas para corrigi-lo. Com base nos limites definidos, verifique se o dimensionamento da base de dados SKU - quer para um maior tamanho de cálculo, quer para um melhor nível de preços, o que resulta num aumento dramático do desempenho. 
- Dimensione até que os seus números de desempenho não desçam drasticamente após uma operação de escala. Para obter informações sobre a monitorização das métricas de um exemplo de [DB, consulte as métricas de DB mySQL](./concepts-monitoring.md#metrics).
 
## <a name="use-innodb-buffer-pool-warmup"></a>Use o aquecimento da piscina tampão InnoDB

Depois de reiniciar a Base de Dados Azure para o servidor MySQL, as páginas de dados que residem no armazenamento são carregadas à medida que as tabelas são consultadas, o que leva a um aumento da latência e a um desempenho mais lento para a primeira execução das consultas. Isto pode não ser aceitável para cargas de trabalho sensíveis à latência. 

A utilização do aquecimento do pool de tampão InnoDB encurta o período de aquecimento recarregando as páginas de disco que estavam na piscina tampão antes do reinício, em vez de esperar que as operações DML ou SELECT acedam às linhas correspondentes.

Pode reduzir o período de aquecimento após reiniciar a base de dados Azure para o servidor MySQL, o que representa uma vantagem de desempenho configurando [os parâmetros do servidor do buffer pool InnoDB](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html). O InnoDB guarda uma percentagem das páginas mais usadas para cada piscina tampão no fecho do servidor e restaura estas páginas no arranque do servidor.

Também é importante notar que um melhor desempenho vem à custa de tempo de arranque mais longo para o servidor. Quando este parâmetro estiver ativado, espera-se que o início do servidor e o tempo de reinicie aumentem dependendo do IOPS previsto no servidor. 

Recomendamos testar e monitorizar o tempo de reinicio para garantir que o desempenho do arranque/reinicie é aceitável, uma vez que o servidor não está disponível durante esse período. Não é aconselhável utilizar este parâmetro com menos de 1000 IOPS provisionados (ou seja, quando o armazenamento previsto é inferior a 335 GB).

Para salvar o estado da piscina tampão no fecho do servidor, desace o parâmetro do servidor `innodb_buffer_pool_dump_at_shutdown` para `ON` . Da mesma forma, desace o parâmetro do servidor `innodb_buffer_pool_load_at_startup` para restaurar o estado do pool de `ON` tampão no arranque do servidor. Pode controlar o impacto no tempo de arranque/reinicio, reduzindo e afinando o valor do parâmetro do servidor `innodb_buffer_pool_dump_pct` . Por predefinição, este parâmetro é definido para `25` .

> [!Note]
> Os parâmetros de aquecimento do pool de tampão InnoDB só são suportados em servidores de armazenamento de finalidade geral com armazenamento até 16-TB. Saiba mais sobre [a Base de Dados Azure para as opções de armazenamento MySQL aqui.](./concepts-pricing-tiers.md#storage)

## <a name="next-steps"></a>Passos seguintes

- [Melhores práticas para operações de servidor usando Azure Database para MySQL](concept-operation-excellence-best-practices.md) <br/>
- [Melhores práticas para monitorizar a sua Base de Dados Azure para o MySQL](concept-monitoring-best-practices.md)<br/>
- [Começa com a Base de Dados Azure para o MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>