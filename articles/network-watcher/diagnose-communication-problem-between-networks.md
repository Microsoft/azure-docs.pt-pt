---
title: Diagnosticar um problema de comunicação entre redes – tutorial – Portal do Azure | Microsoft Docs
description: Saiba como diagnosticar um problema de comunicação entre uma rede virtual do Azure ligada a outras redes virtuais ou a uma rede no local, através de um gateway de rede do Azure, ao utilizar uma funcionalidade de diagnóstico de VPN do Observador de Rede.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
Customer intent: I need to determine why resources in a virtual network can't communicate with resources in a different network.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: a35e65237a3f4278e9154daddff328887d9f36ef
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997383"
---
# <a name="tutorial-diagnose-a-communication-problem-between-networks-using-the-azure-portal"></a>Tutorial: Diagnosticar um problema de comunicação entre redes com o portal do Azure

Um gateway de rede virtual liga uma rede virtual do Azure a outras redes virtuais ou a uma rede no local. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Diagnosticar um problema com um gateway de rede virtual com a funcionalidade de diagnóstico de VPN do Observador de Rede
> * Diagnosticar um problema com uma ligação de gateway
> * Resolver um problema com um gateway

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar o diagnóstico de VPN, tem de ter um gateway de VPN existente e em execução. Se não tiver um gateway de VPN existente para diagnosticar, pode implementar um com o [script do PowerShell](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Pode executar o script do PowerShell a partir de:
- **Uma instalação local do PowerShell**: O script requer o AzureRM PowerShell 5.7.0 de versão do módulo ou posterior. Execute `Get-Module -ListAvailable AzureRM` para localizar a versão instalada. Se precisar de atualizar, veja [Instalar o Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Login-AzureRmAccount` para criar uma ligação com o Azure.
- **O Azure Cloud Shell**: O [Azure Cloud Shell](https://shell.azure.com/powershell) tem a versão mais recente do PowerShell instalado e configurado e iniciar a sua sessão no Azure.

O script demora aproximadamente uma hora para criar um gateway de VPN. Os passos restantes presumem que o gateway que está a diagnosticar é aquele implementado por este script. Se diagnosticar o seu próprio gateway existente, os resultados variam.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="enable-network-watcher"></a>Ativar o Observador de Rede

Se já tiver um observador de rede ativado na região E.U.A. Leste, avance para [Diagnosticar um gateway](#diagnose-a-gateway).

1. No portal, selecione **Todos os serviços**. Na caixa **Filtro**, introduza *Observador de Rede*. Quando a opção **Observador de Rede** aparecer nos resultados, selecione-a.
2. Selecione **Regiões**, para expandir e, em seguida, selecione **...** à direita de **E.U.A. Leste**, conforme apresentado na seguinte imagem:

    ![Ativar o Observador de Rede](./media/diagnose-communication-problem-between-networks/enable-network-watcher.png)

3. Selecione **Ativar o Observador de Rede**.

## <a name="diagnose-a-gateway"></a>Diagnosticar um gateway

1. No lado esquerdo do portal, selecione **Todos os serviços**.
2. Comece a escrever *observador de rede* na caixa **Filtro**. Quando a opção **Observador de Rede** aparecer nos resultados de pesquisa, selecione-a.
3. Em **FERRAMENTAS DE DIAGNÓSTICO DE REDE**, selecione **Diagnóstico de VPN**.
4. Selecione **Conta de armazenamento** e, em seguida, selecione a conta de armazenamento para a qual pretende escrever informações de diagnóstico.
5. A partir da lista de **Contas de armazenamento**, selecione a conta de armazenamento que pretende utilizar. Se não tiver uma conta de armazenamento existente, selecione **+ Conta de armazenamento**, introduza ou selecione as informações necessárias e, em seguida, selecione **Criar** para criar uma. Se tiver criado um gateway de VPN com o script em [pré-requisitos](#prerequisites), é recomendado criar a conta de armazenamento no mesmo grupo de recursos, *TestRG1*, que o gateway.
6. A partir da lista de **Contentores**, selecione o contentor que pretende utilizar e, em seguida, selecione **Selecionar**. Se não tiver contentores, selecione **+ Contentor**, introduza um nome para o contentor e, em seguida, selecione **OK**.
7. Selecione um gateway e, em seguida, selecione **Iniciar resolução de problemas**. Conforme apresentado na seguinte imagem, o teste é executado num gateway chamado **Vnet1GW**:

    ![Diagnóstico de VPN](./media/diagnose-communication-problem-between-networks/vpn-diagnostics.png)

8. Enquanto o teste está a ser executado, **A executar** é apresentado na coluna **ESTADO DE RESOLUÇÃO DE PROBLEMAS** onde **Não iniciado** é apresentado, na imagem anterior. O teste poderá demorar vários minutos para ser executado.
9. Veja o estado do teste concluído. A seguinte imagem mostra os resultados do estado de um teste de diagnóstico concluído:

    ![Estado](./media/diagnose-communication-problem-between-networks/status.png)

    Pode ver que o **ESTADO DE RESOLUÇÃO DE PROBLEMAS** é **Danificado**, bem como um **Resumo** e **Detalhes** do problema no separador **Estado**.
10. Quando seleciona o separador **Ação**, o diagnóstico de VPN fornece informações adicionais. No exemplo, conforme apresentado na seguinte imagem, o diagnóstico de VPN informa-o de que deve verificar o estado de funcionamento de cada ligação:

    ![Ação](./media/diagnose-communication-problem-between-networks/action.png)

## <a name="diagnose-a-gateway-connection"></a>Diagnosticar uma ligação de gateway

Um gateway está ligado a outras redes através de uma ligação de gateway. O gateway e as ligações de gateway têm de ter um bom funcionamento para haver uma comunicação com êxito entre uma rede virtual e uma rede ligada.

1. Conclua o passo 7 em [Diagnosticar um gateway](#diagnose-a-gateway) novamente, mas desta vez selecione uma ligação. No seguinte exemplo, é testada uma ligação chamada **VNet1toSite1**:

    ![Ligação](./media/diagnose-communication-problem-between-networks/connection.png)

    O teste é executado durante vários minutos.
2. Após o teste da ligação ser concluído, receberá resultados semelhantes aos resultados apresentados nas seguintes imagens nos separadores **Estado** e **Ação**:

    ![Estado da ligação](./media/diagnose-communication-problem-between-networks/connection-status.png)

    ![Ação de ligação](./media/diagnose-communication-problem-between-networks/connection-action.png)

    O diagnóstico de VPN informa-o sobre o que está errado no separador **Estado** e fornece várias sugestões sobre o que poderá estar a causar o problema no separador **Ação**.

    Se o gateway testado foi aquele implementado pelo [script](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) nos [Pré-requisitos](#prerequisites), o problema no separador **Estado** e os primeiros dois itens no separador **Ações** são exatamente o problema. O script configura um endereço IP de marcador de posição, 23.99.221.164, para o dispositivo de gateway de VPN no local.

    Para resolver o problema, tem de garantir que o gateway de VPN no local está [configurado corretamente](../vpn-gateway/vpn-gateway-about-vpn-devices.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e de alterar o endereço IP configurado pelo script do gateway de rede local para o endereço público real do gateway de VPN no local.

## <a name="clean-up-resources"></a>Limpar recursos

Se tiver criado um gateway de VPN com o script nos [pré-requisitos](#prerequisites) somente para concluir este tutorial e já não precisar dele, elimine o grupo de recursos e todos os recursos nele contidos:

1. Introduza *TestRG1* na caixa **Procurar**, na parte superior do portal. Quando vir **TestRG1** nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Introduza *TestRG1* em **ESCREVER O NOME DO GRUPO DE RECURSOS:** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a diagnosticar um problema com o gateway de rede virtual. É recomendável registar a comunicação de rede de e para uma VM para que possa rever o registo para saber se existem anomalias. Para saber como, avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [Registar tráfego de rede de e para uma VM](network-watcher-nsg-flow-logging-portal.md)