---
title: Implementar o agente Dessincronização de Ficheiros Azure
description: Implantação do agente Desincronização de Ficheiros Azure. Um bloco de texto comum, partilhado em todos os documentos migratórios.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 66388f14949b4f398de18c9162ca453e7fb3cbe1
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143559"
---
Nesta secção, instala o agente Dessincronização de ficheiros Azure na sua instância do Servidor Windows.

O guia de [implementação](../articles/storage/files/storage-sync-files-deployment-guide.md) ilustra que precisa de desligar a **configuração**de segurança melhorada do Internet Explorer . Esta medida de segurança não é aplicável com o Azure File Sync. Desligá-lo permite-lhe autenticar o Azure sem qualquer problema.

Abra o PowerShell e instale os módulos PowerShell necessários utilizando os seguintes comandos. Certifique-se de instalar o módulo completo e o fornecedor NuGet quando for solicitado.

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Se tem algum problema em chegar à internet a partir do seu servidor, agora é a hora de resolvê-los. O Azure File Sync utiliza qualquer ligação de rede disponível à internet. É também suportado um servidor proxy para chegar à internet. Pode configurar um proxy em toda a máquina agora, ou especificar um proxy que apenas o Azure File Sync irá usar durante a instalação do agente.

Se configurar um proxy significa que precisa de abrir as suas firewalls para este servidor, isso pode ser uma abordagem aceitável para si. No final da instalação do servidor, depois de ter concluído o registo do servidor, um relatório de conectividade de rede mostrar-lhe-á os URLs de ponto final exatos em Azure que o Azure File Sync precisa de comunicar para a região com que selecionou. O relatório também lhe diz a razão pela qual a comunicação é necessária. Pode utilizar o relatório para bloquear as firewalls em torno deste servidor a URLs específicos.

Também pode seguir uma abordagem mais conservadora na qual não abre as firewalls de largura, mas limita o servidor a comunicar com espaços de nome DNS de nível superior. Para mais informações, consulte as definições de [proxy e firewall do Ficheiro Azure](../articles/storage/files/storage-sync-files-firewall-and-proxy.md). Siga as suas próprias práticas de networking.

No final do assistente de *instalação* do servidor, um assistente de *registo* do servidor será aberto. Registe o servidor no recurso Azure do seu Serviço de Sincronização de Armazenamento a partir de mais cedo.

Estes passos são descritos mais detalhadamente no guia de implementação, incluindo os módulos PowerShell que deve instalar primeiro: instalação do [agente Dessincronização de ficheiros Azure](../articles/storage/files/storage-sync-files-deployment-guide.md).

Usa o último agente. Pode descarregá-lo a partir do Microsoft Download Center: [Azure File Sync Agent](https://aka.ms/AFS/agent "Download do Agente sincronizado de ficheiros Azure").

Após uma instalação bem sucedida e registo do servidor, pode verificar se completou este passo com sucesso. Vá ao recurso do Serviço de Sincronização de Armazenamento no portal Azure e, em seguida, siga o menu esquerdo para **servidores registados**. Verá o seu servidor listado lá.
