---
title: Lista de URL seguro do Windows Virtual Desktop - Azure
description: Uma lista de URLs que deve desbloquear para garantir que a sua implementação virtual do Windows funciona como pretendido.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9b1bdfc326ff217e68785d823b4af046af3241b7
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225183"
---
# <a name="safe-url-list"></a>Lista de URL seguro

Terá de desbloquear certos URLs para que a sua implementação virtual do Windows desktop funcione corretamente. Este artigo lista estes URLs para que saiba quais são seguros.

## <a name="virtual-machines"></a>Máquinas virtuais

As máquinas virtuais Azure que cria para o Windows Virtual Desktop devem ter acesso aos seguintes URLs:

|Endereço|Porta TCP de saída|Objetivo|Etiqueta de serviço|
|---|---|---|---|
|*.wvd.microsoft.com|443|Tráfego de serviço|WindowsVirtualDesktop|
|mrsglobalsteus2prod.blob.core.windows.net|443|Atualizações da pilha de agente e SXS|AzureCloud|
|*.core.windows.net|443|Tráfego de agentes|AzureCloud|
|*.servicebus.windows.net|443|Tráfego de agentes|AzureCloud|
|prod.warmpath.msftcloudes.com|443|Tráfego de agentes|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Ativação do Windows|Internet|
|wvdportalstorageblob.blob.core.windows.net|443|Suporte ao portal Azure|AzureCloud|

>[!IMPORTANT]
>O Windows Virtual Desktop suporta agora a tag FQDN. Para obter mais informações, consulte [o Azure Firewall para proteger as implementações do Ambiente de Trabalho Virtual da Janela](../firewall/protect-windows-virtual-desktop.md).
>
>Recomendamos que utilize tags FQDN ou tags de serviço em vez de URLs para evitar problemas de serviço. Os URLs e tags listados apenas correspondem a sites e recursos virtuais do Windows Virtual Desktop. Não incluem URLs para outros serviços como o Azure Ative Directory.

A tabela que se segue lista URLs opcionais aos que as suas máquinas virtuais Azure podem ter acesso:

|Endereço|Porta TCP de saída|Objetivo|Etiqueta de serviço|
|---|---|---|---|
|*.microsoftonline.com|443|Autenticação para serviços online da Microsoft|Nenhum|
|*.events.data.microsoft.com|443|Serviço de Telemetria|Nenhum|
|www.msftconnecttest.com|443|Deteta se o SO está ligado à internet|Nenhum|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|Nenhum|
|login.windows.net|443|Iniciar sôms nos Serviços Online da Microsoft, Microsoft 365|Nenhum|
|*.sfx.ms|443|Atualizações para software de cliente OneDrive|Nenhum|
|*.digicert.com|443|Verificação de revogação do certificado|Nenhum|

>[!NOTE]
>O Windows Virtual Desktop não tem atualmente uma lista de intervalos de endereços IP que possa desbloquear para permitir o tráfego de rede. Só apoiamos o desbloqueio de URLs específicos neste momento.
>
>Para obter uma lista de URLs seguros relacionados com o Office, incluindo urls relacionados com o Azure Ative Directory, consulte [os intervalos de URLs e endereços IP do Office 365](/office365/enterprise/urls-and-ip-address-ranges).
>
>Deve utilizar o caractere wildcard (*) para URLs que envolvam tráfego de serviço. Se preferir não usar * para tráfego relacionado com agentes, eis como encontrar os URLs sem wildcards:
>
>1. Registe as suas máquinas virtuais na piscina de anfitriões virtual do Windows Desktop.
>2. Espectador **de Evento Aberto**, em seguida, vá ao Windows **logs**  >  **Application**  >  **WVD-Agent** e procure o ID do evento 3702.
>3. Whitelist os URLs que encontrar no âmbito do Evento ID 3702. Os URLs no âmbito do ID 3702 do evento são específicos da região. Terá de repetir o processo de desbloqueio com os URLs relevantes para cada região em que pretende implantar as suas máquinas virtuais.

## <a name="remote-desktop-clients"></a>Clientes de Ambiente de Trabalho Remoto

Quaisquer clientes de Ambiente de Trabalho remoto que utilize devem ter acesso aos seguintes URLs:

|Endereço|Porta TCP de saída|Objetivo|Clientes(s)|
|---|---|---|---|
|*.wvd.microsoft.com|443|Tráfego de serviço|Todos|
|*.servicebus.windows.net|443|Dados de resolução de problemas|Todos|
|go.microsoft.com|443|Microsoft FWLinks|Todos|
|aka.ms|443|Encurtador de URL da Microsoft|Todos|
|docs.microsoft.com|443|Documentação|Todos|
|privacy.microsoft.com|443|Declaração de privacidade|Todos|
|query.prod.cms.rt.microsoft.com|443|Atualizações de clientes|Ambiente de Trabalho do Windows|

>[!IMPORTANT]
>A abertura destes URLs é essencial para uma experiência confiável do cliente. O bloqueio do acesso a estes URLs não é suportado e irá afetar a funcionalidade do serviço. Estes URLs apenas correspondem aos sites e recursos do cliente, e não incluem URLs para outros serviços como o Azure Ative Directory.