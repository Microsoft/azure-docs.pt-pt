---
title: Conceitos de servidor - Azure Database for MySQL
description: Este tópico fornece considerações e diretrizes para trabalhar com a Base de Dados Azure para servidores MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 300470b2e8fb10fda7cfc59517cef00c07bf0632
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79537011"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Conceitos de servidor na Base de Dados Azure para o MySQL

Este artigo fornece considerações e diretrizes para trabalhar com a Base de Dados Azure para servidores MySQL.

## <a name="what-is-an-azure-database-for-mysql-server"></a>O que é uma Base de Dados Azure para o servidor MySQL?

Uma Base de Dados Azure para o servidor MySQL é um ponto administrativo central para várias bases de dados. É a mesma construção de servidor MySQL que você pode estar familiarizado no mundo do local. Especificamente, a Base de Dados Azure para o serviço MySQL é gerida, fornece garantias de desempenho e expõe o acesso e funcionalidades ao nível do servidor.

Uma base de dados Azure para o servidor MySQL:

- É criado dentro de uma subscrição Azure.
- É o recurso principal para bases de dados.
- Fornece um espaço de nome para bases de dados.
- É um recipiente com semântica vitalícia forte - elimine um servidor e elimine as bases de dados contidas.
- Colita recursos numa região.
- Fornece um ponto final de ligação para o acesso ao servidor e à base de dados.
- Fornece a margem de manobra para políticas de gestão que se aplicam às suas bases de dados: login, firewall, utilizadores, funções, configurações, etc.
- Está disponível em várias versões. Para obter mais informações, consulte [a Base de Dados Azure suportada para versões de base de dados MySQL](./concepts-supported-versions.md).

Dentro de uma Base de Dados do Azure para o servidor MySQL, pode criar uma ou várias bases de dados. Pode optar por criar uma única base de dados por servidor para utilizar todos os recursos ou criar várias bases de dados para partilhar os recursos. O preço é estruturado por servidor, com base na configuração do nível de preços, vCores e armazenamento (GB). Para obter mais informações, consulte [os níveis de preços.](./concepts-service-tiers.md)

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Como posso ligar e autenticar a uma Base de Dados Azure para o servidor MySQL?

Os seguintes elementos ajudam a garantir um acesso seguro à sua base de dados.

|     |     |
| :-- | :-- |
| **Autenticação e autorização** | A base de dados Azure para servidor MySQL suporta a autenticação nativa do MySQL. Pode ligar e autenticar num servidor com o login de administração do servidor. |
| **Protocolo** | O serviço suporta um protocolo baseado em mensagens utilizado pelo MySQL. |
| **TCP/IP** | O protocolo é suportado sobre TCP/IP e sobre tomadas de domínio Unix. |
| **Firewall** | Para ajudar a proteger os seus dados, uma regra de firewall impede todo o acesso ao servidor de base de dados, até especificar quais os computadores que têm permissão. Consulte [a base de dados Azure para as regras de firewall do MySQL Server](./concepts-firewall-rules.md). |
| **SSL** | O serviço suporta a aplicação de ligações SSL entre as suas aplicações e o servidor de base de dados.  Veja [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Configurar a conectividade SSL na sua aplicação para ligar em segurança à Base de Dados do Azure para o MySQL). |

## <a name="how-do-i-manage-a-server"></a>Como posso gerir um servidor?

Pode gerir a Base de Dados Azure para servidores MySQL utilizando o portal Azure ou o Azure CLI.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral do serviço, consulte [a Base de Dados Azure para a visão geral do MySQL](./overview.md)
- Para obter informações sobre quotas e limitações específicas de recursos com base no seu **nível de serviço,** consulte [os níveis de serviço](./concepts-service-tiers.md)
- Para obter informações sobre a ligação ao serviço, consulte [as bibliotecas connection para Azure Database for MySQL](./concepts-connection-libraries.md).
