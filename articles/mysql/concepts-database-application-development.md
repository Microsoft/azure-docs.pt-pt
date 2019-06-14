---
title: Descrição geral da programação de aplicativo de banco de dados para a base de dados do Azure para MySQL
description: Apresenta as considerações de design que um desenvolvedor deve seguir ao escrever o código da aplicação para ligar à base de dados do Azure para MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 946f7011c51b7c6844e023d03e01e4c2043d2578
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60615645"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Descrição geral do desenvolvimento de aplicativos para a base de dados do Azure para MySQL 
Este artigo aborda considerações de design que um desenvolvedor deve seguir ao escrever o código da aplicação para ligar à base de dados do Azure para MySQL. 

> [!TIP]
> Para obter um tutorial que mostra como criar um servidor, criar um firewall baseado em servidor, ver propriedades do servidor, criar base de dados e ligar e consultar com a Bancada de trabalho e mysql.exe, consulte [conceber a sua primeira base de dados do Azure para MySQL](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Linguagem e plataforma
Estão disponíveis exemplos de código para várias linguagens de programação e plataformas. Pode encontrar ligações para exemplos de código em: [Bibliotecas de conectividade utilizadas para ligar à base de dados do Azure para MySQL](concepts-connection-libraries.md)

## <a name="tools"></a>Ferramentas
A versão de Comunidade do MySQL, compatível com o MySQL ferramentas de gestão comuns, como utilitários Bancada de trabalho ou MySQL como mysql.exe, utiliza a base de dados do Azure para MySQL [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql)entre outros. Também pode utilizar o portal do Azure, CLI do Azure e REST APIs para interagir com o serviço de base de dados.

## <a name="resource-limitations"></a>Limitações de recursos
Base de dados do Azure para MySQL gere os recursos disponíveis para um servidor utilizando o dois mecanismos diferentes: 
- Governação de recursos.
- Imposição de limites.

## <a name="security"></a>Segurança
Base de dados do Azure para MySQL fornece recursos para acesso restritivo, proteção dos dados, configurar utilizadores e funções e atividades de monitorização numa base de dados MySQL.

## <a name="authentication"></a>Autenticação
Base de dados do Azure para MySQL suporta a autenticação de servidor de utilizadores e inícios de sessão.

## <a name="resiliency"></a>Resiliência
Quando ocorre um erro transitório ao ligar à base de dados MySQL, seu código deverá repetir a chamada. Recomendamos que utilize a lógica de repetição lógica de término para que não sobrecarregue a base de dados SQL com vários clientes em simultâneo a tentar novamente.

- Exemplos de código: Para exemplos de código que ilustram a lógica de repetição, consulte exemplos para a linguagem da sua preferência em: [Bibliotecas de conectividade utilizadas para ligar à base de dados do Azure para MySQL](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Gerir ligações
As ligações de base de dados são um recurso limitado, pelo que recomendamos o uso adequado de ligações ao aceder à base de dados MySQL para melhorar o desempenho.
- Acesso a base de dados com o agrupamento de ligações ou ligações persistentes.
- A base de dados com o tempo de vida curtos de ligação de acesso. 
- Utilize a lógica de repetição na sua aplicação no ponto a tentativa de ligação para detectar falhas resultantes de ligações simultâneas alcançou o máximo permitido. A lógica de repetição, definir um pequeno atraso e, em seguida, aguardar por um tempo aleatório antes das tentativas de ligação adicionais.