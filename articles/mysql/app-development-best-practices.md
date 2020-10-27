---
title: Boas práticas de desenvolvimento de aplicativos - Azure Database for MySQL
description: Saiba mais sobre as melhores práticas para a construção de uma aplicação utilizando a Base de Dados Azure para o MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: afe14bc03f0d12e56e1512aeb788a77c64151b58
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547253"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-mysql"></a>Melhores práticas para a construção de uma aplicação com Base de Dados Azure para o MySQL 

Aqui estão algumas boas práticas para ajudá-lo a construir uma aplicação pronta para nuvens usando a Base de Dados Azure para o MySQL. Estas boas práticas podem reduzir o tempo de desenvolvimento da sua aplicação. 

## <a name="configuration-of-application-and-database-resources"></a>Configuração de recursos de aplicação e de base de dados

### <a name="keep-the-application-and-database-in-the-same-region"></a>Mantenha a aplicação e a base de dados na mesma região
Certifique-se de que todas as suas dependências estão na mesma região ao implementar a sua aplicação em Azure. A difusão de casos em regiões ou zonas de disponibilidade cria latência de rede, o que pode afetar o desempenho global da sua aplicação. 

### <a name="keep-your-mysql-server-secure"></a>Mantenha o seu servidor MySQL seguro
Configure o servidor MySQL para ser [seguro](./concepts-security.md) e não acessível publicamente. Utilize uma destas opções para proteger o seu servidor: 
- [Regras da firewall](./concepts-firewall-rules.md)
- [Redes virtuais](./concepts-data-access-and-security-vnet.md) 
- [Azure Private Link](./concepts-data-access-security-private-link.md)

Para obter segurança, deve sempre ligar-se ao seu servidor MySQL sobre SSL e configurar o seu servidor MySQL e a sua aplicação para utilizar o TLS 1.2. Ver [Como configurar o SSL/TLS](./concepts-ssl-connection-security.md). 

### <a name="tune-your-server-parameters"></a>Sintonize os parâmetros do servidor
Para cargas de trabalho pesadas de leitura, afinando os parâmetros do `tmp_table_size` servidor, e `max_heap_table_size` pode ajudar a otimizar para um melhor desempenho. Para calcular os valores necessários para estas variáveis, olhe para os valores totais de memória por ligação e para a memória base. A soma dos parâmetros de memória por ligação, `tmp_table_size` excluindo, combinados com as contas da memória base para a memória total do servidor.

Para calcular o maior tamanho possível `tmp_table_size` de `max_heap_table_size` e, utilize a seguinte fórmula:

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> A memória total indica a quantidade total de memória que o servidor tem através dos vCores previstos.  Por exemplo, numa Base de Dados Azure de dois vCore para o servidor MySQL, a memória total será de 5 GB * 2. Pode encontrar mais detalhes sobre a memória para cada nível na documentação do [nível de preços.](./concepts-pricing-tiers.md)
>
> A memória base indica as variáveis de memória, como `query_cache_size` `innodb_buffer_pool_size` e, que o MySQL rubricará e alocará no início do servidor. Memória por ligação, como `sort_buffer_size` `join_buffer_size` e, é memória que é atribuída apenas quando uma consulta precisa dela.

### <a name="create-non-admin-users"></a>Criar utilizadores não administrativos 
[Criar utilizadores não administrativos](./howto-create-users.md) para cada base de dados. Normalmente, os nomes de utilizador são identificados como os nomes da base de dados.

### <a name="reset-your-password"></a>Repor a palavra-passe
Pode [redefinir a sua palavra-passe](./howto-create-manage-server-portal.md#update-admin-password) para o seu servidor MySQL utilizando o portal Azure. 

Redefinir a palavra-passe do servidor para uma base de dados de produção pode reduzir a sua aplicação. É uma boa prática redefinir a palavra-passe para quaisquer cargas de trabalho de produção fora do pico para minimizar o impacto nos utilizadores da sua aplicação.

## <a name="performance-and-resiliency"></a>Desempenho e resiliência 
Aqui estão algumas ferramentas e práticas que pode usar para ajudar a depurar problemas de desempenho com a sua aplicação.

### <a name="enable-slow-query-logs-to-identify-performance-issues"></a>Permitir registos de consulta lenta para identificar problemas de desempenho
Pode ativar [registos de consultas lentas](./concepts-server-logs.md) e [registos de auditoria](./concepts-audit-logs.md) no seu servidor. Analisar registos de consultas lentas pode ajudar a identificar estrangulamentos de desempenho para a resolução de problemas. 

Os registos de auditoria também estão disponíveis através de registos Azure Diagnostics em registos do Azure Monitor, Azure Event Hubs e contas de armazenamento. Ver [Como resolver problemas de problemas questões de desempenho](./howto-troubleshoot-query-performance.md).

### <a name="use-connection-pooling"></a>Utilize agrupamento de ligação
A gestão de ligações de base de dados pode ter um impacto significativo no desempenho da aplicação como um todo. Para otimizar o desempenho, é necessário reduzir o número de vezes que as ligações são estabelecidas e o tempo para estabelecer ligações em caminhos-chave de código. Utilize [o agrupamento de ligação](./concepts-connectivity.md#access-databases-by-using-connection-pooling-recommended) para ligar à Base de Dados Azure para o MySQL para melhorar a resiliência e o desempenho. 

Pode utilizar o pooler de ligação [ProxySQL](https://proxysql.com/) para gerir eficientemente as ligações. A utilização de um pooler de ligação pode diminuir as ligações ociosas e reutilizar as ligações existentes, o que ajudará a evitar problemas. Veja [como configurar o ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) para saber mais. 

### <a name="retry-logic-to-handle-transient-errors"></a>Relemgar lógica para lidar com erros transitórios
A sua aplicação pode sofrer [erros transitórios](./concepts-connectivity.md#handling-transient-errors) quando as ligações à base de dados são largadas ou perdidas intermitentemente. Nestas situações, o servidor está a funcionar depois de uma a duas retrações em 5 a 10 segundos. 

Um bom treino é esperar 5 segundos antes da primeira repetição. Em seguida, siga cada redandid, aumentando a espera gradualmente, até 60 segundos. Limite o número máximo de retretes em que o seu pedido considera que a operação falhou, para que possa então investigar mais aprofundadamente. Veja [como resolver erros de ligação](./howto-troubleshoot-common-connection-issues.md) para saber mais. 

### <a name="enable-read-replication-to-mitigate-failovers"></a>Permitir a replicação de leitura para mitigar falhas
Pode utilizar [a replicação de dados](./howto-data-in-replication.md) para cenários de falha. Quando se utiliza réplicas de leitura, não ocorre qualquer falha automatizada entre servidores de origem e réplica. 

Vai notar um desfasamento entre a fonte e a réplica porque a replicação é assíncronea. O lag de rede pode ser influenciado por muitos fatores, como o tamanho da carga de trabalho que funciona no servidor de origem e a latência entre centros de dados. Na maioria dos casos, o lag de réplica varia de alguns segundos a alguns minutos.

## <a name="database-deployment"></a>Implantação de bases de dados 

### <a name="configure-an-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>Configure uma base de dados Azure para a tarefa MySQL no seu pipeline de implantação CI/CD
De vez em quando, tem de implementar alterações na sua base de dados. Nesses casos, pode utilizar a integração contínua (CI) e a entrega contínua (CD) através [dos Pipelines Azure](https://azure.microsoft.com/services/devops/pipelines/) e utilizar uma tarefa para [o seu servidor MySQL](/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment?view=azure-devops&preserve-view=true) atualizar a base de dados executando um script personalizado contra ele.

### <a name="use-an-effective-process-for-manual-database-deployment"></a>Utilize um processo eficaz para a implementação de bases de dados manuais 
Durante a implementação da base de dados manual, siga estes passos para minimizar o tempo de inatividade ou reduzir o risco de implantação falhada: 

1. Crie uma cópia de uma base de dados de produção numa nova base de dados utilizando [o mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) ou [o MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html). 
2. Atualize a nova base de dados com as alterações ou atualizações necessárias para a sua base de dados. 
3. Coloque a base de dados de produção num estado só de leitura. Não deve ter operações de escrita na base de dados de produção até que a implantação esteja concluída. 
4. Teste a sua aplicação com a base de dados recentemente atualizada a partir do passo 1.
5. Implemente as alterações na sua aplicação e certifique-se de que a aplicação está agora a utilizar a nova base de dados que possui as últimas atualizações. 
6. Guarde a antiga base de dados de produção para que possa reverter as alterações. Em seguida, pode avaliar para eliminar a antiga base de dados de produção ou exportá-la para o Azure Storage, se necessário. 

>[!NOTE]
>Se a aplicação for como uma aplicação de e-commerce e não puder colocá-la em estado de leitura, implemente as alterações diretamente na base de dados de produção depois de fazer uma cópia de segurança. As alterações de teses devem ocorrer durante as horas de ponta com baixo tráfego para a app para minimizar o impacto, porque alguns utilizadores podem experimentar pedidos falhados. 
>
>Certifique-se de que o seu código de aplicação também lida com quaisquer pedidos falhados.

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>Use métricas nativas mySQL para ver se a sua carga de trabalho está excedendo os tamanhos de tabela temporárias na memória
Com uma carga de trabalho pesada de leitura, as consultas que correm contra o seu servidor MySQL podem exceder os tamanhos de tabela temporários na memória. Uma carga de trabalho pesada de leitura pode fazer com que o seu servidor mude para escrever tabelas temporárias para o disco, o que afeta o desempenho da sua aplicação. Para determinar se o seu servidor está a escrever para o disco como resultado de exceder o tamanho da tabela temporária, veja as seguintes métricas:

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
A `created_tmp_disk_tables` métrica indica quantas tabelas foram criadas no disco. A `created_tmp_table` métrica diz-lhe quantas tabelas temporárias têm de ser formadas na memória, dada a sua carga de trabalho. Para determinar se a execução de uma consulta específica utilizará tabelas temporárias, executa a declaração [EXPLICADA](https://dev.mysql.com/doc/refman/8.0/en/explain.html) sobre a consulta. O detalhe na `extra` coluna indica se a consulta `Using temporary` funcionará com tabelas temporárias.

Para calcular a percentagem da sua carga de trabalho com consultas derramando em discos, utilize os seus valores métricos na seguinte fórmula:

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

Idealmente, esta percentagem deve ser menos 25%. Se vir que a percentagem é de 25% ou mais, sugerimos modificar dois parâmetros do servidor, tmp_table_size e max_heap_table_size.

## <a name="database-schema-and-queries"></a>Esquemas de base de dados e consultas

Aqui ficam algumas dicas a ter em mente quando constrói o seu esquema de base de dados e as suas consultas.

### <a name="use-the-right-datatype-for-your-table-columns"></a>Utilize o tipo de dados certo para as colunas de tabela
A utilização do tipo de dados certo com base no tipo de dados que pretende armazenar pode otimizar o armazenamento e reduzir os erros que podem ocorrer devido a tipos de dados incorretos.

### <a name="use-indexes"></a>Utilizar índices
Para evitar consultas lentas, pode usar índices. Os índices podem ajudar a encontrar linhas com colunas específicas rapidamente. Ver [como utilizar índices no MySQL](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html).

### <a name="use-explain-for-your-select-queries"></a>Use EXPLICA PARA as suas consultas SELECT
Use a `EXPLAIN` declaração para obter informações sobre o que o MySQL está a fazer para executar a sua consulta. Pode ajudá-lo a detetar estrangulamentos ou problemas com a sua consulta. Ver [Como utilizar EXPLICA para o desempenho da consulta de perfil](./howto-troubleshoot-query-performance.md).