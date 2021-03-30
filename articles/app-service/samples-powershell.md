---
title: Exemplos do PowerShell
description: Encontre amostras Azure PowerShell para alguns dos cenários comuns do Serviço de Aplicações. Saiba como automatizar as suas tarefas de implementação ou gestão do Serviço de Aplicações.
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.topic: sample
ms.date: 07/07/2020
ms.custom: mvc
ms.openlocfilehash: a1577d42de9a4452467a448a0de5cd5f9575a55f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86169430"
---
# <a name="powershell-samples-for-azure-app-service"></a>Amostras powerShell para Serviço de Aplicações Azure

A tabela seguinte inclui ligações aos scripts PowerShell construídos utilizando o Azure PowerShell.

| Script | Description |
|-|-|
|**Criar aplicação**||
| [Criar uma app com implementação a partir do GitHub](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações que retira código do GitHub. |
| [Criar uma app com implementação contínua do GitHub](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações que implementa continuamente código a partir do GitHub. |
| [Criar uma app e implementar código com FTP](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e envia ficheiros a partir de um diretório local utilizando FTP. |
| [Crie uma app e implemente código a partir de um repositório local de Git](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e configura o impulso de código a partir de um repositório git local. |
| [Crie uma app e implemente código para um ambiente de encenação](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações com uma ranhura de implementação para alterações de código de paragem. |
|  [Crie uma app e exponha a sua app com um Ponto Final Privado](./scripts/powershell-deploy-private-endpoint.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações com um Ponto Final Privado. |
|**Configurar aplicação**||
| [Mapear um domínio personalizado para uma aplicação](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações e mapeia um nome de domínio personalizado para ele. |
| [Ligue um certificado TLS/SSL personalizado a uma aplicação](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações e liga o certificado TLS/SSL de um nome de domínio personalizado à sua. |
|**Dimensionar aplicação**||
| [Dimensione uma aplicação manualmente](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e escala-a em 2 instâncias. |
| [Dimensione uma app em todo o mundo com uma arquitetura de alta disponibilidade](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria duas aplicações de Serviço de Aplicações em duas regiões geográficas diferentes e disponibiliza-as através de um único ponto final utilizando o Azure Traffic Manager. |
|**Ligar aplicação aos recursos**||
| [Conecte uma aplicação a uma Base de Dados SQL](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações e uma base de dados na Base de Dados Azure SQL, em seguida, adiciona o fio de ligação da base de dados às definições da aplicação. |
| [Conecte uma aplicação a uma conta de armazenamento](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação de Serviço de Aplicações e uma conta de armazenamento e, em seguida, adiciona o fio de ligação de armazenamento às definições da aplicação. |
|**Apoiar e restaurar app**||
| [Efetuar cópia de segurança de uma aplicação](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e cria uma cópia de segurança única para a empresa. |
| [Crie uma cópia de segurança programada para uma aplicação](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações e cria uma cópia de segurança programada para a empresa. |
| [Elimine uma cópia de segurança para uma aplicação](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Elimina uma cópia de segurança existente para uma aplicação. |
| [Restaurar uma aplicação a partir de backup](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Restaura uma aplicação a partir de uma cópia de segurança previamente concluída. |
| [Restaurar uma cópia de segurança através de subscrições](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Restaura uma aplicação web a partir de uma cópia de segurança em outra subscrição. |
|**Monitorizar aplicação**||
| [Monitorize uma aplicação com registos de servidores web](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação de Serviço de Aplicações, permite fazer login e descarrega os registos para a sua máquina local. |
| | |
