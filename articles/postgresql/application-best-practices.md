---
title: Boas práticas de desenvolvimento de aplicativos - Base de Dados Azure para PostgreSQL
description: Saiba mais sobre as melhores práticas para a construção de uma app utilizando a Base de Dados Azure para PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/10/2020
ms.openlocfilehash: 6463f30bc79d937bd5a51a5c8c78fbdd72954b1e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97364605"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-postgresql"></a>Melhores práticas para a construção de uma aplicação com Base de Dados Azure para PostgreSQL

Aqui estão algumas boas práticas para ajudá-lo a construir uma aplicação pronta para nuvens usando a Base de Dados Azure para PostgreSQL. Estas boas práticas podem reduzir o tempo de desenvolvimento da sua aplicação.

## <a name="configuration-of-application-and-database-resources"></a>Configuração de recursos de aplicação e de base de dados

### <a name="keep-the-application-and-database-in-the-same-region"></a>Mantenha a aplicação e a base de dados na mesma região
Certifique-se de que todas as suas dependências estão na mesma região ao implementar a sua aplicação em Azure. A difusão de casos em regiões ou zonas de disponibilidade cria latência de rede, o que pode afetar o desempenho global da sua aplicação.

### <a name="keep-your-postgresql-server-secure"></a>Mantenha o seu servidor PostgreSQL seguro
Configure o seu servidor PostgreSQL para ser [seguro](./concepts-security.md) e não acessível publicamente. Utilize uma destas opções para proteger o seu servidor:
- [Regras da firewall](./concepts-firewall-rules.md)
- [Redes virtuais](./concepts-data-access-and-security-vnet.md)
- [Azure Private Link](./concepts-data-access-and-security-private-link.md)

Para obter segurança, deve sempre ligar-se ao seu servidor PostgreSQL sobre o SSL e configurar o seu servidor PostgreSQL e a sua aplicação para utilizar o TLS 1.2. Ver [Como configurar o SSL/TLS](./concepts-ssl-connection-security.md).

### <a name="tune-your-server-parameters"></a>Sintonize os parâmetros do servidor
Para cargas de trabalho pesadas de leitura, afinando os parâmetros do `tmp_table_size` servidor, e `max_heap_table_size` pode ajudar a otimizar para um melhor desempenho. Para calcular os valores necessários para estas variáveis, olhe para os valores totais de memória por ligação e para a memória base. A soma dos parâmetros de memória por ligação, `tmp_table_size` excluindo, combinados com as contas da memória base para a memória total do servidor.

### <a name="use-environment-variables-for-connection-information"></a>Utilize variáveis ambientais para informações de ligação
Não guarde as suas credenciais de base de dados no seu código de aplicação. Dependendo da aplicação frontal, siga a orientação para configurar variáveis ambientais. Para o uso do serviço app, consulte[como configurar as definições de aplicações](../app-service/configure-common.md#configure-app-settings) e para o serviço Azure Kuberentes, veja [como usar os segredos de Kubernetes.](https://kubernetes.io/docs/concepts/configuration/secret/)

## <a name="performance-and-resiliency"></a>Desempenho e resiliência
Aqui estão algumas ferramentas e práticas que pode usar para ajudar a depurar problemas de desempenho com a sua aplicação.

### <a name="use-connection-pooling"></a>Utilizar pooling de ligação
Com a ligação de pooling, um conjunto fixo de ligações são estabelecidos no tempo de arranque e mantidos. Isto também ajuda a reduzir a fragmentação da memória no servidor que é causada pelas novas ligações dinâmicas estabelecidas no servidor de base de dados. O agrupamento de ligação pode ser configurado no lado da aplicação se a estrutura da aplicação ou o controlador de base de dados a suportarem. Se isso não for suportado, a outra opção recomendada é alavancar um serviço de pooler de ligação proxy como [PgBouncer](https://pgbouncer.github.io/) ou [Pgpool](https://pgpool.net/mediawiki/index.php/Main_Page) correndo fora da aplicação e conectando-se ao servidor de base de dados. Tanto o PgBouncer como o Pgpool são ferramentas baseadas na comunidade que funcionam com a Azure Database para PostgreSQL.

### <a name="retry-logic-to-handle-transient-errors"></a>Relemgar lógica para lidar com erros transitórios
A sua aplicação pode sofrer erros transitórios quando as ligações à base de dados são largadas ou perdidas intermitentemente. Nestas situações, o servidor está a funcionar depois de uma a duas retrações em 5 a 10 segundos. Um bom treino é esperar 5 segundos antes da primeira repetição. Em seguida, siga cada redandid, aumentando a espera gradualmente, até 60 segundos. Limite o número máximo de retretes em que o seu pedido considera que a operação falhou, para que possa então investigar mais aprofundadamente. Veja [como resolver erros de ligação](./concepts-connectivity.md) para saber mais.

### <a name="enable-read-replication-to-mitigate-failovers"></a>Permitir a replicação de leitura para mitigar falhas
Pode utilizar [a replicação de dados](./concepts-read-replicas.md) para cenários de falha. Quando se utiliza réplicas de leitura, não ocorre qualquer falha automatizada entre servidores de origem e réplica. Vai notar um desfasamento entre a fonte e a réplica porque a replicação é assíncronea. O lag de rede pode ser influenciado por muitos fatores, como o tamanho da carga de trabalho que funciona no servidor de origem e a latência entre centros de dados. Na maioria dos casos, o lag de réplica varia de alguns segundos a alguns minutos.


## <a name="database-deployment"></a>Implementação de base de dados

### <a name="configure-cicd-deployment-pipeline"></a>Configure o gasoduto de implantação CI/CD
De vez em quando, tem de implementar alterações na sua base de dados. Nesses casos, pode utilizar a integração contínua (CI) através [das ações do GitHub](https://github.com/Azure/postgresql/blob/master/README.md) para o seu servidor PostgreSQL atualizar a base de dados executando um script personalizado contra ele.

### <a name="define-manual-database-deployment-process"></a>Definir processo de implantação de bases de dados manuais
Durante a implementação da base de dados manual, siga estes passos para minimizar o tempo de inatividade ou reduzir o risco de implantação falhada:

- Crie uma cópia de uma base de dados de produção numa nova base de dados utilizando pg_dump.
- Atualize a nova base de dados com as alterações ou atualizações necessárias para a sua base de dados.
- Coloque a base de dados de produção num estado só de leitura. Não deve ter operações de escrita na base de dados de produção até que a implantação esteja concluída.
- Teste a sua aplicação com a base de dados recentemente atualizada a partir do passo 1.
- Implemente as alterações na sua aplicação e certifique-se de que a aplicação está agora a utilizar a nova base de dados que possui as últimas atualizações.
- Guarde a antiga base de dados de produção para que possa reverter as alterações. Em seguida, pode avaliar para eliminar a antiga base de dados de produção ou exportá-la para o Azure Storage, se necessário.

>  [!NOTE]
> Se a aplicação for como uma aplicação de e-commerce e não puder colocá-la em estado de leitura, implemente as alterações diretamente na base de dados de produção depois de fazer uma cópia de segurança. As alterações de teses devem ocorrer durante as horas de ponta com baixo tráfego para a app para minimizar o impacto, porque alguns utilizadores podem experimentar pedidos falhados. Certifique-se de que o seu código de aplicação também lida com quaisquer pedidos falhados.

## <a name="database-schema-and-queries"></a>Esquemas de base de dados e consultas
Aqui ficam algumas dicas a ter em mente quando constrói o seu esquema de base de dados e as suas consultas.

### <a name="use-bigint-or-uuid-for-primary-keys"></a>Use BIGINT ou UUID para chaves primárias
Ao construir aplicações personalizadas ou algumas estruturas, talvez utilizem `INT` em vez de chaves `BIGINT` primárias. Quando ```INT``` utilizar, corre o risco de o valor na sua base de dados poder exceder a capacidade de armazenamento do tipo de ```INT``` dados. Fazer esta alteração a uma aplicação de produção existente pode ser demorado com tempo de desenvolvimento mais dispendioso. Outra opção é usar [o UUID](https://www.postgresql.org/docs/current/datatype-uuid.html) para as teclas primárias. Este identificador utiliza uma corda gerada automaticamente de 128 bits, por exemplo ```a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11``` . Saiba mais sobre [os tipos de dados PostgreSQL.](https://www.postgresql.org/docs/8.1/datatype.html)

### <a name="use-indexes"></a>Utilizar índices

Existem muitos tipos de [índices](https://www.postgresql.org/docs/9.1/indexes.html) em Postgres que podem ser usados de diferentes maneiras. A utilização de um índice ajuda o servidor a encontrar e recuperar linhas específicas muito mais rapidamente do que poderia fazer sem um índice. Mas os índices também adicionam sobrecarga ao servidor de base de dados, evitando assim ter demasiados índices.

### <a name="use-autovacuum"></a>Use autovacuum
Pode otimizar o seu servidor com autovacuum numa Base de Dados Azure para servidor PostgreSQL. PostgreSQL permite maior concordância na base de dados, mas com cada atualização resulta inserção e eliminação. Para eliminar , os registos são marcados suavemente que serão purgados mais tarde. Para realizar estas tarefas, a PostgreSQL executa um trabalho de vácuo. Se não aspirar de vez em quando, os tuples mortos que se acumulam podem resultar em:

- Inchar dados, como bases de dados e tabelas maiores.
- Índices sub-ideais maiores.
- Aumento de E/S.

Saiba mais [sobre como otimizar com o autovacuum.](howto-optimize-autovacuum.md)

### <a name="use-pg_stats_statements"></a>Use pg_stats_statements
Pg_stat_statements é uma extensão PostgreSQL que é ativada por padrão na Base de Dados Azure para PostgreSQL. A extensão fornece um meio para rastrear as estatísticas de execução de todas as declarações SQL executadas por um servidor. Veja [como usá pg_statement](howto-optimize-query-stats-collection.md).


### <a name="use-the-query-store"></a>Utilize a Loja de Consultas
A funcionalidade [de Loja de Consultas](./concepts-query-store.md) na Base de Dados Azure para PostgreSQL fornece um método mais eficaz para rastrear estatísticas de consulta. Recomendamos esta funcionalidade como alternativa à utilização de pg_stats_statements.

### <a name="optimize-bulk-inserts-and-use-transient-data"></a>Otimizar inserções a granel e utilizar dados transitórios
Se tiver operações de carga de trabalho que envolvam dados transitórios ou que insira grandes conjuntos de dados a granel, considere utilizar tabelas nãologadas. Proporcionaatidade e durabilidade, por defeito. Aatóatriedade, a consistência, o isolamento e a durabilidade compõem as propriedades acid. Veja [como otimizar as inserções a granel.](howto-optimize-bulk-inserts.md)

## <a name="next-steps"></a>Passos Seguintes
[Guia de Postgres](http://postgresguide.com/)
