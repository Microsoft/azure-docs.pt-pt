---
title: Introdução à captura de Pacotes no Azure Network Watcher Microsoft Docs
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76840813"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Introdução à captura de pacotes variáveis no Observador da Rede Azure

A captura de pacotes variáveis do Network Watcher permite-lhe criar sessões de captura de pacotes para rastrear o tráfego de e para uma máquina virtual. A captura de pacotes ajuda a diagnosticar anomalias de rede reativas e proativamente. Outros usos incluem recolher estatísticas de rede, obter informações sobre intrusões de rede, depurar comunicações de servidores de clientes e muito mais.

A captura de pacotes é uma extensão de máquina virtual que é iniciada remotamente através do Network Watcher. Esta capacidade alivia o fardo de executar uma captura de pacote manualmente na máquina virtual desejada, o que poupa tempo valioso. A captura de pacotes pode ser desencadeada através do portal, PowerShell, CLI ou REST API. Um exemplo de como a captura de pacotes pode ser desencadeada é com alertas de Máquina Virtual. Os filtros são fornecidos para a sessão de captura para garantir que captura o tráfego que pretende monitorizar. Os filtros baseiam-se em informações de 5 tuple (protocolo, endereço IP local, endereço IP remoto, porta local e porta remota). Os dados capturados são armazenados no disco local ou numa bolha de armazenamento. Existe um limite de 10 sessões de captura de pacotes por região por subscrição. Este limite aplica-se apenas às sessões e não se aplica aos ficheiros de captura de pacotes guardados, quer localmente, no VM, quer numa conta de armazenamento.

> [!IMPORTANT]
> A captura de pacotes requer uma extensão virtual da `AzureNetworkWatcherExtension` máquina. Para instalar a extensão num Windows VM visite [a extensão da máquina virtual do Observador de Redes Azure para windows](../virtual-machines/windows/extensions-nwa.md) e para Linux VM visite a [extensão virtual do Agente de Observadores de Rede Azure para o Linux](../virtual-machines/linux/extensions-nwa.md).

Para reduzir a informação que captura apenas para as informações que deseja, as seguintes opções estão disponíveis para uma sessão de captura de pacotes:

**Configuração de captura**

|Propriedade|Descrição|
|---|---|
|**Bytes máximos por pacote (bytes)** | O número de bytes de cada pacote que são capturados, todos os bytes são capturados se deixados em branco. O número de bytes de cada pacote que são capturados, todos os bytes são capturados se deixados em branco. Se precisar apenas do cabeçalho IPv4 – indique 34 aqui |
|**Bytes máximos por sessão (bytes)** | O número total de bytes que são capturados, uma vez que o valor é atingido termina a sessão.|
|**Limite de tempo (segundos)** | Define uma restrição de tempo na sessão de captura de pacotes. O valor predefinido é de 18000 segundos ou 5 horas.|

**Filtragem (opcional)**

|Propriedade|Descrição|
|---|---|
|**Protocolo** | O protocolo para filtrar para a captura do pacote. Os valores disponíveis são TCP, UDP e All.|
|**Endereço IP local** | Este valor filtra a captura do pacote para pacotes onde o endereço IP local corresponde a este valor do filtro.|
|**Porta local** | Este valor filtra a captura do pacote para pacotes onde a porta local corresponde a este valor de filtro.|
|**Endereço IP remoto** | Este valor filtra a captura do pacote para pacotes onde o IP remoto corresponde a este valor do filtro.|
|**Porta remota** | Este valor filtra a captura do pacote para pacotes onde a porta remota corresponde a este valor do filtro.|

### <a name="next-steps"></a>Passos seguintes

Saiba como pode gerir as capturas de pacotes através do portal visitando a captura de [pacotes de gestão no portal Azure](network-watcher-packet-capture-manage-portal.md) ou com o PowerShell visitando [Gerir a Captura de Pacotes com PowerShell.](network-watcher-packet-capture-manage-powershell.md)

Saiba como criar capturas de pacotes proactivos com base em alertas de máquinas virtuais visitando [Criar uma captura de pacotes desencadeados por alerta](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













