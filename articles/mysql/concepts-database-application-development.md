---
title: Desenvolvimento de aplicações - Azure Database for MySQL
description: Introduz considerações de design que um desenvolvedor deve seguir ao escrever código de aplicação para ligar à Base de Dados Azure para o MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 45447a26c0455fc5945af8b8e9f7442af7facfbe
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99830690"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Visão geral do desenvolvimento de aplicações para Azure Database for MySQL 
Este artigo discute considerações de design que um desenvolvedor deve seguir ao escrever código de aplicação para ligar à Base de Dados Azure para o MySQL. 

> [!TIP]
> Para um tutorial que lhe mostre como criar um servidor, crie uma firewall baseada em servidor, veja propriedades do servidor, crie base de dados e conecte e consulte a bancada de trabalho e mysql.exe, consulte [Design da sua primeira base de dados Azure para base de dados MySQL](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Linguagem e plataforma
Estão disponíveis exemplos de código para várias linguagens de programação e plataformas. Pode encontrar ligações com as amostras de código em: [Bibliotecas de conectividade utilizadas para ligar à Base de Dados Azure para o MySQL](concepts-connection-libraries.md)

## <a name="tools"></a>Ferramentas
A Azure Database for MySQL utiliza a versão comunitária MySQL, compatível com ferramentas de gestão comum MySQL, tais como workbench ou utilitários MySQL, tais como mysql.exe, [phpMyAdmin,](https://www.phpmyadmin.net/) [Navicat,](https://www.navicat.com/products/navicat-for-mysql) [dbForge Studio para MySQL](https://www.devart.com/dbforge/mysql/studio/) e outros. Também pode utilizar o portal Azure, Azure CLI e REST APIs para interagir com o serviço de base de dados.

## <a name="resource-limitations"></a>Limitações de recursos
A Azure Database for MySQL gere os recursos disponíveis para um servidor utilizando dois mecanismos diferentes: 
- Governação de Recursos.
- Aplicação dos Limites.

## <a name="security"></a>Segurança
A Azure Database for MySQL fornece recursos para limitar o acesso, proteger dados, configurar utilizadores e funções e monitorizar atividades numa base de dados MySQL.

## <a name="authentication"></a>Autenticação
A Azure Database for MySQL suporta a autenticação do servidor de utilizadores e logins.

## <a name="resiliency"></a>Resiliência
Quando ocorrer um erro transitório durante a ligação a uma base de dados MySQL, o seu código deve voltar a tentar a chamada. Recomendamos que a lógica de rejufação utilize a lógica de retrocesso para que não sobrevavada a base de dados SQL com vários clientes a voltarem a tentar simultaneamente.

- Amostras de código: Para amostras de código que ilustram a lógica de retrips, consulte amostras para o idioma à sua escolha em: [Bibliotecas de conectividade utilizadas para ligar à Base de Dados Azure para o MySQL](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Gestão de ligações
As ligações de base de dados são um recurso limitado, pelo que recomendamos uma utilização sensata das ligações ao aceder à sua base de dados MySQL para obter um melhor desempenho.
- Aceda à base de dados utilizando ligações de ligação ou ligações persistentes.
- Aceda à base de dados utilizando uma duração de vida útil de ligação curta. 
- Utilize a lógica de repetição na sua aplicação no ponto da tentativa de captura de falhas resultantes de ligações simultâneas que atingiram o máximo permitido. Na lógica de re-tentativa, desafiu um curto atraso e, em seguida, aguarde por um tempo aleatório antes das tentativas adicionais de ligação.
