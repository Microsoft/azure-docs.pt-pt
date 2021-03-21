---
title: Utilizar a Azure API Management com redes virtuais internas
titleSuffix: Azure API Management
description: Saiba como configurar e configurar a Azure API Management numa rede virtual interna
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/09/2021
ms.author: apimpm
ms.openlocfilehash: 10154f496d76ce6b9eb19d610fdff8d7a4023c2d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565959"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Utilizar o serviço Gestão de API do Azure com uma rede virtual interna
Com a Azure Virtual Networks, a Azure API Management pode gerir APIs não acessíveis na internet. Várias tecnologias VPN estão disponíveis para fazer a ligação. A API Management pode ser implementada em dois modos principais dentro de uma rede virtual:
* Externo
* Interno

Quando a API Management se implanta no modo de rede virtual interna, todos os pontos finais de serviço (o gateway proxy, o portal Developer, gestão direta e Git) só são visíveis dentro de uma rede virtual a que controla o acesso. Nenhum dos pontos finais de serviço está registado no servidor PÚBLICO DNS.

> [!NOTE]
> Como não existem entradas dns para os pontos finais de serviço, estes pontos finais não serão acessíveis até que o [DNS esteja configurado](#apim-dns-configuration) para a rede virtual.

Utilizando a Gestão da API em modo interno, pode alcançar os seguintes cenários:

* Tornar as APIs hospedadas no seu centro de dados privado de forma segura, acessíveis por terceiros fora do mesmo, utilizando ligações site-to-site ou Azure ExpressRoute VPN.
* Ativar cenários de nuvem híbrida expondo as suas APIs baseadas em nuvem e APIs no local através de um gateway comum.
* Gerencie as suas APIs hospedadas em múltiplas localizações geográficas utilizando um único ponto de passagem.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para executar os passos descritos neste artigo, deve ter:

+ **Uma subscrição ativa do Azure.**

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Um caso de Gestão AZURE API**. Para obter mais informações, consulte [Criar uma instância de Gestão API Azure.](get-started-create-service-instance.md)
+ Quando um serviço de Gestão API é implantado numa rede virtual, uma [lista de portas](./api-management-using-with-vnet.md#required-ports) é usada e precisa de ser aberta. 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>Criar uma Gestão de API numa rede virtual interna
O serviço de Gestão API numa rede virtual interna está alojado atrás de um [equilibrador de carga interno (clássico)](/previous-versions/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud). Esta é a única opção disponível e não pode ser alterada.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Ativar uma ligação de rede virtual utilizando o portal Azure

1. Navegue pela sua instância de Gestão API Azure no [portal Azure](https://portal.azure.com/).
2. Selecione **Rede virtual**.
3. Configurar o caso de Gestão da API a ser implantado dentro da rede virtual.

    ![Menu para criação de uma Azure API Management numa rede virtual interna][api-management-using-internal-vnet-menu]

4. Selecione **Guardar**.

Após a implementação ter sucesso, deverá ver o endereço IP virtual **privado** e o endereço IP **virtual público** do seu serviço de Gestão API na lâmina geral. O endereço IP virtual **privado** é um endereço IP equilibrado de carga a partir da sub-rede delegada pela API Management sobre a qual `gateway` , e `portal` `management` `scm` pontos finais podem ser acedidos. O endereço IP virtual **público** é utilizado **apenas** para controlar o tráfego do avião até ao `management` ponto final sobre a porta 3443 e pode ser bloqueado na marca de serviço [ApiManagement.][ServiceTags]

![Painel de gestão API com uma rede virtual interna configurada][api-management-internal-vnet-dashboard]

> [!NOTE]
> A consola de teste disponível no Portal Azure não funcionará para o serviço **interno** de VNET implantado, uma vez que o Url Gateway não está registado no DNS Público. Em vez disso, deve utilizar a Consola de Teste fornecida no **portal Developer**.

### <a name="deploy-api-management-into-virtual-network"></a><a name="deploy-apim-internal-vnet"> </a>Implementar a Gestão da API na Rede Virtual

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-internal-vnet%2Fazuredeploy.json)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Também pode ativar a conectividade da rede virtual utilizando cmdlets PowerShell.

* Criar um serviço de Gestão API dentro de uma rede virtual: Utilize o cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) para criar um serviço de Gestão API Azure dentro de uma rede virtual e configurá-lo para utilizar o tipo de rede virtual interna.

* Atualize uma implementação existente de um serviço de Gestão API dentro de uma rede virtual: Utilize o cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) para mover um serviço de Gestão de API existente dentro de uma rede virtual e configurar-se para utilizar o tipo de rede virtual interna.

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>Configuração do DNS
Quando a API Management está em modo de rede virtual externa, o DNS é gerido pelo Azure. Para o modo de rede virtual interna, tem de gerir o seu próprio DNS. Configurar uma zona privada Azure DNS e ligá-la ao serviço de Gestão de API de rede virtual é implementada é a opção recomendada.  Clique [aqui](../dns/private-dns-getstarted-portal.md) para aprender a configurar uma zona privada em Azure DNS.

> [!NOTE]
> O serviço de Gestão API não ouve pedidos provenientes de endereços IP. Apenas responde aos pedidos ao nome de anfitrião configurado nos seus pontos finais de serviço. Estes pontos finais incluem gateway, o portal Azure e o portal Developer, o ponto final de gestão direta, e Git.

### <a name="access-on-default-host-names"></a>Acesso em nomes de anfitriões predefinidos
Quando cria um serviço de Gestão API, denominado "contosointernalvnet", por exemplo, os seguintes pontos finais de serviço são configurados por padrão:

   * Gateway ou proxy: contosointernalvnet.azure-api.net

   * O portal Developer: contosointernalvnet.portal.azure-api.net

   * O novo portal developer: contosointernalvnet.developer.azure-api.net

   * Ponto final de gestão direta: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Para aceder a estes pontos finais do serviço API Management, pode criar uma máquina virtual numa sub-rede ligada à rede virtual na qual a API Management está implantada. Assumindo que o endereço IP virtual interno do seu serviço é de 10.1.0.5, pode mapear o ficheiro dos anfitriões, %SystemDrive%\\drivers\etc\hosts, da seguinte forma:

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10.1.0.5 contosointernalvnet.scm.azure-api.net

Em seguida, pode aceder a todos os pontos finais de serviço a partir da máquina virtual que criou.
Se utilizar um servidor DNS personalizado numa rede virtual, também pode criar registos DNS e aceder a estes pontos finais a partir de qualquer lugar da sua rede virtual.

### <a name="access-on-custom-domain-names"></a>Acesso em nomes de domínio personalizados

1. Se não quiser aceder ao serviço de Gestão API com os nomes de anfitriões predefinidos, pode configurar nomes de domínio personalizados para todos os seus pontos finais de serviço, como mostra a seguinte imagem:

   ![Criação de um domínio personalizado para a Gestão de API][api-management-custom-domain-name]

2. Em seguida, pode criar registos no seu servidor DNS para aceder aos pontos finais que só estão acessíveis dentro da sua rede virtual.

## <a name="routing"></a><a name="routing"></a> Encaminhamento

* Um endereço IP *virtual privado* equilibrado de carga da gama sub-rede será reservado e utilizado para aceder aos pontos finais do serviço de Gestão API a partir da rede virtual. Este endereço IP *privado* pode ser encontrado na lâmina de visão geral para o serviço no portal Azure. Este endereço deve ser registado nos servidores DNS utilizados pela rede virtual.
* Um endereço IP *público* equilibrado de carga (VIP) também será reservado para fornecer acesso ao ponto final do serviço de gestão sobre o porto 3443. Este endereço IP *público* pode ser encontrado na lâmina de visão geral para o serviço no portal Azure. O endereço IP *público* é utilizado apenas para controlar o tráfego do avião até ao ponto final sobre a `management` porta 3443 e pode ser bloqueado na marca de serviço [ApiManagement.][ServiceTags]
* Os endereços IP da gama IP da sub-rede (DIP) serão atribuídos a cada VM no serviço e serão utilizados para aceder a recursos dentro da rede virtual. Um endereço IP público (VIP) será usado para aceder a recursos fora da rede virtual. Se forem utilizadas listas de restrições IP para garantir recursos dentro da rede virtual, toda a gama para a sub-rede onde o serviço de Gestão API é implantado deve ser especificada para conceder ou restringir o acesso a partir do serviço.
* Os endereços IP equilibrados em carga podem ser encontrados na lâmina geral do portal Azure.
* Os endereços IP atribuídos para acesso público e privado podem ser alterados se o serviço for removido e depois adicionado de volta à rede virtual. Se isso acontecer, poderá ser necessário atualizar registos de DNS, regras de encaminhamento e listas de restrições IP dentro da rede virtual.

## <a name="related-content"></a><a name="related-content"> </a>Conteúdo relacionado
Para saber mais, leia os artigos seguintes:
* [Problemas comuns de configuração da rede ao criar a Azure API Management numa rede virtual][Common network configuration problems]
* [FaQs de rede virtual](../virtual-network/virtual-networks-faq.md)
* [Criar um recorde no DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
