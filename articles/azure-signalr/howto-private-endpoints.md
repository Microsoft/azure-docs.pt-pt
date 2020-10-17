---
title: Utilizar pontos finais privados
titleSuffix: Azure SignalR Service
description: Visão geral dos pontos finais privados para acesso seguro ao Serviço Azure SignalR a partir de redes virtuais.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 80369883b84ca30cae475235d41addcfba7e52e1
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92152330"
---
# <a name="use-private-endpoints-for-azure-signalr-service"></a>Utilize pontos finais privados para o serviço Azure SignalR

Pode utilizar [pontos finais privados](../private-link/private-endpoint-overview.md) para o seu Serviço Azure SignalR para permitir que os clientes de uma rede virtual (VNet) acedam de forma segura aos dados através de uma [Ligação Privada](../private-link/private-link-overview.md). O ponto final privado utiliza um endereço IP a partir do espaço de endereço VNet para o seu Serviço Azure SignalR. O tráfego de rede entre os clientes do Serviço VNet e Azure SignalR atravessa uma ligação privada na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública.

A utilização de pontos finais privados para o seu Serviço Azure SignalR permite-lhe:

- Proteja o seu Serviço Azure SignalR utilizando o controlo de acesso à rede para bloquear todas as ligações no ponto final público do Serviço Azure SignalR.
- Aumentar a segurança para a rede virtual (VNet), permitindo-lhe bloquear a exfiltração de dados a partir do VNet.
- Ligue-se seguramente aos Serviços Azure SignalR a partir de redes no local que se ligam ao VNet [utilizando VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoutes](../expressroute/expressroute-locations.md) com observação privada.

## <a name="conceptual-overview"></a>Descrição geral conceptual

![Visão geral dos pontos finais privados para o Serviço Azure SignalR](media/howto-private-endpoints/private-endpoint-overview.png)

Um ponto final privado é uma interface de rede especial para um serviço Azure na sua [Rede Virtual](../virtual-network/virtual-networks-overview.md) (VNet). Quando cria um ponto final privado para o seu Serviço Azure SignalR, proporciona uma conectividade segura entre os clientes no seu VNet e o seu serviço. O ponto final privado é atribuído um endereço IP a partir do intervalo de endereço IP do seu VNet. A ligação entre o ponto final privado e o Serviço Azure SignalR utiliza uma ligação privada segura.

As aplicações no VNet podem ligar-se ao Serviço Azure SignalR através do ponto final privado de forma perfeita, **utilizando as mesmas cordas de ligação e mecanismos de autorização que utilizariam de outra forma**. Os pontos finais privados podem ser utilizados com todos os protocolos suportados pelo Serviço Azure SignalR, incluindo a API REST.

Quando cria um ponto final privado para um Serviço Azure SignalR no seu VNet, é enviado um pedido de consentimento para aprovação ao proprietário do Serviço Azure SignalR. Se o utilizador que solicita a criação do ponto final privado for também proprietário do Serviço Azure SignalR, este pedido de consentimento é automaticamente aprovado.

Os proprietários do Serviço Azure SignalR podem gerir os pedidos de consentimento e os pontos finais privados, através do separador '*Pontos finais Privados'* para o Serviço Azure SignalR no [portal Azure](https://portal.azure.com).

> [!TIP]
> Se pretender restringir o acesso ao seu Serviço Azure SignalR apenas através do ponto final privado, [configunja o Controlo de Acesso à Rede](howto-network-access-control.md#managing-network-access-control) para negar ou controlar o acesso através do ponto final público.

### <a name="connecting-to-private-endpoints"></a>Ligação a pontos finais privados

Os clientes de um VNet que utilizem o ponto final privado devem utilizar a mesma cadeia de ligação para o Serviço Azure SignalR, como clientes que se ligam ao ponto final público. Contamos com a resolução DNS para encaminhar automaticamente as ligações do VNet para o Serviço Azure SignalR através de um link privado.

> [!IMPORTANT]
> Utilize o mesmo fio de ligação para ligar ao Serviço Azure SignalR utilizando pontos finais privados, como utilizaria de outra forma. Por favor, não se ligue ao Serviço Azure SignalR utilizando o seu `privatelink` URL de subdomínio.

Criamos uma [zona de DNS privada](../dns/private-dns-overview.md) anexada ao VNet com as atualizações necessárias para os pontos finais privados, por padrão. No entanto, se estiver a utilizar o seu próprio servidor DNS, poderá ter de es fazer alterações adicionais na sua configuração DNS. A secção sobre [as alterações do DNS](#dns-changes-for-private-endpoints) abaixo descreve as atualizações necessárias para os pontos finais privados.

## <a name="dns-changes-for-private-endpoints"></a>DNS alterações para pontos finais privados

Quando cria um ponto final privado, o registo de recursos DNS CNAME para o seu Serviço Azure SignalR é atualizado para um pseudónimo num subdomínio com o prefixo `privatelink` . Por padrão, também criamos uma [zona privada de DNS,](../dns/private-dns-overview.md)correspondente ao `privatelink` subdomínio, com os registos de recursos DNS A para os pontos finais privados.

Quando resolve o nome de domínio do serviço Azure SignalR de fora do VNet com o ponto final privado, resolve-se para o ponto final público do Serviço Azure SignalR. Quando resolvido a partir do VNet que hospeda o ponto final privado, o nome de domínio resolve-se para o endereço IP do ponto final privado.

Para o exemplo ilustrado acima, os registos de recursos DNS para o Serviço Azure SignalR 'foobar', quando resolvidos de fora do VNet que alberga o ponto final privado, serão:

| Nome                                                  | Tipo  | Valor                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | \<Azure SignalR Service public IP address\>           |

Como mencionado anteriormente, pode negar ou controlar o acesso a clientes fora do VNet através do ponto final público utilizando o controlo de acesso à rede.

Os registos de recursos DNS para 'foobar', quando resolvidos por um cliente no VNet que acolhe o ponto final privado, serão:

| Nome                                                  | Tipo  | Valor                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | 10.1.1.5                                              |

Esta abordagem permite o acesso ao Serviço Azure SignalR **utilizando a mesma cadeia de ligação** para clientes no VNet que hospedam os pontos finais privados, bem como clientes fora do VNet.

Se estiver a utilizar um servidor DNS personalizado na sua rede, os clientes devem ser capazes de resolver o FQDN para o ponto final do Serviço Azure SignalR para o endereço IP do ponto final privado. Deverá configurar o seu servidor DNS para delegar o seu subdomínio de ligação privada para a zona privada de DNS para o VNet, ou configurar os registos A `foobar.privatelink.service.signalr.net` para com o endereço IP do ponto final privado.

> [!TIP]
> Ao utilizar um servidor DNS personalizado ou no local, deverá configurar o seu servidor DNS para resolver o nome do Serviço Azure SignalR no `privatelink` subdomínio para o endereço IP do ponto final privado. Pode fazê-lo delegando o `privatelink` subdomínio para a zona privada de DNS do VNet, ou configurando a zona DNS no seu servidor DNS e adicionando os registos DNS A.

O nome recomendado da zona DNS para pontos finais privados para o Serviço Azure SignalR é: `privatelink.service.signalr.net` .

Para obter mais informações sobre a configuração do seu próprio servidor DNS para suportar pontos finais privados, consulte os seguintes artigos:

- [Name resolution for resources in Azure virtual networks](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) (Resolução de nomes para recursos em redes virtuais do Azure)
- [Configuração DE DNS para pontos finais privados](../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="create-a-private-endpoint"></a>Criar um ponto final privado

### <a name="create-a-private-endpoint-along-with-a-new-azure-signalr-service-in-the-azure-portal"></a>Criar um ponto final privado juntamente com um novo Serviço Azure SignalR no portal Azure

1. Ao criar um novo Serviço Azure SignalR, selecione **o separador de rede.** Escolha **o ponto final privado** como método de conectividade.

    ![Criar serviço Azure SignalR - separador de rede](media/howto-private-endpoints/portal-create-blade-networking-tab.png)

1. Clique em **Adicionar**. Preencha a subscrição, grupo de recursos, localização, nome para o novo ponto final privado. Escolha uma rede virtual e uma sub-rede.

    ![Criar serviço Azure SignalR - Adicionar ponto final privado](media/howto-private-endpoints/portal-create-blade-add-private-endpoint.png)

1. Clique em **Rever + criar**.

### <a name="create-a-private-endpoint-for-an-existing-azure-signalr-service-in-the-azure-portal"></a>Criar um ponto final privado para um serviço Azure SignalR existente no portal Azure

1. Vá ao Serviço Azure SignalR.

1. Clique no menu de definições chamado **Ligações de ponto final privado .**

1. Clique no botão **+ Ponto final privado** na parte superior.

    ![Lâmina de ligações de ponto final privado](media/howto-private-endpoints/portal-private-endpoint-connections-blade.png)

1. Preencha a subscrição, o grupo de recursos, o nome de recursos e a região para o novo ponto final privado.
    
    ![Criar ponto final privado - Básicos](media/howto-private-endpoints/portal-create-private-endpoint-basics.png)

1. Escolha o recurso target Azure SignalR Service.

    ![Criar ponto final privado - Recurso](media/howto-private-endpoints/portal-create-private-endpoint-resource.png)

1. Escolha a rede virtual alvo

    ![Criar ponto final privado - Configuração](media/howto-private-endpoints/portal-create-private-endpoint-configuration.png)

1. Clique em **Rever + criar**.

### <a name="create-a-private-endpoint-using-azure-cli"></a>Criar um ponto final privado usando O Azure CLI

1. Login no Azure CLI
    ```console
    az login
    ```
1. Selecione a sua subscrição do Azure
    ```console
    az account set --subscription {AZURE SUBSCRIPTION ID}
    ```
1. Criar um novo Grupo de Recursos
    ```console
    az group create -n {RG} -l {AZURE REGION}
    ```
1. Registe o Microsoft.SignalRService como fornecedor
    ```console
    az provider register -n Microsoft.SignalRService
    ```
1. Criar um novo Serviço Azure SignalR
    ```console
    az signalr create --name {NAME} --resource-group {RG} --location {AZURE REGION} --sku Standard_S1
    ```
1. Criar uma Rede Virtual
    ```console
    az network vnet create --resource-group {RG} --name {vNet NAME} --location {AZURE REGION}
    ```
1. Adicionar uma sub-rede
    ```console
    az network vnet subnet create --resource-group {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}
    ```
1. Desativar políticas de rede virtual
    ```console
    az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
    ```
1. Adicione uma zona privada de DNS
    ```console
    az network private-dns zone create --resource-group {RG} --name privatelink.service.signalr.net
    ```
1. Ligue a Zona Privada de DNS à Rede Virtual
    ```console
    az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.service.signalr.net --name {dnsZoneLinkName} --registration-enabled true
    ```
1. Criar um ponto final privado (aprovar automaticamente)
    ```console
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION}
    ```
1. Criar um ponto final privado (Aprovação de pedido manual)
    ```console
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
    ```
1. Mostrar estado de ligação
    ```console
    az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
    ```

## <a name="pricing"></a>Preços

Para obter detalhes sobre os preços, consulte [os preços do Azure Private Link](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Problemas Conhecidos

Tenha em mente as seguintes questões conhecidas sobre pontos finais privados para o Serviço Azure SignalR

### <a name="free-tier"></a>Escalão gratuito

Não é possível criar nenhum ponto final privado para o serviço Azure SignalR de nível gratuito.

### <a name="access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Restrições de acesso para clientes em VNets com pontos finais privados

Os clientes em VNets com pontos finais privados existentes enfrentam constrangimentos ao aceder a outras instâncias do Serviço Azure SignalR que têm pontos finais privados. Por exemplo, suponha que um VNet N1 tenha um ponto final privado para uma instância de serviço Azure SignalR S1. Se o Azure SignalR Service S2 tiver um ponto final privado num VNet N2, então os clientes do VNet N1 também devem aceder ao Serviço Azure SignalR S2 utilizando um ponto final privado. Se o Azure SignalR Service S2 não tiver quaisquer pontos finais privados, então os clientes do VNet N1 podem aceder ao Serviço Azure SignalR nessa conta sem um ponto final privado.

Este constrangimento é o resultado das alterações ao DNS efetuadas quando o Serviço Azure SignalR S2 cria um ponto final privado.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Network Security Group rules for subnets with private endpoints (Regras do Grupo de Segurança de Rede para sub-redes com pontos de extremidade privados)

Atualmente, não é possível configurar as regras [do Grupo de Segurança da Rede](../virtual-network/network-security-groups-overview.md) (NSG) e as rotas definidas pelo utilizador para pontos finais privados. As regras NSG aplicadas à sub-rede que acolhe o ponto final privado são aplicadas ao ponto final privado. Uma solução limitada para esta questão é implementar as suas regras de acesso a pontos finais privados nas sub-redes de origem, embora esta abordagem possa exigir uma maior sobrecarga de gestão.

## <a name="next-steps"></a>Passos seguintes

- [Configure controlo de acesso à rede](howto-network-access-control.md)