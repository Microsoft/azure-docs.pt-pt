---
title: Servidores-Banco de dados do Azure para MariaDB
description: Este tópico fornece considerações e diretrizes para trabalhar com o banco de dados do Azure para servidores MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 048d9f3089a433dbf8c2647ed86ddab69c78ebaa
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772049"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Conceitos de servidor no banco de dados do Azure para MariaDB
Este artigo fornece considerações e diretrizes para trabalhar com o banco de dados do Azure para servidores MariaDB.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>O que é um banco de dados do Azure para o servidor MariaDB?

Um banco de dados do Azure para MariaDB Server é um ponto administrativo central para vários bancos de dados. É a mesma construção de servidor MariaDB com a qual você pode estar familiarizado no mundo local. Especificamente, o banco de dados do Azure para o serviço MariaDB é gerenciado, fornece garantias de desempenho e expõe o acesso e os recursos no nível do servidor.

Um banco de dados do Azure para o servidor MariaDB:

- É criado em uma assinatura do Azure.
- É o recurso pai para bancos de dados.
- Fornece um namespace para bancos de dados.
- É um contêiner com semântica de tempo de vida forte – exclui um servidor e exclui os bancos de dados independentes.
- Posiciona recursos em uma região.
- Fornece um ponto de extremidade de conexão para acesso de servidor e banco de dados.
- Fornece o escopo para políticas de gerenciamento que se aplicam a seus bancos de dados: logon, firewall, usuários, funções, configurações, etc.
- Está disponível na versão 10,2 do mecanismo do MariaDB. Para obter mais informações, consulte [banco de dados do Azure com suporte para versões do banco de dados MariaDB](./concepts-supported-versions.md).

Dentro de um Azure Database for MariaDB Server, pode criar uma ou múltiplas bases de dados. Você pode optar por criar um único banco de dados por servidor para usar todos os recursos ou para criar vários deles para compartilhar os recursos. O preço é estruturado por servidor, com base na configuração do tipo de preço, vCores e armazenamento (GB). Para obter mais informações, consulte [tipos de preço](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Como fazer proteger um banco de dados do Azure para o MariaDB Server?

Os elementos a seguir ajudam a garantir o acesso seguro ao seu banco de dados.

|||
| :--| :--|
| **Autenticação e autorização** | O banco de dados do Azure para MariaDB Server dá suporte à autenticação do MySQL nativo. Você pode se conectar e autenticar em um servidor com o logon de administrador do servidor. |
| **Protocolo** | O serviço oferece suporte a um protocolo baseado em mensagem usado pelo MySQL. |
| **TCP/IP** | O protocolo tem suporte em TCP/IP e em soquetes de domínio do UNIX. |
| **Firewall** | Para ajudar a proteger seus dados, uma regra de Firewall impede todo acesso ao seu servidor de banco de dado, até que você especifique quais computadores têm permissão. Consulte [banco de dados do Azure para regras de firewall do servidor MariaDB](./concepts-firewall-rules.md). |
| **SSL** | O serviço dá suporte à imposição de conexões SSL entre seus aplicativos e seu servidor de banco de dados. Consulte [configurar conectividade SSL em seu aplicativo para se conectar com segurança ao banco de dados do Azure para MariaDB](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Como fazer gerenciar um servidor?
Você pode gerenciar o banco de dados do Azure para servidores MariaDB usando o portal do Azure ou o CLI do Azure.

## <a name="next-steps"></a>Passos seguintes
- Para obter uma visão geral do serviço, consulte [visão geral do banco de dados do Azure para MariaDB](./overview.md)
- Para obter informações sobre cotas e limitações de recursos específicas com base em sua **camada de serviço**, consulte [camadas de serviço](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
