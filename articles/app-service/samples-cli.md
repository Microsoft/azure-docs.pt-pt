---
title: Exemplos da CLI do Azure - serviço de aplicações | Documentos da Microsoft
description: Exemplos da CLI do Azure - serviço de aplicações
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 12/12/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: fe5649951b1b19ce52c13648f897f4a83e1f761b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835401"
---
# <a name="cli-samples-for-azure-app-service"></a>Exemplos da CLI para o serviço de aplicações do Azure

A tabela seguinte inclui ligações para scripts bash criados com a CLI do Azure.

| | |
|-|-|
|**Criar aplicação**||
| [Criar uma aplicação e implementar ficheiros com FTP](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de serviço de aplicações e implementa um arquivo ao mesmo através de FTP. |
| [Criar uma aplicação e implementar código a partir do GitHub](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de serviço de aplicações e implementa código a partir de um repositório do GitHub público. |
| [Criar uma aplicação com implementação contínua a partir do GitHub](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de serviço de aplicações com a publicação contínua a partir de um repositório do GitHub que possui. |
| [Criar uma aplicação e implementar código a partir de um repositório de Git local](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de serviço de aplicações e configura o push de código de um repositório de Git local. |
| [Criar uma aplicação e implementar código num ambiente de teste](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de serviço de aplicações com um bloco de implementação para alterações de código de teste. |
| [Criar uma aplicação ASP.NET Core num contentor do Docker](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de serviço de aplicações no Linux e carrega uma imagem do Docker a partir do Docker Hub. |
|**Configurar aplicação**||
| [Mapear um domínio personalizado para uma aplicação](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de serviço de aplicações e mapeia um nome de domínio personalizado ao mesmo. |
| [Vincular um certificado SSL personalizado a uma aplicação](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de serviço de aplicações e vincula o certificado SSL de um nome de domínio personalizado ao mesmo. |
|**Aplicação de dimensionamento**||
| [Dimensionar manualmente uma aplicação](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de serviço de aplicações e dimensiona-a entre 2 instâncias. |
| [Dimensionar uma aplicação todo o mundo com uma arquitetura de elevada disponibilidade](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Cria duas aplicações de serviço de aplicações em duas regiões geográficas diferentes e disponibiliza-os através de um único ponto final de utilização do Gestor de tráfego do Azure. |
|**Ligar a aplicação aos recursos**||
| [Ligar uma aplicação para uma base de dados SQL](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de serviço de aplicações e uma base de dados SQL, em seguida, adiciona a cadeia de ligação de base de dados para as definições da aplicação. |
| [Ligar uma aplicação a uma conta de armazenamento](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de serviço de aplicações e uma conta de armazenamento, em seguida, adiciona a cadeia de ligação de armazenamento para as definições da aplicação. |
| [Ligar uma aplicação para uma Cache do Azure para Redis](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de serviço de aplicações e de uma Cache do Azure para Redis, em seguida, adiciona os detalhes de ligação redis às definições da aplicação.) |
| [Ligar uma aplicação ao Cosmos DB](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Cria um Cosmos DB e a uma aplicação de serviço de aplicações, em seguida, adiciona os detalhes de ligação do Cosmos DB às definições da aplicação. |
|**Criar cópias de segurança e restaurar a aplicação**||
| [Fazer cópias de segurança de uma aplicação](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de serviço de aplicações e cria uma cópia de segurança única para o mesmo. |
| [Criar uma cópia de segurança agendada para uma aplicação](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de serviço de aplicações e cria uma cópia de segurança agendada para o mesmo. |
| [Restaura uma aplicação a partir de uma cópia de segurança](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Restaura uma aplicação de serviço de aplicações a partir de uma cópia de segurança. |
|**Monitorizar aplicação**||
| [Monitorizar uma aplicação com os registos de servidor web](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de serviço de aplicações, ativa o registo para ele e transfere os registos para o computador local. |
| | |