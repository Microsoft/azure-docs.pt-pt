---
title: Como usar o gerenciamento de API do Azure com redes virtuais
description: Saiba como configurar uma conexão com uma rede virtual no gerenciamento de API do Azure e acessar serviços Web por meio dele.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/13/2019
ms.author: apimpm
ms.openlocfilehash: 26a353251bd85a30ab26c86f3d6b363b0a84e074
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75889535"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Como usar o gerenciamento de API do Azure com redes virtuais
As Redes Virtuais (VNETs) do Azure permitem-lhe colocar quaisquer recursos do Azure numa rede encaminhável sem Internet para a qual controla o acesso. Essas redes podem ser conectadas às suas redes locais usando várias tecnologias de VPN. Para saber mais sobre as redes virtuais do Azure, comece com as informações aqui: [visão geral da rede virtual do Azure](../virtual-network/virtual-networks-overview.md).

O gerenciamento de API do Azure pode ser implantado dentro da rede virtual (VNET), para que possa acessar os serviços de back-end na rede. O portal do desenvolvedor e o gateway de API podem ser configurados para serem acessíveis da Internet ou apenas dentro da rede virtual.

> [!NOTE]
> A URL do documento de importação de API deve ser hospedada em um endereço de Internet acessível publicamente.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas neste artigo, você deve ter:

+ Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Uma instância de APIM. Para obter mais informações, consulte [criar uma instância de gerenciamento de API do Azure](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Habilitar conexão VNET

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Habilitar conectividade VNET usando o portal do Azure

1. Vá para a [portal do Azure](https://portal.azure.com) para localizar sua instância de gerenciamento de API. Procure e selecione **serviços de gerenciamento de API**.

2. Escolha sua instância de gerenciamento de API.

3. Selecione **Rede virtual**.
4. Configure a instância de gerenciamento de API a ser implantada dentro de uma rede virtual.

    ![Menu de rede virtual do gerenciamento de API][api-management-using-vnet-menu]
5. Selecione o tipo de acesso desejado:

    * **Off**: esse é o padrão. O gerenciamento de API não é implantado em uma rede virtual.

    * **Externo**: o gateway de gerenciamento de API e o portal do desenvolvedor podem ser acessados da Internet pública por meio de um balanceador de carga externo. O gateway pode acessar recursos na rede virtual.

        ![Peering público][api-management-vnet-public]

    * **Interno**: o gateway de gerenciamento de API e o portal do desenvolvedor são acessíveis somente de dentro da rede virtual por meio de um balanceador de carga interno. O gateway pode acessar recursos na rede virtual.

        ![Peering privado][api-management-vnet-private]

6. Se você selecionou **externo** ou **interno**, verá uma lista de todas as regiões em que o serviço de gerenciamento de API é provisionado. Escolha um **local**e, em seguida, selecione sua **rede virtual** e **sub-rede**. A lista de redes virtuais é preenchida com as redes virtuais clássicas e do Resource Manager disponíveis em suas assinaturas do Azure configuradas na região que você está configurando.

    > [!IMPORTANT]
    > Ao implantar uma instância de gerenciamento de API do Azure em uma VNET do Resource Manager, o serviço deve estar em uma sub-rede dedicada que não contém outros recursos, exceto para instâncias de gerenciamento de API do Azure. Se for feita uma tentativa de implantar uma instância de gerenciamento de API do Azure em uma sub-rede VNET do Resource Manager que contenha outros recursos, a implantação falhará.

    Em seguida, selecione **Aplicar**. A página **rede virtual** da sua instância de gerenciamento de API é atualizada com as novas opções de rede virtual e sub-rede.

    ![Selecionar VPN][api-management-setup-vpn-select]

7. Na barra de navegação superior, selecione **salvar**e, em seguida, selecione **aplicar configuração de rede**.

> [!NOTE]
> O endereço VIP da instância de gerenciamento de API será alterado toda vez que a VNET estiver habilitada ou desabilitada.
> O endereço VIP também será alterado quando o gerenciamento de API for movido de **externo** para **interno**ou vice-versa.
>

> [!IMPORTANT]
> Se você remover o gerenciamento de API de uma VNET ou alterar aquela em que ela está implantada, a VNET usada anteriormente poderá permanecer bloqueada por até seis horas. Durante esse período, não será possível excluir a VNET ou implantar um novo recurso nele. Esse comportamento é verdadeiro para clientes que usam a API-versão 2018-01-01 e anterior. Clientes que usam a API-Version 2019-01-01 e posterior, a VNET é liberada assim que o serviço de gerenciamento de API associado é excluído.

## <a name="enable-vnet-powershell"> </a>Habilitar conexão VNET usando cmdlets do PowerShell
Você também pode habilitar a conectividade VNET usando os cmdlets do PowerShell.

* **Criar um serviço de gerenciamento de API dentro de uma vnet**: Use o cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) para criar um serviço de gerenciamento de API do Azure dentro de uma vnet.

* **Implantar um serviço de gerenciamento de API existente dentro de uma VNET**: Use o cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) para mover um serviço de gerenciamento de API do Azure existente dentro de uma rede virtual.

## <a name="connect-vnet"> </a>Conectar-se a um serviço Web hospedado em uma rede virtual
Depois que o serviço de gerenciamento de API estiver conectado à VNET, o acesso aos serviços de back-end dentro dele não será diferente do acesso aos serviços públicos. Basta digitar o endereço IP local ou o nome do host (se um servidor DNS estiver configurado para a VNET) do serviço Web no campo **URL do serviço Web** ao criar uma nova API ou editar uma existente.

![Adicionar API da VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Problemas comuns de configuração de rede
Veja a seguir uma lista de problemas comuns de configuração incorreta que podem ocorrer durante a implantação do serviço de gerenciamento de API em uma rede virtual.

* **Configuração do servidor DNS personalizado**: o serviço de gerenciamento de API depende de vários serviços do Azure. Quando o gerenciamento de API é hospedado em uma VNET com um servidor DNS personalizado, ele precisa resolver os nomes de host desses serviços do Azure. Siga [este](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) guia sobre a configuração de DNS personalizada. Consulte a tabela portas abaixo e outros requisitos de rede para referência.

> [!IMPORTANT]
> Se você planeja usar um servidor DNS personalizado para a VNET, você deve configurá-lo **antes** de implantar um serviço de gerenciamento de API nele. Caso contrário, você precisa atualizar o serviço de gerenciamento de API sempre que alterar o (s) servidor (es) DNS executando a [operação aplicar configuração de rede](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Portas necessárias para o gerenciamento de API**: tráfego de entrada e de saída na sub-rede na qual o gerenciamento de API é implantado pode ser controlado usando o [grupo de segurança de rede][Network Security Group]. Se qualquer uma dessas portas estiver indisponível, o gerenciamento de API poderá não funcionar corretamente e poderá se tornar inacessível. Ter uma ou mais dessas portas bloqueadas é outro problema comum de configuração incorreta ao usar o gerenciamento de API com uma VNET.

<a name="required-ports"></a> Quando uma instância de serviço de gerenciamento de API é hospedada em uma VNET, as portas na tabela a seguir são usadas.

| Porta (s) de origem/destino | Direção          | Protocolo de transporte |   [Marcas de serviço](../virtual-network/security-overview.md#service-tags) <br> Origem/destino   | Finalidade (*)                                                 | Tipo de rede virtual |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / 80, 443                  | Entrada            | TCP                | INTERNET / VIRTUAL_NETWORK            | Comunicação do cliente com o gerenciamento de API                      | Externo             |
| * / 3443                     | Entrada            | TCP                | ApiManagement/VIRTUAL_NETWORK       | Ponto de extremidade de gerenciamento para portal do Azure e PowerShell         | Interno de & externo  |
| * / 80, 443                  | Saída           | TCP                | VIRTUAL_NETWORK/armazenamento             | **Dependência no armazenamento do Azure**                             | Interno de & externo  |
| * / 80, 443                  | Saída           | TCP                | VIRTUAL_NETWORK/AzureActiveDirectory | Azure Active Directory (quando aplicável)                   | Interno de & externo  |
| * / 1433                     | Saída           | TCP                | VIRTUAL_NETWORK / SQL                 | **Acesso aos pontos de extremidade do SQL Azure**                           | Interno de & externo  |
| */5671, 5672, 443          | Saída           | TCP                | VIRTUAL_NETWORK/EventHub            | Dependência de log para política do hub de eventos e agente de monitoramento | Interno de & externo  |
| * / 445                      | Saída           | TCP                | VIRTUAL_NETWORK/armazenamento             | Dependência no compartilhamento de arquivos do Azure para GIT                      | Interno de & externo  |
| * / 1886                     | Saída           | TCP                | VIRTUAL_NETWORK / INTERNET            | Necessário para publicar o status de integridade para Resource Health          | Interno de & externo  |
| */443                     | Saída           | TCP                | VIRTUAL_NETWORK/AzureMonitor         | Publicar logs e métricas de diagnóstico                        | Interno de & externo  |
| */25                       | Saída           | TCP                | VIRTUAL_NETWORK / INTERNET            | Conectar-se à retransmissão de SMTP para enviar emails                    | Interno de & externo  |
| */587                      | Saída           | TCP                | VIRTUAL_NETWORK / INTERNET            | Conectar-se à retransmissão de SMTP para enviar emails                    | Interno de & externo  |
| * / 25028                    | Saída           | TCP                | VIRTUAL_NETWORK / INTERNET            | Conectar-se à retransmissão de SMTP para enviar emails                    | Interno de & externo  |
| * / 6381 - 6383              | Saída de & de entrada | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Acessar o cache do Azure para instâncias de Redis entre RoleInstances          | Interno de & externo  |
| * / *                        | Entrada            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Load Balancer de infraestrutura do Azure                          | Interno de & externo  |

>[!IMPORTANT]
> As portas para as quais a *finalidade* é **negrito** são necessárias para que o serviço de gerenciamento de API seja implantado com êxito. No entanto, bloquear as outras portas causará degradação na capacidade de usar e monitorar o serviço em execução.

+ **Funcionalidade SSL**: para habilitar a criação e validação da cadeia de certificados SSL, o serviço de gerenciamento de API precisa de conectividade de rede de saída para ocsp.msocsp.com, mscrl.microsoft.com e CRL.Microsoft.com. Essa dependência não é necessária, se qualquer certificado que você carregar no gerenciamento de API contiver a cadeia completa para a raiz da autoridade de certificação.

+ **Acesso ao DNS**: o acesso de saída na porta 53 é necessário para a comunicação com servidores DNS. Se um servidor DNS personalizado existir na outra extremidade de um gateway de VPN, o servidor DNS deverá estar acessível a partir da sub-rede que hospeda o gerenciamento de API.

+ **Monitoramento de integridade e métricas**: conectividade de rede de saída para pontos de extremidade de monitoramento do Azure, que são resolvidas nos seguintes domínios:

    | Ambiente do Azure | Pontos Finais                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Público      | <ul><li>prod.warmpath.msftcloudes.com</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li><li>prod3-black.prod3.metrics.nsatc.net</li><li>prod3-red.prod3.metrics.nsatc.net</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`. warm.ingestion.msftcloudes.com em que `East US 2` é eastus2.warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |
    | Azure China       | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |

+ **Retransmissão de SMTP**: conectividade de rede de saída para a retransmissão de SMTP, que resolve na `smtpi-co1.msn.com`do host, `smtpi-ch1.msn.com`, `smtpi-db3.msn.com`, `smtpi-sin.msn.com` e `ies.global.microsoft.com`

+ **Portal do desenvolvedor captcha**: conectividade de rede de saída para o captcha do portal do desenvolvedor, que resolve nos hosts `client.hip.live.com` e `partner.hip.live.com`.

+ **Diagnóstico de portal do Azure**: para habilitar o fluxo de logs de diagnóstico do portal do Azure ao usar a extensão de gerenciamento de API de dentro de uma rede virtual, é necessário ter acesso de saída para `dc.services.visualstudio.com` na porta 443. Isso ajuda a solucionar problemas que você pode enfrentar ao usar a extensão.

+ **Forçar o túnel de tráfego para o firewall local usando a rota expressa ou a solução de virtualização de rede**: uma configuração de cliente comum é definir sua própria rota padrão (0.0.0.0/0) que força todo o tráfego da sub-rede delegada do gerenciamento de API para fluir por um firewall local ou para um dispositivo de rede virtual. Esse fluxo de tráfego invariavelmente interrompe a conectividade com o gerenciamento de API do Azure porque o tráfego de saída é bloqueado localmente ou NAT para um conjunto de endereços irreconhecível que não funciona mais com vários pontos de extremidade do Azure. A solução exige que você faça algumas coisas:

  * Habilite os pontos de extremidade de serviço na sub-rede em que o serviço de gerenciamento de API está implantado. Os [pontos de extremidade de serviço][ServiceEndpoints] precisam ser habilitados para SQL do Azure, armazenamento do Azure, EventHub do Azure e ServiceBus do Azure. Habilitar pontos de extremidade diretamente da sub-rede delegada do gerenciamento de API para esses serviços permite que eles usem o Microsoft Azure rede de backbone fornecendo roteamento ideal para o tráfego de serviço. Se você usar pontos de extremidade de serviço com um gerenciamento de API em túnel forçado, o tráfego dos serviços do Azure acima não será forçado em túnel. O outro tráfego de dependência do serviço de gerenciamento de API é forçado e não pode ser perdido ou o serviço de gerenciamento de API não funcionaria corretamente.
    
  * Todo o tráfego do plano de controle da Internet para o ponto de extremidade de gerenciamento do serviço de gerenciamento de API é roteado por meio de um conjunto específico de IPs de entrada hospedados pelo gerenciamento de API. Quando o tráfego é forçado por túnel, as respostas não são mapeadas de volta de forma simétrica para esses IPs de origem de entrada. Para superar a limitação, precisamos adicionar as seguintes[UDRs][UDRs](rotas definidas pelo usuário) para direcionar o tráfego de volta ao Azure definindo o destino dessas rotas de host como "Internet". O conjunto de IPs de entrada para o tráfego do plano de controle é o seguinte:
    
     | Ambiente do Azure | Endereços IP de gerenciamento                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Público      | 13.84.189.17/32, 13.85.22.63/32, 23.96.224.175/32, 23.101.166.38/32, 52.162.110.80/32, 104.214.19.224/32, 52.159.16.255/32, 40.82.157.167/32, 51.137.136.0/32, 40.81.185.8/32, 40.81.47.216/32, 51.145.56.125/32, 40.81.89.24/32, 52.224.186.99/32, 51.145.179.78/32, 52.140.238.179/32, 40.66.60.111/32, 52.139.80.117/32, 20.46.144.85/32, 191.233.24.179/32, 40.90.185.46/32, 102.133.130.197/32, 52.139.20.34/32, 40.80.232.185/32, 13.71.49.1/32, 13.64.39.16/32, 20.40.160.107/32, 20.37.52.67/32, 20.44.33.246/32, 13.86.102.66/32, 20.40.125.155/32, 51.143.127.203/32, 52.253.225.124/32, 52.253.159.160/32, 20.188.77.119/32, 20.44.72.3/32, 52.142.95.35/32, 52.139.152.27/32, 20.39.80.2/32, 51.107.96.8/32, 20.39.99.81/32, 20.37.81.41/32, 51.107.0.91/32, 102.133.0.79/32, 51.116.96.0/32, 51.116.0.0/32 |
    | Azure Government  | 52.127.42.160/32, 52.127.34.192/32 |
    | Azure China       | 139.217.51.16/32, 139.217.171.176/32 |

  * Para outras dependências de serviço de gerenciamento de API que são disparadas em túnel, deve haver uma maneira de resolver o nome de host e entrar em contato com o ponto de extremidade. Isso inclui
      - Monitoramento de integridade e métricas
      - Diagnóstico de portal do Azure
      - Retransmissão de SMTP
      - Portal do desenvolvedor CAPTCHA

## <a name="troubleshooting"> </a>Solução de problemas
* **Configuração inicial**: quando a implantação inicial do serviço de gerenciamento de API em uma sub-rede não é realizada com sucesso, é aconselhável primeiro implantar uma máquina virtual na mesma sub-rede. Na próxima área de trabalho remota na máquina virtual e valide se há conectividade com um de cada recurso abaixo em sua assinatura do Azure
    * Blob de armazenamento do Azure
    * Base de Dados SQL do Azure
    * Tabela de armazenamento do Azure

  > [!IMPORTANT]
  > Depois de validar a conectividade, certifique-se de remover todos os recursos implantados na sub-rede antes de implantar o gerenciamento de API na sub-rede.

* **Atualizações incrementais**: ao fazer alterações em sua rede, consulte [API NetworkStatus](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/networkstatus)para verificar se o serviço de gerenciamento de API não perdeu o acesso a nenhum dos recursos críticos dos quais ele depende. O status de conectividade deve ser atualizado a cada 15 minutos.

* **Links de navegação de recursos**: ao implantar na sub-rede vnet do estilo do Resource Manager, o gerenciamento de API reserva a sub-rede, criando um link de navegação de recurso. Se a sub-rede já contiver um recurso de um provedor diferente, a implantação **falhará**. Da mesma forma, ao mover um serviço de gerenciamento de API para uma sub-rede diferente ou excluí-lo, removeremos esse link de navegação do recurso.

## <a name="subnet-size"></a> Requisito de tamanho de sub-rede
O Azure reserva alguns endereços IP dentro de cada sub-rede e esses endereços não podem ser usados. O primeiro e o último endereços IP das sub-redes são reservados para a conformidade do protocolo, juntamente com mais três endereços usados para os serviços do Azure. Para obter mais informações, consulte [existem restrições sobre como usar endereços IP dentro dessas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Além dos endereços IP usados pela infraestrutura de VNET do Azure, cada instância de gerenciamento de API na sub-rede usa dois endereços IP por unidade de SKU Premium ou um endereço IP para a SKU do desenvolvedor. Cada instância reserva um endereço IP adicional para o balanceador externo de carga. Ao implantar em vnet interna, ele requer um endereço IP adicional para o balanceador de carga interno.

Dado o cálculo acima do tamanho mínimo da sub-rede, no qual o gerenciamento de API pode ser implantado é/29 que fornece três endereços IP.

## <a name="routing"></a> Roteamento do
+ Um endereço IP público com balanceamento de carga (VIP) será reservado para fornecer acesso a todos os pontos de extremidade de serviço.
+ Um endereço IP de um intervalo de IP de sub-rede (DIP) será usado para acessar recursos na vnet e um endereço IP público (VIP) será usado para acessar recursos fora da vnet.
+ O endereço IP público com balanceamento de carga pode ser encontrado na folha visão geral/Essentials no portal do Azure.

## <a name="limitations"> </a>Limitações do
* Uma sub-rede que contém instâncias de gerenciamento de API não pode conter nenhum outro tipo de recurso do Azure.
* A sub-rede e o serviço de gerenciamento de API devem estar na mesma assinatura.
* Uma sub-rede que contém instâncias de gerenciamento de API não pode ser movida entre assinaturas.
* Para implantações de gerenciamento de API de várias regiões configuradas no modo de rede virtual interna, os usuários são responsáveis por gerenciar o balanceamento de carga em várias regiões, pois elas possuem o roteamento.
* A conectividade de um recurso em uma VNET emparelhada globalmente em outra região para o serviço de gerenciamento de API no modo interno não funcionará devido à limitação da plataforma. Para obter mais informações, consulte os [recursos em uma rede virtual não podem se comunicar com o balanceador de carga interno do Azure na rede virtual emparelhada](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)


## <a name="related-content"> </a>Conteúdo relacionado
* [Conectando uma rede virtual ao back-end usando o gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Conectando uma rede virtual de diferentes modelos de implantação](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Como usar o Inspetor de API para rastrear chamadas no gerenciamento de API do Azure](api-management-howto-api-inspector.md)
* [Perguntas frequentes sobre rede virtual](../virtual-network/virtual-networks-faq.md)
* [Marcas de serviço](../virtual-network/security-overview.md#service-tags)

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
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/security-overview.md#service-tags
