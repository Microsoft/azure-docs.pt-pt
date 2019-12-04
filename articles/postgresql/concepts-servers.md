---
title: Servidores-Banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo fornece considerações e diretrizes para configurar e gerenciar o banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: d86170a53b4bfbe712bbca12db4d6063214aba21
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768168"
---
# <a name="azure-database-for-postgresql---single-server"></a>Banco de dados do Azure para PostgreSQL-servidor único
Este artigo fornece considerações e diretrizes para trabalhar com o banco de dados do Azure para PostgreSQL-servidor único.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>O que é um banco de dados do Azure para o servidor PostgreSQL?
Um servidor no banco de dados do Azure para PostgreSQL-opção de implantação de servidor único é um ponto administrativo central para vários bancos de dados. É a mesma construção de servidor PostgreSQL com a qual você pode estar familiarizado no mundo local. Especificamente, o serviço PostgreSQL é gerenciado, fornece garantias de desempenho, expõe o acesso e os recursos no nível do servidor.

Um banco de dados do Azure para servidor PostgreSQL:

- É criado em uma assinatura do Azure.
- É o recurso pai para bancos de dados.
- Fornece um namespace para bancos de dados.
- É um contêiner com semântica de tempo de vida forte – exclui um servidor e exclui os bancos de dados independentes.
- Posiciona recursos em uma região.
- Fornece um ponto de extremidade de conexão para acesso de servidor e de banco de dados 
- Fornece o escopo para políticas de gerenciamento que se aplicam a seus bancos de dados: logon, firewall, usuários, funções, configurações, etc.
- Está disponível em várias versões. Para obter mais informações, consulte [versões de banco de dados PostgreSQL com suporte](concepts-supported-versions.md).
- É extensível por usuários. Para obter mais informações, consulte [as extensões do PostgreSQL](concepts-extensions.md).

Dentro de um banco de dados do Azure para servidor PostgreSQL, você pode criar um ou vários bancos de dados. Pode optar por criar uma base de dados por servidor para utilizar todos os recursos ou criar várias bases de dados para partilhar os recursos. O preço é estruturado por servidor, com base na configuração do tipo de preço, vCores e armazenamento (GB). Para obter mais informações, consulte [tipos de preço](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Como fazer conectar e autenticar em um banco de dados do Azure para o servidor PostgreSQL?
Os elementos a seguir ajudam a garantir o acesso seguro ao seu banco de dados:

|||
|:--|:--|
| **Autenticação e autorização** | O banco de dados do Azure para servidor PostgreSQL dá suporte à autenticação PostgreSQL nativa. Você pode se conectar e autenticar no servidor com o logon de administrador do servidor. |
| **Protocolo** | O serviço oferece suporte a um protocolo baseado em mensagem usado pelo PostgreSQL. |
| **TCP/IP** | O protocolo tem suporte em TCP/IP e em soquetes de domínio do UNIX. |
| **Firewall** | Para ajudar a proteger seus dados, uma regra de Firewall impede todo acesso ao seu servidor e a seus bancos de dado, até que você especifique quais computadores têm permissão. Consulte [banco de dados do Azure para PostgreSQL regras de firewall do servidor](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>Gerenciando seu servidor
Você pode gerenciar o banco de dados do Azure para servidores PostgreSQL usando o [portal do Azure](https://portal.azure.com) ou o [CLI do Azure](/cli/azure/postgres).

Ao criar um servidor, você configura as credenciais para o usuário administrador. O usuário administrador é o usuário de privilégio mais alto que você tem no servidor. Ele pertence à função azure_pg_admin. Essa função não tem permissões totais de superusuário. 

O atributo de superusuário do PostgreSQL é atribuído ao azure_superuser, que pertence ao serviço gerenciado. Você não tem acesso a essa função.

Um banco de dados do Azure para servidor PostgreSQL tem bancos de dados padrão: 
- **postgres** -um banco de dados padrão ao qual você pode se conectar quando o servidor é criado.
- **azure_maintenance** -esse banco de dados é usado para separar os processos que fornecem o serviço gerenciado das ações do usuário. Você não tem acesso a este banco de dados.
- **azure_sys** -um banco de dados para o repositório de consultas. Esse banco de dados não acumulará o dado quando Repositório de Consultas estiver desativado; Essa é a configuração padrão. Para obter mais informações, consulte a [visão geral de repositório de consultas](concepts-query-store.md).


## <a name="server-parameters"></a>Parâmetros do servidor
Os parâmetros do servidor PostgreSQL determinam a configuração do servidor. No banco de dados do Azure para PostgreSQL, a lista de parâmetros pode ser exibida e editada usando o portal do Azure ou o CLI do Azure. 

Como um serviço gerenciado para Postgres, os parâmetros configuráveis no banco de dados do Azure para PostgreSQL são um subconjunto dos parâmetros em uma instância postgres local (para obter mais informações sobre parâmetros de Postgres, consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). O banco de dados do Azure para servidor PostgreSQL está habilitado com valores padrão para cada parâmetro na criação. Alguns parâmetros que exigem uma reinicialização do servidor ou o acesso de superusuário para que as alterações entrem em vigor não podem ser configurados pelo usuário.


## <a name="next-steps"></a>Passos seguintes
- Para obter uma visão geral do serviço, consulte [visão geral do banco de dados do Azure para PostgreSQL](overview.md).
- Para obter informações sobre cotas de recursos e limitações específicas com base em sua **camada de serviço**, consulte [camadas de serviço](concepts-pricing-tiers.md).
- Para obter informações sobre como se conectar ao serviço, consulte [bibliotecas de conexões para o banco de dados do Azure para PostgreSQL](concepts-connection-libraries.md).
- Exiba e edite parâmetros de servidor por meio de [portal do Azure](howto-configure-server-parameters-using-portal.md) ou [CLI do Azure](howto-configure-server-parameters-using-cli.md).
