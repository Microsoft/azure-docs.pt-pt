---
title: Desenvolvimento de aplicativos-banco de dados do Azure para MySQL
description: Apresenta considerações de design que um desenvolvedor deve seguir ao gravar o código do aplicativo para se conectar ao banco de dados do Azure para MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 74abf680223d562522a11ecb8999fedb37de9907
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770276"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Visão geral do desenvolvimento de aplicativos para o banco de dados do Azure para MySQL 
Este artigo discute considerações de design que um desenvolvedor deve seguir ao gravar o código do aplicativo para se conectar ao banco de dados do Azure para MySQL. 

> [!TIP]
> Para obter um tutorial mostrando como criar um servidor, criar um firewall baseado em servidor, exibir propriedades do servidor, criar banco de dados e conectar e consultar usando o Workbench e o MySQL. exe, consulte [criar seu primeiro banco de dados do Azure para MySQL](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Linguagem e plataforma
Estão disponíveis exemplos de código para várias linguagens de programação e plataformas. Você pode encontrar links para os exemplos de código em: [bibliotecas de conectividade usadas para se conectar ao banco de dados do Azure para MySQL](concepts-connection-libraries.md)

## <a name="tools"></a>Ferramentas
O banco de dados do Azure para MySQL usa a versão da Comunidade do MySQL, compatível com as ferramentas de gerenciamento comuns do MySQL, como o Workbench ou utilitários do MySQL, como MySQL. exe, [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql)e outros. Você também pode usar as APIs portal do Azure, CLI do Azure e REST para interagir com o serviço de banco de dados.

## <a name="resource-limitations"></a>Limitações de recursos
O banco de dados do Azure para MySQL gerencia os recursos disponíveis para um servidor usando dois mecanismos diferentes: 
- Governança de recursos.
- Imposição de limites.

## <a name="security"></a>Segurança
O banco de dados do Azure para MySQL fornece recursos para limitar o acesso, proteger dados, configurar usuários e funções e monitorar atividades em um banco de dados MySQL.

## <a name="authentication"></a>Autenticação
O banco de dados do Azure para MySQL dá suporte à autenticação de servidor de usuários e logons.

## <a name="resiliency"></a>Resiliência
Quando ocorre um erro transitório durante a conexão com um banco de dados MySQL, seu código deve repetir a chamada. Recomendamos que a lógica de repetição use a lógica de retirada para que ela não sobrecarregue o banco de dados SQL com vários clientes repetindo simultaneamente.

- Exemplos de código: para obter exemplos de código que ilustram a lógica de repetição, consulte exemplos para o idioma de sua escolha em: [bibliotecas de conectividade usadas para se conectar ao banco de dados do Azure para MySQL](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Gerenciando conexões
As conexões de banco de dados são um recurso limitado, portanto, recomendamos o uso adequado de conexões ao acessar seu banco de dados MySQL para obter um melhor desempenho.
- Acesse o banco de dados usando o pool de conexões ou conexões persistentes.
- Acesse o banco de dados usando a conexão de curta duração. 
- Use a lógica de repetição em seu aplicativo no ponto da tentativa de conexão de detectar falhas resultantes de conexões simultâneas atingiram o máximo permitido. Na lógica de repetição, defina um pequeno atraso e aguarde um tempo aleatório antes das tentativas de conexão adicionais.