---
title: Desenvolvimento de aplicações - Base de Dados Azure para MySQL
description: Introduz considerações de design que um desenvolvedor deve seguir ao escrever código de aplicação para ligar à Base de Dados Azure para MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 02ce6b00b6555f849d162b9f3b381c0ab358d712
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532846"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Visão geral do desenvolvimento de aplicações para base de dados Azure para MySQL 
Este artigo discute considerações de design que um desenvolvedor deve seguir ao escrever código de aplicação para ligar à Base de Dados Azure para mySQL. 

> [!TIP]
> Para um tutorial que lhe mostre como criar um servidor, crie uma firewall baseada em servidores, veja propriedades do servidor, crie base de dados e cone e consulte utilizando a bancada de trabalho e mysql.exe, consulte o Design da sua primeira base de dados Azure para base [de dados MySQL](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Linguagem e plataforma
Estão disponíveis exemplos de código para várias linguagens de programação e plataformas. Pode encontrar links para as amostras de código em: [Bibliotecas de conectividade utilizadas para ligar à Base de Dados Azure para MySQL](concepts-connection-libraries.md)

## <a name="tools"></a>Ferramentas
A Base de Dados Azure para MySQL utiliza a versão comunitária MySQL, compatível com ferramentas de gestão comuns MySQL, tais como workbench ou utilitários MySQL, tais como mysql.exe, [phpMyAdmin,](https://www.phpmyadmin.net/) [Navicat,](https://www.navicat.com/products/navicat-for-mysql)entre outros. Também pode utilizar o portal Azure, Azure CLI e REST APIs para interagir com o serviço de base de dados.

## <a name="resource-limitations"></a>Limitações de recursos
A Base de Dados Azure para o MySQL gere os recursos disponíveis para um servidor utilizando dois mecanismos diferentes: 
- Governação de Recursos.
- Aplicação dos limites.

## <a name="security"></a>Segurança
A Base de Dados Azure para o MySQL fornece recursos para limitar o acesso, proteger dados, configurar utilizadores e funções e monitorizar atividades numa base de dados mySQL.

## <a name="authentication"></a>Autenticação
A Base de Dados Azure para MySQL suporta a autenticação do servidor de utilizadores e logins.

## <a name="resiliency"></a>Resiliência
Quando ocorre um erro transitório ao ligar-se a uma base de dados MySQL, o seu código deve voltar a tentar a chamada. Recomendamos que a lógica de retry use back off logic para que não sobrecarregue a base de dados SQL com vários clientes tentando simultaneamente.

- Amostras de código: Para amostras de código que ilustram a lógica de retry, consulte amostras para a linguagem da sua escolha em: Bibliotecas de conectividade utilizadas para ligar à Base de [Dados Azure para MySQL](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Gestão de ligações
As ligações de base de dados são um recurso limitado, por isso recomendamos uma utilização sensata das ligações ao aceder à sua base de dados MySQL para obter um melhor desempenho.
- Aceda à base de dados utilizando ligações de ligação ou ligações persistentes.
- Aceda à base de dados utilizando uma duração de vida de ligação curta. 
- Utilize a lógica de retry na sua aplicação no ponto da tentativa de ligação para apanhar falhas resultantes de ligações simultâneas atingiu o máximo permitido. Na lógica de retry, detete um curto atraso e, em seguida, aguarde por um tempo aleatório antes que a ligação adicional tente.