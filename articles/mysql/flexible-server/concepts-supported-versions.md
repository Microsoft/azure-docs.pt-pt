---
title: Versões suportadas - Base de Dados Azure para MySQL Flexible Server
description: Saiba quais as versões do servidor MySQL suportadas na Base de Dados Azure para o MySQL Flexible Server
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: f29a34fd8916110355c0122fee9db29599a01231
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937107"
---
# <a name="supported-versions-for-azure-database-for-mysql---flexible-server"></a>Versões suportadas para Base de Dados Azure para MySQL - Servidor Flexível


> [!IMPORTANT]
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.


A base de dados Azure para o MySQL Flexible Server é alimentada pela [MySQL Community Edition,](https://www.mysql.com/products/community/)utilizando o motor InnoDB.

MySQL usa o esquema de nomeação X.Y.Z. X é a versão principal, Y é a versão menor, e Z é o lançamento da correção de erro. Para obter mais informações sobre o esquema, consulte a [documentação mySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> Para determinar a versão da instância do servidor MySQL, utilize o comando `SELECT VERSION();` no prompt do MySQL.

A Azure Database for MySQL suporta atualmente as seguintes versões:

## <a name="mysql-version-57"></a>MySQL Versão 5.7

Desbloqueio de correção de erros: 5.7.29

O serviço executa patching automatizado do hardware subjacente, sistema operativo e motor de base de dados. O patching inclui atualizações de segurança e software. Para o motor MySQL, as atualizações de versão menores também estão incluídas como parte do lançamento de manutenção planeado. Os utilizadores podem configurar o calendário de remendos para serem geridos pelo sistema ou definir o seu horário personalizado. Durante o programa de manutenção, o patch é aplicado e o servidor pode necessitar de um reinício como parte do processo de remendação para completar a atualização. Com o horário personalizado, os utilizadores podem tornar o seu ciclo de remendos previsível e escolher uma janela de manutenção com o mínimo impacto para o negócio. Em geral, o serviço segue o horário de lançamento mensal como parte da integração e libertação contínuas.

Consulte as [notas de lançamento](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) do MySQL para saber mais sobre melhorias e correções nesta versão.

## <a name="managing-updates-and-upgrades"></a>Gerir atualizações e atualizações
O serviço gere automaticamente a correção para atualizações de versões de correção de erros. Por exemplo, 5.7.29 a 5.7.30.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Construir uma aplicação PHP no Windows com o MySQL](../../app-service/app-service-web-tutorial-php-mysql.md)<br/>
>[Construa aplicativo PHP no Linux com MySQL](../../app-service/containers/tutorial-php-mysql-app.md)<br/>
>[Construir aplicativo de primavera baseado em Java com MySQL](https://docs.microsoft.com/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)<br/>
