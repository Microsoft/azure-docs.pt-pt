---
title: Política de versão - Azure Database for MySQL - Single Server and Flexible Server (Preview)
description: Descreve a política em torno das versões principais e menores do MySQL na Base de Dados Azure para o MySQL
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 4903f1e48eb2f33c68d62c635201474b841ed146
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591517"
---
# <a name="azure-database-for-mysql-versioning-policy"></a>Base de dados Azure para a política de versão MySQL

Esta página descreve a base de dados Azure para a política de versão MySQL, e é aplicável à Base de Dados Azure para o MySQL - Single Server e Azure Database para os modos de implementação mySQL - Servidor Flexível (Pré-visualização).

## <a name="supported--mysql-versions"></a>Versões MySQL suportadas

A Azure Database for MySQL suporta as seguintes versões de base de dados.

| Versão | Servidor Único | Servidor Flexível (Pré-visualização) |
| ----- | :------: | :----: |
| MySQL 8 | X |  | 
| MySQL 5.7 | X | X |
| MySQL 5.6| X |  |


## <a name="major-version-support"></a>Suporte de versão principal
Cada versão principal do MySQL será suportada pela Azure Database for MySQL a partir da data em que o Azure começa a suportar a versão até que a versão seja retirada pela comunidade MySQL, conforme indicado na [política de versão.](https://www.mysql.com/support/eol-notice.html)

## <a name="minor-version-support"></a>Suporte de versão menor
A Azure Database for MySQL executa automaticamente atualizações de versão menores para a versão MySQL preferida do Azure como parte da manutenção periódica. 

## <a name="major-version-retirement-policy"></a>Política de reforma da versão principal
O quadro abaixo fornece os detalhes da reforma para as principais versões do MySQL. As datas seguem a [política de versão MySQL](https://www.mysql.com/support/eol-notice.html).

| Versão | Novidades | Data de início do suporte Azure | Data da reforma|
| ----- | ----- | ------ | ----- |
| [MySQL 5.6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [Funcionalidades](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 20 de março de 2018 | fevereiro de 2021
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
