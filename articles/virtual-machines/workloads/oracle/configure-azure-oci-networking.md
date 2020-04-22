---
title: Ligue azure ExpressRoute com a Oracle Cloud Infrastructure [ Microsoft Docs
description: Connect Azure ExpressRoute com Oracle Cloud Infrastructure (OCI) FastConnect para ativar soluções de aplicação Oracle em nuvem cruzada
documentationcenter: virtual-machines
author: BorisB2015
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/16/2020
ms.author: borisb
ms.openlocfilehash: 70556cbbfefd6ad22ef96ee16065209031ea456c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683759"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Criar uma interligação direta entre a Azure e a Oracle Cloud Infrastructure  

Para criar uma [experiência multi-cloud integrada,](oracle-oci-overview.md)a Microsoft e a Oracle oferecem interligação direta entre a Azure e a Oracle Cloud Infrastructure (OCI) através do [ExpressRoute](../../../expressroute/expressroute-introduction.md) e [do FastConnect.](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) Através da interligação ExpressRoute e FastConnect, os clientes podem experimentar baixa latência, alta entrada, conectividade direta privada entre as duas nuvens.

> [!IMPORTANT]
> A Oracle certificará estas aplicações para funcionar em Azure quando utilizar a solução de interligação Azure/Oracle Cloud até maio de 2020.
> * Suíte E-Business
> * JD Edwards EnterpriseOne
> * PeopleSoft
> * Aplicações oracle Retail
> * Gestão Financeira Oracle Hyperion

A imagem seguinte mostra uma visão geral de alto nível da interconexão:

![Conexão de rede de nuvem cruzada](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Pré-requisitos

* Para estabelecer conectividade entre o Azure e o OCI, deve ter uma subscrição azure ativa e um arrendamento OCI ativo.

* A conectividade só é possível quando um local de observação do Azure ExpressRoute estiver próximo ou no mesmo local de observação que o OCI FastConnect. Ver [Disponibilidade da Região](oracle-oci-overview.md#region-availability).

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Configure a conectividade direta entre expressRoute e FastConnect

1. Crie um circuito ExpressRoute padrão na sua subscrição Azure sob um grupo de recursos. 
    * Ao criar o ExpressRoute, escolha o **Oracle Cloud FastConnect** como fornecedor de serviços. Para criar um circuito ExpressRoute, consulte o [guia passo a passo](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Um circuito Azure ExpressRoute fornece opções de largura de banda granular, enquanto o FastConnect suporta 1, 2, 5 ou 10 Gbps. Por isso, recomenda-se escolher uma destas opções de largura de banda correspondentes no ExpressRoute.

    ![Criar circuito ExpressRoute](media/configure-azure-oci-networking/exr-create-new.png)
1. Anote a sua chave de **serviço**ExpressRoute . Tem de fornecer a tecla enquanto configura o circuito FastConnect.

    ![Chave de serviço ExpressRoute](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Será cobrado para taxas ExpressRoute logo que o circuito ExpressRoute seja provisionado (mesmo que o Estatuto do **Fornecedor** não esteja **provisionado).**

1. Esculpe dois espaços privados de endereçoIP de /30 cada um que não se sobreponham à sua rede virtual Azure ou ao espaço IP Address da rede virtual de nuvem virtual OCI. Vamos referir o primeiro espaço de endereçoIP como espaço de endereço primário e o segundo espaço de endereçoIP como espaço de endereço secundário. Anote os endereços, que necessita ao configurar o circuito FastConnect.
1. Crie um Gateway de Encaminhamento Dinâmico (DRG). Vai precisar disto ao criar o seu circuito FastConnect. Para mais informações, consulte a documentação [Dynamic Routing Gateway.](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm)
1. Crie um circuito FastConnect sob o seu inquilino Oracle. Para mais informações, consulte a documentação do [Oráculo.](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)
  
    * Sob a configuração FastConnect, selecione **Microsoft Azure: ExpressRoute** como fornecedor.
    * Selecione o Gateway de Encaminhamento Dinâmico que forprovisionou no passo anterior.
    * Selecione a largura de banda a fornecer. Para um desempenho ótimo, a largura de banda deve corresponder à largura de banda selecionada ao criar o circuito ExpressRoute.
    * Na **Chave de Serviço do Fornecedor,** cola a chave de serviço ExpressRoute.
    * Utilize o primeiro espaço de endereço IP privado /30 esculpido num passo anterior para o **Endereço IP BGP primário** e o segundo espaço de endereço IP privado /30 para o Endereço IP **BGP secundário.**
        * Atribuir o primeiro endereço utilizável das duas gamas para o Endereço IP Oracle BGP (Primário e Secundário) e o segundo endereço para o endereço IP do cliente BGP (do ponto de vista fastConnect). O primeiro endereço IP utilizável é o segundo endereço IP no espaço de endereço /30 (o primeiro endereço IP é reservado pela Microsoft).
    * Clique em **Criar**.
1. Complete ligando o FastConnect à rede de nuvem virtual sob o seu inquilino Oracle através do Dynamic Routing Gateway, utilizando a Route Table.
1. Navegue para o Azure e certifique-se de que o Estatuto do **Fornecedor** para o seu circuito ExpressRoute mudou para **Provisioned** e que foi aprovisionado um epeering de tipo **Azure privado.** Este é um pré-requisito para os seguintes passos.

    ![Estado do fornecedor ExpressRoute](media/configure-azure-oci-networking/exr-provider-status.png)
1. Clique no olho **privado azure.** Verá que os dados de observação foram configurados automaticamente com base nas informações que introduziu ao configurar o circuito FastConnect.

    ![Configurações de observação privada](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Ligue a rede virtual ao ExpressRoute

1. Crie uma rede virtual e um portal de rede virtual, se ainda não o fez. Para mais detalhes, consulte o [guia passo a passo](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Configure a ligação entre o gateway da rede virtual e o circuito ExpressRoute executando o [script Terraform](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) ou executando o comando PowerShell para Configurar o [FastPath ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

Uma vez concluída a configuração da rede, pode verificar a validade da sua configuração clicando na **Tabela Get ARP Records** e Obter a tabela de **rotas** sob a lâmina de observação privada ExpressRoute no portal Azure.

## <a name="automation"></a>Automatização

A Microsoft criou scripts Terraform para permitir a implementação automatizada da interligação da rede. Os scripts Terraform precisam de autenticar com o Azure antes da execução, porque requerem permissões adequadas na subscrição do Azure. A autenticação pode ser realizada através de um [diretor de serviço Azure Ative Directory](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) ou utilizando o Azure CLI. Para mais informações, consulte a [documentação terraforme.](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)

Os scripts Terraform e documentação relacionada para implementar o inter-connect podem ser encontrados neste [repositório GitHub](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Monitorização

Instalando agentes em ambas as nuvens, pode aproveitar o Monitor de Desempenho da Rede Azure [(NPM)](../../../expressroute/how-to-npm.md) para monitorizar o desempenho da rede de ponta a ponta. O NPM ajuda-o a identificar facilmente os problemas de rede e ajuda a eliminá-los.

## <a name="delete-the-interconnect-link"></a>Eliminar o link de interligação

Para eliminar a interligação, devem ser seguidos os seguintes passos, na ordem específica dada. Se não o fizer, resultará num circuito expressroute "estado falhado".

1. Elimine a ligação ExpressRoute. Elimine a ligação clicando no ícone **Eliminar** na página para a sua ligação. Para mais informações, consulte a [documentação ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Elimine o Oracle FastConnect da Consola Oracle Cloud.
1. Uma vez eliminado o circuito Oracle FastConnect, pode eliminar o circuito Azure ExpressRoute.

Neste momento, o processo de eliminação e dedeprovisionamento está concluído.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a ligação entre o OCI e o Azure, consulte a documentação do [Oráculo](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* Utilize [scripts Terraform](https://aka.ms/azureociinterconnecttf) para implementar infraestruturas para aplicações oráculos direcionadas sobre o Azure e configurar a interligação da rede. 
