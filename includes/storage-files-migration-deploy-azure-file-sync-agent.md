---
title: Implementar o agente Azure File Sync
description: Implemente o agente Azure File Sync. Um bloco de texto comum, partilhado através de documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: f038392f03b94aa2c2450531c9da4a11d9900295
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043129"
---
Nesta secção, instale o agente Azure File Sync na sua instância do Windows Server.

O [guia de implementação](../articles/storage/files/storage-sync-files-deployment-guide.md) ilustra que é necessário desligar a **configuração de segurança melhorada do Internet Explorer.** Esta medida de segurança não é aplicável com a Azure File Sync. Desligá-lo permite-lhe autenticar para Azure sem qualquer problema.

Abra o PowerShell e instale os módulos PowerShell necessários utilizando os seguintes comandos. Certifique-se de que instala o módulo completo e o fornecedor NuGet quando o solicitar.

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Se tiver algum problema em chegar à internet a partir do seu servidor, agora é a hora de resolvê-los. O Azure File Sync utiliza qualquer ligação de rede disponível à internet. Exigir que um servidor proxy chegue à internet também é suportado. Pode configurar agora um representante de toda a máquina ou especificar um proxy que apenas o Azure File Sync utilizará durante a instalação do agente.

Se configurar um representante significa que precisa de abrir as suas firewalls para este servidor, essa abordagem pode ser aceitável para si. No final da instalação do servidor, depois de ter concluído o registo do servidor, um relatório de conectividade de rede irá mostrar-lhe os URLs de ponto final exatos em Azure que o Azure File Sync precisa de comunicar para a região que selecionou. O relatório diz-lhe também a razão pela qual a comunicação é necessária. Pode utilizar o relatório para bloquear as firewalls em torno deste servidor para URLs específicos.

Também pode seguir uma abordagem mais conservadora na qual não abre as firewalls de largura, mas limita o servidor a comunicar com espaços de nome DNS de nível superior. Para obter mais informações, consulte as [definições de procuração e firewall do Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md). Siga as suas próprias práticas de networking.

No final do assistente de instalação do servidor, será aberto um assistente de registo do servidor. Registe o servidor no recurso Azure do seu Serviço de Sincronização de Armazenamento a partir de anteriormente.

Estes passos são descritos com mais detalhe no guia de implantação, que inclui os módulos PowerShell que deve instalar primeiro: instalação do [agente Azure File Sync](../articles/storage/files/storage-sync-files-deployment-guide.md).

Usa o último agente. Pode descarregá-lo a partir do Microsoft Download Center: [Azure File Sync Agent](https://aka.ms/AFS/agent "Download do Agente de Sincronização de Ficheiros Azure").

Após uma instalação e registo de servidor bem sucedidos, pode verificar se completou este passo com sucesso. Aceda ao recurso de Serviço de Sincronização de Armazenamento no portal Azure. No menu esquerdo, aceda aos **servidores registados.** Verá o seu servidor listado lá.
