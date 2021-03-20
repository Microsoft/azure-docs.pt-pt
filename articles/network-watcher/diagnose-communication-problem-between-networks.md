---
title: Tutorial - Diagnosticar problema de comunicação entre redes utilizando o portal Azure
titleSuffix: Azure Network Watcher
description: Neste tutorial, aprenda a diagnosticar um problema de comunicação entre uma rede virtual Azure ligada a uma rede virtual no local, ou outra rede virtual, através de um gateway de rede virtual Azure, utilizando a capacidade de diagnóstico VPN do Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
Customer intent: I need to determine why resources in a virtual network can't communicate with resources in a different network.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: 1a556ea66bb54f994349ce9ba4d12fb486827edf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98013301"
---
# <a name="tutorial-diagnose-a-communication-problem-between-networks-using-the-azure-portal"></a>Tutorial: Diagnosticar um problema de comunicação entre redes com o Portal do Azure

Um gateway de rede virtual liga uma rede virtual do Azure a outras redes virtuais ou a uma rede no local. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Diagnosticar um problema com um gateway de rede virtual com a funcionalidade de diagnóstico de VPN do Observador de Rede
> * Diagnosticar um problema com uma ligação de gateway
> * Resolver um problema com um gateway

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar o diagnóstico de VPN, tem de ter um gateway de VPN existente e em execução. Se não tiver um gateway de VPN existente para diagnosticar, pode implementar um com o [script do PowerShell](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Pode executar o script do PowerShell a partir de:
- **Uma instalação local da PowerShell**: O script requer o módulo Azure PowerShell. `Az` Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Instalar o Azure PowerShell](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- **O Azure Cloud Shell**: o [Azure Cloud Shell](https://shell.azure.com/powershell) tem a versão mais recente do PowerShell instalada e configurada e inicia a sua sessão no Azure.

O script demora aproximadamente uma hora para criar um gateway de VPN. Os passos restantes presumem que o gateway que está a diagnosticar é aquele implementado por este script. Se diagnosticar o seu próprio gateway existente, os resultados variam.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="enable-network-watcher"></a>Ativar o Observador de Rede

Se já tiver um observador de rede ativado na região E.U.A. Leste, avance para [Diagnosticar um gateway](#diagnose-a-gateway).

1. No portal, selecione **Todos os serviços**. Na caixa **Filtro**, introduza *Observador de Rede*. Quando **o Observador de Rede** aparecer nos resultados, selecione-o.
2. Selecione **Regiões**, para expandir e, em seguida, selecione **...** à direita de **E.U.A. Leste**, conforme apresentado na seguinte imagem:

    ![Ativar o Observador de Rede](./media/diagnose-communication-problem-between-networks/enable-network-watcher.png)

3. Selecione **Ativar Observador de Rede**.

## <a name="diagnose-a-gateway"></a>Diagnosticar um gateway

1. No lado esquerdo do portal, selecione **Todos os serviços**.
2. Comece a escrever *observador de rede* na caixa **Filtro**. Quando o **Observador de Rede** aparecer nos resultados de pesquisa, selecione-o.
3. Em **FERRAMENTAS DE DIAGNÓSTICO DE REDE**, selecione **Diagnóstico de VPN**.
4. Selecione **Conta de armazenamento** e, em seguida, selecione a conta de armazenamento para a qual pretende escrever informações de diagnóstico.
5. A partir da lista de **Contas de armazenamento**, selecione a conta de armazenamento que pretende utilizar. Se não tiver uma conta de armazenamento existente, selecione **+ Conta de armazenamento**, introduza ou selecione as informações necessárias e, em seguida, selecione **Criar** para criar uma. Se tiver criado um gateway de VPN com o script em [pré-requisitos](#prerequisites), é recomendado criar a conta de armazenamento no mesmo grupo de recursos, *TestRG1*, que o gateway.
6. A partir da lista de **Contentores**, selecione o contentor que pretende utilizar e, em seguida, selecione **Selecionar**. Se não tiver contentores, selecione **+ Contentor**, introduza um nome para o contentor e, em seguida, selecione **OK**.
7. Selecione um gateway e, em seguida, selecione **Iniciar resolução de problemas**. Conforme apresentado na seguinte imagem, o teste é executado num gateway chamado **Vnet1GW**:

    ![Diagnóstico de VPN](./media/diagnose-communication-problem-between-networks/vpn-diagnostics.png)

8. Enquanto o teste está a ser executado, **A executar** é apresentado na coluna **ESTADO DE RESOLUÇÃO DE PROBLEMAS** onde **Não iniciado** é apresentado, na imagem anterior. O teste poderá demorar vários minutos para ser executado.
9. Veja o estado do teste concluído. A seguinte imagem mostra os resultados do estado de um teste de diagnóstico concluído:

    ![A screenshot mostra os resultados de um teste de diagnóstico, pouco saudável neste exemplo, incluindo um resumo e detalhes.](./media/diagnose-communication-problem-between-networks/status.png)

    Pode ver que o **ESTADO DE RESOLUÇÃO DE PROBLEMAS** é **Danificado**, bem como um **Resumo** e **Detalhes** do problema no separador **Estado**.
10. Quando seleciona o separador **Ação**, o diagnóstico de VPN fornece informações adicionais. No exemplo, conforme apresentado na seguinte imagem, o diagnóstico de VPN informa-o de que deve verificar o estado de funcionamento de cada ligação:

    ![A screenshot mostra o separador Ação, que lhe dá informações adicionais.](./media/diagnose-communication-problem-between-networks/action.png)

## <a name="diagnose-a-gateway-connection"></a>Diagnosticar uma ligação de gateway

Um gateway está ligado a outras redes através de uma ligação de gateway. O gateway e as ligações de gateway têm de ter um bom funcionamento para haver uma comunicação com êxito entre uma rede virtual e uma rede ligada.

1. Conclua o passo 7 em [Diagnosticar um gateway](#diagnose-a-gateway) novamente, mas desta vez selecione uma ligação. No seguinte exemplo, é testada uma ligação chamada **VNet1toSite1**:

    ![A screenshot mostra como começar a resolução de problemas para uma ligação selecionada.](./media/diagnose-communication-problem-between-networks/connection.png)

    O teste é executado durante vários minutos.
2. Após o teste da ligação ser concluído, receberá resultados semelhantes aos resultados apresentados nas seguintes imagens nos separadores **Estado** e **Ação**:

    ![Estado da ligação](./media/diagnose-communication-problem-between-networks/connection-status.png)

    ![Ação de ligação](./media/diagnose-communication-problem-between-networks/connection-action.png)

    O diagnóstico de VPN informa-o sobre o que está errado no separador **Estado** e fornece várias sugestões sobre o que poderá estar a causar o problema no separador **Ação**.

    Se o gateway testado foi aquele implementado pelo [script](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) nos [Pré-requisitos](#prerequisites), o problema no separador **Estado** e os primeiros dois itens no separador **Ações** são exatamente o problema. O script configura um endereço IP de marcador de posição, 23.99.221.164, para o dispositivo de gateway de VPN no local.

    Para resolver o problema, tem de garantir que o gateway de VPN no local está [configurado corretamente](../vpn-gateway/vpn-gateway-about-vpn-devices.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e de alterar o endereço IP configurado pelo script do gateway de rede local para o endereço público real do gateway de VPN no local.

## <a name="clean-up-resources"></a>Limpar os recursos

Se tiver criado um gateway de VPN com o script nos [pré-requisitos](#prerequisites) somente para concluir este tutorial e já não precisar dele, elimine o grupo de recursos e todos os recursos nele contidos:

1. Introduza *TestRG1* na caixa **Procurar**, na parte superior do portal. Quando vir **TestRG1** nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Introduza *TestRG1* em **ESCREVER O NOME DO GRUPO DE RECURSOS:** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a diagnosticar um problema com o gateway de rede virtual. É recomendável registar a comunicação de rede de e para uma VM para que possa rever o registo para saber se existem anomalias. Para saber como, avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [Registar tráfego de rede de e para uma VM](network-watcher-nsg-flow-logging-portal.md)
