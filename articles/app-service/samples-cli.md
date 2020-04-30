---
title: Exemplos de CLI
description: Encontre amostras azure CLI para alguns dos cenários comuns do Serviço de Aplicações. Saiba automatizar as suas tarefas de implementação ou gestão do Serviço de Aplicações.
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.topic: sample
ms.date: 12/12/2017
ms.custom: mvc
ms.openlocfilehash: 499bf25f06030ae9fc5c1f8381ef4cd6ff6873dc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81535626"
---
# <a name="cli-samples-for-azure-app-service"></a>Amostras cli para serviço de aplicações Azure

A tabela seguinte inclui ligações para scripts bash criados com a CLI do Azure.

| | |
|-|-|
|**Criar aplicação**||
| [Criar uma aplicação e implementar ficheiros com FTP](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações e implementa um ficheiro utilizando FTP. |
| [Criar uma aplicação e implementar código a partir do GitHub](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações e implementa código a partir de um repositório público do GitHub. |
| [Criar uma app com implantação contínua do GitHub](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações com publicação contínua a partir de um repositório GitHub que possui. |
| [Criar uma aplicação e implementar código a partir de um repositório git local](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e configura o code push de um repositório git local. |
| [Criar uma app e implementar código para um ambiente de encenação](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações com uma ranhura de implementação para a realização de alterações de código. |
| [Crie uma aplicação ASP.NET Core num recipiente Dodocker](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações no Linux e carrega uma imagem do Docker do Docker Hub. |
|**Configurar aplicação**||
| [Mapear um domínio personalizado para uma aplicação](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações e mapeia-lhe um nome de domínio personalizado. |
| [Ligue um certificado Personalizado TLS/SSL a uma aplicação](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Cria-lhe uma aplicação de Serviço de Aplicações e liga-lhe o certificado TLS/SSL de um nome de domínio personalizado. |
|**Dimensionar aplicação**||
| [Escala risadamente uma aplicação](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e escala-a em 2 instâncias. |
| [Escala uma app em todo o mundo com uma arquitetura de alta disponibilidade](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Cria duas aplicações do App Service em duas regiões geográficas diferentes e disponibiliza-as através de um único ponto final usando o Azure Traffic Manager. |
|**Proteger app**||
| [Integrar com o Portal de Aplicação Azure](./scripts/cli-integrate-app-service-with-application-gateway.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e integra-a com o Application Gateway utilizando restrições de ponto final de serviço e acesso. |
|**Ligar aplicação aos recursos**||
| [Ligue uma aplicação a uma Base de Dados SQL](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações e uma base de dados SQL e, em seguida, adiciona a cadeia de ligação à base de dados às definições da aplicação. |
| [Ligar uma aplicação a uma conta de armazenamento](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação do Serviço de Aplicações e uma conta de armazenamento e, em seguida, adiciona a cadeia de ligação de armazenamento às definições da aplicação. |
| [Ligue uma aplicação a um Azure Cache para Redis](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e um Cache Azure para Redis, em seguida, adiciona os detalhes da ligação redis às definições da aplicação.) |
| [Ligue uma aplicação ao Cosmos DB](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e um Cosmos DB, depois adiciona os detalhes da ligação Cosmos DB às definições da aplicação. |
|**Back up e restaurar app**||
| [Efetuar cópia de segurança de uma aplicação](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e cria uma cópia de segurança única para o mesmo. |
| [Criar um backup agendado para uma aplicação](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e cria uma cópia de segurança agendada para o mesmo. |
| [Restaura uma aplicação a partir de uma cópia de segurança](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Restaura uma aplicação do Serviço de Aplicações a partir de uma cópia de segurança. |
|**Monitorizar aplicação**||
| [Monitorize uma aplicação com registos de servidores web](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações, permite o registo e descarrega os registos para a sua máquina local. |
| | |