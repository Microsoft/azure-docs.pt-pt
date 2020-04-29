---
title: Servidores - Base de Dados Azure para MariaDB
description: Este tópico fornece considerações e orientações para trabalhar com a Base de Dados Azure para servidores MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 444d7f1574cf1517b01250bcb9d810731030182d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79527797"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Conceitos de servidor em Base de Dados Azure para MariaDB
Este artigo fornece considerações e orientações para trabalhar com a Base de Dados Azure para servidores MariaDB.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>O que é uma Base de Dados Azure para servidor MariaDB?

Uma Base de Dados Azure para servidor MariaDB é um ponto administrativo central para várias bases de dados. É a mesma construção de servidor MariaDB que você pode estar familiarizado no mundo das instalações. Especificamente, a Base de Dados Azure para o serviço MariaDB é gerida, fornece garantias de desempenho e expõe acesso e funcionalidades ao nível do servidor.

Uma Base de Dados Azure para servidor MariaDB:

- É criado dentro de uma subscrição Azure.
- É o recurso dos pais para bases de dados.
- Fornece um espaço de nome para bases de dados.
- É um recipiente com semântica de vida forte - elimine um servidor e apague as bases de dados contidas.
- Cooloderecursos numa região.
- Fornece um ponto final de ligação para o acesso ao servidor e à base de dados.
- Fornece a possibilidade de políticas de gestão aplicáveis às suas bases de dados: login, firewall, utilizadores, funções, configurações, etc.
- Está disponível na versão 10.2 do motor MariaDB. Para mais informações, consulte a [Base de Dados Azure Suportada para versões](./concepts-supported-versions.md)de base de dados MariaDB .

Dentro de um Azure Database for MariaDB Server, pode criar uma ou múltiplas bases de dados. Pode optar por criar uma única base de dados por servidor para utilizar todos os recursos ou criar várias bases de dados para partilhar os recursos. O preço é estruturado por servidor, com base na configuração do nível de preços, vCores e armazenamento (GB). Para mais informações, consulte [os níveis de preços](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Como posso garantir uma Base de Dados Azure para o servidor MariaDB?

Os seguintes elementos ajudam a garantir um acesso seguro à sua base de dados.

|||
| :--| :--|
| **Autenticação e autorização** | A Base de Dados Azure para servidor MariaDB suporta a autenticação mySQL nativa. Pode ligar e autenticar a um servidor com o login de administração do servidor. |
| **Protocolo** | O serviço suporta um protocolo baseado em mensagens usado pela MySQL. |
| **TCP/IP** | O protocolo é suportado sobre TCP/IP e sobre tomadas de domínio Unix. |
| **Firewall** | Para ajudar a proteger os seus dados, uma regra de firewall impede todo o acesso ao seu servidor de base de dados, até especificar quais os computadores que têm permissão. Consulte a [Base de Dados Azure para obter regras](./concepts-firewall-rules.md)de firewall do Servidor MariaDB . |
| **SSL** | O serviço suporta a aplicação de ligações SSL entre as suas aplicações e o seu servidor de base de dados. Consulte a [conectividade Configure SSL na sua aplicação para ligar de forma segura à Base de Dados Azure para MariaDB](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Como posso gerir um servidor?
Pode gerir a Base de Dados Azure para servidores MariaDB utilizando o portal Azure ou o Azure CLI.

## <a name="next-steps"></a>Passos seguintes
- Para uma visão geral do serviço, consulte a Base de [Dados Azure para a visão geral](./overview.md) do MariaDB
- Para obter informações sobre quotas e limitações específicas de recursos com base no seu **nível de serviço,** consulte [os níveis](./concepts-pricing-tiers.md) de serviço

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
