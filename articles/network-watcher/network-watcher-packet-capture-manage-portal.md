---
title: Gerir capturas de pacotes - Portal Azure
titleSuffix: Azure Network Watcher
description: Saiba como gerir a funcionalidade de captura de pacotes do Network Watcher utilizando o portal Azure.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: damendo
ms.openlocfilehash: d7a61438187534a05a7d3f0307a1a4ded89fc147
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204088"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Gerir capturas de pacotes com o Azure Network Watcher usando o portal

A captura de pacotes do Network Watcher permite-lhe criar sessões de captura para rastrear o tráfego de e para uma máquina virtual. Os filtros são fornecidos para a sessão de captura para garantir que captura apenas o tráfego que deseja. A captura de pacotes ajuda a diagnosticar anomalias de rede, tanto reactivamente como proactivamente. Outros usos incluem recolher estatísticas de rede, obter informações sobre intrusões de rede, depurar a comunicação cliente-servidor, e muito mais. Ser capaz de disparar remotamente capturas de pacotes, alivia o fardo de executar uma captura de pacote manualmente numa máquina virtual desejada, o que poupa tempo valioso.

Neste artigo, aprende-se a começar, parar, descarregar e eliminar uma captura de pacotes. 

## <a name="before-you-begin"></a>Antes de começar

A captura do pacote requer a seguinte conectividade TCP de saída:
- para a conta de armazenamento escolhida sobre a porta 443
- para 169.254.169.254 sobre o porto 80
- para 168.63.129.16 sobre o porto 8037

> [!NOTE]
> As portas mencionadas nos dois últimos casos acima são comuns em todas as funcionalidades do Network Watcher que envolvem a extensão do Observador de Rede e podem ocasionalmente mudar.


Se um grupo de segurança de rede estiver associado à interface da rede, ou subnet a que a interface de rede está, certifique-se de que existem regras que permitem as portas anteriores. Da mesma forma, adicionar rotas de tráfego definidas pelo utilizador à sua rede pode impedir a conectividade com os IPs e portas acima mencionados. Por favor, certifique-se de que estão acessíveis. 

## <a name="start-a-packet-capture"></a>Iniciar uma captura de pacote

1. No seu navegador, navegue para o [portal Azure](https://portal.azure.com) e selecione **Todos os serviços**, e depois selecione **O Observador** de Rede na **secção Networking**.
2. Selecione **a captura de pacotes** sob **as ferramentas de diagnóstico da rede**. Quaisquer capturas de pacotes existentes estão listadas, independentemente do seu estatuto.
3. Selecione **Adicionar** para criar uma captura de pacote. Pode selecionar valores para as seguintes propriedades:
   - **Subscrição**: A subscrição que a máquina virtual que pretende criar está dentro da captura do pacote.
   - **Grupo de recursos**: O grupo de recursos da máquina virtual.
   - **Máquina virtual alvo**: A máquina virtual para a que pretende criar a captura do pacote.
   - **Nome de captura do pacote**: Um nome para a captura do pacote.
   - **Conta de armazenamento ou ficheiro**: Selecione conta de **armazenamento,** **Ficheiro,** ou ambos. Se selecionar **File,** a captura está escrita para um caminho dentro da máquina virtual.
   - **Caminho de arquivo local**: O caminho local na máquina virtual onde a captura do pacote será guardado (válido apenas quando o *Ficheiro* for selecionado). O caminho deve ser um caminho válido. Se estiver a utilizar uma máquina virtual Linux, o caminho deve começar com */var/capturas*.
   - **Contas de armazenamento**: Selecione uma conta de armazenamento existente, se selecionou *a conta de armazenamento*. Esta opção só está disponível se selecionar **o Armazenamento**.
   
     > [!NOTE]
     > As contas de armazenamento premium não são atualmente suportadas para armazenar capturas de pacotes.

   - **Bytes máximos por pacote**: O número de bytes de cada pacote capturados. Se ficarem em branco, todos os bytes são capturados.
   - **Bytes máximos por sessão**: O número total de bytes capturados. Uma vez atingido o valor, a captura do pacote para.
   - **Limite de tempo (segundos)**: O limite de tempo antes da captura do pacote é interrompido. O padrão é de 18.000 segundos.
   - Filtragem (Opcional). Selecione **+ Adicionar filtro**
     - **Protocolo**: O protocolo para filtrar para a captura do pacote. Os valores disponíveis são TCP, UDP e Any.
     - **Endereço IP local**: Filtra a captura do pacote para pacotes onde o endereço IP local corresponde a este valor.
     - **Porto local**: Filtra a captura do pacote para pacotes onde a porta local corresponde a este valor.
     - **Endereço IP remoto**: Filtra a captura do pacote para pacotes onde o endereço IP remoto corresponde a este valor.
     - **Porta remota**: Filtra a captura do pacote para pacotes onde a porta remota corresponde a este valor.
    
     > [!NOTE]
     > Os valores de endereço sal e IP podem ser um valor único, uma gama de valores, ou uma gama, como 80-1024, para o porto. Pode definir quantos filtros precisar.

4. Selecione **OK**.

Após o prazo estabelecido na captura do pacote ter expirado, a captura do pacote é interrompida e pode ser revista. Também pode parar manualmente uma sessão de captura de pacotes.

> [!NOTE]
> O portal automaticamente:
>  * Cria um observador de rede na mesma região que a região onde a máquina virtual selecionada existe, se a região já não tiver um observador de rede.
>  * Adiciona o [Linux](../virtual-machines/linux/extensions-nwa.md) de *Extensão AzureNetworkWatcher* ou a extensão virtual da máquina [do Windows](../virtual-machines/windows/extensions-nwa.md) à máquina virtual, caso ainda não esteja instalada.

## <a name="delete-a-packet-capture"></a>Eliminar uma captura de pacote

1. Na vista de captura do pacote, selecione... no lado direito da captura do pacote, ou clique à direita numa captura de pacote existente, e selecione **Delete**. **...**
2. É-lhe pedido que confirme que pretende eliminar a captura do pacote. Selecione **Sim**.

> [!NOTE]
> A eliminação de uma captura de pacotenão elimina o ficheiro de captura na conta de armazenamento ou na máquina virtual.

## <a name="stop-a-packet-capture"></a>Parar uma captura de pacote

Na vista de captura do pacote, selecione... no lado direito da captura do pacote, ou clique à direita numa captura de pacote existente, e selecione **Stop**. **...**

## <a name="download-a-packet-capture"></a>Descarregue uma captura de pacotes

Uma vez concluída a sua sessão de captura de pacotes, o ficheiro de captura é enviado para armazenamento de bolhas ou para um ficheiro local na máquina virtual. A localização de armazenamento da captura do pacote é definida durante a criação da captura do pacote. Uma ferramenta conveniente para aceder a ficheiros de captura guardados numa conta de armazenamento é o Microsoft Azure Storage Explorer, que pode [descarregar.](https://storageexplorer.com/)

Se for especificada uma conta de armazenamento, os ficheiros de captura de pacotes são guardados numa conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

Se selecionou o **Ficheiro** quando criou a captura, pode visualizar ou transferir o ficheiro a partir do caminho configurado na máquina virtual.

## <a name="next-steps"></a>Passos seguintes

- Para aprender a automatizar capturas de pacotes com alertas de máquinas virtuais, consulte [Criar um alerta desencadeado pela captura do pacote](network-watcher-alert-triggered-packet-capture.md).
- Para determinar se o tráfego específico é permitido entrar ou sair de uma máquina virtual, consulte Diagnosticar um problema de filtro de tráfego de [rede de máquinas virtuais](diagnose-vm-network-traffic-filtering-problem.md).
