---
title: Solução Azure VMware by CloudSimple - Configure vSAN encriptação para nuvem privada
description: Descreve como configurar a funcionalidade de encriptação de software vSAN para que o seu CloudSimple Private Cloud possa trabalhar com um servidor de gestão chave em execução na sua rede virtual Azure.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 638b60bd3612fa25350ecef0a738fea75c2f53d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77020646"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>Configure encriptação vSAN para CloudSimple Private Cloud

Pode configurar a funcionalidade de encriptação de software vSAN para que o cloudSimple Private Cloud possa funcionar com um servidor de gestão chave em execução na sua rede virtual Azure.

O VMware requer a utilização de uma ferramenta externa de gestão de servidores de gestão de chaves de terceiros (KMS) compatível com o KMS quando utilizar encriptação vSAN. Pode aproveitar qualquer KMS suportado que seja certificado pela VMware e esteja disponível para o Azure.

Este guia descreve como utilizar o HyTrust KeyControl KMS numa rede virtual Azure. Uma abordagem semelhante pode ser usada para qualquer outra solução KMS certificada de terceiros para vSAN.

Esta solução KMS requer:

* Instale, configure e gere ncisa uma ferramenta KMS certificada por VMware na sua rede virtual Azure.
* Forneça as suas próprias licenças para a ferramenta KMS.
* Configure e gere a encriptação vSAN na sua Nuvem Privada utilizando a ferramenta KMS de terceiros que funciona na sua rede virtual Azure.

## <a name="kms-deployment-scenario"></a>Cenário de implantação kms

O cluster de servidorES KMS funciona na sua rede virtual Azure e é acessível ip a partir do vCenter Cloud Privado sobre a ligação Configurada Azure ExpressRoute.

![.. aglomerado /media/KMS na rede virtual Azure](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>Como implementar a solução

O processo de implantação tem os seguintes passos:

1. [Verifique se os pré-requisitos são cumpridos](#verify-prerequisites-are-met)
2. [Portal CloudSimple: Obtenha informações de peering expressroute](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Portal Azure: Ligue a sua rede virtual à Nuvem Privada](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Portal Azure: Implemente um Cluster HyTrust KeyControl na sua rede virtual](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI: Configure servidor KMIP](#hytrust-webui-configure-the-kmip-server)
6. [vCenter UI: Configure a encriptação vSAN para utilizar o cluster KMS na sua rede virtual Azure](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>Verificar se os pré-requisitos são cumpridos

Verifique o seguinte antes da implantação:

* O fornecedor, ferramenta e versão selecionados do KMS estão na lista de compatibilidade san.
* O fornecedor selecionado suporta uma versão da ferramenta para funcionar no Azure.
* A versão Azure da ferramenta KMS é compatível com KMIP 1.1.
* Já foram criados um Gestor de Recursos Azure e uma rede virtual.
* Já foi criada uma CloudSimple Private Cloud.

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>Portal CloudSimple: Obtenha informações de peering ExpressRoute

Para continuar a configuração, necessita da chave de autorização e do circuito de pares URI para o ExpressRoute mais acesso à sua Subscrição Azure. Esta informação está disponível na página de Ligação de Rede Virtual no portal CloudSimple. Para obter instruções, consulte [Configurar uma ligação de rede virtual à Nuvem Privada](virtual-network-connection.md). Se tiver algum problema em obter a informação, abra um pedido de [apoio.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Portal Azure: Ligue a sua rede virtual à sua Nuvem Privada

1. Crie um portal de rede virtual para a sua rede virtual seguindo as instruções em Configurar um portal de [rede virtual para a ExpressRoute utilizando o portal Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
2. Ligue a sua rede virtual ao circuito CloudSimple ExpressRoute seguindo as instruções em [Ligar uma rede virtual a um circuito ExpressRoute utilizando o portal](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).
3. Utilize as informações do circuito CloudSimple ExpressRoute recebidas no seu e-mail de boas-vindas da CloudSimple para ligar a sua rede virtual ao circuito CloudSimple ExpressRoute em Azure.
4. Introduza a chave de autorização e o circuito de pares URI, dê à ligação um nome e clique **OK**.

![Fornecer circuito de pares CS URI ao criar a rede virtual](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Portal Azure: Implemente um cluster HyTrust KeyControl no Gestor de Recursos Azure na sua rede virtual

Para implementar um cluster HyTrust KeyControl no Gestor de Recursos Azure na sua rede virtual, execute as seguintes tarefas. Consulte a documentação do [HyTrust](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0) para obter mais detalhes.

1. Crie um grupo de segurança de rede Azure (nsg-hytrust) com regras de entrada especificadas seguindo as instruções na documentação hyTrust.
2. Gere um par de chaves SSH em Azure.
3. Implemente o nó keyControl inicial a partir da imagem no Azure Marketplace.  Utilize a chave pública do par chave que foi gerado e selecione **nsg-hytrust** como o grupo de segurança da rede para o nó KeyControl.
4. Converta o endereço IP privado do KeyControl num endereço IP estático.
5. SSH para o KeyControl VM utilizando o seu endereço IP público e a chave privada do par de chaves anteriormente mencionado.
6. Quando solicitado na concha SSH, selecione `No` para definir o nó como o nó keyControl inicial.
7. Adicione nós de KeyControl adicionais repetindo os passos `Yes` 3-5 deste procedimento e selecionando quando solicitado para adicionar a um cluster existente.

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI: Configure o servidor KMIP

Vá a https://*public-ip,* onde o *IP público* é o endereço IP público do nó KeyControl VM. Siga estes passos da [documentação hyTrust](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0).

1. [Configurar um servidor KMIP](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [Criação de um pacote de certificados para encriptação VMware](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>vCenter UI: Configure a encriptação vSAN para utilizar o cluster KMS na sua rede virtual Azure

Siga as instruções do HyTrust para [criar um cluster KMS no vCenter](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4).

![Adicione detalhes do cluster KMS no vCenter](media/vsan-config01.png)

No vCenter, vá ao **Cluster > Configure** e selecione a opção **geral** para vSAN. Ative a encriptação e selecione o cluster KMS que foi previamente adicionado ao vCenter.

![Ativar encriptação vSAN e configurar cluster KMS no vCenter](media/vsan-config02.png)

## <a name="references"></a>Referências

### <a name="azure"></a>Azure

[Configure um portal de rede virtual para expressRoute utilizando o portal Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[Ligar uma rede virtual a um circuito do ExpressRoute com o portal](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust

[HyTrust DataControl e Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[Configurar um servidor KMPI](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[Criação de um pacote de certificados para encriptação VMware](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[Criação do Cluster KMS na vSphere](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
