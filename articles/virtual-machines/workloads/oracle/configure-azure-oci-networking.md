---
title: Ligue o Azure ExpressRoute à infraestrutura de nuvens oracle | Microsoft Docs
description: Conecte o Azure ExpressRoute com a Oracle Cloud Infrastructure (OCI) FastConnect para permitir soluções de aplicação oracle em nuvem cruzada
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 03/16/2020
ms.author: rogardle
ms.openlocfilehash: 09264f9f20411e7536eb4a1dbf12ac297e7e3ef9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101675713"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Criar uma interligação direta entre a Infraestrutura Azure e a Oracle Cloud  

Para criar uma [experiência multi-nuvem integrada,](oracle-oci-overview.md)a Microsoft e a Oracle oferecem interligação direta entre a Azure e a Oracle Cloud Infrastructure (OCI) através [do ExpressRoute](../../../expressroute/expressroute-introduction.md) e [fastConnect.](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) Através da interligação ExpressRoute e FastConnect, os clientes podem experimentar baixa latência, alta produção, conectividade direta privada entre as duas nuvens.

> [!IMPORTANT]
> A Oracle certificará estas aplicações a funcionar em Azure quando utilizar a solução de interligação Azure/Oracle Cloud até maio de 2020.
> * Suíte E-Business
> * JD Edwards EnterpriseOne
> * PeopleSoft
> * Aplicações Oracle Retail
> * Oracle Hyperion Gestão Financeira

A imagem a seguir mostra uma visão geral de alto nível da interconexão:

![Ligação de rede cross-cloud](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Pré-requisitos

* Para estabelecer conectividade entre a Azure e o OCI, tem de ter uma subscrição ativa do Azure e um arrendamento ativo do OCI.

* A conectividade só é possível quando um local de observação Azure ExpressRoute estiver próximo ou no mesmo local de observação que o OCI FastConnect. Ver [Disponibilidade da Região.](oracle-oci-overview.md#region-availability)

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Configure a conectividade direta entre ExpressRoute e FastConnect

1. Crie um circuito ExpressRoute padrão na sua subscrição Azure sob um grupo de recursos. 
    * Ao criar o ExpressRoute, escolha **o Oracle Cloud FastConnect** como fornecedor de serviços. Para criar um circuito ExpressRoute, consulte o [guia passo a passo](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Um circuito Azure ExpressRoute oferece opções de largura de banda granular, enquanto o FastConnect suporta 1, 2, 5 ou 10 Gbps. Por isso, recomenda-se escolher uma destas opções de largura de banda correspondentes ao abrigo do ExpressRoute.

    ![Criar circuito ExpressRoute](media/configure-azure-oci-networking/exr-create-new.png)
1. Note a sua **chave de Serviço** ExpressRoute . Tem de fornecer a chave enquanto configura o circuito FastConnect.

    ![Chave de serviço ExpressRoute](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Será cobrado para os encargos ExpressRoute assim que o circuito ExpressRoute for provisionado (mesmo que o **Estado do Fornecedor** não seja **provisionado).**

1. Esculpe dois espaços de endereço IP privados de /30 cada um que não se sobreponham à sua rede virtual Azure ou ao espaço ip address da rede virtual de nuvem OCI. Vamos referir-nos ao primeiro espaço de endereço IP como espaço de endereço primário e o segundo espaço de endereço IP como espaço de endereço secundário. Observe os endereços, que necessita ao configurar o circuito FastConnect.
1. Criar um Gateway de encaminhamento dinâmico (DRG). Vai precisar disto ao criar o seu circuito FastConnect. Para obter mais informações, consulte a documentação [dynamic Routing Gateway.](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm)
1. Crie um circuito FastConnect sob o seu inquilino Oracle. Para mais informações, consulte a documentação do [Oráculo.](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)
  
    * Na configuração FastConnect, selecione **Microsoft Azure: ExpressRoute** como fornecedor.
    * Selecione o Gateway de encaminhamento dinâmico que a provisionou no passo anterior.
    * Selecione a largura de banda a ser a provisionada. Para um desempenho ótimo, a largura de banda deve corresponder à largura de banda selecionada ao criar o circuito ExpressRoute.
    * Na **Chave de Serviço do Fornecedor,** cole a chave de serviço ExpressRoute.
    * Utilize o primeiro espaço de endereço IP privado /30 esculpido num passo anterior para o **Endereço IP BGP primário** e o segundo espaço de endereço IP privado /30 para o Endereço IP secundário do **BGP.**
        * Atribua o primeiro endereço utilizável das duas gamas para o Endereço IP do Oracle BGP (Primário e Secundário) e o segundo endereço para o endereço IP do cliente BGP (numa perspetiva FastConnect). O primeiro endereço IP utilizável é o segundo endereço IP no espaço de endereço /30 (o primeiro endereço IP é reservado pela Microsoft).
    * Clique em **Criar**.
1. Complete a ligação do FastConnect à rede de nuvem virtual sob o seu inquilino Oracle via Dynamic Routing Gateway, utilizando a Tabela de Rotas.
1. Navegue para Azure e certifique-se de que o **Estado do Fornecedor** do seu circuito ExpressRoute foi alterado para **Provisioned** e que foi abastado um espreitamento do tipo **Azure privado.** Este é um pré-requisito para os seguintes passos.

    ![Estado do fornecedor ExpressRoute](media/configure-azure-oci-networking/exr-provider-status.png)
1. Clique no **Azure private** olhando. Verá que os detalhes do espreitamento foram configurados automaticamente com base nas informações que introduziu ao configurar o seu circuito FastConnect.

    ![Configurações de observação privada](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Ligue a rede virtual ao ExpressRoute

1. Crie uma rede virtual e um gateway de rede virtual, se ainda não o fez. Para mais detalhes, consulte o [guia passo a passo.](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
1. Configure a ligação entre o gateway de rede virtual e o circuito ExpressRoute executando o [script Terraform](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) ou executando o comando PowerShell para [configurar o ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

Uma vez concluída a configuração da rede, pode verificar a validade da sua configuração clicando na **Tabela Get ARP Records** e Get a tabela de **rotas** sob a lâmina de espreitar Private ExpressRoute no portal Azure.

## <a name="automation"></a>Automatização

A Microsoft criou scripts Terraform para permitir a implementação automatizada da interligação da rede. Os scripts Terraform precisam de autenticar com o Azure antes da execução, porque requerem permissões adequadas na subscrição do Azure. A autenticação pode ser realizada através de um [diretor de serviço Azure Ative Directory](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) ou utilizando o Azure CLI. Para mais informações, consulte a [documentação Terraform.](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)

Os scripts Terraform e documentação relacionada para implantar o inter-conector podem ser encontrados neste [repositório GitHub](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Monitorização

Instalando agentes em ambas as nuvens, pode aproveitar o Azure [Network Performance Monitor (NPM)](../../../expressroute/how-to-npm.md) para monitorizar o desempenho da rede de ponta a ponta. O NPM ajuda-o a identificar facilmente problemas de rede e ajuda a eliminá-los.

## <a name="delete-the-interconnect-link"></a>Eliminar a ligação de interligação

Para eliminar a interligação, devem ser seguidos os seguintes passos, na ordem específica dada. Se não o fizer, resultará num circuito ExpressRoute "estado falhado".

1. Elimine a ligação ExpressRoute. Elimine a ligação clicando no ícone **Eliminar** na página para a sua ligação. Para mais informações, consulte a [documentação ExpressRoute.](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#clean-up-resources)
1. Elimine o Oracle FastConnect da Consola de Nuvem Oracle.
1. Uma vez eliminado o circuito Oracle FastConnect, pode eliminar o circuito Azure ExpressRoute.

Neste momento, o processo de eliminação e desprovisionamento está concluído.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a ligação entre o OCI e o Azure, consulte a documentação do [Oráculo.](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)
* Utilize [scripts Terraform](https://aka.ms/azureociinterconnecttf) para implantar infraestruturas para aplicações Oráculos direcionadas sobre a Azure e configurar a interligação da rede. 
