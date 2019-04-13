---
title: Como utilizar a API Management do Azure com as redes virtuais internas | Documentos da Microsoft
description: Saiba como definir e configurar a gestão de API do Azure numa rede virtual interna
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: 738dd451ca44801c799d793e6501c547655bd0e0
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59524154"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Utilizar o serviço de gestão de API do Azure com uma rede virtual interna
Com as redes virtuais do Azure, gestão de API do Azure pode gerir APIs não está acessíveis na internet. Um número de tecnologias VPN está disponível para fazer a conexão. Gestão de API pode ser implementada em dois modos principais dentro de uma rede virtual:
* Externo
* Interno

Quando implementar a gestão de API no modo de rede virtual interna, só estão visíveis numa rede virtual que controla o acesso a todos os pontos finais de serviço (gateway, o portal do programador, o portal do Azure, gestão direta e Git). Nenhum dos pontos de extremidade de serviço estão registados no servidor DNS público.

Utilizar a gestão de API no modo interno, é possível obter os seguintes cenários:

* Tornar APIs alojadas no seu datacenter privada acessível de forma segura por terceiros fora dele com o site a site ou ligações de VPN do Azure ExpressRoute.
* Ative cenários de nuvem híbrida, expondo as suas APIs com base na cloud e APIs no local através de um gateway comum.
* Gerir as suas APIs alojados em várias localizações geográficas utilizando um ponto de final de gateway único.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para efetuar os passos descritos neste artigo, tem de ter:

+ **Uma subscrição do Azure Active Directory**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Uma instância de gestão de API do Azure**. Para obter mais informações, consulte [criar uma instância de gestão de API do Azure](get-started-create-service-instance.md).
+ Quando um serviço de gestão de API é implementado numa rede virtual, um [lista de portas](./api-management-using-with-vnet.md#required-ports) são utilizados e tem de ser aberto. 

## <a name="enable-vpn"> </a>Criação de uma gestão de API numa rede virtual interna
O serviço de gestão de API numa rede virtual interna é hospedado por trás de um [Balanceador de carga interno (clássico)](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud). Esta é a única opção disponível e não pode ser alterada.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Ativar uma ligação de rede virtual com o portal do Azure

1. Navegue para a sua instância de gestão de API do Azure no [portal do Azure](https://portal.azure.com/).
2. Selecione **Rede virtual**.
3. Configure a instância de gestão de API para ser implantado dentro da rede virtual.

    ![Menu para configurar uma API Management do Azure numa rede virtual interna][api-management-using-internal-vnet-menu]

4. Selecione **Guardar**.

Após a implementação com êxito, deverá ver **privada** endereço IP virtual e **público** endereço IP virtual do seu serviço de gestão de API no painel de descrição geral. O **privada** endereço IP virtual é uma carga de endereço IP com balanceamento de dentro da gestão de API delegada sub-rede através do qual `gateway`, `portal`, `management` e `scm` pontos de extremidade podem ser acedidos. O **pública** é utilizado o endereço IP virtual **apenas** para controlar o tráfego de plano de `management` ponto final mais 3443 de porta e pode ser bloqueado para baixo para o [ApiManagement] [ ServiceTags] servicetag.

![Dashboard de gestão de API com uma rede virtual interna configurada][api-management-internal-vnet-dashboard]

> [!NOTE]
> A consola de teste disponível no Portal do Azure não funcionará para **interno** VNET implementado o serviço, como o Url do Gateway não está registado no DNS público. Deve usar o Console de teste fornecido no **portal do programador**.

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Ativar uma ligação de rede virtual utilizando cmdlets do PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Também pode ativar a conectividade de rede virtual utilizando cmdlets do PowerShell.

* Crie um serviço de gestão de API numa rede virtual: Utilize o cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) para criar um serviço de gestão de API do Azure numa rede virtual e configurá-lo para usar o tipo de rede virtual interna.

* Atualize uma implementação existente de um serviço de gestão de API numa rede virtual: Utilize o cmdlet [AzApiManagementRegion atualização](/powershell/module/az.apimanagement/update-azapimanagementregion) para mover um serviço de gestão de API existente numa rede virtual e configurá-lo para usar o tipo de rede virtual interna.

## <a name="apim-dns-configuration"></a>Configuração de DNS
Quando a gestão de API está no modo de rede virtual externa, o DNS é gerido pelo Azure. Para o modo de rede virtual interna, terá de gerir o seu próprio tipo de encaminhamento.

> [!NOTE]
> Serviço de gestão de API não escutar para pedidos provenientes de endereços IP. Ele responde apenas a pedidos para o nome de anfitrião configurado nos seus pontos de extremidade de serviço. Estes pontos finais incluem o gateway, o portal do Azure e o portal do programador, o ponto final de gestão direta e o Git.

### <a name="access-on-default-host-names"></a>Acesso em nomes de anfitrião predefinido
Quando cria um serviço de gestão de API, com o nome "contosointernalvnet", por exemplo, os seguintes pontos finais de serviço são configurados por predefinição:

   * Gateway ou proxy: contosointernalvnet.azure-api.net

   * Portal do Azure e o portal do programador: contosointernalvnet.portal.azure-api.net

   * Ponto final de gestão direta: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Para aceder a estes pontos finais de serviço de gestão de API, pode criar uma máquina virtual numa sub-rede ligada à rede virtual em que a gestão de API é implementada. Partindo do princípio de que o endereço IP virtual interno para o seu serviço é 10.1.0.5, pode mapear o ficheiro de anfitriões, % SystemDrive%\drivers\etc\hosts, da seguinte forma:

   * 10.1.0.5     contosointernalvnet.azure-api.net

   * 10.1.0.5     contosointernalvnet.portal.azure-api.net

   * 10.1.0.5     contosointernalvnet.management.azure-api.net

   * 10.1.0.5     contosointernalvnet.scm.azure-api.net

Em seguida, pode acessar todos os pontos finais do serviço da máquina virtual que criou.
Se utilizar um servidor DNS personalizado numa rede virtual, também pode criar registos DNS de um e aceder a estes pontos finais de qualquer lugar na sua rede virtual.

### <a name="access-on-custom-domain-names"></a>Acesso em nomes de domínio personalizados

1. Se não quiser aceder ao serviço de gestão de API com os nomes de anfitrião predefinido, pode configurar nomes de domínio personalizados para todos os seus pontos finais de serviço conforme mostrado na imagem seguinte:

   ![Configurar um domínio personalizado para gestão de API][api-management-custom-domain-name]

2. Em seguida, pode criar registos no seu servidor DNS para aceder os pontos de extremidade que só estão acessíveis a partir da sua rede virtual.

## <a name="routing"> </a> Encaminhamento
+ Com balanceamento de carga virtual endereços IP privados do intervalo de sub-rede serão ser reservado e utilizado para aceder os gestão de API pontos finais de serviço de dentro da vnet.
+ Para fornecer acesso ao ponto final de serviço de gestão apenas através da porta 3443 também será reservado a um endereço IP público com balanceamento de carga (VIP).
+ Um endereço IP de um intervalo de sub-rede IP (DIP) será utilizado para aceder aos recursos dentro da vnet e um endereço IP público (VIP) será utilizado para aceder aos recursos fora da vnet.
+ Público com balanceamento de carga e endereços IP privados podem ser encontrados no painel de descrição geral/Essentials no portal do Azure.

## <a name="related-content"> </a>Conteúdo relacionado
Para obter mais informações, consulte os artigos seguintes:
* [Problemas comuns de configuração de rede ao configurar a gestão de API do Azure numa rede virtual][Common network configuration problems]
* [FAQs de rede virtual](../virtual-network/virtual-networks-faq.md)
* [Criar um registo no DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/security-overview.md#service-tags

