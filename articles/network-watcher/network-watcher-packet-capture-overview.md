---
title: Introdução à captura de Pacotes no Vigilante da Rede Azure [ Microsoft Docs
description: Esta página fornece uma visão geral da capacidade de captura de pacotes do Observador de Rede
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 1f17463125cead64bd58a2d07e53eee4d3cfcd70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840813"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Introdução à captura de pacotes variáveis no Vigilante da Rede Azure

A captura de pacotes variáveis do Network Watcher permite-lhe criar sessões de captura de pacotes para rastrear o tráfego de e para uma máquina virtual. A captura de pacotes ajuda a diagnosticar anomalias de rede tanto de forma reativa como proactiva. Outros usos incluem a recolha de estatísticas de rede, a obtenção de informação sobre intrusões de rede, para depurar comunicações de servidores de clientes e muito mais.

A captura de pacotes é uma extensão virtual da máquina que é iniciada remotamente através do Observador de Rede. Esta capacidade alivia o fardo de executar uma captura manual de pacotes na máquina virtual desejada, o que poupa tempo valioso. A captura do pacote pode ser desencadeada através do portal PowerShell, CLI ou REST API. Um exemplo de como a captura de pacotes pode ser desencadeada é com alertas de Máquina Virtual. Os filtros são fornecidos para a sessão de captura para garantir que capta o tráfego que pretende monitorizar. Os filtros baseiam-se em informações de 5 tuple (protocolo, endereço IP local, endereço IP remoto, porta local e porta remota). Os dados capturados são armazenados no disco local ou numa bolha de armazenamento. Há um limite de 10 sessões de captura de pacotes por região por subscrição. Este limite aplica-se apenas às sessões e não se aplica aos ficheiros de captura de pacotes guardados, quer localmente no VM, quer numa conta de armazenamento.

> [!IMPORTANT]
> A captura do pacote `AzureNetworkWatcherExtension`requer uma extensão virtual da máquina . Para instalar a extensão de um Windows VM visite a extensão virtual do Agente observador de [rede Azure para windows](../virtual-machines/windows/extensions-nwa.md) e para o Linux VM visite a extensão virtual do Agente observador de rede [Azure para o Linux](../virtual-machines/linux/extensions-nwa.md).

Para reduzir a informação que captura apenas para as informações que deseja, as seguintes opções estão disponíveis para uma sessão de captura de pacotes:

**Configuração de captura**

|Propriedade|Descrição|
|---|---|
|**Bytes máximos por pacote (bytes)** | O número de bytes de cada pacote capturado, todos os bytes são capturados se deixados em branco. O número de bytes de cada pacote capturado, todos os bytes são capturados se deixados em branco. Se precisar apenas do cabeçalho IPv4 - indique 34 aqui |
|**Bytes máximos por sessão (bytes)** | Número total de bytes em que são capturados, uma vez que o valor é atingido termina a sessão.|
|**Limite de tempo (segundos)** | Estabelece uma restrição de tempo na sessão de captura do pacote. O valor padrão é de 18000 segundos ou 5 horas.|

**Filtragem (opcional)**

|Propriedade|Descrição|
|---|---|
|**Protocolo** | O protocolo para filtrar para a captura do pacote. Os valores disponíveis são TCP, UDP e Tudo.|
|**Endereço IP local** | Este valor filtra a captura do pacote para pacotes onde o endereço IP local corresponde a este valor de filtro.|
|**Porta local** | Este valor filtra a captura do pacote para pacotes onde a porta local corresponde a este valor de filtro.|
|**Endereço IP remoto** | Este valor filtra a captura do pacote em pacotes onde o IP remoto corresponde a este valor de filtro.|
|**Porta remota** | Este valor filtra a captura do pacote para pacotes onde a porta remota corresponde a este valor de filtro.|

### <a name="next-steps"></a>Passos seguintes

Saiba como pode gerir capturas de pacotes através do portal visitando a captura de [pacotes Manage no portal Azure](network-watcher-packet-capture-manage-portal.md) ou com powerShell visitando [Manage Packet Capture com PowerShell](network-watcher-packet-capture-manage-powershell.md).

Saiba como criar capturas proactivas de pacotes baseados em alertas de máquinas virtuais visitando [Criar um alerta desencadeado captura de pacotes](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













