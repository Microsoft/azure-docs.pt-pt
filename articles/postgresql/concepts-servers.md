---
title: Servidores - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo fornece considerações e diretrizes para configurar e gerir a Base de Dados Azure para PostgreSQL - Servidor Único.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 1d1751ed56f63105ce7775ceaed9ea2dcca1f8bf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628140"
---
# <a name="azure-database-for-postgresql---single-server"></a>Base de Dados do Azure para PostgreSQL – Servidor Único
Este artigo fornece considerações e diretrizes para trabalhar com a Base de Dados Azure para PostgreSQL - Servidor Único.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>O que é um servidor da Base de Dados do Azure para PostgreSQL?
Um servidor na Base de Dados Azure para a opção de implementação do Servidor Único - Servidor Único é um ponto administrativo central para várias bases de dados. É a mesma construção de servidor PostgreSQL que você pode estar familiarizado no mundo do local. Especificamente, o serviço PostgreSQL é gerido, fornece garantias de desempenho, expõe o acesso e as funcionalidades ao nível do servidor.

Uma base de dados Azure para o servidor PostgreSQL:

- É criado dentro de uma subscrição Azure.
- É o recurso principal para bases de dados.
- Fornece um espaço de nome para bases de dados.
- É um recipiente com semântica vitalícia forte - elimine um servidor e elimine as bases de dados contidas.
- Colita recursos numa região.
- Fornece um ponto final de ligação para o servidor e acesso à base de dados 
- Fornece a margem de manobra para políticas de gestão que se aplicam às suas bases de dados: login, firewall, utilizadores, funções, configurações, etc.
- Está disponível em várias versões. Para obter mais informações, consulte as [versões de base de dados postgreSQL suportadas.](concepts-supported-versions.md)
- É extensível pelos utilizadores. Para obter mais informações, consulte [as extensões PostgreSQL](concepts-extensions.md).

Dentro de uma Base de Dados Azure para servidor PostgreSQL, pode criar uma ou várias bases de dados. Pode optar por criar uma base de dados por servidor para utilizar todos os recursos ou criar várias bases de dados para partilhar os recursos. O preço é estruturado por servidor, com base na configuração do nível de preços, vCores e armazenamento (GB). Para obter mais informações, consulte [os níveis de preços.](./concepts-pricing-tiers.md)

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Como posso ligar e autenticar a uma Base de Dados Azure para servidor PostgreSQL?
Os seguintes elementos ajudam a garantir um acesso seguro à sua base de dados:

|Conceito de segurança|Description|
|:--|:--|
| **Autenticação e autorização** | O servidor de Base de Dados do Azure para PostgreSQL suporta autenticação PostgreSQL nativa. Pode ligar e autenticar-se no servidor com o início de sessão de administrador do servidor. |
| **Protocolo** | O serviço suporta um protocolo baseado em mensagens utilizado pela PostgreSQL. |
| **TCP/IP** | O protocolo é suportado sobre TCP/IP, e sobre tomadas de domínio Unix. |
| **Firewall** | Para ajudar a proteger os seus dados, uma regra de firewall impede todo o acesso ao seu servidor e às suas bases de dados, até que especifique quais os computadores que têm permissão. Consulte [a base de dados Azure para as regras de firewall do servidor postgresQL](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>Gerir o seu servidor
Pode gerir a Base de Dados Azure para servidores PostgreSQL utilizando o [portal Azure](https://portal.azure.com) ou o [Azure CLI](/cli/azure/postgres).

Ao criar um servidor, configura as credenciais para o utilizador administrador. O utilizador administrativo é o utilizador de maior privilégio que tem no servidor. Pertence ao papel azure_pg_admin. Este papel não tem permissões completas de super-er. 

O atributo superuser PostgreSQL é atribuído ao azure_superuser, que pertence ao serviço gerido. Não tem acesso a este papel.

Uma base de dados Azure para servidor PostgreSQL tem bases de dados predefinidas: 
- **postgres** - Uma base de dados padrão a que pode ligar assim que o seu servidor for criado.
- **azure_maintenance** - Esta base de dados é utilizada para separar os processos que fornecem o serviço gerido das ações do utilizador. Não tem acesso a esta base de dados.
- **azure_sys** - Uma base de dados para a Loja de Consultas. Esta base de dados não acumula dados quando a Loja de Consultas está desligada; esta é a definição padrão. Para mais informações, consulte a [visão geral da Loja de Consultas.](concepts-query-store.md)


## <a name="server-parameters"></a>Parâmetros do servidor
Os parâmetros do servidor PostgreSQL determinam a configuração do servidor. Na Base de Dados Azure para PostgreSQL, a lista de parâmetros pode ser visualizada e editada através do portal Azure ou do Azure CLI. 

Como serviço gerido para Postgres, os parâmetros configuráveis na Base de Dados Azure para PostgreSQL são um subconjunto dos parâmetros num caso de Postgres local (Para obter mais informações sobre os parâmetros postgres, consulte a [documentação PostgreSQL).](https://www.postgresql.org/docs/9.6/static/runtime-config.html) A sua Base de Dados Azure para o servidor PostgreSQL está ativada com valores predefinidos para cada parâmetro na criação. Alguns parâmetros que exigiriam um reinício do servidor ou acesso de super-utilização para que as alterações produzam efeitos não possam ser configurados pelo utilizador.


## <a name="next-steps"></a>Passos seguintes
- Para obter uma visão geral do serviço, consulte [a Base de Dados Azure para obter uma visão geral pós-SQL.](overview.md)
- Para obter informações sobre quotas e limitações específicas de recursos com base no seu **nível de serviço,** consulte [os níveis de Serviço](concepts-pricing-tiers.md).
- Para obter informações sobre a ligação ao serviço, consulte [as bibliotecas de ligação para a base de dados Azure para PostgreSQL](concepts-connection-libraries.md).
- Ver e editar parâmetros do servidor através do [portal Azure](howto-configure-server-parameters-using-portal.md) ou [do Azure CLI](howto-configure-server-parameters-using-cli.md).
