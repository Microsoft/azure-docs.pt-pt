---
title: Versões suportadas - Base de Dados Azure para MySQL
description: Saiba quais as versões do servidor MySQL suportadas no serviço Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 314462517ba4e63694266b5e49231cb8536f3635
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604736"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versões do servidor da Base de Dados MySQL do Azure suportadas

A Azure Database for MySQL foi desenvolvida a partir da [MySQL Community Edition,](https://www.mysql.com/products/community/)utilizando o motor de armazenamento InnoDB. O serviço suporta toda a versão principal atual suportada pela comunidade, nomeadamente MySQL 5.6, 5.7 e 8.0. MySQL usa o esquema de nomeação X.Y.Z onde X é a versão principal, Y é a versão menor, e Z é o lançamento da correção de bug. Para obter mais informações sobre o esquema, consulte a [documentação mySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).



## <a name="connect-to-a-gateway-node-that-is-running-a-specific-mysql-version"></a>Ligue-se a um nó de gateway que está executando uma versão específica do MySQL

Na opção de implementação do Servidor Único, é utilizado um gateway para redirecionar as ligações para as instâncias do servidor. Depois de a ligação ser estabelecida, o cliente MySQL apresenta a versão do MySQL definida no gateway, não a versão real em execução na instância do servidor MySQL. Para determinar a versão da instância do servidor MySQL, utilize o comando `SELECT VERSION();` no prompt do MySQL. [Reveja a arquitetura de conectividade](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture#connectivity-architecture) para saber mais sobre gateways na Azure Database para arquitetura de serviço MySQL.

Como a Base de Dados Azure para o MySQL suporta a versão principal v5.6, v5.7 e v8.0, a porta padrão 3306 para ligar à Base de Dados Azure para o MySQL executa a versão 5.6 (denominação menos comum) para suportar ligações a servidores de todas as 3 versões principais suportadas. No entanto, se a sua aplicação tiver a obrigação de ligar a versão importante específica, digamos v5.7 ou v8.0, pode fazê-lo alterando a porta na cadeia de ligação do servidor.

Na Base de Dados Azure para o serviço MySQL, os nós gateway ouvem na porta 3308 para clientes v5.7 e porta 3309 para clientes v8.0. Por outras palavras, se quiser ligar-se ao cliente de gateway v5.7, deve utilizar o nome do servidor e a porta 3308 totalmente qualificados para ligar ao seu servidor a partir da aplicação do cliente. Da mesma forma, se quiser ligar-se ao cliente de gateway v8.0, pode utilizar o nome do servidor e a porta 3309 totalmente qualificados para se ligar ao seu servidor. Verifique o seguinte exemplo para obter mais clareza.

:::image type="content" source="./media/concepts-supported-versions/concepts-supported-versions-gateway.png" alt-text="Exemplo de ligação através de diferentes versões mysql gateway":::


## <a name="azure-database-for-mysql-currently-supports-the-following-major-and-minor-versions-of-mysql"></a>A Azure Database for MySQL suporta atualmente as seguintes versões principais e menores do MySQL:


| Versão | Servidor Único <br/> Versão menor atual |Servidor Flexível (Pré-visualização) <br/> Versão menor atual  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL Versão 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (Aposentado) | Não suportado|
|MySQL Versão 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL Versão 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

Leia a política de suporte da versão para versões aposentadas na [documentação de política de suporte de versão.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="managing-updates-and-upgrades"></a>Gerir atualizações e atualizações
O serviço gere automaticamente a correção para atualizações de versões de correção de erros. Por exemplo, 5.7.20 a 5.7.21.  

A atualização da versão principal é atualmente suportada pelo serviço para upgrades do MySQL v5.6 para v5.7. Para mais detalhes, consulte [como realizar as principais atualizações de versão](how-to-major-version-upgrade.md). Se quiser fazer upgrade de 5.7 para 8.0, recomendamos que realize [despejo e restaure](./concepts-migrate-dump-restore.md) para um servidor que foi criado com a nova versão do motor.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a política de versão Azure Database para a versão MySQL, consulte [este documento](concepts-version-policy.md).
- Para obter informações sobre quotas e limitações específicas de recursos com base no seu **nível de serviço,** consulte [os níveis de serviço](./concepts-pricing-tiers.md)