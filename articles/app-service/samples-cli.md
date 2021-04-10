---
title: Exemplos de CLI
description: Encontre amostras de CLI Azure para alguns dos cenários comuns do Serviço de Aplicações. Saiba como automatizar as suas tarefas de implementação ou gestão do Serviço de Aplicações.
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.topic: sample
ms.date: 07/07/2020
ms.custom: mvc
ms.openlocfilehash: 61ce290f8ed5e8ad919a253d426d3278233caa3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98747294"
---
# <a name="cli-samples-for-azure-app-service"></a>Amostras de CLI para O Serviço de Aplicações Azure

A tabela seguinte inclui ligações para scripts bash criados com a CLI do Azure.

| Script | Description |
|-|-|
|**Criar aplicação**||
| [Criar uma app e implementar ficheiros com FTP](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações e implementa um ficheiro para ele usando FTP. |
| [Criar uma app e implementar código a partir do GitHub](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações e implementa código a partir de um repositório público do GitHub. |
| [Criar uma app com implementação contínua do GitHub](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações com publicação contínua a partir de um repositório GitHub que possui. |
| [Crie uma app e implemente código a partir de um repositório local de Git](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e configura o impulso de código a partir de um repositório git local. |
| [Crie uma app e implemente código para um ambiente de encenação](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações com uma ranhura de implementação para alterações de código de paragem. |
| [Crie uma aplicação core ASP.NET num recipiente Docker](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações no Linux e carrega uma imagem docker do Docker Hub. |
| [Crie uma app e exponha-a com um Ponto Final Privado](./scripts/cli-deploy-privateendpoint.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma app de Serviço de Aplicações e um Ponto Final Privado |
|**Configurar aplicação**||
| [Mapear um domínio personalizado para uma aplicação](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações e mapeia um nome de domínio personalizado para ele. |
| [Ligue um certificado TLS/SSL personalizado a uma aplicação](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações e liga o certificado TLS/SSL de um nome de domínio personalizado à sua. |
|**Dimensionar aplicação**||
| [Dimensione uma aplicação manualmente](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e escala-a em 2 instâncias. |
| [Dimensione uma app em todo o mundo com uma arquitetura de alta disponibilidade](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Cria duas aplicações de Serviço de Aplicações em duas regiões geográficas diferentes e disponibiliza-as através de um único ponto final utilizando o Azure Traffic Manager. |
|**Proteger app**||
| [Integre com gateway de aplicação Azure](./scripts/cli-integrate-app-service-with-application-gateway.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e integra-a com o Application Gateway utilizando restrições de serviço e acesso. |
|**Ligar aplicação aos recursos**||
| [Conecte uma aplicação a uma Base de Dados SQL](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações e uma base de dados na Base de Dados Azure SQL, em seguida, adiciona o fio de ligação da base de dados às definições da aplicação. |
| [Conecte uma aplicação a uma conta de armazenamento](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações e uma conta de armazenamento e, em seguida, adiciona o fio de ligação de armazenamento às definições da aplicação. |
| [Conecte uma aplicação a um Azure Cache para Redis](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e um Cache Azure para Redis, em seguida, adiciona os detalhes da ligação redis às definições da aplicação.) |
| [Conecte uma aplicação ao Cosmos DB](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e um Cosmos DB, em seguida, adiciona os detalhes da conexão Cosmos DB às definições da aplicação. |
|**App de backup e restauro**||
| [Backup de uma aplicação](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e cria uma cópia de segurança única para a empresa. |
| [Crie uma cópia de segurança programada para uma aplicação](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e cria uma cópia de segurança programada para a empresa. |
| [Restaura uma aplicação a partir de uma cópia de segurança](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Restaura uma aplicação de Serviço de Aplicações a partir de uma cópia de segurança. |
|**Monitorizar aplicação**||
| [Monitorize uma aplicação com registos de servidores web](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações, permite fazer login e descarrega os registos para a sua máquina local. |
| | |
