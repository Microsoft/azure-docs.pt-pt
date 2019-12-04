---
title: Conceitos de servidor-banco de dados do Azure para MySQL
description: Este tópico fornece considerações e diretrizes para trabalhar com o banco de dados do Azure para servidores MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 9a2e2eb022d96af1437ea4189d11f5fa69339325
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770004"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Conceitos de servidor no banco de dados do Azure para MySQL

Este artigo fornece considerações e diretrizes para trabalhar com o banco de dados do Azure para servidores MySQL.

## <a name="what-is-an-azure-database-for-mysql-server"></a>O que é um servidor de banco de dados do Azure para MySQL?

Um banco de dados do Azure para servidor MySQL é um ponto administrativo central para vários bancos de dados. É a mesma construção de servidor MySQL com a qual você pode estar familiarizado no mundo local. Especificamente, o serviço de banco de dados do Azure para MySQL é gerenciado, fornece garantias de desempenho e expõe acesso e recursos em nível de servidor.

Um banco de dados do Azure para servidor MySQL:

- É criado em uma assinatura do Azure.
- É o recurso pai para bancos de dados.
- Fornece um namespace para bancos de dados.
- É um contêiner com semântica de tempo de vida forte – exclui um servidor e exclui os bancos de dados independentes.
- Posiciona recursos em uma região.
- Fornece um ponto de extremidade de conexão para acesso de servidor e banco de dados.
- Fornece o escopo para políticas de gerenciamento que se aplicam a seus bancos de dados: logon, firewall, usuários, funções, configurações, etc.
- Está disponível em várias versões. Para obter mais informações, consulte [versões do banco de dados do Azure para MySQL com suporte](./concepts-supported-versions.md).

Dentro de uma Base de Dados do Azure para o servidor MySQL, pode criar uma ou várias bases de dados. Você pode optar por criar um único banco de dados por servidor para usar todos os recursos ou para criar vários deles para compartilhar os recursos. O preço é estruturado por servidor, com base na configuração do tipo de preço, vCores e armazenamento (GB). Para obter mais informações, consulte [tipos de preço](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Como fazer conectar e autenticar em um banco de dados do Azure para servidor MySQL?

Os elementos a seguir ajudam a garantir o acesso seguro ao seu banco de dados.

|     |     |
| :-- | :-- |
| **Autenticação e autorização** | O banco de dados do Azure para servidor MySQL dá suporte à autenticação do MySQL nativo. Você pode se conectar e autenticar em um servidor com o logon de administrador do servidor. |
| **Protocolo** | O serviço oferece suporte a um protocolo baseado em mensagem usado pelo MySQL. |
| **TCP/IP** | O protocolo tem suporte em TCP/IP e em soquetes de domínio do UNIX. |
| **Firewall** | Para ajudar a proteger seus dados, uma regra de Firewall impede todo acesso ao seu servidor de banco de dado, até que você especifique quais computadores têm permissão. Consulte o [banco de dados do Azure para servidor MySQL regras de firewall](./concepts-firewall-rules.md). |
| **SSL** | O serviço dá suporte à imposição de conexões SSL entre seus aplicativos e seu servidor de banco de dados.  Veja [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Configurar a conectividade SSL na sua aplicação para ligar em segurança à Base de Dados do Azure para o MySQL). |

## <a name="how-do-i-manage-a-server"></a>Como fazer gerenciar um servidor?

Você pode gerenciar o banco de dados do Azure para servidores MySQL usando o portal do Azure ou o CLI do Azure.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral do serviço, consulte [visão geral do banco de dados do Azure para MySQL](./overview.md)
- Para obter informações sobre cotas e limitações de recursos específicas com base em sua **camada de serviço**, consulte [camadas de serviço](./concepts-service-tiers.md)
- Para obter informações sobre como se conectar ao serviço, consulte [bibliotecas de conexões para o banco de dados do Azure para MySQL](./concepts-connection-libraries.md).
