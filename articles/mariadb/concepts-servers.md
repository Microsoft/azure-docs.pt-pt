---
title: Conceitos de servidor na base de dados do Azure para MariaDB
description: Este tópico fornece considerações e diretrizes para trabalhar com a base de dados do Azure para MariaDB servidores.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f61f8740c9514f6276afb2ee84bcdccdc54c0710
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997892"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Conceitos de servidor na base de dados do Azure para MariaDB
Este artigo fornece considerações e diretrizes para trabalhar com a base de dados do Azure para MariaDB servidores.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>O que é uma base de dados do Azure para MariaDB server?

Uma base de dados do Azure para MariaDB server é um ponto administrativo central para várias bases de dados. É a construção de servidor MariaDB mesmo que talvez esteja familiarizado com o mundo no local. Especificamente, a base de dados do Azure para MariaDB serviço é gerida, fornece garantias de desempenho e expõe o acesso e funcionalidades ao nível do servidor.

Uma base de dados do Azure para MariaDB server:

- É criado dentro de uma subscrição do Azure.
- É o recurso principal para bases de dados.
- Fornece um espaço de nomes para bases de dados.
- É um contentor com semântica de duração forte - elimine um servidor e elimina as bases de dados contidas.
- Coloca recursos numa região.
- Fornece um ponto final de ligação para o servidor e acesso de base de dados.
- Fornece o âmbito para políticas de gestão que se aplicam às respetivas bases de dados: início de sessão, firewall, os utilizadores, funções, configurações, etc.
- Está disponível na versão do motor MariaDB 10.2. Para obter mais informações, consulte [suportado base de dados para as versões de base de dados de MariaDB](./concepts-supported-versions.md).

Dentro de um Azure Database for MariaDB Server, pode criar uma ou múltiplas bases de dados. Pode optar por criar uma base de dados por servidor para utilizar todos os recursos ou para criar várias bases de dados partilham os recursos. O preço é estruturado por servidor, com base na configuração do armazenamento (GB), vCores e escalão de preço. Para obter mais informações, consulte [escalões de preço](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Como posso proteger uma base de dados do Azure para MariaDB server?

Os elementos seguintes ajudam a garantir um acesso seguro à sua base de dados.

|||
| :--| :--|
| **Autenticação e autorização** | Base de dados do Azure para MariaDB server suporta a autenticação de MySQL nativa. Pode ligar e autenticar um servidor com o início de sessão de administrador do servidor. |
| **Protocolo** | O serviço suporta um protocolo baseada em mensagens usado pelo MySQL. |
| **TCP/IP** | O protocolo é suportado por TCP/IP e através de sockets de domínio Unix. |
| **Firewall** | Para ajudar a proteger os seus dados, uma regra de firewall impede todo o acesso ao seu servidor de base de dados, até que especifica que computadores têm permissão. Ver [base de dados do Azure para as regras de firewall do servidor de MariaDB](./concepts-firewall-rules.md). |
| **SSL** | O serviço suporta a imposição de ligações de SSL entre seus aplicativos e o servidor de base de dados. Ver [configurar a conectividade SSL na sua aplicação para ligar em segurança à base de dados do Azure para MariaDB](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Como posso gerir um servidor?
Pode gerir a base de dados do Azure para MariaDB servidores utilizando o portal do Azure ou a CLI do Azure.

## <a name="next-steps"></a>Passos Seguintes
- Para uma descrição geral do serviço, consulte [base de dados do Azure para MariaDB descrição-geral](./overview.md)
- Para obter informações sobre recursos específicos quotas e limitações com base no seu **escalão de serviço**, consulte [escalões de serviço](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
