---
title: Exemplos do Azure PowerShell - serviço de aplicações | Documentos da Microsoft
description: Exemplos do Azure PowerShell - serviço de aplicações
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 69fd27785e5fc16a79fc23728b6d1e50a0a7b834
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835384"
---
# <a name="powershell-samples-for-azure-app-service"></a>Exemplos do PowerShell para o serviço de aplicações do Azure

A tabela seguinte inclui ligações para scripts do PowerShell criadas com o Azure PowerShell.

| | |
|-|-|
|**Criar aplicação**||
| [Criar uma aplicação com a implementação do GitHub](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação de serviço de aplicações que solicita código a partir do GitHub. |
| [Criar uma aplicação com implementação contínua a partir do GitHub](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação de serviço de aplicações que implementa continuamente o código do GitHub. |
| [Criar uma aplicação e implementar código com FTP](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria ficheiros de aplicação e o carregamento de um serviço de aplicações a partir de um diretório local com o FTP. |
| [Criar uma aplicação e implementar código a partir de um repositório de Git local](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de serviço de aplicações e configura o push de código de um repositório de Git local. |
| [Criar uma aplicação e implementar código num ambiente de teste](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de serviço de aplicações com um bloco de implementação para alterações de código de teste. |
|**Configurar aplicação**||
| [Mapear um domínio personalizado para uma aplicação](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação de serviço de aplicações e mapeia um nome de domínio personalizado ao mesmo. |
| [Vincular um certificado SSL personalizado a uma aplicação](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação de serviço de aplicações e vincula o certificado SSL de um nome de domínio personalizado ao mesmo. |
|**Aplicação de dimensionamento**||
| [Dimensionar manualmente uma aplicação](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de serviço de aplicações e dimensiona-a entre 2 instâncias. |
| [Dimensionar uma aplicação todo o mundo com uma arquitetura de elevada disponibilidade](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria duas aplicações de serviço de aplicações em duas regiões geográficas diferentes e disponibiliza-os através de um único ponto final de utilização do Gestor de tráfego do Azure. |
|**Ligar a aplicação aos recursos**||
| [Ligar uma aplicação para uma base de dados SQL](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação de serviço de aplicações e uma base de dados SQL, em seguida, adiciona a cadeia de ligação de base de dados para as definições da aplicação. |
| [Ligar uma aplicação a uma conta de armazenamento](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação de serviço de aplicações e uma conta de armazenamento, em seguida, adiciona a cadeia de ligação de armazenamento para as definições da aplicação. |
|**Criar cópias de segurança e restaurar a aplicação**||
| [Fazer cópias de segurança de uma aplicação](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de serviço de aplicações e cria uma cópia de segurança única para o mesmo. |
| [Criar uma cópia de segurança agendada para uma aplicação](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de serviço de aplicações e cria uma cópia de segurança agendada para o mesmo. |
| [Eliminar uma cópia de segurança para uma aplicação](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Elimina uma cópia de segurança existente para uma aplicação. |
| [Restaurar uma aplicação a partir de cópia de segurança](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Restaura uma aplicação a partir de uma cópia de segurança anteriormente concluída. |
| [Restaurar uma cópia de segurança entre subscrições](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Restaura uma aplicação web a partir de uma cópia de segurança noutra subscrição. |
|**Monitorizar aplicação**||
| [Monitorizar uma aplicação com os registos de servidor web](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de serviço de aplicações, ativa o registo para ele e transfere os registos para o computador local. |
| | |
