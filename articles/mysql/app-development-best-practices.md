---
title: Boas práticas de desenvolvimento de aplicativos - Azure Database for MySQL
description: Saiba mais sobre as melhores práticas ao construir uma app com Azure Database para o MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 36f9cfa2369032351f6ed2948fc0c98c1648bcb6
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2020
ms.locfileid: "88259256"
---
# <a name="best-practices-for-building-applications-with-azure-database-for-mysql"></a>Melhores práticas para a construção de aplicações com Base de Dados Azure para o MySQL 

Aqui estão algumas boas práticas para ajudar a construir aplicações prontas para nuvens usando a Base de Dados Azure para o MySQL que pode reduzir o tempo de desenvolvimento para a sua aplicação. 

## <a name="application-and-database-resource-configuration"></a>Configuração de recursos de aplicação e base de dados

### <a name="application-and-database-in-the-same-region"></a>Aplicação e Base de Dados na mesma região
Certifique-se de que **todas as suas dependências estão na mesma região ao**  implementar a sua aplicação em Azure. A difusão de casos em regiões ou zonas de disponibilidade cria latência de rede, o que pode afetar o desempenho global da sua aplicação. 

### <a name="keep-your-mysql-server-secure"></a>Mantenha o seu servidor MySQL seguro
O seu servidor MySQL deve ser configurado para ser [seguro](https://docs.microsoft.com/azure/mysql/concepts-security) e não acessível publicamente. Utilize qualquer uma destas opções para proteger o seu servidor: 
- [Regras de firewall](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) ou
- [Redes Virtuais](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet) ou 
- [Ligação Privada](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)

Para obter segurança, deve ligar-se sempre ao seu servidor MySQL sobre **SSL** e configurar o seu servidor MySQL e a sua aplicação para utilizar **o TLS1.2**. Ver [Como configurar o SSL/TLS](https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security). 

### <a name="tune-your-server-parameters"></a>Sintonize os parâmetros do servidor
Para cargas de trabalho pesadas de leitura que afinam estes parâmetros, "tmp_table_size e max_heap_table_size" pode ajudar a otimizar para um melhor desempenho. Para calcular os valores necessários para tmp_table_size e max_heap_table_size, analise os valores totais de memória por ligação e a memória base. A soma dos parâmetros de memória por ligação, excluindo tmp_table_size, combinada com as contas de memória base para a memória total do servidor.

Para calcular o maior tamanho possível de tmp_table_size e max_heap_table_size, utilize a seguinte fórmula:

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> A memória total indica a quantidade total de memória que o servidor tem através do vCores previsto.  Por exemplo, numa Base de Dados Azure De Finalidade Geral 2 vCore para servidor MySQL, a memória total será de 5GB * 2.  Mais detalhes sobre a memória para cada nível podem ser encontrados na documentação do [nível de preços.](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers)
> A memória base indica as variáveis de memória, como query_cache_size e innodb_buffer_pool_size, que o MySQL rubricará e alocará no início do servidor.  Por memória de conexão, como sort_buffer_size e join_buffer_size, é a memória que é atribuída apenas quando uma consulta a exige.

### <a name="create-a-non-admin-user"></a>Criar um utilizador não administrador 
[Criar utilizadores não administrativos](https://docs.microsoft.com/azure/mysql/howto-create-users) para cada uma das bases de dados. Normalmente, os nomes de utilizador são identificados como os nomes DB.

### <a name="resetting-your-password"></a>Repor a sua palavra-passe
Pode [redefinir a sua palavra-passe](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password) para o seu servidor MySQL utilizando o portal Azure. 

Redefinir a palavra-passe do servidor para uma base de dados de produção pode reduzir a sua aplicação. É um bom padrão para redefinir a palavra-passe para quaisquer cargas de trabalho de produção fora do pico para minimizar o impacto para os utilizadores finais da sua aplicação.

## <a name="performance-and-resiliency"></a>Performance e Resiliência 
Aqui estão algumas ferramentas e padrões que pode usar para ajudar a depurar problemas de desempenho com a sua aplicação.

### <a name="enable-slow-query-logs-identify-performance-issues"></a>Ativar registos de consulta lenta identificar problemas de desempenho
Pode ativar [registos de consultas lentas](https://docs.microsoft.com/azure/mysql/concepts-server-logs) e [registos de auditoria](https://docs.microsoft.com/azure/mysql/concepts-audit-logs) no seu servidor. Analisar registos de consultas lentas pode ajudar a identificar estrangulamentos de desempenho para a resolução de problemas. 

Os registos de auditoria também estão disponíveis através de Registos de Diagnóstico Azure em registos do Monitor Azure, Centros de Eventos e Conta de Armazenamento também. Veja [como resolver problemas de problemas de desempenho](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance).

### <a name="use-connection-pooling"></a>Utilize agrupamento de ligação
A gestão de ligações de base de dados pode ter um impacto significativo no desempenho da aplicação como um todo. Para otimizar o desempenho, deve reduzir o número de vezes que as ligações são estabelecidas e tempo para estabelecer ligações em caminhos-chave de código.  Utilize [o agrupamento de ligação](https://docs.microsoft.com/azure/mysql/concepts-connectivity#access-databases-by-using-connection-pooling-recommended) para ligar à Base de Dados Azure para o MySQL para melhorar a resiliência e o desempenho. 

[ProxySQL,](https://proxysql.com/)que é um pooler de ligação pode ser usado eficientemente para gerir ligações. A utilização de um pooler de ligação pode diminuir as ligações ociosas e reutilizar as ligações existentes ajudará a evitar problemas. Veja [como configurar o ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) para saber mais. 

### <a name="retry-logic-to-handle-transient-errors"></a>Relemgar lógica para lidar com erros transitórios
A sua aplicação pode sofrer [erros transitórios](https://docs.microsoft.com/azure/mysql/concepts-connectivity#handling-transient-errors) em que as ligações à base de dados estão a ser largadas ou perdidas intermitentemente. Nestas situações, o servidor está a funcionar depois de um a dois tentar novamente em 5-10 segundos. 

Um bom padrão a seguir com a repetição é esperar 5 segundos antes da primeira repetição e, em seguida, seguir cada volta, aumentando a espera gradualmente até 60 segundos. Limite o número máximo de retríss em que a sua aplicação considera que a operação falhou para que possa então investigar mais aprofundadamente. Veja [como resolver erros de ligação](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-common-connection-issues) para aprender mais. 

### <a name="enable-read-replication-to-mitigate-failovers"></a>Permitir a replicação de leitura para mitigar falhas
Pode utilizar [a replicação de data-in](https://docs.microsoft.com/azure/mysql/howto-data-in-replication) para cenários de failover. Ao utilizar réplicas de leitura, não ocorrem falhas automatizadas entre servidores mestre e réplica. Notarão um desfasamento entre o mestre e a réplica, uma vez que a replicação é assíncronea. O lag de rede pode ser influenciado por muitos fatores, como o peso da carga de trabalho que funciona no servidor principal e a latência entre centros de dados. Na maioria dos casos, o atraso da réplica varia entre alguns segundos e alguns minutos.

## <a name="database-deployment"></a>Implementação de bases de dados 

### <a name="configure-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>Configure base de dados Azure para a tarefa MySQL no seu pipeline de implantação CI/CD
De vez em quando, terá de implementar alterações na sua base de dados. Nesses casos, pode criar a integração contínua (CI) e a entrega contínua (CD) através [dos oleodutos Azure](https://azure.microsoft.com/services/devops/pipelines/) e utilizar uma tarefa para [o seu servidor MySQL](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment?view=azure-devops) atualizar a base de dados executando um script personalizado na sua base de dados.

### <a name="manual-database-deployment"></a>Implementação de base de dados manual 
Durante a implementação da base de dados manual, aqui está um bom padrão a seguir para minimizar o tempo de inatividade ou reduzir o risco de implantação falhada: 

1. Criar uma cópia da base de dados de produção numa nova base de dados utilizando [a bancada mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) ou [mySQL](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html) 
2. Atualize a nova base de dados com as alterações ou atualizações necessárias para a sua base de dados. 
3. Coloque a base de dados de produção em estado de leitura. Não deve ter operações de escrita na base de dados de produção até que a implantação esteja concluída. 
4. Teste a sua aplicação com a base de dados recentemente atualizada a partir do passo 1.
5. Implemente as alterações na sua aplicação e certifique-se de que a aplicação está agora a utilizar a nova base de dados que possui as últimas atualizações. 
6. Guarde a antiga base de dados de produção para que possa reverter as alterações. Em seguida, pode avaliar para eliminar a antiga base de dados de produção ou exportá-la para armazenamento Azure, se necessário. 

>[!NOTE]
>  - Se a aplicação for como uma aplicação de ecommerce onde pode não ser capaz de colocá-la em estado de leitura, então implemente as alterações diretamente na base de dados de produção depois de fazer uma cópia de segurança.  As alterações de teses deverão ocorrer durante as horas de ponta com baixo tráfego para a app para minimizar o impacto, uma vez que alguns utilizadores podem experimentar um pedido falhado. 
>  - Certifique-se de que o seu código de aplicação também lida com quaisquer pedidos falhados.

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>Use métricas nativas mySQL para ver se a sua carga de trabalho está excedendo os tamanhos de tabela temporárias na memória
Com uma carga de trabalho pesada de leitura, as consultas executadas contra o seu servidor MySQL podem exceder os tamanhos de tabela temporários na memória. Pode fazer com que o seu servidor mude para escrever tabelas temporárias para o disco que afeta o desempenho da sua aplicação. Para determinar se o seu servidor está a escrever para o disco como resultado de exceder o tamanho da tabela temporária, veja as seguintes métricas:

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
A métrica created_tmp_disk_tables indica quantas tabelas foram criadas no disco, enquanto a métrica created_tmp_table indica quantas tabelas temporárias têm de ser formadas na memória dada a sua carga de trabalho. Para determinar se a execução de uma consulta específica utilizará tabelas temporárias, faça uma explicação sobre a consulta. O detalhe na coluna 'extra' indica "Utilização temporária" se a consulta funcionar com tabelas temporárias.

Para calcular a percentagem da sua carga de trabalho com consultas derramando em discos, utilize os seus valores métricos na fórmula abaixo:

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

Idealmente, esta percentagem deve ser menos 25%. Se vir que a percentagem é de 25% ou mais, sugerimos modificar dois parâmetros do servidor, tmp_table_size e max_heap_table_si


## <a name="database-schema-and-queries"></a>Esquemas de base de dados e consultas

Aqui ficam algumas dicas e truques a ter em mente quando constrói o seu esquema de base de dados e as suas consultas.

### <a name="always-use-the-right-datatype-for-your--table-columns"></a>Utilize sempre o tipo de dados certo para as colunas de tabela
A utilização dos tipos de dados certos com base no tipo de dados que pretende armazenar pode otimizar o armazenamento e reduzir quaisquer erros que possam ocorrer devido a tipos de dados incorretos.

### <a name="use-indexes"></a>Utilizar índices
Para evitar consultas lentas, pode utilizar Índices. Os índices podem ajudar a encontrar linhas com colunas específicas rapidamente. Veja [como utilizar índices no MySQL](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html).

### <a name="explain-your-select-queries"></a>EXPLIQUE as suas consultas SELECT
Utilize o [EXPLAIN](https://dev.mysql.com/doc/refman/8.0/en/explain.html) para obter informações sobre o que o MySQL está a fazer para executar a sua consulta. Isto pode ajudá-lo a detetar estrangulamentos ou problemas com a sua consulta. Ver [Como utilizar EXPLICA para o desempenho da consulta de perfil](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance).


