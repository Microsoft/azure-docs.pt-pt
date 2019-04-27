---
title: Introdução à captura de pacotes no observador de rede do Azure | Documentos da Microsoft
description: Esta página fornece uma visão geral da capacidade de captura de pacotes do observador de rede
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 3a81afaa-ecd9-4004-b68e-69ab56913356
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 3ca9619dcfe9054ab69d248e20f6d8e59324d308
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60727034"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Introdução à captura de pacote variável no observador de rede do Azure

Captura de pacote variável do observador de rede permite-lhe criar sessões de captura de pacotes para controlar o tráfego de e para uma máquina virtual. Ajuda de captura de pacotes para diagnosticar anomalias rede de forma reativa e proactivity. Outras utilizações incluem a recolha de estatísticas de rede, obter informações sobre a invasões de rede, para depurar as comunicações cliente-servidor e muito mais.

Captura de pacotes é uma extensão de máquina virtual que é iniciada remotamente através do observador de rede. Esta capacidade alivia a carga da execução manual de uma captura de pacotes na máquina virtual pretendida, que economiza um valioso tempo. Captura de pacotes pode ser acionada através do portal, o PowerShell, a CLI ou a REST API. É um exemplo de como a captura de pacotes pode ser acionada com alertas de Máquina Virtual. Filtros são fornecidos para a sessão de captura garantir a que capturar o tráfego que pretende monitorizar. Filtros baseiam-se em 5 cadeias de identificação (protocolo, endereço IP local, endereço IP remoto, porta local e remota porta) informações. Os dados capturados são armazenados no disco local ou um blob de armazenamento. Existe um limite de 10 sessões de captura de pacotes por região por subscrição. Este limite aplica-se apenas para as sessões e não se aplica aos arquivos de captura de pacotes guardado localmente na VM ou numa conta de armazenamento.

> [!IMPORTANT]
> Captura de pacotes requer uma extensão de máquina virtual `AzureNetworkWatcherExtension`. Para instalar a extensão numa VM do Windows, visite [extensão de máquina virtual de agente do observador de rede do Azure para Windows](../virtual-machines/windows/extensions-nwa.md) e para visite de VM do Linux [extensão da máquina virtual de agente do observador de rede do Azure para Linux](../virtual-machines/linux/extensions-nwa.md).

Para reduzir as informações de que captura para apenas as informações que pretende, as seguintes opções estão disponíveis para uma sessão de captura de pacotes:

**Capturar configuração**

|Propriedade|Descrição|
|---|---|
|**Máximo de bytes por pacote (bytes)** | O número de bytes de cada pacote que são capturados, todos os bytes são capturados se deixado em branco. O número de bytes de cada pacote que são capturados, todos os bytes são capturados se deixado em branco. Se tiver apenas o cabeçalho IPv4 – indicar 34 aqui |
|**Máximo de bytes por sessão (bytes)** | Número total de bytes em que é capturado, assim que o valor for atingido a sessão é terminada.|
|**Tempo limite (segundos)** | Conjuntos de uma restrição de tempo no pacote capturar sessão. O valor predefinido é 18000 segundos ou 5 horas.|

**Filtragem (opcional)**

|Propriedade|Descrição|
|---|---|
|**Protocolo** | O protocolo para filtrar para a captura de pacotes. Os valores disponíveis são TCP, UDP e tudo.|
|**Endereço IP local** | Este valor filtra a captura de pacotes para os pacotes em que o endereço IP local corresponde a este valor de filtro.|
|**Porta local** | Este valor filtra a captura de pacotes para pacotes, onde a porta local corresponde a este valor de filtro.|
|**Endereço IP remoto** | Este valor filtra a captura de pacotes para pacotes, onde o IP remoto corresponde a este valor de filtro.|
|**Porta remota** | Este valor filtra a captura de pacotes para pacotes, onde a porta remota corresponde a este valor de filtro.|

### <a name="next-steps"></a>Passos Seguintes

Saiba como pode gerir capturas de pacotes através do portal, visitando [captura de pacote no portal do Azure](network-watcher-packet-capture-manage-portal.md) ou com o PowerShell, visite a página [gerir a captura de pacotes com o PowerShell](network-watcher-packet-capture-manage-powershell.md).

Saiba como criar capturas de pacotes proativa com base em alertas de máquina virtual, visite a página [criar uma captura de pacotes acionadas alerta](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













