---
title: Exemplos do PowerShell
description: Encontre amostras da Azure PowerShell para alguns dos cenários comuns do App Service. Saiba automatizar as suas tarefas de implementação ou gestão do Serviço de Aplicações.
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.topic: sample
ms.date: 03/08/2017
ms.custom: mvc
ms.openlocfilehash: f32db2a4dda0ceb69644b64a5c76155b448c5a28
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74687018"
---
# <a name="powershell-samples-for-azure-app-service"></a>Amostras powerShell para serviço de aplicações Azure

A tabela seguinte inclui links para scripts PowerShell construídos com o Azure PowerShell.

| | |
|-|-|
|**Criar aplicação**||
| [Criar uma aplicação com implementação do GitHub](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações que retira código do GitHub. |
| [Criar uma app com implantação contínua do GitHub](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações que implementa continuamente código a partir do GitHub. |
| [Criar uma app e implementar código com FTP](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e faz upload de ficheiros de um diretório local utilizando FTP. |
| [Criar uma aplicação e implementar código a partir de um repositório git local](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e configura o code push de um repositório git local. |
| [Criar uma app e implementar código para um ambiente de encenação](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações com uma ranhura de implementação para a realização de alterações de código. |
|**Configurar aplicação**||
| [Mapear um domínio personalizado para uma aplicação](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações e mapeia-lhe um nome de domínio personalizado. |
| [Ligue um certificado SSL personalizado a uma aplicação](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria-lhe uma aplicação de Serviço de Aplicações e liga-lhe o certificado SSL de um nome de domínio personalizado. |
|**Dimensionar aplicação**||
| [Escala risadamente uma aplicação](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e escala-a em 2 instâncias. |
| [Escala uma app em todo o mundo com uma arquitetura de alta disponibilidade](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria duas aplicações do App Service em duas regiões geográficas diferentes e disponibiliza-as através de um único ponto final usando o Azure Traffic Manager. |
|**Ligar aplicação aos recursos**||
| [Ligue uma aplicação a uma Base de Dados SQL](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações e uma base de dados SQL e, em seguida, adiciona a cadeia de ligação à base de dados às definições da aplicação. |
| [Ligar uma aplicação a uma conta de armazenamento](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação do Serviço de Aplicações e uma conta de armazenamento e, em seguida, adiciona a cadeia de ligação de armazenamento às definições da aplicação. |
|**Back up e restaurar app**||
| [Efetuar cópia de segurança de uma aplicação](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e cria uma cópia de segurança única para o mesmo. |
| [Criar um backup agendado para uma aplicação](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e cria uma cópia de segurança agendada para o mesmo. |
| [Eliminar uma cópia de segurança para uma aplicação](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Elimina uma cópia de segurança existente para uma aplicação. |
| [Restaurar uma aplicação a partir de backup](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Restaura uma aplicação a partir de uma cópia de segurança previamente concluída. |
| [Restaurar um backup através de subscrições](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Restaura uma aplicação web a partir de uma cópia de segurança em outra subscrição. |
|**Monitorizar aplicação**||
| [Monitorize uma aplicação com registos de servidores web](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações, permite o registo e descarrega os registos para a sua máquina local. |
| | |
