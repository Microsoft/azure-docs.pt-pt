---
title: Conectar o Azure ExpressRoute com a infraestrutura de nuvem do Oracle | Microsoft Docs
description: Conectar o Azure ExpressRoute com o FastConnect de infraestrutura de nuvem da Oracle (OCI) para habilitar soluções de aplicativos Oracle entre nuvem
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2019
ms.author: rogirdh
ms.openlocfilehash: 63543c0ac34536b736bd4b8cdbd47fdd98e9f9be
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802216"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Configurar uma interconexão direta entre o Azure e a infraestrutura de nuvem do Oracle  

Para criar uma [experiência integrada de várias nuvens](oracle-oci-overview.md) (versão prévia), a Microsoft e a Oracle oferecem interconexão direta entre o Azure e o Oracle Cloud Infrastructure (OCI) por meio do [ExpressRoute](../../../expressroute/expressroute-introduction.md) e do [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm). Por meio da interconexão do ExpressRoute e do FastConnect, os clientes podem experimentar baixa latência, alta taxa de transferência, conectividade direta privada entre as duas nuvens.

> [!IMPORTANT]
> A conexão entre Microsoft Azure e OCI está no estágio de visualização. Para estabelecer conectividade de baixa latência entre o Azure e o OCI, sua assinatura do Azure deve primeiro ser habilitada para esse recurso. Você deve se registrar na visualização concluindo este pequeno [formulário de pesquisa](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu). Receberá um e-mail assim que a sua subscrição tiver sido inscrita. Você não poderá usar a funcionalidade até receber um email de confirmação. Você também pode contatar seu representante da Microsoft para ser habilitado para esta versão prévia. O acesso ao recurso de visualização está sujeito à disponibilidade e restrito pela Microsoft a seu exclusivo critério. A conclusão da pesquisa não garante o acesso. Essa visualização é fornecida sem um contrato de nível de serviço e não deve ser usada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Consulte os [termos de uso complementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para ver os detalhes de Microsoft Azure. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

A imagem a seguir mostra uma visão geral de alto nível da interconexão:

![Conexão de rede entre redes em nuvem](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Pré-requisitos

* Para estabelecer a conectividade entre o Azure e o OCI, você deve ter uma assinatura ativa do Azure e uma locação de OCI ativa.

* A conectividade só é possível quando um local de emparelhamento do ExpressRoute do Azure está em proximidade com ou no mesmo local de emparelhamento que o FastConnect de OCI. Consulte [limitações de visualização](oracle-oci-overview.md#preview-limitations).

* Sua assinatura do Azure deve ser habilitada para esse recurso de visualização.

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Configurar a conectividade direta entre o ExpressRoute e o FastConnect

1. Crie um circuito de ExpressRoute padrão em sua assinatura do Azure em um grupo de recursos. 
    * Ao criar o ExpressRoute, escolha **Oracle Cloud FastConnect** como o provedor de serviços. Para criar um circuito do ExpressRoute, consulte o [guia passo a passo](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Um circuito do Azure ExpressRoute fornece opções de largura de banda granulares, enquanto FastConnect dá suporte a 1, 2, 5 ou 10 Gbps. Portanto, é recomendável escolher uma dessas opções de largura de banda correspondentes em ExpressRoute.

    ![Criar circuito de ExpressRoute](media/configure-azure-oci-networking/exr-create-new.png)
1. Anote sua chave de **serviço**do ExpressRoute. Você precisa fornecer a chave ao configurar o circuito do FastConnect.

    ![Chave de serviço do ExpressRoute](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Você será cobrado pelos encargos do ExpressRoute assim que o circuito do ExpressRoute for provisionado (mesmo que o **status** do provedor **não seja provisionado**).

1. Anote dois espaços de endereço IP privado de/30 cada um que não se sobreponha à sua rede virtual do Azure ou ao espaço de endereço IP de rede de nuvem virtual de OCI. Iremos nos referir ao primeiro espaço de endereço IP como espaço de endereço primário e ao segundo espaço de endereço IP que o espaço de endereço secundário. Anote os endereços que você precisa ao configurar o circuito FastConnect.
1. Crie um DRG (gateway de roteamento dinâmico). Você precisará disso ao criar seu circuito FastConnect. Para obter mais informações, consulte a documentação do [Gateway de roteamento dinâmico](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) .
1. Crie um circuito FastConnect em seu locatário do Oracle. Para obter mais informações, consulte a [documentação do Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
  
    * Em configuração do FastConnect, selecione **Microsoft Azure: ExpressRoute** como o provedor.
    * Selecione o gateway de roteamento dinâmico que você provisionou na etapa anterior.
    * Selecione a largura de banda a ser provisionada. Para um desempenho ideal, a largura de banda deve corresponder à largura de banda selecionada ao criar o circuito de ExpressRoute.
    * Em **chave de serviço do provedor**, Cole a chave de serviço do ExpressRoute.
    * Use o primeiro/30 espaço de endereço IP privado gravado em uma etapa anterior para o **endereço IP do BGP primário** e o segundo/30 espaço de endereço IP privado para o endereço **IP do BGP secundário** .
        * Atribua o primeiro endereço utilizável dos dois intervalos para o endereço IP do Oracle BGP (primário e secundário) e o segundo endereço para o endereço IP do BGP do cliente (de uma perspectiva FastConnect). O primeiro endereço IP utilizável é o segundo endereço IP no espaço de endereço/30 (o primeiro endereço IP é reservado pela Microsoft).
    * Clique em **Criar**.
1. Conclua a vinculação do FastConnect à rede de nuvem virtual em seu locatário do Oracle por meio do gateway de roteamento dinâmico, usando a tabela de rotas.
1. Navegue até o Azure e verifique se o **status do provedor** do seu circuito do ExpressRoute foi alterado para **provisionado** e se um emparelhamento do tipo **privado do Azure** foi provisionado. Este é um pré-requisito para as etapas a seguir.

    ![Status do provedor de ExpressRoute](media/configure-azure-oci-networking/exr-provider-status.png)
1. Clique no emparelhamento **privado do Azure** . Você verá os detalhes de emparelhamento que foram automaticamente configurados com base nas informações inseridas durante a configuração do circuito FastConnect.

    ![Configurações de emparelhamento privado](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Conectar a rede virtual ao ExpressRoute

1. Crie uma rede virtual e um gateway de rede virtual, se ainda não tiver feito isso. Para obter detalhes, consulte o [guia passo a passo](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Configure a conexão entre o gateway de rede virtual e o circuito do ExpressRoute executando o [script Terraform](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) ou executando o comando do PowerShell para [Configurar o ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

Depois de concluir a configuração de rede, você pode verificar a validade da sua configuração clicando em **obter registros ARP** e **obter tabela de rotas** na folha emparelhamento privado do ExpressRoute no portal do Azure.

## <a name="automation"></a>Automatização

A Microsoft criou scripts Terraform para habilitar a implantação automatizada da interconexão de rede. Os scripts do Terraform precisam se autenticar no Azure antes da execução, porque eles exigem permissões adequadas na assinatura do Azure. A autenticação pode ser executada usando uma [entidade de serviço Azure Active Directory](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) ou usando o CLI do Azure. Para obter mais informações, consulte a [documentação do Terraform](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

Os scripts Terraform e a documentação relacionada para implantar a inter-Connect podem ser encontrados neste [repositório GitHub](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Monitorização

Instalando agentes em ambas as nuvens, você pode aproveitar o Azure [Monitor de desempenho de rede (NPM)](../../../expressroute/how-to-npm.md) para monitorar o desempenho da rede de ponta a ponta. O NPM ajuda você a identificar rapidamente os problemas de rede e a eliminá-los.

## <a name="delete-the-interconnect-link"></a>Excluir o link de interconexão

Para excluir a interconexão, as etapas a seguir devem ser seguidas, na ordem específica especificada. Se não fizer isso, isso resultará em um circuito de ExpressRoute de "estado de falha".

1. Exclua a conexão do ExpressRoute. Exclua a conexão clicando no ícone **excluir** na página de sua conexão. Para obter mais informações, consulte a [documentação do ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Exclua o Oracle FastConnect do console de nuvem da Oracle.
1. Depois que o circuito do Oracle FastConnect tiver sido excluído, você poderá excluir o circuito do Azure ExpressRoute.

Neste ponto, o processo de exclusão e desprovisionamento é concluído.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a conexão entre nuvem entre o OCI e o Azure, consulte a [documentação do Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* Use [scripts Terraform](https://aka.ms/azureociinterconnecttf) para implantar a infraestrutura para aplicativos Oracle direcionados no Azure e configurar a interconexão de rede. 
