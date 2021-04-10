---
title: Política de suporte de versão - Azure Database for MySQL - Single Server and Flexible Server (Preview)
description: Descreve a política em torno das versões principais e menores do MySQL na Base de Dados Azure para o MySQL
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 35ed677c3176fb990f752f3204a1ae11bf39896c
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121698"
---
# <a name="azure-database-for-mysql-version-support-policy"></a>Política de suporte à versão Azure Database para a versão MySQL

Esta página descreve a base de dados Azure para a política de versão MySQL, e é aplicável à Base de Dados Azure para o MySQL - Single Server e Azure Database para os modos de implementação mySQL - Servidor Flexível (Pré-visualização).

## <a name="supported--mysql-versions"></a>Versões MySQL suportadas

A Azure Database for MySQL foi desenvolvida a partir da [MySQL Community Edition,](https://www.mysql.com/products/community/)utilizando o motor de armazenamento InnoDB. O serviço suporta toda a versão principal atual suportada pela comunidade, nomeadamente MySQL 5.6, 5.7 e 8.0. MySQL usa o esquema de nomeação X.Y.Z onde X é a versão principal, Y é a versão menor, e Z é o lançamento da correção de bug. Para obter mais informações sobre o esquema, consulte a [documentação mySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

A Azure Database for MySQL suporta atualmente as seguintes versões principais e menores do MySQL:

| Versão | [Servidor Único](overview.md) <br/> Versão menor atual |[Servidor Flexível (Pré-visualização)](/../flexible-server/overview.md) <br/> Versão menor atual  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL Versão 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html)(Aposentado) | Não suportado|
|MySQL Versão 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL Versão 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

> [!NOTE]
> Na opção de implementação do Servidor Único, é utilizado um gateway para redirecionar as ligações para as instâncias do servidor. Depois de a ligação ser estabelecida, o cliente MySQL apresenta a versão do MySQL definida no gateway, não a versão real em execução na instância do servidor MySQL. Para determinar a versão da instância do servidor MySQL, utilize o comando `SELECT VERSION();` no prompt do MySQL. Se a sua aplicação tiver um requisito para ligar a uma versão específica importante, digamos v5.7 ou v8.0, pode fazê-lo alterando a porta na cadeia de ligação do seu servidor, como explicado na nossa documentação [aqui.](concepts-supported-versions.md#connect-to-a-gateway-node-that-is-running-a-specific-mysql-version)

> [!IMPORTANT]
> MySQL v5.6 está aposentado no Single Server a partir de Febuary 2021. A partir de 1 de setembro de 2021, não será possível criar novos servidores V5.6 na Base de Dados Azure para a opção de implementação do MySQL - Servidor Único. No entanto, poderá realizar recuperações pontuais e criar réplicas de leitura para os seus servidores existentes.

Leia a política de suporte da versão para versões aposentadas na [documentação de política de suporte de versão.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="major-version-support"></a>Suporte de versão principal
Cada versão principal do MySQL será suportada pela Azure Database for MySQL a partir da data em que o Azure começa a suportar a versão até que a versão seja retirada pela comunidade MySQL, conforme indicado na [política de versão.](https://www.mysql.com/support/eol-notice.html)

## <a name="minor-version-support"></a>Suporte de versão menor
A Azure Database for MySQL executa automaticamente atualizações de versão menores para a versão MySQL preferida do Azure como parte da manutenção periódica. 

## <a name="major-version-retirement-policy"></a>Política de reforma da versão principal
O quadro abaixo fornece os detalhes da reforma para as principais versões do MySQL. As datas seguem a [política de versão MySQL](https://www.mysql.com/support/eol-notice.html).

| Versão | Novidades | Data de início do suporte Azure | Data da reforma|
| ----- | ----- | ------ | ----- |
| [MySQL 5.6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [Funcionalidades](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 20 de março de 2018 | Fevereiro de 2021
| [MySQL 5.7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [Funcionalidades](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 20 de março de 2018 | outubro de 2023
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [Características](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)) | 11 de dezembro de 2019 | abril de 2026


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>Versões de motor MySQL aposentadas não suportadas na Base de Dados Azure para o MySQL

Após a data de aposentadoria de cada versão da base de dados MySQL, se continuar a executar a versão aposentada, note as seguintes restrições:
- Uma vez que a comunidade não irá lançar quaisquer correções de bugs ou correções de segurança, a Azure Database for MySQL não irá corrigir o motor de base de dados reformado para quaisquer problemas de segurança ou tomar medidas de segurança no que diz respeito ao motor de base de dados reformado. No entanto, o Azure continuará a realizar manutenção e remendos periódicos para o hospedeiro, os contentores e quaisquer outros componentes relacionados com o serviço.
- Se algum problema de suporte que possa ter relacionado com a base de dados MySQL, poderemos não ser capazes de lhe fornecer suporte. Nesses casos, terá de atualizar a sua base de dados para que lhe forneçamos qualquer suporte.
- Não será possível criar novos servidores de base de dados para a versão reformada. No entanto, poderá realizar recuperações pontuais e criar réplicas de leitura para os seus servidores existentes.
- As novas capacidades de serviço desenvolvidas pela Azure Database para o MySQL só podem estar disponíveis para versões suportadas do servidor de bases de dados.
- As SLAs de uptime aplicar-se-ão exclusivamente à Base de Dados Azure para problemas relacionados com o serviço MySQL e não a qualquer tempo de inatividade causado por erros relacionados com o motor da base de dados.  
- No caso extremo de uma ameaça séria ao serviço causado pela vulnerabilidade do motor de base de dados MySQL identificada na versão de base de dados aposentada, a Azure pode optar por parar o nó de computação do seu servidor de base de dados para garantir primeiro o serviço. Será solicitado que atualize o servidor antes de colocar o servidor on-line. Durante o processo de atualização, os seus dados serão sempre protegidos utilizando cópias de segurança automáticas realizadas no serviço que podem ser utilizadas para restaurar a versão mais antiga, se desejar. 



## <a name="next-steps"></a>Passos seguintes
- Ver Base de Dados Azure para MySQL - [Versões suportadas por](./concepts-supported-versions.md) servidor único
- Ver Base de Dados Azure para MySQL - Versões [suportadas pelo](flexible-server/concepts-supported-versions.md) Servidor Flexível (Pré-visualização)
- Consulte a [lixeira](./concepts-migrate-dump-restore.md) MySQL e restaure para realizar upgrades.
