---
title: Exemplos de CLI
description: Encontre CLI do Azure exemplos para alguns dos cenários comuns do serviço de aplicativo. Saiba como automatizar suas tarefas de implantação ou gerenciamento do serviço de aplicativo.
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.topic: sample
ms.date: 12/12/2017
ms.custom: mvc
ms.openlocfilehash: 06f03e0005c4f98370bb1f6430fff506f9145d5a
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688512"
---
# <a name="cli-samples-for-azure-app-service"></a>Exemplos de CLI para o serviço Azure App

A tabela seguinte inclui ligações para scripts bash criados com a CLI do Azure.

| | |
|-|-|
|**Criar aplicação**||
| [Criar um aplicativo e implantar arquivos com FTP](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Cria um aplicativo do serviço de aplicativo e implanta um arquivo usando FTP. |
| [Criar um aplicativo e implantar o código do GitHub](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Cria um aplicativo do serviço de aplicativo e implanta o código de um repositório GitHub público. |
| [Criar um aplicativo com a implantação contínua do GitHub](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Cria um aplicativo do serviço de aplicativo com a publicação contínua de um repositório GitHub que você possui. |
| [Criar um aplicativo e implantar o código de um repositório git local](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Cria um aplicativo do serviço de aplicativo e configura o push de código de um repositório git local. |
| [Criar um aplicativo e implantar o código em um ambiente de preparo](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Cria um aplicativo do serviço de aplicativo com um slot de implantação para alterações de código de preparo. |
| [Criar um aplicativo ASP.NET Core em um contêiner do Docker](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Cria um aplicativo do serviço de aplicativo no Linux e carrega uma imagem do Docker do Hub do Docker. |
|**Configurar aplicação**||
| [Mapear um domínio personalizado para um aplicativo](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Cria um aplicativo do serviço de aplicativo e mapeia um nome de domínio personalizado para ele. |
| [Associar um certificado SSL personalizado a um aplicativo](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Cria um aplicativo do serviço de aplicativo e associa o certificado SSL de um nome de domínio personalizado a ele. |
|**Dimensionar aplicativo**||
| [Dimensionar um aplicativo manualmente](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Cria um aplicativo do serviço de aplicativo e o dimensiona em duas instâncias. |
| [Dimensionar um aplicativo em todo o mundo com uma arquitetura de alta disponibilidade](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Cria dois aplicativos do serviço de aplicativo em duas regiões geográficas diferentes e as disponibiliza por meio de um único ponto de extremidade usando o Gerenciador de tráfego do Azure. |
|**Conectar o aplicativo aos recursos**||
| [Conectar um aplicativo a um banco de dados SQL](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Cria um aplicativo do serviço de aplicativo e um banco de dados SQL e, em seguida, adiciona a cadeia de conexão do banco de dados às configurações do aplicativo. |
| [Conectar um aplicativo a uma conta de armazenamento](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Cria um aplicativo do serviço de aplicativo e uma conta de armazenamento e, em seguida, adiciona a cadeia de conexão de armazenamento às configurações do aplicativo. |
| [Conectar um aplicativo a um cache do Azure para Redis](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Cria um aplicativo do serviço de aplicativo e um cache do Azure para Redis e adiciona os detalhes de conexão do Redis às configurações do aplicativo.) |
| [Conectar um aplicativo ao Cosmos DB](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Cria um aplicativo do serviço de aplicativo e um Cosmos DB e, em seguida, adiciona os detalhes da conexão Cosmos DB às configurações do aplicativo. |
|**Fazer backup e restaurar o aplicativo**||
| [Fazer backup de um aplicativo](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Cria um aplicativo do serviço de aplicativo e cria um backup único para ele. |
| [Criar um backup agendado para um aplicativo](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Cria um aplicativo do serviço de aplicativo e cria um backup agendado para ele. |
| [Restaura um aplicativo de um backup](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Restaura um aplicativo do serviço de aplicativo de um backup. |
|**Monitorar aplicativo**||
| [Monitorar um aplicativo com logs do servidor Web](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Cria um aplicativo do serviço de aplicativo, habilita o registro para ele e baixa os logs em seu computador local. |
| | |