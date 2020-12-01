---
title: MySQL servidor operando as melhores práticas - Azure Database for MySQL
description: Este artigo descreve as melhores práticas para operar a sua base de dados MySQL no Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1d84483a701bf7fb4e6c50b9dc4f4f89993c64d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355117"
---
# <a name="best-practices-for-server-operations-on-azure-database-for-mysql--single-server"></a>Melhores práticas para operações de servidores na Base de Dados Azure para MySQL -Servidor único

Conheça as melhores práticas para trabalhar com a Azure Database para o MySQL. À medida que adicionamos novas capacidades à plataforma, continuaremos a focar-nos na refinação das melhores práticas detalhadas nesta secção.

## <a name="azure-database-for-mysql-operational-guidelines"></a>Base de Dados Azure para Diretrizes Operacionais mySQL 

Seguem-se as diretrizes operacionais que devem ser seguidas ao trabalhar com a sua Base de Dados Azure para o MySQL para melhorar o desempenho da sua base de dados: 

* **Co-localização**: Para reduzir a latência da rede, coloque o cliente e o servidor de base de dados na mesma região do Azure.

* **Monitorize a sua memória, CPU e utilização de armazenamento**: Pode [configurar alertas](howto-alert-on-metric.md) para o notificar quando os padrões de utilização mudarem ou quando se aproximar da capacidade da sua implantação, para que possa manter o desempenho e a disponibilidade do sistema. 

* **Dimensione o seu exemplo DB**: Pode [aumentar](howto-create-manage-server-portal.md) quando estiver a aproximar-se dos limites de capacidade de armazenamento. Deverá ter algum tampão no armazenamento e na memória para acomodar aumentos imprevistos na procura das suas aplicações. Também pode [ativar a](howto-auto-grow-storage-portal.md) funcionalidade de crescimento automático de armazenamento 'ON' apenas para garantir que o serviço escala automaticamente o armazenamento à medida que se aproxima dos limites de armazenamento. 

* **Configurar backups**: Permitir [backups locais ou geo-redundantes](howto-restore-server-portal.md#set-backup-configuration) com base na exigência do negócio. Além disso, modifica o período de retenção sobre quanto tempo as cópias de segurança estão disponíveis para a continuidade do negócio. 

* **Aumentar a capacidade de E/O**: Se a carga de trabalho da sua base de dados necessitar de mais E/S do que as suas operações transacionais para a sua base de dados será lenta. Para aumentar a capacidade de E/S de uma instância do servidor, faça qualquer ou todas as seguintes seguintes: 

    * A base de dados Azure para o MySQL fornece escala de IOPS à taxa de três IOPS por armazenamento de GB a fornecê-lo. [Aumente o armazenamento a provisionado](howto-create-manage-server-portal.md#scale-storage-up) para escalar o IOPS para um melhor desempenho. 

    * Se já estiver a utilizar o armazenamento de IOPS provisionado, provisa [capacidade de produção adicional](howto-create-manage-server-portal.md#scale-storage-up). 

* **Cálculo de escala**: A carga de trabalho da base de dados também pode ser limitada devido à CPU ou à memória, o que pode ter um impacto sério no processamento de transações. Note que o cálculo (nível de preços) pode ser dimensionado para cima ou para baixo apenas entre os níveis [de Finalidade Geral ou De Memória Otimizado.](concepts-pricing-tiers.md) 

* **Teste para falha:** Teste manualmente falha para a sua instância do servidor para entender o tempo que o processo demora para o seu caso de utilização e para garantir que a aplicação que acede à sua instância do servidor pode ligar-se automaticamente à nova instância do servidor após a falha.

* **Utilize a tecla principal**: Certifique-se de que as suas tabelas têm uma chave primária ou única ao operar na Base de Dados Azure para o MySQL. Isto ajuda muito a fazer backups, réplicas, etc. e melhora o desempenho.

* **Configure o valor TTL**: Se a sua aplicação de cliente estiver a caching os dados do Serviço de Nome de Domínio (DNS) das instâncias do seu servidor, desafie um valor de tempo para viver (TTL) inferior a 30 segundos. Uma vez que o endereço IP subjacente de uma instância do servidor pode ser alterado após uma falha, a caching dos dados DNS por um tempo prolongado pode levar a falhas de ligação se a sua aplicação tentar ligar-se a um endereço IP que já não está em serviço.

* Utilize agrupamentos de ligação para evitar atingir os [limites máximos](concepts-server-parameters.md#max_connections)de ligação e utilize a lógica de tentar novamente para evitar problemas de ligação intermitentes. 

* Se estiver a utilizar réplica, utilize [o ProxySQL para equilibrar a carga](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847) entre o servidor primário e o servidor de réplicas secundárias legíveis. Veja os passos de configuração aqui. </br> 

* Ao aprovisionar o recurso, certifique-se de que [ativou o autogrow](howto-auto-grow-storage-portal.md) para a sua Base de Dados Azure para o MySQL. Isto não adiciona qualquer custo adicional e protegerá a base de dados de quaisquer estrangulamentos de armazenamento que possa encontrar. </br> 


### <a name="using-innodb-with-azure-database-for-mysql"></a>Utilização de InnoDB com Base de Dados Azure para o MySQL

*   Se utilizar `ibdata1` a função, que é um ficheiro de dados do espaço de mesa do sistema, não pode encolher ou ser purgado baixando os dados da tabela ou movendo a tabela para ficheiro por tabela `tablespaces` .

* Para uma base de dados superior a 1 TB de tamanho, deverá criar a tabela em **innodb_file_per_table** `tablespace` . Para uma única tabela com tamanho superior a 1 TB, deve ser a tabela [de divisórias.](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html)

*   Para um servidor que tenha um grande número de `tablespace` , o arranque do motor será muito lento devido à verificação sequencial do espaço de mesa durante a startup MySQL ou failover. 

* Desa esta innodb_file_per_table = ON antes de criar uma tabela, se o número total da tabela for inferior a 500.

* Se tiver mais de 500 tabelas numa base de dados, reveja o tamanho da tabela para cada tabela. Para uma mesa grande, você ainda deve considerar a utilização do espaço de mesa de ficheiro por mesa para evitar que o ficheiro de espaço de mesa do sistema atinja o limite máximo de armazenamento.

> [!NOTE]
> Para tabelas com tamanho inferior a 5GB, considere usar o espaço de mesa do sistema 
> ```sql
>    CREATE TABLE tbl_name ... *TABLESPACE* = *innodb_system*;
> ```

* [Partição](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) da sua mesa na criação de mesa se tiver uma mesa muito grande pode potencialmente crescer para além de 1 TB.

* Utilize vários servidores MySQL e espalhe as tabelas através desses servidores. Evite colocar demasiadas mesas num único servidor se tiver cerca de 10000 mesas ou mais. 

## <a name="next-steps"></a>Passos seguintes
- [Melhores práticas para o desempenho da Base de Dados Azure para o MySQL](concept-performance-best-practices.md)
- [Melhores práticas para monitorizar a sua Base de Dados Azure para o MySQL](concept-monitoring-best-practices.md)
