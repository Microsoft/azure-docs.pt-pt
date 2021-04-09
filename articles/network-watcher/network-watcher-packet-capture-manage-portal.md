---
title: Gerir capturas de pacotes - Portal Azure
titleSuffix: Azure Network Watcher
description: Saiba como gerir a funcionalidade de captura de pacotes do Network Watcher utilizando o portal Azure.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: d4112bd1d632ec21e2d8cb565027277eeb85452d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018264"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Gerir capturas de pacotes com o Azure Network Watcher usando o portal

A captura de pacotes do Network Watcher permite-lhe criar sessões de captura para rastrear o tráfego de e para uma máquina virtual. Os filtros são fornecidos para a sessão de captura para garantir que captura apenas o tráfego que deseja. A captura de pacotes ajuda a diagnosticar anomalias de rede, tanto reativas como proativamente. Outros usos incluem recolher estatísticas de rede, obter informações sobre intrusões de rede, depurar comunicações de servidores de clientes, e muito mais. Ser capaz de ativar remotamente capturas de pacotes, alivia o fardo de executar uma captura de pacote manualmente numa máquina virtual desejada, o que poupa tempo valioso.

Neste artigo, aprende-se a começar, parar, descarregar e apagar uma captura de pacotes. 

## <a name="before-you-begin"></a>Antes de começar

A captura de pacotes requer a seguinte conectividade TCP de saída:
- para a conta de armazenamento escolhida sobre a porta 443
- a 169.254.169.254 sobre o porto 80
- a 168.63.129.16 sobre o porto 8037

> [!NOTE]
> As portas mencionadas nos dois últimos casos acima são comuns em todas as funcionalidades do Network Watcher que envolvem a extensão do Observador de Rede e podem ocasionalmente mudar.


Se um grupo de segurança de rede estiver associado à interface de rede, ou sub-rede em que a interface de rede está, certifique-se de que existem regras que permitem as portas anteriores. Da mesma forma, a adição de rotas de tráfego definidas pelo utilizador à sua rede pode impedir a conectividade com os IPs e portas acima mencionados. Por favor, certifique-se de que estão alcançáveis. 

## <a name="start-a-packet-capture"></a>Inicie uma captura de pacotes

1. No seu navegador, navegue no [portal Azure](https://portal.azure.com) e selecione **Todos os serviços** e, em seguida, selecione **Network Watcher** na **secção Networking**.
2. Selecione **a captura de pacotes** em **ferramentas de diagnóstico de rede**. Quaisquer capturas de pacotes existentes estão listadas, independentemente do seu estado.
3. **Selecione Adicionar** para criar uma captura de pacote. Pode selecionar valores para as seguintes propriedades:
   - **Subscrição**: A subscrição para a qual a máquina virtual para a qual pretende criar a captura do pacote está dentro.
   - **Grupo de recursos**: O grupo de recursos da máquina virtual.
   - **Máquina virtual de destino**: A máquina virtual para a que pretende criar a captura de pacotes.
   - **Nome de captura de** pacote: Um nome para a captura do pacote.
   - **Conta de armazenamento ou ficheiro**: Selecione a conta de **armazenamento,** **ficheiro,** ou ambos. Se selecionar **Ficheiro,** a captura é escrita para um caminho dentro da máquina virtual.
   - **Caminho do ficheiro local**: O caminho local na máquina virtual onde a captura do pacote será guardada (válida apenas quando *o Ficheiro* for selecionado). O caminho deve ser um caminho válido. Se estiver a utilizar uma máquina virtual Linux, o caminho deve começar com */var/captures*.
   - **Contas de armazenamento**: Selecione uma conta de armazenamento existente, se selecionar *a conta de Armazenamento*. Esta opção só está disponível se selecionar **o Storage.**
   
     > [!NOTE]
     > As contas de armazenamento premium não são atualmente suportadas para armazenar capturas de pacotes.

   - **Bytes máximos por pacote**: O número de bytes de cada pacote capturado. Se ficarem em branco, todos os bytes são capturados.
   - **Bytes máximos por sessão**: O número total de bytes capturados. Uma vez atingido o valor, a captura do pacote para.
   - **Limite de tempo (segundos)**: O prazo antes da captura do pacote é interrompido. O padrão é de 18.000 segundos.
   - Filtragem (Opcional). Selecione **+ Adicionar filtro**
     - **Protocolo**: O protocolo para filtrar para a captura do pacote. Os valores disponíveis são TCP, UDP e Any.
     - **Endereço IP local**: Filtra a captura de pacotes para pacotes onde o endereço IP local corresponde a este valor.
     - **Porta local**: Filtra a captura de pacotes para pacotes onde a porta local corresponde a este valor.
     - **Endereço IP remoto**: Filtra a captura do pacote para pacotes onde o endereço IP remoto corresponde a este valor.
     - **Porta remota**: Filtra a captura do pacote para pacotes onde a porta remota corresponde a este valor.
    
     > [!NOTE]
     > Os valores de endereços porta e IP podem ser um único valor, gama de valores, ou um intervalo, como 80-1024, para a porta. Pode definir quantos filtros precisar.

4. Selecione **OK**.

Após o prazo fixado na captura do pacote ter expirado, a captura do pacote é interrompida e pode ser revista. Também pode parar manualmente uma sessão de captura de pacotes.

> [!NOTE]
> O portal automaticamente:
>  * Cria um observador de rede na mesma região da região onde existe a máquina virtual em que selecionou, se a região ainda não tiver um observador de rede.
>  * Adiciona a extensão virtual do *AzureNetworkWatcherExtension* [Linux](../virtual-machines/extensions/network-watcher-linux.md) ou da máquina virtual [do Windows](../virtual-machines/extensions/network-watcher-windows.md) à máquina virtual, se ainda não estiver instalada.

## <a name="delete-a-packet-capture"></a>Excluir uma captura de pacotes

1. Na vista de captura do pacote, selecione... no lado direito da captura do pacote, ou clique com o botão direito numa captura de pacote existente e selecione **Delete**. 
2. É-lhe pedido que confirme que pretende eliminar a captura do pacote. Selecione **Yes** (Sim).

> [!NOTE]
> A eliminação de uma captura de pacote não apaga o ficheiro de captura na conta de armazenamento ou na máquina virtual.

## <a name="stop-a-packet-capture"></a>Pare uma captura de pacotes

Na vista de captura do pacote, selecione... no lado direito da captura do pacote, ou clique com o botão direito numa captura de pacote existente, e selecione **Stop**. 

## <a name="download-a-packet-capture"></a>Faça o download de uma captura de pacotes

Uma vez concluída a sessão de captura do seu pacote, o ficheiro de captura é carregado para o armazenamento de bolhas ou para um ficheiro local na máquina virtual. A localização de armazenamento da captura do pacote é definida durante a criação da captura do pacote. Uma ferramenta conveniente para aceder a ficheiros de captura guardados numa conta de armazenamento é o Microsoft Azure Storage Explorer, que pode [descarregar.](https://storageexplorer.com/)

Se uma conta de armazenamento for especificada, os ficheiros de captura de pacotes são guardados numa conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

Se selecionou **O Ficheiro** quando criou a captura, pode visualizar ou descarregar o ficheiro a partir do caminho configurado na máquina virtual.

## <a name="next-steps"></a>Passos seguintes

- Para aprender a automatizar capturas de pacotes com alertas de máquinas virtuais, consulte [Criar uma captura de pacote acionada em alerta](network-watcher-alert-triggered-packet-capture.md).
- Para determinar se o tráfego específico é permitido dentro ou fora de uma máquina virtual, consulte diagnosticar um problema de filtro de [tráfego de rede de máquinas virtuais](diagnose-vm-network-traffic-filtering-problem.md).