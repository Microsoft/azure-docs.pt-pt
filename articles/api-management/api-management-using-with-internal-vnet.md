---
title: Como usar o gerenciamento de API do Azure com redes virtuais internas | Microsoft Docs
description: Saiba como configurar e configurar o gerenciamento de API do Azure em uma rede virtual interna
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
ms.openlocfilehash: 29c86363842299870179b35a0466d2e44d2e56e0
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072190"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Usando o serviço de gerenciamento de API do Azure com uma rede virtual interna
Com as redes virtuais do Azure, o gerenciamento de API do Azure pode gerenciar APIs não acessíveis na Internet. Várias tecnologias de VPN estão disponíveis para fazer a conexão. O gerenciamento de API pode ser implantado em dois modos principais dentro de uma rede virtual:
* Externo
* Interna

Quando o gerenciamento de API é implantado no modo de rede virtual interna, todos os pontos de extremidade de serviço (o gateway de proxy, o portal do desenvolvedor, o gerenciamento direto e o git) só são visíveis em uma rede virtual com a qual você controla o acesso. Nenhum dos pontos de extremidade de serviço está registrado no servidor DNS público.

> [!NOTE]
> Como não há entradas DNS para os pontos de extremidade de serviço, esses pontos de extremidade não estarão acessíveis até que o [DNS seja configurado](#apim-dns-configuration) para a rede virtual.

Usando o gerenciamento de API no modo interno, você pode obter os seguintes cenários:

* Torne as APIs hospedadas em seu datacenter privado acessíveis de forma segura por terceiros, usando conexões VPN site a site ou do Azure ExpressRoute.
* Habilite cenários de nuvem híbrida expondo suas APIs baseadas em nuvem e APIs locais por meio de um gateway comum.
* Gerencie suas APIs hospedadas em várias localizações geográficas usando um único ponto de extremidade de gateway.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas neste artigo, você deve ter:

+ **Uma assinatura ativa do Azure**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Uma instância de gerenciamento de API do Azure**. Para obter mais informações, consulte [criar uma instância de gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Quando um serviço de gerenciamento de API é implantado em uma rede virtual, uma [lista de portas](./api-management-using-with-vnet.md#required-ports) é usada e precisa ser aberta. 

## <a name="enable-vpn"> </a>Criando um gerenciamento de API em uma rede virtual interna
O serviço de gerenciamento de API em uma rede virtual interna é hospedado atrás de um balanceador de [carga interno (clássico)](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud). Essa é a única opção disponível e não pode ser alterada.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Habilitar uma conexão de rede virtual usando o portal do Azure

1. Navegue até sua instância de gerenciamento de API do Azure no [portal do Azure](https://portal.azure.com/).
2. Selecione **Rede virtual**.
3. Configure a instância de gerenciamento de API a ser implantada dentro da rede virtual.

    ![Menu para configurar um gerenciamento de API do Azure em uma rede virtual interna][api-management-using-internal-vnet-menu]

4. Selecione **Guardar**.

Depois que a implantação for realizada com sucesso, você deverá ver o endereço IP virtual **privado** e o endereço IP virtual **público** do seu serviço de gerenciamento de API na folha visão geral. O endereço IP virtual **privado** é um endereço IP com balanceamento de carga de dentro da sub-rede delegada do gerenciamento `gateway`de `portal`API em que os pontos de extremidade, `management` e `scm` podem ser acessados. O endereço IP virtual **público** é usado **somente** para o tráfego do plano `management` de controle para o ponto de extremidade pela porta 3443 e pode ser bloqueado para o [ApiManagement][ServiceTags] servicetag.

![Painel de gerenciamento de API com uma rede virtual interna configurada][api-management-internal-vnet-dashboard]

> [!NOTE]
> O console de teste disponível no portal do Azure não funcionará para o serviço **interno** implantado VNET, pois a URL do gateway não está registrada no DNS público. Em vez disso, você deve usar o console de teste fornecido no **portal do desenvolvedor**.

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Habilitar uma conexão de rede virtual usando cmdlets do PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você também pode habilitar a conectividade de rede virtual usando cmdlets do PowerShell.

* Criar um serviço de gerenciamento de API dentro de uma rede virtual: Use o cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) para criar um serviço de gerenciamento de API do Azure dentro de uma rede virtual e configurá-lo para usar o tipo de rede virtual interna.

* Atualizar uma implantação existente de um serviço de gerenciamento de API dentro de uma rede virtual: Use o cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) para mover um serviço de gerenciamento de API existente dentro de uma rede virtual e configurá-lo para usar o tipo de rede virtual interna.

## <a name="apim-dns-configuration"></a>Configuração de DNS
Quando o gerenciamento de API está no modo de rede virtual externa, o DNS é gerenciado pelo Azure. Para o modo de rede virtual interna, você precisa gerenciar seu próprio roteamento.

> [!NOTE]
> O serviço de gerenciamento de API não ouve solicitações provenientes de endereços IP. Ele responde apenas às solicitações para o nome de host configurado em seus pontos de extremidade de serviço. Esses pontos de extremidade incluem gateway, o portal do Azure e o portal do desenvolvedor, ponto de extremidade de gerenciamento direto e git.

### <a name="access-on-default-host-names"></a>Acesso em nomes de host padrão
Quando você cria um serviço de gerenciamento de API, chamado "contosointernalvnet", por exemplo, os seguintes pontos de extremidade de serviço são configurados por padrão:

   * Gateway ou proxy: contosointernalvnet.azure-api.net

   * O portal do desenvolvedor: contosointernalvnet.portal.azure-api.net

   * O novo portal do desenvolvedor: contosointernalvnet.developer.azure-api.net

   * Ponto de extremidade de gerenciamento direto: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Para acessar esses pontos de extremidade do serviço de gerenciamento de API, você pode criar uma máquina virtual em uma sub-rede conectada à rede virtual na qual o gerenciamento de API é implantado. Supondo que o endereço IP virtual interno do seu serviço seja 10.1.0.5, você pode mapear o arquivo de hosts,%SystemDrive%\drivers\etc\hosts, da seguinte maneira:

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10.1.0.5 contosointernalvnet.scm.azure-api.net

Você pode acessar todos os pontos de extremidade de serviço da máquina virtual que você criou.
Se você usar um servidor DNS personalizado em uma rede virtual, também poderá criar registros DNS e acessar esses pontos de extremidade de qualquer lugar em sua rede virtual.

### <a name="access-on-custom-domain-names"></a>Acesso em nomes de domínio personalizados

1. Se você não quiser acessar o serviço de gerenciamento de API com os nomes de host padrão, poderá configurar nomes de domínio personalizados para todos os pontos de extremidade de serviço, conforme mostrado na imagem a seguir:

   ![Configurando um domínio personalizado para o gerenciamento de API][api-management-custom-domain-name]

2. Em seguida, você pode criar registros no servidor DNS para acessar os pontos de extremidade que só podem ser acessados de dentro de sua rede virtual.

## <a name="routing"></a> Roteamento do

* Um endereço IP virtual *privado* com balanceamento de carga do intervalo de sub-rede será reservado e usado para acessar os pontos de extremidade do serviço de gerenciamento de API de dentro da rede virtual. Esse endereço IP *privado* pode ser encontrado na folha de visão geral do serviço no portal do Azure. Esse endereço deve ser registrado com os servidores DNS usados pela rede virtual.
* Um endereço IP *público* com balanceamento de carga (VIP) também será reservado para fornecer acesso ao ponto de extremidade do serviço de gerenciamento pela porta 3443. Esse endereço IP *público* pode ser encontrado na folha de visão geral do serviço no portal do Azure. O endereço IP *público* é usado somente para o tráfego do plano de `management` controle para o ponto de extremidade pela porta 3443 e pode ser bloqueado para o [ApiManagement][ServiceTags] servicetag.
* Os endereços IP do intervalo de IPS de sub-rede (DIP) serão atribuídos a cada VM no serviço e serão usados para acessar recursos na rede virtual. Um endereço IP público (VIP) será usado para acessar recursos fora da rede virtual. Se as listas de restrição de IP forem usadas para proteger os recursos na rede virtual, todo o intervalo para a sub-rede em que o serviço de gerenciamento de API é implantado deverá ser especificado para conceder ou restringir o acesso do serviço.
* Os endereços IP públicos e privados com balanceamento de carga podem ser encontrados na folha visão geral do portal do Azure.
* Os endereços IP atribuídos para acesso público e privado poderão ser alterados se o serviço for removido do e, em seguida, adicionado novamente à rede virtual. Se isso acontecer, pode ser necessário atualizar os registros de DNS, as regras de roteamento e as listas de restrição de IP dentro da rede virtual.

## <a name="related-content"> </a>Conteúdo relacionado
Para saber mais, confira os seguintes artigos:
* [Problemas comuns de configuração de rede durante a configuração do gerenciamento de API do Azure em uma rede virtual][Common network configuration problems]
* [Perguntas frequentes sobre rede virtual](../virtual-network/virtual-networks-faq.md)
* [Criando um registro no DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/security-overview.md#service-tags

