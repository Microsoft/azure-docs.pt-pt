---
title: Utilizar a Gestão aPI Azure com redes virtuais internas
titleSuffix: Azure API Management
description: Saiba como configurar e configurar a Gestão de API Azure numa rede virtual interna
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
ms.date: 07/31/2019
ms.author: apimpm
ms.openlocfilehash: 6054c595bca26dc2a0432c53369a60a61e3efde0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841868"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Utilizar o serviço Gestão de API do Azure com uma rede virtual interna
Com redes virtuais Azure, a Azure API Management pode gerir APIs não acessíveis na internet. Várias tecnologias VPN estão disponíveis para fazer a ligação. A Gestão API pode ser implantada em dois modos principais dentro de uma rede virtual:
* Externo
* Interno

Quando a API Management se implanta no modo de rede virtual interna, todos os pontos finais do serviço (o gateway proxy, o portal Developer, a gestão direta e o Git) só são visíveis dentro de uma rede virtual a que controla o acesso. Nenhum dos pontos finais do serviço está registado no servidor público do DNS.

> [!NOTE]
> Como não existem entradas de DNS para os pontos finais do serviço, estes pontos finais não estarão acessíveis até que o [DNS esteja configurado](#apim-dns-configuration) para a rede virtual.

Utilizando a Gestão API em modo interno, pode alcançar os seguintes cenários:

* Torne as APIs alojadas no seu datacenter privado de forma segura mente acessível por terceiros fora do mesmo utilizando ligações VPN site-to-site ou Azure ExpressRoute VPN.
* Ative cenários de nuvem híbrida expondo as suas APIs baseadas em nuvem e apis no local através de um portal comum.
* Gerencie as suas APIs alojadas em vários locais geográficos utilizando um único ponto final de gateway.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para realizar os passos descritos neste artigo, deve ter:

+ **Uma subscrição ativa do Azure.**

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Uma instância de **Gestão API Azure.** Para mais informações, consulte Criar uma instância de [Gestão API Azure.](get-started-create-service-instance.md)
+ Quando um serviço de Gestão API é implantado numa rede virtual, é utilizada uma [lista de portas](./api-management-using-with-vnet.md#required-ports) e precisa de ser aberta. 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>Criação de uma Gestão API numa rede virtual interna
O serviço de Gestão API numa rede virtual interna está hospedado por trás de um [equilibrador de carga interna (clássico)](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud). Esta é a única opção disponível e não pode ser alterada.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Ativar uma ligação de rede virtual utilizando o portal Azure

1. Navegue na sua instância de Gestão API Azure no [portal Azure.](https://portal.azure.com/)
2. Selecione **Rede virtual**.
3. Configure a instância de Gestão API a ser implantada dentro da rede virtual.

    ![Menu para a criação de uma Gestão API Azure numa rede virtual interna][api-management-using-internal-vnet-menu]

4. Selecione **Guardar**.

Após o sucesso da implementação, deve ver o endereço IP virtual **privado** e o endereço IP virtual **público** do seu serviço de Gestão API na lâmina de visão geral. O endereço IP virtual **privado** é um endereço IP equilibrado de carga `gateway` `portal`a `management` `scm` partir da subnet delegada da API Management sobre a qual, e pontos finais podem ser acedidos. O endereço IP virtual **público** é utilizado `management` **apenas** para controlar o tráfego de aviões para ponto final sobre o porto 3443 e pode ser bloqueado até à marca de serviço [ApiManagement.][ServiceTags]

![Painel de gestão api com uma rede virtual interna configurada][api-management-internal-vnet-dashboard]

> [!NOTE]
> A consola de teste disponível no Portal Azure não funcionará para o serviço **implementado** internal VNET, uma vez que o Gateway Url não está registado no DNS público. Em vez disso, deve utilizar a consola de teste fornecida no **portal Developer**.

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Ativar uma ligação de rede virtual utilizando cmdlets PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Também pode ativar a conectividade de rede virtual utilizando cmdlets PowerShell.

* Criar um serviço de Gestão API dentro de uma rede virtual: Utilize o cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) para criar um serviço de Gestão API Azure dentro de uma rede virtual e configurá-lo para usar o tipo de rede virtual interna.

* Atualizar uma implementação existente de um serviço de Gestão API dentro de uma rede virtual: Utilize o cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) para mover um serviço de Gestão API existente dentro de uma rede virtual e configurá-lo para usar o tipo de rede virtual interna.

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>Configuração DNS
Quando a API Management está em modo de rede virtual externa, o DNS é gerido pelo Azure. Para o modo de rede virtual interna, tem de gerir o seu próprio encaminhamento.

> [!NOTE]
> O serviço de Gestão API não ouve pedidos provenientes de endereços IP. Apenas responde aos pedidos ao nome do anfitrião configurado nos seus pontos finais de serviço. Estes pontos finais incluem gateway, o portal Azure e o portal Developer, ponto final de gestão direta, e Git.

### <a name="access-on-default-host-names"></a>Acesso aos nomes de anfitriões padrão
Quando cria um serviço de Gestão API, denominado "contosointernalvnet" por exemplo, os seguintes pontos finais de serviço são configurados por padrão:

   * Gateway ou procuração: contosointernalvnet.azure-api.net

   * O portal Developer: contosointernalvnet.portal.azure-api.net

   * O novo portal Developer: contosointernalvnet.developer.azure-api.net

   * Ponto final de gestão direta: contosointernalvnet.management.azure-api.net

   * Contosointernalvnet.scm.azure-api.net

Para aceder a estes pontos finais do serviço de Gestão API, pode criar uma máquina virtual numa subnet ligada à rede virtual em que a API Management é implantada. Assumindo que o endereço IP virtual interno para o seu serviço é de 10.1.0.5, pode mapear o ficheiro dos anfitriões, %SystemDrive%\drivers\etc\hosts, da seguinte forma:

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10.1.0.5 contosointernalvnet.scm.azure-api.net

Em seguida, pode aceder a todos os pontos finais de serviço da máquina virtual que criou.
Se utilizar um servidor DNS personalizado numa rede virtual, também pode criar registos A DNS e aceder a estes pontos finais a partir de qualquer lugar da sua rede virtual.

### <a name="access-on-custom-domain-names"></a>Acesso a nomes de domínio personalizados

1. Se não quiser aceder ao serviço de Gestão API com os nomes de anfitriões predefinidos, pode configurar nomes de domínio personalizados para todos os pontos finais do seu serviço, como mostrado na seguinte imagem:

   ![Criação de um domínio personalizado para gestão de API][api-management-custom-domain-name]

2. Em seguida, pode criar registos no seu servidor DNS para aceder aos pontos finais que só são acessíveis a partir da sua rede virtual.

## <a name="routing"></a><a name="routing"> </a> Encaminhamento

* Será reservado um endereço IP virtual *virtual equilibrado* da gama de subnetes e utilizado para aceder aos pontos finais do serviço de Gestão API a partir da rede virtual. Este endereço IP *privado* pode ser encontrado na lâmina de visão geral para o serviço no portal Azure. Este endereço deve ser registado nos servidores DNS utilizados pela rede virtual.
* Será também reservado um endereço *IP público* equilibrado em carga (VIP) para fornecer acesso ao ponto final do serviço de gestão sobre a porta 3443. Este endereço *IP público* pode ser encontrado na lâmina de visão geral para o serviço no portal Azure. O endereço *IP público* é utilizado apenas para controlar o tráfego de aviões até ao ponto final sobre a `management` porta 3443 e pode ser bloqueado até à marca de serviço [ApiManagement.][ServiceTags]
* Os endereços IP da gama IP da sub-rede (DIP) serão atribuídos a cada VM no serviço e serão utilizados para aceder a recursos dentro da rede virtual. Um endereço IP público (VIP) será usado para aceder a recursos fora da rede virtual. Se forem utilizadas listas de restrições IP para garantir recursos dentro da rede virtual, toda a gama para a subrede onde o serviço de Gestão API é implementado deve ser especificada para conceder ou restringir o acesso do serviço.
* Os endereços IP públicos e privados equilibrados podem ser encontrados na lâmina de visão geral no portal Azure.
* Os endereços IP atribuídos para acesso público e privado podem alterar-se se o serviço for removido e depois adicionados de volta à rede virtual. Se isso acontecer, poderá ser necessário atualizar os registos de DNS, as regras de encaminhamento e as listas de restrições IP dentro da rede virtual.

## <a name="related-content"></a><a name="related-content"> </a>Conteúdo relacionado
Para saber mais, consulte os seguintes artigos:
* [Problemas comuns de configuração da rede ao criar a Azure API Management numa rede virtual][Common network configuration problems]
* [FaQs de rede virtual](../virtual-network/virtual-networks-faq.md)
* [Criar um recorde no DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/security-overview.md#service-tags

