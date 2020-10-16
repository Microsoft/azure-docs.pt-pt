---
title: Servidores - Base de Dados Azure para MariaDB
description: Este tópico fornece considerações e diretrizes para trabalhar com a Base de Dados Azure para servidores MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 444d7f1574cf1517b01250bcb9d810731030182d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "79527797"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Conceitos de servidor na Base de Dados Azure para MariaDB
Este artigo fornece considerações e diretrizes para trabalhar com a Base de Dados Azure para servidores MariaDB.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>O que é uma Base de Dados Azure para o servidor MariaDB?

Uma Base de Dados Azure para o servidor MariaDB é um ponto administrativo central para várias bases de dados. É a mesma construção de servidorEs MariaDB que você pode estar familiarizado no mundo do local. Especificamente, a Base de Dados Azure para o serviço MariaDB é gerida, fornece garantias de desempenho e expõe o acesso e funcionalidades ao nível do servidor.

Uma base de dados Azure para o servidor MariaDB:

- É criado dentro de uma subscrição Azure.
- É o recurso principal para bases de dados.
- Fornece um espaço de nome para bases de dados.
- É um recipiente com semântica vitalícia forte - elimine um servidor e elimine as bases de dados contidas.
- Colita recursos numa região.
- Fornece um ponto final de ligação para o acesso ao servidor e à base de dados.
- Fornece a margem de manobra para políticas de gestão que se aplicam às suas bases de dados: login, firewall, utilizadores, funções, configurações, etc.
- Está disponível na versão 10.2 do motor MariaDB. Para obter mais informações, consulte [a Base de Dados Azure Suportada para versões de base de dados MariaDB](./concepts-supported-versions.md).

Dentro de um Azure Database for MariaDB Server, pode criar uma ou múltiplas bases de dados. Pode optar por criar uma única base de dados por servidor para utilizar todos os recursos ou criar várias bases de dados para partilhar os recursos. O preço é estruturado por servidor, com base na configuração do nível de preços, vCores e armazenamento (GB). Para obter mais informações, consulte [os níveis de preços.](./concepts-pricing-tiers.md)

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Como posso assegurar uma base de dados Azure para o servidor MariaDB?

Os seguintes elementos ajudam a garantir um acesso seguro à sua base de dados.

|||
| :--| :--|
| **Autenticação e autorização** | A base de dados Azure para servidor MariaDB suporta a autenticação nativa do MySQL. Pode ligar e autenticar num servidor com o login de administração do servidor. |
| **Protocolo** | O serviço suporta um protocolo baseado em mensagens utilizado pelo MySQL. |
| **TCP/IP** | O protocolo é suportado sobre TCP/IP e sobre tomadas de domínio Unix. |
| **Firewall** | Para ajudar a proteger os seus dados, uma regra de firewall impede todo o acesso ao servidor de base de dados, até especificar quais os computadores que têm permissão. Consulte [a base de dados Azure para as regras de firewall do Servidor MariaDB](./concepts-firewall-rules.md). |
| **SSL** | O serviço suporta a aplicação de ligações SSL entre as suas aplicações e o servidor de base de dados. Consulte [a conectividade Configurar SSL na sua aplicação para ligar de forma segura à Base de Dados Azure para MariaDB](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Como posso gerir um servidor?
Pode gerir a Base de Dados Azure para servidores MariaDB utilizando o portal Azure ou o Azure CLI.

## <a name="next-steps"></a>Passos seguintes
- Para uma visão geral do serviço, consulte [a Base de Dados Azure para a Visão Geral da MariaDB](./overview.md)
- Para obter informações sobre quotas e limitações específicas de recursos com base no seu **nível de serviço,** consulte [os níveis de serviço](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
