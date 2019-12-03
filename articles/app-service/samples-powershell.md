---
title: Exemplos do PowerShell
description: Encontre Azure PowerShell exemplos para alguns dos cenários comuns do serviço de aplicativo. Saiba como automatizar suas tarefas de implantação ou gerenciamento do serviço de aplicativo.
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.topic: sample
ms.date: 03/08/2017
ms.custom: mvc
ms.openlocfilehash: f32db2a4dda0ceb69644b64a5c76155b448c5a28
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687018"
---
# <a name="powershell-samples-for-azure-app-service"></a>Exemplos do PowerShell para o serviço Azure App

A tabela a seguir inclui links para scripts do PowerShell criados usando o Azure PowerShell.

| | |
|-|-|
|**Criar aplicação**||
| [Criar um aplicativo com a implantação do GitHub](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria um aplicativo do serviço de aplicativo que recebe o código do GitHub. |
| [Criar um aplicativo com a implantação contínua do GitHub](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria um aplicativo do serviço de aplicativo que implanta continuamente o código do GitHub. |
| [Criar um aplicativo e implantar o código com o FTP](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo do serviço de aplicativo e carrega arquivos de um diretório local usando FTP. |
| [Criar um aplicativo e implantar o código de um repositório git local](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo do serviço de aplicativo e configura o push de código de um repositório git local. |
| [Criar um aplicativo e implantar o código em um ambiente de preparo](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo do serviço de aplicativo com um slot de implantação para alterações de código de preparo. |
|**Configurar aplicação**||
| [Mapear um domínio personalizado para um aplicativo](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria um aplicativo do serviço de aplicativo e mapeia um nome de domínio personalizado para ele. |
| [Associar um certificado SSL personalizado a um aplicativo](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria um aplicativo do serviço de aplicativo e associa o certificado SSL de um nome de domínio personalizado a ele. |
|**Dimensionar aplicativo**||
| [Dimensionar um aplicativo manualmente](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo do serviço de aplicativo e o dimensiona em duas instâncias. |
| [Dimensionar um aplicativo em todo o mundo com uma arquitetura de alta disponibilidade](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria dois aplicativos do serviço de aplicativo em duas regiões geográficas diferentes e as disponibiliza por meio de um único ponto de extremidade usando o Gerenciador de tráfego do Azure. |
|**Conectar o aplicativo aos recursos**||
| [Conectar um aplicativo a um banco de dados SQL](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria um aplicativo do serviço de aplicativo e um banco de dados SQL e, em seguida, adiciona a cadeia de conexão do banco de dados às configurações do aplicativo. |
| [Conectar um aplicativo a uma conta de armazenamento](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria um aplicativo do serviço de aplicativo e uma conta de armazenamento e, em seguida, adiciona a cadeia de conexão de armazenamento às configurações do aplicativo. |
|**Fazer backup e restaurar o aplicativo**||
| [Fazer backup de um aplicativo](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo do serviço de aplicativo e cria um backup único para ele. |
| [Criar um backup agendado para um aplicativo](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo do serviço de aplicativo e cria um backup agendado para ele. |
| [Excluir um backup de um aplicativo](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Exclui um backup existente de um aplicativo. |
| [Restaurar um aplicativo do backup](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Restaura um aplicativo de um backup concluído anteriormente. |
| [Restaurar um backup entre assinaturas](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Restaura um aplicativo Web de um backup em outra assinatura. |
|**Monitorar aplicativo**||
| [Monitorar um aplicativo com logs do servidor Web](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo do serviço de aplicativo, habilita o registro para ele e baixa os logs em seu computador local. |
| | |
