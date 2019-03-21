---
title: Gerir capturas de pacotes com o observador de rede do Azure - portal do Azure | Documentos da Microsoft
description: Saiba como gerir a funcionalidade de captura de pacotes do observador de rede com o portal do Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: jdial
ms.openlocfilehash: 22bdd50f129a48ade97db323f904f7e652a00d39
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57889986"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Gerir capturas de pacotes com o observador de rede do Azure com o portal

Captura de pacotes do observador de rede permite-lhe criar sessões de captura para controlar o tráfego de e para uma máquina virtual. Filtros são fornecidos para a sessão de captura garantir que capturar apenas o tráfego que pretende. Captura de pacotes ajuda a diagnosticar anomalias de rede, de forma reativa, proativa e efetiva. Outras utilizações incluem a recolha de estatísticas de rede, obter informações sobre a invasões de rede, a comunicação de cliente-servidor de depuração e muito mais. A capacidade de disparar remotamente pacotes captura, alivia a carga da execução manual de uma captura de pacotes numa máquina virtual pretendida, que economiza um valioso tempo.

Neste artigo, vai aprender a iniciar, parar, transferir e eliminar uma captura de pacotes. 

## <a name="before-you-begin"></a>Antes de começar

Captura de pacotes requer a conectividade seguinte:
* Conectividade de saída para uma conta de armazenamento através da porta 443.
* Conectividade de entrada e saída para 169.254.169.254
* Conectividade de entrada e saída para 168.63.129.16

Se um grupo de segurança de rede está associado à interface de rede ou sub-rede que está a interface de rede, certifique-se de que existem regras que permitem que as portas anteriores. 

## <a name="start-a-packet-capture"></a>Iniciar uma captura de pacotes

1. No seu browser, navegue para o [portal do Azure](https://portal.azure.com) e selecione **todos os serviços**e, em seguida, selecione **observador de rede** no **redes secção**.
2. Selecione **captura de pacotes** sob **ferramentas de diagnóstico de rede**. Quaisquer capturas de pacotes existentes estão listadas, independentemente do respetivo estado.
3. Selecione **adicionar** para criar uma captura de pacotes. Pode selecionar valores para as seguintes propriedades:
   - **Subscrição**: A subscrição que a máquina virtual que pretende criar o pacote capturar para está.
   - **Grupo de recursos**: O grupo de recursos da máquina virtual.
   - **Máquina virtual de destino**: A máquina virtual que pretende criar a captura de pacotes para.
   - **O nome de captura de pacotes**: Um nome para a captura de pacotes.
   - **Conta de armazenamento ou ficheiro**: Selecione **conta de armazenamento**, **ficheiro**, ou ambos. Se selecionou **ficheiro**, a captura é escrita para um caminho da máquina virtual.
   - **Caminho do ficheiro local**: O caminho local na máquina virtual em que a captura de pacotes será guardada (válido apenas quando *ficheiro* está selecionado). O caminho tem de ser um caminho válido. Se estiver a utilizar uma máquina virtual do Linux, o caminho tem de começar com */var/captura*.
   - **Contas de armazenamento**: Selecione uma conta de armazenamento existente, se tiver selecionado *conta de armazenamento*. Esta opção só está disponível se tiver selecionado **armazenamento**.
   
     > [!NOTE]
     > Contas de armazenamento Premium não são atualmente suportadas para armazenar o pacote de captura.

   - **Máximo de bytes por pacote**: O número de bytes a partir de cada pacote que são capturados. Se deixado em branco, todos os bytes são capturadas.
   - **Máximo de bytes por sessão**: O número total de bytes que são capturados. Assim que o valor for atingido o pára de captura de pacotes.
   - **Tempo limite (segundos)**: O limite de tempo antes da captura de pacotes está parada. A predefinição é 18,000 segundos.
   - Filtragem (opcional). Selecione **+ Adicionar filtro**
     - **Protocolo**: O protocolo para filtrar para a captura de pacotes. Os valores disponíveis são TCP, UDP e qualquer um.
     - **Endereço IP local**: Filtra a captura de pacotes para os pacotes em que o endereço IP local corresponde a este valor.
     - **Porta local**: Filtra a captura de pacotes para os pacotes em que a porta local corresponde a este valor.
     - **Endereço IP remoto**: Filtra a captura de pacotes para os pacotes em que o endereço IP remoto corresponde a este valor.
     - **Porta remota**: Filtra a captura de pacotes para os pacotes em que a porta remota corresponde a este valor.
    
     > [!NOTE]
     > Valores de endereços IP e porta podem ser um valor único, intervalo de valores ou um intervalo, como 80-1024, para a porta. Pode definir os filtros, conforme necessário.

4. Selecione **OK**.

Depois do limite de tempo definido na captura de pacotes tiver expirado, a captura de pacotes é parada e pode ser revista. Pode parar manualmente uma sessão de captura de pacotes.

> [!NOTE]
> O portal automaticamente:
>  * Cria um observador de rede na mesma região que a região que a máquina virtual que selecionou existe, se a região ainda não tiver um observador de rede.
>  * Adiciona os *AzureNetworkWatcherExtension* [Linux](../virtual-machines/linux/extensions-nwa.md) ou [Windows](../virtual-machines/windows/extensions-nwa.md) extensão da máquina virtual à máquina virtual, se ainda não estiver instalado.

## <a name="delete-a-packet-capture"></a>eliminar uma captura de pacotes

1. Na vista de captura de pacote, selecione **...**  à direita do pacote capturar, ou uma captura de pacotes existente com o botão direito e selecione **eliminar**.
2. É-lhe perguntado para confirmar que pretende eliminar a captura de pacotes. Selecione **Sim**.

> [!NOTE]
> Eliminar uma captura de pacotes não elimina o ficheiro de captura na conta de armazenamento ou na máquina virtual.

## <a name="stop-a-packet-capture"></a>Parar uma captura de pacotes

Na vista de captura de pacote, selecione **...**  à direita do pacote capturar, ou uma captura de pacotes existente com o botão direito e selecione **parar**.

## <a name="download-a-packet-capture"></a>Transferir uma captura de pacotes

Depois de concluída a sua sessão de captura de pacotes, o ficheiro de captura é carregado para o armazenamento de BLOBs ou para um ficheiro local na máquina virtual. A localização de armazenamento da captura de pacotes é definida durante a criação de captura de pacotes. Uma ferramenta conveniente para aceder aos ficheiros de captura guardados para uma conta de armazenamento é o Explorador de armazenamento do Microsoft Azure, que pode [transferir](https://storageexplorer.com/).

Se for especificada uma conta de armazenamento, os arquivos de captura de pacotes são guardados para uma conta de armazenamento na seguinte localização:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

Se tiver selecionado **ficheiro** quando criou a captura, pode ver ou transferir o ficheiro a partir do caminho que configurou na máquina virtual.

## <a name="next-steps"></a>Passos Seguintes

- Para saber como automatizar as capturas de pacotes com alertas de máquina virtual, veja [criar uma captura de pacotes acionadas alerta](network-watcher-alert-triggered-packet-capture.md).
- Para determinar se o tráfego específico é permitido dentro ou fora de uma máquina virtual, veja [diagnosticar um problema de filtragem de tráfego de rede de máquina virtual](diagnose-vm-network-traffic-filtering-problem.md).
