---
title: Conceitos de servidor - Base de Dados Azure para MySQL
description: Este tópico fornece considerações e orientações para trabalhar com a Base de Dados Azure para servidores MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 300470b2e8fb10fda7cfc59517cef00c07bf0632
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537011"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Conceitos de servidor em Base de Dados Azure para MySQL

Este artigo fornece considerações e orientações para trabalhar com a Base de Dados Azure para servidores MySQL.

## <a name="what-is-an-azure-database-for-mysql-server"></a>O que é uma Base de Dados Azure para servidor MySQL?

Uma base de dados Azure para servidor MySQL é um ponto administrativo central para várias bases de dados. É a mesma construção de servidor MySQL que você pode estar familiarizado no mundo no local. Especificamente, o serviço Azure Database para mySQL é gerido, fornece garantias de desempenho, e expõe acesso e funcionalidades ao nível do servidor.

Uma base de dados Azure para servidor MySQL:

- É criado dentro de uma subscrição Azure.
- É o recurso dos pais para bases de dados.
- Fornece um espaço de nome para bases de dados.
- É um recipiente com semântica de vida forte - elimine um servidor e apague as bases de dados contidas.
- Cooloderecursos numa região.
- Fornece um ponto final de ligação para o acesso ao servidor e à base de dados.
- Fornece a possibilidade de políticas de gestão aplicáveis às suas bases de dados: login, firewall, utilizadores, funções, configurações, etc.
- Está disponível em várias versões. Para mais informações, consulte a Base de [Dados Azure Suportada para versões de base de dados MySQL](./concepts-supported-versions.md).

Dentro de uma Base de Dados do Azure para o servidor MySQL, pode criar uma ou várias bases de dados. Pode optar por criar uma única base de dados por servidor para utilizar todos os recursos ou criar várias bases de dados para partilhar os recursos. O preço é estruturado por servidor, com base na configuração do nível de preços, vCores e armazenamento (GB). Para mais informações, consulte [os níveis de preços](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Como posso ligar e autenticar a uma Base de Dados Azure para servidor MySQL?

Os seguintes elementos ajudam a garantir um acesso seguro à sua base de dados.

|     |     |
| :-- | :-- |
| **Autenticação e autorização** | A Base de Dados Azure para servidor MySQL suporta a autenticação mySQL nativa. Pode ligar e autenticar a um servidor com o login de administração do servidor. |
| **Protocolo** | O serviço suporta um protocolo baseado em mensagens usado pela MySQL. |
| **TCP/IP** | O protocolo é suportado sobre TCP/IP e sobre tomadas de domínio Unix. |
| **Firewall** | Para ajudar a proteger os seus dados, uma regra de firewall impede todo o acesso ao seu servidor de base de dados, até especificar quais os computadores que têm permissão. Consulte a Base de Dados Azure para obter as regras de [firewall do Servidor MySQL](./concepts-firewall-rules.md). |
| **SSL** | O serviço suporta a aplicação de ligações SSL entre as suas aplicações e o seu servidor de base de dados.  Veja [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Configurar a conectividade SSL na sua aplicação para ligar em segurança à Base de Dados do Azure para o MySQL). |

## <a name="how-do-i-manage-a-server"></a>Como posso gerir um servidor?

Pode gerir a Base de Dados Azure para servidores MySQL utilizando o portal Azure ou o Azure CLI.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral do serviço, consulte a Base de Dados Azure para visão geral do [MySQL](./overview.md)
- Para obter informações sobre quotas e limitações específicas de recursos com base no seu **nível de serviço,** consulte [os níveis](./concepts-service-tiers.md) de serviço
- Para obter informações sobre a ligação ao serviço, consulte as bibliotecas de ligação para a Base de [Dados Azure para mySQL](./concepts-connection-libraries.md).
