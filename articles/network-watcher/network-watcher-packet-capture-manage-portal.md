---
title: Gerenciar capturas de pacote-portal do Azure
titleSuffix: Azure Network Watcher
description: Saiba como gerenciar o recurso de captura de pacote do observador de rede usando o portal do Azure.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: kumud
ms.openlocfilehash: 4950ef8b763967e4e852e319429cc263a4a85f6c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277857"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Gerenciar capturas de pacote com o observador de rede do Azure usando o portal

A captura de pacotes do observador de rede permite que você crie sessões de captura para controlar o tráfego de e para uma máquina virtual. Os filtros são fornecidos para a sessão de captura para garantir que você capture somente o tráfego desejado. A captura de pacotes ajuda a diagnosticar anomalias de rede, de forma reativa e proativa. Outros usos incluem a coleta de estatísticas de rede, a obtenção de informações sobre invasões de rede, a depuração da comunicação cliente-servidor e muito mais. Ser capaz de disparar remotamente capturas de pacotes, facilita a carga de executar uma captura de pacotes manualmente em uma máquina virtual desejada, o que economiza tempo valioso.

Neste artigo, você aprende a iniciar, parar, baixar e excluir uma captura de pacote. 

## <a name="before-you-begin"></a>Antes de começar

A captura de pacote requer a seguinte conectividade:
* Conectividade de saída para uma conta de armazenamento pela porta 443.
* Conectividade de entrada e saída para 169.254.169.254
* Conectividade de entrada e saída para 168.63.129.16

Se um grupo de segurança de rede estiver associado ao adaptador de rede ou à sub-rede em que a interface de rede está, verifique se existem regras que permitam as portas anteriores. Da mesma forma, adicionar rotas de tráfego definidas pelo usuário à sua rede pode impedir a conectividade com os IPs e as portas mencionados acima. Verifique se eles estão acessíveis. 

## <a name="start-a-packet-capture"></a>Iniciar uma captura de pacote

1. No navegador, navegue até a [portal do Azure](https://portal.azure.com) e selecione **todos os serviços**e, em seguida, selecione **observador de rede** na **seção rede**.
2. Selecione **captura de pacote** em **ferramentas de diagnóstico de rede**. Todas as capturas de pacote existentes são listadas, independentemente de seu status.
3. Selecione **Adicionar** para criar uma captura de pacote. Você pode selecionar valores para as seguintes propriedades:
   - **Assinatura**: a assinatura na qual a máquina virtual para a qual você deseja criar a captura de pacote está.
   - **Grupo de recursos**: o grupo de recursos da máquina virtual.
   - **Máquina virtual de destino**: a máquina virtual para a qual você deseja criar a captura de pacote.
   - **Nome da captura de pacote**: um nome para a captura de pacote.
   - **Conta de armazenamento ou arquivo**: selecione **conta de armazenamento**, **arquivo**ou ambos. Se você selecionar **arquivo**, a captura será gravada em um caminho dentro da máquina virtual.
   - **Caminho do arquivo local**: o caminho local na máquina virtual em que a captura de pacote será salva (válida somente quando o *arquivo* for selecionado). O caminho deve ser um caminho válido. Se você estiver usando uma máquina virtual do Linux, o caminho deverá começar com */var/Captures*.
   - **Contas de armazenamento**: selecione uma conta de armazenamento existente se você selecionou *conta de armazenamento*. Essa opção só estará disponível se você tiver selecionado **armazenamento**.
   
     > [!NOTE]
     > Atualmente, não há suporte para contas de armazenamento Premium no armazenamento de capturas de pacote.

   - **Máximo de bytes por pacote**: o número de bytes de cada pacote que são capturados. Se for deixado em branco, todos os bytes serão capturados.
   - **Máximo de bytes por sessão**: o número total de bytes capturados. Depois que o valor for atingido, a captura de pacote será interrompida.
   - **Limite de tempo (segundos)** : o limite de tempo antes que a captura de pacote seja interrompida. O padrão é 18.000 segundos.
   - Filtragem (opcional). Selecionar **+ Adicionar filtro**
     - **Protocolo**: o protocolo a ser filtrado para a captura de pacotes. Os valores disponíveis são TCP, UDP e any.
     - **Endereço IP local**: filtra a captura de pacotes para os pacotes em que o endereço IP local corresponde a esse valor.
     - **Porta local**: filtra a captura de pacotes para os pacotes em que a porta local corresponde a esse valor.
     - **Endereço IP remoto**: filtra a captura de pacotes para os pacotes em que o endereço IP remoto corresponde a esse valor.
     - **Porta remota**: filtra a captura de pacotes para os pacotes em que a porta remota corresponde a esse valor.
    
     > [!NOTE]
     > Os valores de porta e endereço IP podem ser um único valor, intervalo de valores ou um intervalo, como 80-1024, para a porta. Você pode definir quantos filtros forem necessários.

4. Selecione **OK**.

Depois que o limite de tempo definido na captura de pacote tiver expirado, a captura de pacote será interrompida e poderá ser revisada. Você também pode interromper manualmente uma sessão de captura de pacote.

> [!NOTE]
> O portal automaticamente:
>  * Cria um observador de rede na mesma região da região em que a máquina virtual selecionada existe, se a região ainda não tiver um observador de rede.
>  * Adiciona a extensão de máquina virtual *AzureNetworkWatcherExtension* [Linux](../virtual-machines/linux/extensions-nwa.md) ou [Windows](../virtual-machines/windows/extensions-nwa.md) à máquina virtual, se ainda não estiver instalada.

## <a name="delete-a-packet-capture"></a>Excluir uma captura de pacote

1. Na exibição de captura de pacote, selecione **...** no lado direito da captura de pacote, ou clique com o botão direito do mouse em uma captura de pacote existente e selecione **excluir**.
2. Você será solicitado a confirmar que deseja excluir a captura de pacote. Selecione **Sim**.

> [!NOTE]
> A exclusão de uma captura de pacote não exclui o arquivo de captura na conta de armazenamento ou na máquina virtual.

## <a name="stop-a-packet-capture"></a>Parar uma captura de pacote

Na exibição de captura de pacote, selecione **...** no lado direito da captura de pacote, ou clique com o botão direito do mouse em uma captura de pacote existente e selecione **parar**.

## <a name="download-a-packet-capture"></a>Baixar uma captura de pacote

Depois que a sessão de captura de pacote for concluída, o arquivo de captura será carregado no armazenamento de BLOBs ou em um arquivo local na máquina virtual. O local de armazenamento da captura de pacote é definido durante a criação da captura de pacote. Uma ferramenta conveniente para acessar arquivos de captura salvos em uma conta de armazenamento é Gerenciador de Armazenamento do Microsoft Azure, que você pode [baixar](https://storageexplorer.com/).

Se uma conta de armazenamento for especificada, os arquivos de captura de pacote serão salvos em uma conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

Se você selecionou **arquivo** quando criou a captura, poderá exibir ou baixar o arquivo do caminho configurado na máquina virtual.

## <a name="next-steps"></a>Passos seguintes

- Para saber como automatizar as capturas de pacote com alertas de máquina virtual, consulte [criar uma captura de pacote disparada por alerta](network-watcher-alert-triggered-packet-capture.md).
- Para determinar se o tráfego específico é permitido dentro ou fora de uma máquina virtual, consulte [diagnosticar um problema de filtro de tráfego de rede de máquina virtual](diagnose-vm-network-traffic-filtering-problem.md).
