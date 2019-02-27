---
title: Leia as réplicas na base de dados do Azure para MySQL.
description: Este artigo descreve a leitura de réplicas da base de dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 6e33c7571dc735ce9984a0ce1b37275a6c4c7eca
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888477"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Réplicas de leitura na base de dados do Azure para MySQL

A funcionalidade de réplica de leitura permite-lhe replicar dados de uma base de dados do Azure para o servidor MySQL (principal) para até cinco só de leitura servidores (réplicas) na mesma região do Azure. Réplicas só de leitura assíncrona são atualizadas utilizando a tecnologia de replicação baseada em posição de ficheiro de registo binário nativo (binlog) do motor MySQL. Para saber mais sobre a replicação de binlog, consulte a [descrição geral de replicação do MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Réplicas criadas na base de dados do Azure para o serviço MySQL são novos servidores que podem ser geridos da mesma forma como servidores de MySQL normal/autónomos. Para cada réplica de leitura, é-lhe faturada a computação aprovisionada em vCores e o armazenamento aprovisionado em GB/mês.

Para saber mais sobre os recursos de replicação do MySQL e problemas, consulte a [documentação de replicação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-read-replicas"></a>Quando a utilização de réplicas de leitura

Aplicações e cargas de trabalho que são de leitura intensiva podem ser atendidas pelas réplicas só de leitura. Réplicas de leitura ajudam a aumentar a quantidade de capacidade de leitura disponível em comparação comparada se fosse usar apenas um único servidor para leitura e escrita. As cargas de trabalho de leitura podem ser isoladas para as réplicas, enquanto as cargas de trabalho de escrita podem ser direcionadas para o mestre.

Um cenário comum é fazer com que o BI e cargas de trabalho analíticas utilizam a réplica de leitura como a origem de dados de relatórios.

## <a name="considerations-and-limitations"></a>Considerações e limitações

### <a name="pricing-tiers"></a>Escalões de preço

Réplicas de leitura só estão atualmente disponíveis nos escalões de preços para fins gerais e memória otimizada.

### <a name="master-server-restart"></a>Reiniciar o servidor mestre

Quando cria uma réplica para um modelo que tenha não réplicas existentes, o mestre pela primeira vez será reiniciado para se preparar para a replicação. . Levar isso em consideração e executar estas operações durante um período de ponta.

### <a name="stopping-replication"></a>A parar a replicação

Pode optar por parar a replicação entre um mestre e um servidor de réplica. A parar a replicação remove a relação de replicação entre o servidor mestre e de réplica.

Assim que a replicação foi interrompida, o servidor de réplica se torna um servidor autónomo. Os dados no servidor autónomo são os dados que estava disponíveis na réplica no momento, que o comando "parar a replicação" foi iniciado. O servidor autónomo não captura com o servidor mestre. Este servidor não pode se transformar numa réplica novamente.

### <a name="replicas-are-new-servers"></a>As réplicas são novos servidores

As réplicas são criadas como nova base de dados do Azure para servidores MySQL. Servidores existentes não podem ser feitos em réplicas.

### <a name="replica-server-configuration"></a>Configuração do servidor de réplica

Servidores de réplica são criados usando as mesmas configurações de servidor como o modelo, que inclui as seguintes configurações:

- Escalão de preço
- Geração de computação
- vCores
- Armazenamento
- Período de retenção de cópia de segurança
- Opção de redundância de cópia de segurança
- Versão de motor do MySQL
- Regras da firewall

Depois de ter sido criada uma réplica, pode alterar o escalão de preço (exceto para e do Basic), geração, vCores, armazenamento e retenção de cópia de segurança independentemente do servidor mestre de computação.

### <a name="master-server-configuration"></a>Configuração do servidor mestre

Se um mestre configuração do servidor (ex. vCores ou armazenamento) é atualizado, a configuração dos réplicas também deve ser atualizada para valores iguais ou superior. Sem isso, o servidor de réplica pode não ser capaz de acompanhar as alterações efetuadas a mestre e pode falhar assim.

Novas regras de firewall adicionadas ao servidor mestre, depois de criar um servidor de réplica não são replicadas para a réplica. A réplica deve ser atualizada com esta regra de firewall novas.

### <a name="deleting-the-master-server"></a>A eliminar o servidor mestre

Quando um servidor mestre for eliminado, a replicação é parada para todas as réplicas de leitura. Essas réplicas tornam-se a servidores autónomos. O servidor mestre em si é eliminado.

### <a name="user-accounts"></a>Contas de utilizador

Os utilizadores no servidor principal são replicados para as réplicas de leitura. Só pode ligar a uma réplica de leitura, com as contas de utilizador disponíveis no servidor principal.

### <a name="other"></a>Outros

- Identificadores de transação global (GTID) não são suportadas.
- Não é suportada a criação de uma réplica de uma réplica.
- Tabelas em memória podem tornar as réplicas fora de sincronia. Esta é uma limitação da tecnologia de replicação do MySQL. Leia mais no [documentação de referência do MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) para obter mais informações.
- Ajuste a [ `innodb_file_per_table` ](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) parâmetro num servidor principal após a criação de um servidor de réplica pode fazer com que a réplica se torna fora de sincronia. O servidor de réplica não está ciente do tablespaces diferentes.
- Certifique-se de que o servidor mestre tabelas tiverem chaves primárias. Falta de chaves primárias pode resultar numa latência de replicação entre o mestre e as réplicas.
- Reveja a lista completa de limitações de replicação do MySQL no [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)


## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar e gerir réplicas de leitura com o portal do Azure](howto-read-replicas-portal.md)
- Saiba como [criar e gerir réplicas de leitura que utilizam a CLI do Azure](howto-read-replicas-cli.md)
