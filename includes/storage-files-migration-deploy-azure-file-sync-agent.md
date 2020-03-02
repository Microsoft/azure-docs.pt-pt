---
title: Implantação do agente Dessincronização de Ficheiros Azure
description: Implantação do agente Desincronização de Ficheiros Azure. Um bloco de texto comum, partilhado entre documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209418"
---
Nesta secção, instala o agente Dessincronização de ficheiros Azure no seu Servidor Windows.
O guia de [implantação](../articles/storage/files/storage-sync-files-deployment-guide.md) ilustra que precisa de desligar a segurança reforçada do **IE**. IE segurança reforçada é uma medida de segurança que não é aplicável com o Azure File Sync e desligá-lo permite-lhe autenticar o Azure sem qualquer problema.

Abra a PowerShell e instale os módulos PowerShell necessários com os seguintes comandos. Certifique-se de instalar o módulo completo e o fornecedor NuGet quando solicitado:

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Se tem algum problema em chegar à internet a partir do seu servidor, agora é a hora de resolvê-los. O Azure File Sync utiliza qualquer ligação de rede disponível à internet.
É também suportado um servidor proxy para chegar à internet. Pode configurar um proxy em toda a máquina agora, ou especificar um proxy que apenas sincronizar ficheiros irá usar durante a instalação do agente.

Se isso significa que precisa de abrir as suas firewalls para este servidor, então essa pode ser uma abordagem aceitável para si. No final da instalação do servidor, após o registo completo do servidor, haverá um relatório de conectividade de rede que lhe mostra os URLs de ponto final exatos em Azure, que a sincronização de ficheiros precisa de comunicar para a região com que selecionou. O relatório também lhe diz a razão pela qual a comunicação é necessária. Pode utilizar o relatório para, em seguida, bloquear as firewalls em torno deste servidor, para URLs específicos.

Também pode seguir uma abordagem mais conservadora, na qual não abre as firewalls de largura, mas limita o servidor a comunicar para espaços de nome DNS de nível superior - há mais documentação e detalhes disponíveis no artigo de proxy e configurações de [firewall do Ficheiro Azure.](../articles/storage/files/storage-sync-files-firewall-and-proxy.md) Siga as suas próprias práticas de networking.

No final do assistente de *instalação* do servidor, aparecerá um assistente de *registo* do servidor.
Registe o servidor no seu recurso Storage Sync Service Azure a partir de mais cedo.

Estes passos são descritos mais detalhadamente no guia de implementação, incluindo os módulos PowerShell acima que deve instalar primeiro: Instalação do [agente Dessincronização de ficheiros Azure](../articles/storage/files/storage-sync-files-deployment-guide.md).

O agente mais recente deve ser utilizado e pode ser descarregado do Microsoft Download Center: [Azure File Sync - agente](https://aka.ms/AFS/agent "Download de agente de Sincronização de Ficheiros Azure").

Após uma instalação bem sucedida e registo do servidor, pode verificar se completou com sucesso este passo: Navegue para o recurso do Serviço de Sincronização de Armazenamento no portal Azure e, em seguida, siga o menu à esquerda para "Servidores registados". Verá o seu servidor listado lá imediatamente.
