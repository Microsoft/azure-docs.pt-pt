---
title: Como utilizar a API Management do Azure com as redes virtuais
description: Saiba como configurar uma ligação a uma rede virtual nos serviços de web API Management do Azure e o acesso através do mesmo.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: apimpm
ms.openlocfilehash: 98d8f530b91c2b2483d00838cd4001be88e18a6c
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011216"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Como utilizar a API Management do Azure com as redes virtuais
Redes virtuais do Azure (VNETs) permitem-lhe colocar qualquer um dos seus recursos do Azure numa rede de endereçáveis não internet que controlam o acesso a. Estas redes, em seguida, podem ser ligadas às suas redes no local utilizando várias tecnologias VPN. Para saber mais sobre o início de redes virtuais do Azure com as informações aqui: [Descrição geral da rede Virtual do Azure](../virtual-network/virtual-networks-overview.md).

Gestão de API do Azure pode ser implementado dentro da rede virtual (VNET), para que possa aceder aos serviços de back-end dentro da rede. O portal do programador e o gateway de API, pode ser configurado para ser acessível a partir da Internet, ou apenas dentro da rede virtual.

> [!NOTE]
> Gestão de API do Azure suporta clássicas e do Azure VNets do Gestor de recursos.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para efetuar os passos descritos neste artigo, tem de ter:

+ Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Uma instância APIM. Para obter mais informações, consulte [criar uma instância de gestão de API do Azure](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Ativar a ligação de VNET

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Ativar a conectividade VNET com o portal do Azure

1. Navegue para a instância APIM no [portal do Azure](https://portal.azure.com/).
2. Selecione **rede Virtual**.
3. Configure a instância de gestão de API devem ser implantados dentro de uma rede Virtual.

    ![Menu de rede virtual da gestão de API][api-management-using-vnet-menu]
4. Selecione o tipo de acesso desejado:

    * **Externo**: portal de gateway e desenvolvedor de gestão de API são acessíveis a partir da internet pública através de um balanceador de carga externo. O gateway pode aceder a recursos na rede virtual.

    ![Peering público][api-management-vnet-public]

    * **Interno**: portal de gateway e desenvolvedor de gestão de API são acessíveis apenas a partir de dentro da rede virtual através de um balanceador de carga interno. O gateway pode aceder a recursos na rede virtual.

    ![Peering privado][api-management-vnet-private]`

    Agora verá uma lista de todas as regiões onde o seu serviço de gestão de API está aprovisionado. Selecione uma VNET e sub-rede para cada região. A lista é preenchida com clássica e Resource Manager redes virtuais disponíveis nas suas subscrições do Azure que são configuradas na região que está a configurar.

    > [!NOTE]
    > **Ponto final de serviço** no diagrama acima inclui Gateway/Proxy, o portal do Azure, o portal do programador, GIT e o ponto de final de gestão direta.
    > **Ponto final de gestão** no diagrama acima é o ponto final alojado no serviço para gerir a configuração através do portal do Azure e Powershell.
    > Além disso, tenha em atenção que, mesmo que o diagrama mostra os endereços IP dos pontos de extremidade vários, serviço de gestão de API **apenas** responde em seus nomes de anfitriões configurados.

    > [!IMPORTANT]
    > Ao implementar uma instância de gestão de API do Azure a uma VNET do Resource Manager, o serviço tem de ser numa sub-rede dedicada que não contém outros recursos, exceto para instâncias de API Management do Azure. Se for feita uma tentativa para implementar uma instância de gestão de API do Azure a uma sub-rede de VNET do Resource Manager que contém outros recursos, a implementação irá falhar.
    >

    ![Selecione a VPN][api-management-setup-vpn-select]

5. Clique em **guardar** na parte superior do ecrã.

> [!NOTE]
> O endereço VIP da instância de gestão de API irá alterar em cada VNET está ativado ou desativado.
> O endereço VIP também será alterado quando a gestão de API é movida de **externo** ao **interno** , ou vice versa
>

> [!IMPORTANT]
> Se remover a gestão de API a partir de uma VNET ou alterar aquela que é implementada na, a VNET utilizada anteriormente pode permanecer bloqueada até duas horas. Durante este período não é possível eliminar a VNET ou implementar um novo recurso no mesmo.

## <a name="enable-vnet-powershell"> </a>Ativar a ligação de VNET utilizando cmdlets do PowerShell
Também pode ativar a conectividade VNET utilizando os cmdlets do PowerShell

* **Criar um serviço de gestão de API dentro de uma VNET**: Utilize o cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) para criar um serviço de gestão de API do Azure dentro de uma VNET.

* **Implementar um serviço de gestão de API existente dentro de uma VNET**: Utilize o cmdlet [AzApiManagementRegion atualização](/powershell/module/az.apimanagement/update-azapimanagementregion) para mover um serviço de gestão de API do Azure existente numa rede Virtual.

## <a name="connect-vnet"> </a>Ligar a um serviço web hospedado dentro de uma rede virtual
Depois do seu serviço de gestão de API está ligado à VNET, aceder aos serviços de back-end dentro da mesma não é diferente de aceder aos serviços do público. Apenas digitar o endereço IP local ou o nome de anfitrião (se um servidor DNS está configurado para a VNET) do seu serviço web para o **URL do serviço Web** campo ao criar uma nova API ou editar um já existente.

![Adicionar API a partir de VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Problemas comuns de configuração de rede
Segue-se uma lista dos problemas de configurações incorretas comuns que podem ocorrer durante a implementação de serviço de API Management numa rede Virtual.

* **Configuração de servidor DNS personalizado**: O serviço de gestão de API depende de vários serviços do Azure. Quando a gestão de API está alojada numa VNET com um servidor DNS personalizado, tem de resolver os nomes de anfitrião desses serviços do Azure. Siga [isso](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) orientações sobre a configuração de DNS personalizado. Consulte a tabela de portas abaixo e outros requisitos de rede para referência.

> [!IMPORTANT]
> Se planeia utilizar um servidor de DNS personalizado (es) para a VNET, deve configurá-lo **antes de** implementar um serviço de gestão de API para o mesmo. Caso contrário, tem de atualizar o serviço de gestão de API sempre que alterar o servidor ou servidores DNS executando o [aplicar a operação de configuração de rede](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Portas necessárias para a gestão de API**: Tráfego de entrada e saída para a sub-rede na qual está implementada a gestão de API pode ser controlado através de [grupo de segurança de rede][Network Security Group]. Se qualquer uma destas portas não estão disponíveis, a gestão de API poderá não funcionar corretamente e poderá tornar-se inacessível. Ter uma ou mais destas portas bloqueadas é o outro problema de configuração incorreta comum, ao utilizar a gestão de API com uma VNET.

Quando uma instância de serviço de gestão de API está alojada numa VNET, as portas na tabela seguinte são utilizadas.

| Origem / porta de destino (s) | Direção          | Protocolo de transporte |   [Etiquetas de serviço](../virtual-network/security-overview.md#service-tags) <br> Origem / destino   | Finalidade (*)                                                 | Tipo de rede virtual |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / 80, 443                  | Entrada            | TCP                | INTERNET / VIRTUAL_NETWORK            | Comunicação do cliente para gestão de API                      | Externo             |
| * / 3443                     | Entrada            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Ponto final de gestão para o portal do Azure e Powershell         | Externo e interno  |
| * / 80, 443                  | Saída           | TCP                | VIRTUAL_NETWORK / armazenamento             | **Dependência do armazenamento do Azure**                             | Externo e interno  |
| * / 80, 443                  | Saída           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | O Azure Active Directory (quando aplicável)                   | Externo e interno  |
| * / 1433                     | Saída           | TCP                | VIRTUAL_NETWORK / SQL                 | **Acesso a pontos finais do SQL do Azure**                           | Externo e interno  |
| * / 5672                     | Saída           | TCP                | VIRTUAL_NETWORK / EventHub            | Dependência para o registo de política do Hub de eventos e o agente de monitorização | Externo e interno  |
| * / 445                      | Saída           | TCP                | VIRTUAL_NETWORK / armazenamento             | Dependência na partilha de ficheiros do Azure para o GIT                      | Externo e interno  |
| * / 1886                     | Saída           | TCP                | VIRTUAL_NETWORK / INTERNET            | Necessário para publicar o estado de funcionamento para Estado de funcionamento do recurso          | Externo e interno  |
| * / 443                     | Saída           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Publicar o diagnóstico de registos e métricas                        | Externo e interno  |
| * / 25                       | Saída           | TCP                | VIRTUAL_NETWORK / INTERNET            | Ligar para o reencaminhamento de SMTP para enviar emails                    | Externo e interno  |
| * / 587                      | Saída           | TCP                | VIRTUAL_NETWORK / INTERNET            | Ligar para o reencaminhamento de SMTP para enviar emails                    | Externo e interno  |
| * / 25028                    | Saída           | TCP                | VIRTUAL_NETWORK / INTERNET            | Ligar para o reencaminhamento de SMTP para enviar emails                    | Externo e interno  |
| * / 6381 - 6383              | Entrada e saída | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Aceder a Cache do Azure para instâncias de Redis entre RoleInstances          | Externo e interno  |
| * / *                        | Entrada            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Balanceador de carga da infraestrutura do Azure                          | Externo e interno  |

>[!IMPORTANT]
> As portas para o qual o *finalidade* é **negrito** são necessárias para o serviço de gestão de API devem ser implantados com êxito. Bloquear as outras portas entretanto fará com que uma degradação na capacidade de utilizar e monitorizar o serviço em execução.

+ **Uma funcionalidade SSL**: Para ativar a criação de cadeia de certificado SSL e a validação a gestão de API de serviço necessita de conectividade de rede de saída ocsp.msocsp.com, mscrl.microsoft.com e crl.microsoft.com. Esta dependência não é necessária, se algum dos certificados carregados para a gestão de API contêm a cadeia completa para a raiz de AC.

+ **Acesso DNS**: Acesso de saída na porta 53 é necessário para a comunicação com servidores DNS. Se existir um servidor DNS personalizado na outra extremidade de um gateway VPN, o servidor DNS tem de ser acessível a partir da sub-rede de gestão de API de hospedagem.

+ **Métricas e monitorização de estado de funcionamento**: Conectividade de rede de saída para monitorização do Azure pontos finais, que resolver sob os seguintes domínios:

    | Azure Environment | Pontos Finais                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Público do Azure      | <ul><li>prod.warmpath.msftcloudes.com</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li><li>prod3-black.prod3.metrics.nsatc.net</li><li>prod3-red.prod3.metrics.nsatc.net</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`. warm.ingestion.msftcloudes.com onde `East US 2` é eastus2.warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |
    | Azure China       | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |

+ **Reencaminhamento de SMTP**: Conectividade de rede de saída para o reencaminhamento de SMTP, o qual resolve em anfitrião `ies.global.microsoft.com`.

+ **Portal do programador CAPTCHA**: Conectividade de rede de saída para o CAPTCHA do portal do programador, que é resolvido em anfitrião `client.hip.live.com`.

+ **Portal do Azure Diagnostics**: Para ativar o fluxo de registos de diagnóstico do portal do Azure ao utilizar a extensão de gestão de API de dentro de uma rede Virtual, o acesso de saída `dc.services.visualstudio.com` na porta 443 é necessária. Isto ajuda a resolução de problemas, que poderá deparar ao utilizar a extensão.

+ **Configuração de rota rápida**: Uma configuração de cliente comum é definir sua própria rota predefinida (0.0.0.0/0) que força o tráfego de Internet de saída para o fluxo em vez disso, no local. Este fluxo de tráfego quebra Invariavelmente conectividade com a API Management do Azure, porque o tráfego de saída está bloqueado no local ou o NAT iria para um conjunto irreconhecível de endereços que já não funcionam com vários pontos de extremidade do Azure. A solução é definir uma (ou mais) rotas definidas pelo utilizador ([UDRs][UDRs]) na sub-rede que contém a gestão de API do Azure. Um UDR define as rotas de sub-rede específica que serão cumpridas em vez da rota predefinida.

    Se possível, é recomendado que utilize a seguinte configuração:

     * A configuração de ExpressRoute anuncia 0.0.0.0/0 e por padrão, force túneis de todo o tráfego de saída no local.
     * O UDR aplicado à sub-rede que contém a gestão de API do Azure define 0.0.0.0/0 com um tipo de próximo salto de Internet.

    O efeito combinado uma dessas etapas é que o nível de sub-rede UDR tem precedência sobre o ExpressRoute imposição de túnel, que garante o acesso de Internet de saída da gestão de API do Azure.

+ **Encaminhamento através de aplicações virtuais de rede**: As configurações que utilizam um UDR com uma rota predefinida (0.0.0.0/0) para encaminhar internet destinado tráfego da gestão de API sub-rede através de uma aplicação virtual de rede em execução no Azure irá bloquear o tráfego de gestão proveniente de Internet para o serviço de gestão de API instância implantada dentro da sub-rede da rede virtual. Esta configuração não é suportada.

>[!WARNING]
>Gestão de API do Azure não é suportado com configurações do ExpressRoute que **incorretamente anunciem transversalmente rotas do caminho de peering público para o caminho de peering privado**. Configurações do ExpressRoute com peering público configurado, receberá anúncios de rota da Microsoft para um grande conjunto de intervalos de endereços IP do Microsoft Azure. Se estes intervalos de endereços forem incorretamente anunciados transversalmente no caminho de peering privado, o resultado final é que todos os pacotes de rede de saída da sub-rede da instância de gestão de API do Azure estão incorretamente imposição de túnel para rede no local de um cliente infraestrutura. Este fluxo de rede quebra a API Management do Azure. A solução para esse problema é parar anunciar transversalmente rotas do caminho de peering público para o caminho de peering privado.


## <a name="troubleshooting"> </a>Resolução de problemas
* **Inicial configuração**: Quando a implementação inicial do serviço de gestão de API numa sub-rede não tiver êxito, recomenda-se primeiro implantem uma máquina virtual na mesma sub-rede. Seguinte ambiente de trabalho remoto à máquina virtual e confirme que existe conectividade a uma de cada recurso abaixo na sua subscrição do azure
    * BLOBs de armazenamento do Azure
    * Base de Dados SQL do Azure
    * Tabela de armazenamento do Azure

 > [!IMPORTANT]
 > Depois de validar a conectividade, certifique-se remover todos os recursos implementados na sub-rede, antes de implementar a gestão de API para a sub-rede.

* **As atualizações incrementais**: Ao fazer alterações à sua rede, consulte [NetworkStatus API](https://docs.microsoft.com/rest/api/apimanagement/networkstatus)para confirmar que o serviço de gestão de API não perdeu acesso a qualquer um dos recursos críticos que ele depende. O estado de conectividade, deverão ser atualizado a cada 15 minutos.

* **Ligações de navegação de recursos**: Ao implementar numa sub-rede de vnet do Resource Manager estilo, gestão de API reserva-se a sub-rede, através da criação de uma ligação de navegação de recursos. Se a sub-rede já contém um recurso de um fornecedor diferente, implementação irá **falhar**. Da mesma forma, quando move um serviço de gestão de API para outra sub-rede ou eliminá-lo, podemos remover essa ligação de navegação de recursos.

## <a name="subnet-size"> </a> Requisito de tamanho de sub-rede
O Azure reserva alguns endereços IP em cada sub-rede e não não possível utilizar estes endereços. Os endereços IP primeiros e últimos das sub-redes são reservados para conformidade com o protocolo, juntamente com três outros endereços utilizados para serviços do Azure. Para obter mais informações, consulte [existem restrições sobre como utilizar endereços IP dentro destas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Além dos endereços IP usados pela infra-estrutura de VNET do Azure, cada instância de gestão de Api na sub-rede utiliza dois endereços IP por unidade de Premium SKU ou um endereço IP para o SKU de programador. Cada instância de reserva-se um endereço IP adicional para o Balanceador de carga externo. Ao implementar numa vnet interna, requer um endereço IP adicional para o Balanceador de carga interno.

De acordo com o cálculo acima o tamanho mínimo da sub-rede, no qual pode ser implementada a gestão de API é /29 que dá 3 endereços IP.

## <a name="routing"> </a> Encaminhamento
+ Será reservado a um endereço IP público com balanceamento de carga (VIP) para fornecer acesso a todos os pontos finais de serviço.
+ Um endereço IP de um intervalo de sub-rede IP (DIP) será utilizado para aceder aos recursos dentro da vnet e um endereço IP público (VIP) será utilizado para aceder aos recursos fora da vnet.
+ IP público com balanceamento de carga pode encontrar o endereço no painel de descrição geral/Essentials no portal do Azure.

## <a name="limitations"> </a>Limitações
* Uma sub-rede que contém instâncias de gestão de API não pode conter outros tipos de recursos do Azure.
* A sub-rede e o serviço de gestão de API tem de ser na mesma subscrição.
* Uma sub-rede que contém instâncias de gestão de API não é possível mover entre subscrições.
* Para implementações de gestão de API de várias regiões configuradas no modo de rede virtual interna, os utilizadores são responsáveis pelo gerenciamento a balanceamento de carga entre várias regiões, como possuem o encaminhamento.
* Conectividade entre um recurso numa VNET em modo de peering global noutra região e o serviço de gestão de API no modo interno não irá funcionar devido à limitação de plataforma. Para obter mais informações, consulte [recursos numa rede virtual não consegue comunicar com o Balanceador de carga interno do Azure na rede virtual em modo de peering](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)


## <a name="related-content"> </a>Conteúdo relacionado
* [Ligar uma rede Virtual para o back-end utilizando o Gateway de Vpn](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Ligar uma rede Virtual a partir de modelos de implementação diferentes](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Como utilizar o Inspetor de API para rastreamento chama-se na gestão de API do Azure](api-management-howto-api-inspector.md)
* [Faq da rede virtual](../virtual-network/virtual-networks-faq.md)
* [Etiquetas de serviço](../virtual-network/security-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
