---
title: Melhores práticas de desempenho - Azure Database for MySQL
description: Este artigo descreve as melhores práticas para monitorizar e sintonizar o desempenho da sua Base de Dados Azure para o MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 30176e2df850e6d2794ab9c1542bcb6a89d8f89f
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880411"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>Melhores práticas para um melhor desempenho da sua Base de Dados Azure para MySQL - Servidor único

Conheça as melhores práticas para obter o melhor desempenho enquanto trabalha com a sua Base de Dados Azure para o MySQL - Servidor Único. À medida que adicionamos novas capacidades à plataforma, continuaremos a aperfeiçoar as melhores práticas detalhadas nesta secção.

## <a name="physical-proximity"></a>Proximidade Física

 Certifique-se de que implementa uma aplicação e a base de dados na mesma região. Uma verificação rápida antes de iniciar qualquer corrida de benchmarking de desempenho é determinar a latência da rede entre o cliente e a base de dados usando uma consulta select 1 simples. 

## <a name="accelerated-networking"></a>Redes Aceleradas

Utilize rede acelerada para o servidor de aplicações se estiver a utilizar a máquina virtual Azure , Azure Kubernetes ou Serviços de Aplicações. O Networking Acelerado permite uma virtualização de E/S de raiz única (SR-IOV) a um VM, melhorando consideravelmente o seu desempenho em rede. Este percurso de alto desempenho contorna o hospedeiro do datapath, reduzindo a latência, o nervosismo e a utilização do CPU, para utilização com as cargas de trabalho de rede mais exigentes em tipos de VM suportados.

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
- Configurar alertas sobre estes números para garantir que à medida que os servidores atingem limites, pode tomar ações rápidas para corrigi-lo. Com base nos limites definidos, verifique se o escalonamento da base de dados SKU - quer para um tamanho de computação mais elevado, quer para um melhor nível de preços, o que resulta num aumento dramático do desempenho. 
- Dimensione até que os seus números de desempenho não desçam drasticamente após uma operação de escala. Para obter informações sobre a monitorização das métricas de um exemplo de [DB, consulte as métricas de DB mySQL](./concepts-monitoring.md#metrics).

## <a name="next-steps"></a>Próximos passos

- [Melhores práticas para operações de servidor usando Azure Database para MySQL](concept-operation-excellence-best-practices.md) <br/>
- [Melhores práticas para monitorizar a sua Base de Dados Azure para o MySQL](concept-monitoring-best-practices.md)<br/>
- [Começa com a Base de Dados Azure para o MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>