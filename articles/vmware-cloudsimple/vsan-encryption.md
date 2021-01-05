---
title: Solução Azure VMware by CloudSimple - Configuração vSAN encriptação para nuvem privada
description: Descreve como configurar a funcionalidade de encriptação de software vSAN para que a cloudSimple Private Cloud possa funcionar com um servidor de gestão de chaves em execução na sua rede virtual Azure.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f5da05c7f3c6878b0804799360e512676b9002d3
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899053"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>Configurar encriptação vSAN para CloudSimple Private Cloud

Pode configurar a funcionalidade de encriptação do software vSAN para que a cloudsimple Private Cloud possa funcionar com um servidor de gestão de chaves em execução na sua rede virtual Azure.

O VMware requer a utilização de uma ferramenta externa de servidor de gestão de chaves de terceiros (KMS) em conformidade com a utilização da encriptação vSAN. Você pode aproveitar qualquer KMS suportado que seja certificado pela VMware e está disponível para Azure.

Este guia descreve como utilizar o HyTrust KeyControl KMS em execução numa rede virtual Azure. Uma abordagem semelhante pode ser utilizada para qualquer outra solução KMS certificada de terceiros para vSAN.

Esta solução KMS requer que:

* Instale, configuure e gere uma ferramenta KMS certificada em VMware na sua rede virtual Azure.
* Forneça as suas próprias licenças para a ferramenta KMS.
* Configure e gere a encriptação vSAN na sua Nuvem Privada utilizando a ferramenta KMS de terceiros em execução na sua rede virtual Azure.

## <a name="kms-deployment-scenario"></a>Cenário de implantação de KMS

O cluster de servidorES KMS funciona na sua rede virtual Azure e é ip acessível a partir do Private Cloud vCenter sobre a conexão Azure ExpressRoute configurada.

![.. Cluster /media/KMS na rede virtual Azure](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>Como implementar a solução

O processo de implantação tem os seguintes passos:

1. [Verifique se os pré-requisitos são cumpridos](#verify-prerequisites-are-met)
2. [Portal CloudSimple: Obtenha informações de peering ExpressRoute](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Portal Azure: Ligue a sua rede virtual à Nuvem Privada](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Portal Azure: Implementar um Cluster HyTrust KeyControl na sua rede virtual](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI: Servidor Configure KMIP](#hytrust-webui-configure-the-kmip-server)
6. [vCenter UI: Configurar encriptação vSAN para usar o cluster KMS na sua rede virtual Azure](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>Verificar os pré-requisitos são cumpridos

Verifique o seguinte antes da implementação:

* O fornecedor, ferramenta e versão KMS selecionados estão na lista de compatibilidade vSAN.
* O fornecedor selecionado suporta uma versão da ferramenta a ser executada em Azure.
* A versão Azure da ferramenta KMS é compatível com KMIP 1.1.
* Já estão criados um Gestor de Recursos Azure e uma rede virtual.
* Uma Nuvem Privada CloudSimple já está criada.

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>Portal CloudSimple: Obtenha informações de peering ExpressRoute

Para continuar a configuração, precisa da chave de autorização e do circuito de pares URI para ExpressRoute mais acesso à sua Assinatura Azure. Esta informação está disponível na página De Ligação de Rede Virtual no portal CloudSimple. Para obter instruções, consulte [Configurar uma ligação de rede virtual à Nuvem Privada](virtual-network-connection.md). Se tiver algum problema em obter a informação, abrida um pedido de [apoio](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Portal Azure: Ligue a sua rede virtual à sua Nuvem Privada

1. Crie um gateway de rede virtual para a sua rede virtual seguindo as instruções em [Configure um gateway de rede virtual para ExpressRoute utilizando o portal Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
2. Ligue a sua rede virtual ao circuito CloudSimple ExpressRoute seguindo as instruções da [Connect uma rede virtual a um circuito ExpressRoute utilizando o portal](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).
3. Utilize as informações do circuito CloudSimple ExpressRoute recebidas no seu e-mail de boas-vindas da CloudSimple para ligar a sua rede virtual ao circuito CloudSimple ExpressRoute em Azure.
4. Introduza a chave de autorização e o circuito de pares URI, dê um nome à ligação e clique **em OK**.

![Fornecer circuito de pares CS URI ao criar a rede virtual](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Portal Azure: Implementar um cluster HyTrust KeyControl no Azure Resource Manager na sua rede virtual

Para implementar um cluster HyTrust KeyControl no Gestor de Recursos Azure na sua rede virtual, execute as seguintes tarefas. Consulte a [documentação da HyTrust](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0) para mais detalhes.

1. Crie um grupo de segurança de rede Azure (nsg-hytrust) com regras de entrada especificadas seguindo as instruções na documentação HyTrust.
2. Gere um par de chaves SSH em Azure.
3. Implemente o nó keycontrol inicial a partir da imagem no Azure Marketplace.  Utilize a chave pública do par de chaves que foi gerado e selecione **nsg-hytrust** como o grupo de segurança da rede para o nó KeyControl.
4. Converta o endereço IP privado do KeyControl num endereço IP estático.
5. SSH para o KeyControl VM usando o seu endereço IP público e a chave privada do par de chaves mencionado anteriormente.
6. Quando solicitado na concha SSH, selecione `No` para definir o nó como o nó chaver inicial.
7. Adicione os nós keyControl adicionais repetindo os passos 3-5 deste procedimento e selecionando `Yes` quando solicitado para adicionar a um cluster existente.

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI: Configurar o servidor KMIP

Vá a https://*public-ip,* onde *o public-ip* é o endereço IP público do nó de teclas VM. Siga estes passos da [documentação hyTrust.](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

1. [Configurar um servidor KMIP](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [Criação de um pacote de certificados para encriptação VMware](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>vCenter UI: Configurar encriptação vSAN para usar o cluster KMS na sua rede virtual Azure

Siga as instruções do HyTrust para [criar um cluster KMS em vCenter](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4).

![Adicione detalhes do cluster KMS no vCenter](media/vsan-config01.png)

Em vCenter, vá ao **Cluster > Configurar** e selecione a opção **Geral** para vSAN. Ativar a encriptação e selecionar o cluster KMS que foi previamente adicionado ao vCenter.

![Ativar a encriptação vSAN e configurar o cluster KMS no vCenter](media/vsan-config02.png)

## <a name="references"></a>Referências

### <a name="azure"></a>Azure

[Configurar uma porta de rede virtual para o ExpressRoute utilizando o portal Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[Ligar uma rede virtual a um circuito do ExpressRoute com o portal](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust

[HyTrust DataControl e Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[Configurar um servidor KMPI](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[Criação de um pacote de certificados para encriptação VMware](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[Criação do Cluster KMS em vSphere](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
