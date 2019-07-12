---
title: Ligar o Azure ExpressRoute com a infraestrutura de nuvem de Oracle | Documentos da Microsoft
description: Ligar o Azure ExpressRoute com FastConnect de infraestrutura de nuvem da Oracle (OCI) para ativar soluções de aplicações entre Clouds Oracle
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/24/2019
ms.author: rogirdh
ms.openlocfilehash: 671d7c8eb9f10e346b49056e1cc117c9882bb6e8
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707584"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Configurar uma interconexão direta entre o Azure e infraestrutura de nuvem da Oracle  

Para criar uma [multiclouds experiência integrada](oracle-oci-overview.md) (pré-visualização), a Microsoft e Oracle oferecem interconexão direta entre o Azure e infraestrutura de nuvem da Oracle (OCI) por meio [ExpressRoute](../../../expressroute/expressroute-introduction.md) e [ FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm). Por meio de interconexão FastConnect e do ExpressRoute, os clientes podem sofrer baixa latência, débito elevado, privada conectividade direta entre as duas nuvens.

> [!IMPORTANT]
> A ligação entre o Microsoft Azure e o OCI está na fase de pré-visualização. Para ativar a conectividade de baixa latência entre o Azure e OCI, a subscrição do Azure e os inquilinos OCI tem primeiro de ser na lista de permissões para esta capacidade.

A imagem seguinte mostra uma visão geral sobre a interligação:

![Ligação de rede entre Clouds](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Pré-requisitos

* Para estabelecer a conectividade entre o Azure e OCI, tem de ter uma subscrição do Azure Active Directory e um inquilinos OCI Active Directory.

* Conectividade só é possível onde uma localização de peering do ExpressRoute do Azure está na proximidade para ou na mesma localização de peering como o FastConnect OCI. Ver [limitações de pré-visualização](oracle-oci-overview.md#preview-limitations).

* A subscrição do Azure tem de estar na lista de permissões para esta capacidade de pré-visualização. Contacte o seu representante da Microsoft para ativar esta funcionalidade na sua subscrição.

* Seus inquilinos OCI tem de estar na lista de permissões para esta capacidade de pré-visualização. Contacte o seu representante do Oracle para obter detalhes.

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Configurar a conectividade direta entre o ExpressRoute e FastConnect

1. Crie um circuito de ExpressRoute standard na sua subscrição do Azure num grupo de recursos. 
    * Ao criar o ExpressRoute, escolha **Oracle Cloud FastConnect** como o fornecedor de serviços. Para criar um circuito do ExpressRoute, veja a [guia passo a passo](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Um circuito do ExpressRoute do Azure oferece opções de largura de banda granular, enquanto FastConnect suporta 1, 2, 5 ou 10 Gbps. Por conseguinte, é recomendado para escolher uma destas opções correspondentes de largura de banda no ExpressRoute.

    ![Criar circuito do ExpressRoute](media/configure-azure-oci-networking/exr-create-new.png)
1. Anote o ExpressRoute **chave de serviço**. Tem de fornecer a chave ao configurar o seu circuito FastConnect.

    ![Chave do serviço de ExpressRoute](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Será cobrado para os custos do ExpressRoute assim que o circuito do ExpressRoute está aprovisionado (mesmo que o **estado do fornecedor** é **não aprovisionado**).

1. Extrair dois espaços de endereços IP privados das /30 cada que não se sobrepõem com a sua rede virtual do Azure ou a rede de virtual cloud OCI espaços de endereços IP. Iremos dar o primeiro espaço de endereços IP como espaço de endereço principal e o segundo espaço de endereços IP como espaço de endereço secundário. Tome nota endereços, o que precisa ao configurar o seu circuito FastConnect.
1. Crie um Gateway de encaminhamento dinâmico (DRG). Precisará isso ao criar o seu circuito FastConnect. Para obter mais informações, consulte a [Gateway de encaminhamento dinâmico](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) documentação.
1. Crie um circuito de FastConnect sob o seu inquilino do Oracle. Para obter mais informações, consulte a [documentação de Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
  
    * Em configuração FastConnect, selecione **Microsoft Azure: ExpressRoute** como o fornecedor.
    * Selecione o Gateway de encaminhamento dinâmico que aprovisionou no passo anterior.
    * Selecione a largura de banda a ser aprovisionado. Para um desempenho ideal, a largura de banda tem de corresponder a largura de banda selecionada quando criar o circuito do ExpressRoute.
    * Na **chave de serviço do fornecedor**, cole a chave do serviço ExpressRoute.
    * Utilização/30 primeiro espaço de endereços IP privados retirar num passo anterior para o **endereço de IP de BGP primário** e/30 segundo espaço de endereços IP privados para o **secundário IP de BGP** endereço.
        * Atribua o endereço pode ser utilizado primeiro dos dois intervalos para o endereço de IP de BGP Oracle (primária e secundária) e o segundo endereço para o endereço de IP de BGP do cliente (a partir de uma perspectiva de FastConnect). O primeiro endereço IP pode ser utilizado é o segundo endereço IP/30 (o primeiro endereço IP está reservado pela Microsoft) de espaço de endereços.
    * Clique em **Criar**.
1. Conclua o FastConnect de ligação à rede virtual de cloud no seu inquilino do Oracle através do Gateway de encaminhamento dinâmico, utilizando a tabela de rotas.
1. Navegue para o Azure e certifique-se de que o **estado do fornecedor** para o ExpressRoute o circuito foi alterado para **aprovisionado** e que um peering do tipo **privado do Azure** tem sido aprovisionada. Este é um pré-requisito para os seguintes passos.

    ![Estado de fornecedor do ExpressRoute](media/configure-azure-oci-networking/exr-provider-status.png)
1. Clique nas **privado do Azure** peering. Verá que os detalhes do peering automaticamente foram configurados com base nas informações que introduziu durante a configuração se o seu circuito FastConnect.

    ![Definições de peering privadas](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Ligar uma rede virtual ao ExpressRoute

1. Crie uma rede virtual e gateway de rede virtual, se ainda não o fez. Para obter detalhes, consulte a [guia passo a passo](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Configurar a ligação entre o gateway de rede virtual e o seu circuito do ExpressRoute ao executar o [script do Terraform](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) ou ao executar o comando do PowerShell para [configurar o ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

Depois de concluir a configuração de rede, pode verificar a validade da sua configuração clicando em **obter registos de ARP** e **tabela de rotas de Get** sob o painel de peering privado do ExpressRoute no o portal do Azure.

## <a name="automation"></a>Automatização

A Microsoft criou o Terraform scripts para permitir a implantação automatizada de interligação da rede. Os scripts do Terraform tem de autenticar com o Azure antes da execução, pois requerem as permissões adequadas na subscrição do Azure. Autenticação pode ser realizada usando uma [principal de serviço do Azure Active Directory](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) ou com a CLI do Azure. Para obter mais informações, consulte a [documentação do Terraform](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

Os scripts de Terraform e documentação relacionada para implementar o inter-connect podem ser encontradas neste [repositório do GitHub](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Monitorização

Instalar agentes em ambas as nuvens, pode tirar partido do Azure [Monitor de desempenho de rede (NPM)](../../../expressroute/how-to-npm.md) para monitorizar o desempenho da rede ponto a ponto. NPM ajuda-o a identificar prontamente a problemas de rede e a eliminá-los.

## <a name="delete-the-interconnect-link"></a>Eliminar a ligação de interconexão

Para eliminar a interconexão, devem ser seguidos os passos seguintes, por ordem específica, tendo em conta. Falha ao fazer isso irá resultar num circuito de ExpressRoute "Estado de falha".

1. Elimine a ligação do ExpressRoute. Eliminar a ligação ao clicar o **eliminar** ícone na página para a sua ligação. Para obter mais informações, consulte a [documentação do ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Elimine o FastConnect Oracle a partir da consola de nuvem da Oracle.
1. Depois do circuito de Oracle FastConnect tiver sido eliminado, pode eliminar o circuito do ExpressRoute do Azure.

Neste momento, a eliminação e o processo de desaprovisionamento está concluída.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre a ligação entre Clouds entre OCI e o Azure, consulte a [documentação de Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* Uso [scripts do Terraform](https://aka.ms/azureociinterconnecttf) para implementar a infraestrutura Oracle para aplicativos de destino através do Azure e configurar o interconexão de rede. 