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
ms.date: 02/03/2020
ms.author: apimpm
ms.openlocfilehash: 59839df1e67c5ea7f18df373ad0530a2ea740209
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77030902"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Como usar o gerenciamento de API do Azure com redes virtuais
As Redes Virtuais (VNETs) do Azure permitem-lhe colocar quaisquer recursos do Azure numa rede encaminhável sem Internet para a qual controla o acesso. Essas redes podem ser conectadas às suas redes locais usando várias tecnologias de VPN. Para saber mais sobre as Redes Virtuais Azure comece com a informação aqui: [Visão geral da rede virtual Azure](../virtual-network/virtual-networks-overview.md).

O gerenciamento de API do Azure pode ser implantado dentro da rede virtual (VNET), para que possa acessar os serviços de back-end na rede. O portal do desenvolvedor e o gateway de API podem ser configurados para serem acessíveis da Internet ou apenas dentro da rede virtual.

> [!NOTE]
> A URL do documento de importação de API deve ser hospedada em um endereço de Internet acessível publicamente.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas neste artigo, você deve ter:

+ Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Uma instância de APIM. Para mais informações, consulte Criar uma instância de [Gestão API Azure.](get-started-create-service-instance.md)

## <a name="enable-vpn"> </a>Ativar a ligação VNET

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Habilitar conectividade VNET usando o portal do Azure

1. Vá ao [portal Azure](https://portal.azure.com) para encontrar a sua instância de gestão da API. Procure e selecione **serviços de Gestão API.**

2. Escolha sua instância de gerenciamento de API.

3. Selecione **Rede virtual**.
4. Configure a instância de gerenciamento de API a ser implantada dentro de uma rede virtual.

    ![Menu de rede virtual do gerenciamento de API][api-management-using-vnet-menu]
5. Selecione o tipo de acesso desejado:

    * **Off**: Este é o padrão. O gerenciamento de API não é implantado em uma rede virtual.

    * **Externo**: O portal de gestão da API e o portal de desenvolvimento estão acessíveis a partir da internet pública através de um equilibrador de carga externo. O gateway pode acessar recursos na rede virtual.

        ![Peering público][api-management-vnet-public]

    * **Interna**: O portal de gestão da API e o portal de desenvolvimento só estão acessíveis a partir da rede virtual através de um equilibrador de carga interna. O gateway pode acessar recursos na rede virtual.

        ![Peering privado][api-management-vnet-private]

6. Se selecionou **Externa** ou **Interna,** verá uma lista de todas as regiões onde o seu serviço de Gestão API está aprovisionado. Escolha um **Local**e, em seguida, escolha a sua **rede Virtual** e **subnet**. A lista de redes virtuais é preenchida com as redes virtuais clássicas e do Resource Manager disponíveis em suas assinaturas do Azure configuradas na região que você está configurando.

    > [!IMPORTANT]
    > Ao implantar uma instância de gerenciamento de API do Azure em uma VNET do Resource Manager, o serviço deve estar em uma sub-rede dedicada que não contém outros recursos, exceto para instâncias de gerenciamento de API do Azure. Se for feita uma tentativa de implantar uma instância de gerenciamento de API do Azure em uma sub-rede VNET do Resource Manager que contenha outros recursos, a implantação falhará.

    Em seguida, selecione **Aplicar**. A página de **rede Virtual** da sua instância de Gestão API é atualizada com as suas novas opções de rede virtual e sub-rede.

    ![Selecionar VPN][api-management-setup-vpn-select]

7. Na barra de navegação superior, selecione **Guardar**, e, em seguida, selecione **Aplicar a configuração da rede**.

> [!NOTE]
> O endereço VIP da instância de gerenciamento de API será alterado toda vez que a VNET estiver habilitada ou desabilitada.
> O endereço VIP também mudará quando a API Management for transferida de **Externa** para **Interna**, ou vice-versa.
>

> [!IMPORTANT]
> Se você remover o gerenciamento de API de uma VNET ou alterar aquela em que ela está implantada, a VNET usada anteriormente poderá permanecer bloqueada por até seis horas. Durante esse período, não será possível excluir a VNET ou implantar um novo recurso nele. Esse comportamento é verdadeiro para clientes que usam a API-versão 2018-01-01 e anterior. Clientes que usam a API-Version 2019-01-01 e posterior, a VNET é liberada assim que o serviço de gerenciamento de API associado é excluído.

## <a name="enable-vnet-powershell"> </a>Ativar a ligação VNET utilizando cmdlets PowerShell
Você também pode habilitar a conectividade VNET usando os cmdlets do PowerShell.

* Criar um serviço de **Gestão API dentro de um VNET:** Use o cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) para criar um serviço de Gestão API Azure dentro de um VNET.

* **Implemente um serviço de Gestão API existente dentro**de um VNET : Utilize a cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) para mover um serviço de Gestão de API Azure existente dentro de uma Rede Virtual.

## <a name="connect-vnet"> </a>Ligue-se a um serviço web hospedado dentro de uma Rede virtual
Depois que o serviço de gerenciamento de API estiver conectado à VNET, o acesso aos serviços de back-end dentro dele não será diferente do acesso aos serviços públicos. Basta digitar o endereço IP local ou o nome do anfitrião (se um servidor DNS estiver configurado para o VNET) do seu serviço web no campo URL do **serviço Web** ao criar um novo API ou editar um existente.

![Adicionar API da VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Problemas comuns de configuração da rede
Veja a seguir uma lista de problemas comuns de configuração incorreta que podem ocorrer durante a implantação do serviço de gerenciamento de API em uma rede virtual.

* **Configuração personalizada**do servidor DNS : O serviço de Gestão API depende de vários serviços Azure. Quando o gerenciamento de API é hospedado em uma VNET com um servidor DNS personalizado, ele precisa resolver os nomes de host desses serviços do Azure. Por favor, siga [esta](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) orientação sobre a configuração personalizada do DNS. Consulte a tabela portas abaixo e outros requisitos de rede para referência.

> [!IMPORTANT]
> Se planeia utilizar um (s) Servidor(s) Personalizado dNS para o VNET, deverá instalá-lo **antes** de implementar um serviço de Gestão API no mesmo. Caso contrário, precisa de atualizar o serviço de Gestão API sempre que alterar o Servidor(s) DNS, executando a Operação de [Configuração](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/ApplyNetworkConfigurationUpdates) da Rede Aplicada

* **Os portos necessários para**a Gestão da API : O tráfego de entrada e saída para a subnet em que a API Management é implantada pode ser controlado através do Grupo de Segurança da [Rede][Network Security Group]. Se qualquer uma dessas portas estiver indisponível, o gerenciamento de API poderá não funcionar corretamente e poderá se tornar inacessível. Ter uma ou mais dessas portas bloqueadas é outro problema comum de configuração incorreta ao usar o gerenciamento de API com uma VNET.

<a name="required-ports"></a> Quando uma instância de serviço de Gestão API é alojada num VNET, as portas da tabela seguinte são utilizadas.

| Porta (s) de origem/destino | Direção          | Protocolo de transporte |   [Etiquetas de serviço](../virtual-network/security-overview.md#service-tags) <br> Origem/destino   | Finalidade (*)                                                 | Tipo de rede virtual |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / 80, 443                  | Entrada            | TCP                | INTERNET / VIRTUAL_NETWORK            | Comunicação do cliente com o gerenciamento de API                      | Externo             |
| * / 3443                     | Entrada            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Ponto de extremidade de gerenciamento para portal do Azure e PowerShell         | Interno de & externo  |
| * / 80, 443                  | Saída           | TCP                | VIRTUAL_NETWORK / Armazenamento             | **Dependência do armazenamento azure**                             | Interno de & externo  |
| * / 80, 443                  | Saída           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | Azure Active Directory (quando aplicável)                   | Interno de & externo  |
| * / 1433                     | Saída           | TCP                | VIRTUAL_NETWORK / SQL                 | **Acesso a pontos finais Azure SQL**                           | Interno de & externo  |
| */5671, 5672, 443          | Saída           | TCP                | VIRTUAL_NETWORK / EventHub            | Dependência de log para política do hub de eventos e agente de monitoramento | Interno de & externo  |
| * / 445                      | Saída           | TCP                | VIRTUAL_NETWORK / Armazenamento             | Dependência no compartilhamento de arquivos do Azure para GIT                      | Interno de & externo  |
| * / 1886                     | Saída           | TCP                | VIRTUAL_NETWORK / INTERNET            | Necessário para publicar o status de integridade para Resource Health          | Interno de & externo  |
| */443                     | Saída           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Publicar logs e métricas de diagnóstico                        | Interno de & externo  |
| */25                       | Saída           | TCP                | VIRTUAL_NETWORK / INTERNET            | Conectar-se à retransmissão de SMTP para enviar emails                    | Interno de & externo  |
| */587                      | Saída           | TCP                | VIRTUAL_NETWORK / INTERNET            | Conectar-se à retransmissão de SMTP para enviar emails                    | Interno de & externo  |
| * / 25028                    | Saída           | TCP                | VIRTUAL_NETWORK / INTERNET            | Conectar-se à retransmissão de SMTP para enviar emails                    | Interno de & externo  |
| * / 6381 - 6383              | Saída de & de entrada | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Acessar o cache do Azure para instâncias de Redis entre RoleInstances          | Interno de & externo  |
| * / *                        | Entrada            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Load Balancer de infraestrutura do Azure                          | Interno de & externo  |

>[!IMPORTANT]
> As Portas para as quais o *Objetivo* é **arrojado** são necessárias para que o serviço de Gestão DaPi seja implantado com sucesso. No entanto, bloquear as outras portas causará degradação na capacidade de usar e monitorar o serviço em execução.

+ **Funcionalidade SSL**: Para permitir a construção e validação da cadeia de certificados SSL, o serviço de Gestão API necessita de conectividade de rede de saída para ocsp.msocsp.com, mscrl.microsoft.com e crl.microsoft.com. Essa dependência não é necessária, se qualquer certificado que você carregar no gerenciamento de API contiver a cadeia completa para a raiz da autoridade de certificação.

+ **Acesso DNS**: O acesso de saída na porta 53 é necessário para a comunicação com servidores DNS. Se um servidor DNS personalizado existir na outra extremidade de um gateway de VPN, o servidor DNS deverá estar acessível a partir da sub-rede que hospeda o gerenciamento de API.

+ **Métricas e Monitorização da Saúde**: Conectividade da rede de saída para os pontos finais de monitorização do Azure, que se resolvem nos seguintes domínios:

    | Ambiente do Azure | Pontos Finais                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li>prod.warmpath.msftcloudes.com</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li><li>prod3-black.prod3.metrics.nsatc.net</li><li>prod3-red.prod3.metrics.nsatc.net</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`.warm.ingestion.msftcloudes.com onde está eastus2.warm.ingestion.msftcloudes.com `East US 2`</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |
    | Azure China 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |

+ **Relé SMTP**: Conectividade de rede de saída para o Relé SMTP, que se resolve sob o `smtpi-co1.msn.com`hospedeiro , `smtpi-ch1.msn.com`, `smtpi-db3.msn.com`, `smtpi-sin.msn.com` e `ies.global.microsoft.com`

+ **Portal de desenvolvimento CAPTCHA**: Conectividade de rede de saída para o CAPTCHA do portal de desenvolvimento, que se resolve sob os anfitriões `client.hip.live.com` e `partner.hip.live.com`.

+ **Portal Azure Diagnósticos**: Para permitir o fluxo de registos de diagnóstico do portal Azure ao utilizar a extensão de Gestão API a partir de dentro de uma Rede Virtual, é necessário o acesso de saída a `dc.services.visualstudio.com` na porta 443. Isso ajuda a solucionar problemas que você pode enfrentar ao usar a extensão.

+ Forçar o tráfego de **túneis para firewall on-prem Utilizando a Rota Expressa ou o Aparelho Virtual**da Rede : Uma configuração comum do cliente é definir a sua própria rota padrão (0.0.0.0/0) que obriga todo o tráfego da subnet delegada da API Management a fluir através de uma firewall no local ou para um aparelho virtual da Rede. Esse fluxo de tráfego invariavelmente interrompe a conectividade com o gerenciamento de API do Azure porque o tráfego de saída é bloqueado localmente ou NAT para um conjunto de endereços irreconhecível que não funciona mais com vários pontos de extremidade do Azure. A solução exige que você faça algumas coisas:

  * Habilite os pontos de extremidade de serviço na sub-rede em que o serviço de gerenciamento de API está implantado. [Os pontos finais][ServiceEndpoints] de serviço precisam de ser ativados para azure Sql, Azure Storage, Azure EventHub e Azure ServiceBus. Habilitar pontos de extremidade diretamente da sub-rede delegada do gerenciamento de API para esses serviços permite que eles usem o Microsoft Azure rede de backbone fornecendo roteamento ideal para o tráfego de serviço. Se você usar pontos de extremidade de serviço com um gerenciamento de API em túnel forçado, o tráfego dos serviços do Azure acima não será forçado em túnel. O outro tráfego de dependência do serviço de gerenciamento de API é forçado e não pode ser perdido ou o serviço de gerenciamento de API não funcionaria corretamente.
    
  * Todo o tráfego do plano de controle da Internet para o ponto de extremidade de gerenciamento do serviço de gerenciamento de API é roteado por meio de um conjunto específico de IPs de entrada hospedados pelo gerenciamento de API. Quando o tráfego é forçado por túnel, as respostas não são mapeadas de volta de forma simétrica para esses IPs de origem de entrada. Para ultrapassar a limitação, precisamos de adicionar as seguintes rotas definidas pelo utilizador[(UDRs)][UDRs]para orientar o tráfego de volta para Azure, definindo o destino destas rotas hospedeiras para "Internet". O conjunto de IPs de entrada para controlo O tráfego de avião é documentado [Controlo De destinos IP do avião](#control-plane-ips)

  * Para outras dependências de serviço de gerenciamento de API que são disparadas em túnel, deve haver uma maneira de resolver o nome de host e entrar em contato com o ponto de extremidade. Isso inclui
      - Monitoramento de integridade e métricas
      - Diagnóstico de portal do Azure
      - Retransmissão de SMTP
      - Portal do desenvolvedor CAPTCHA

## <a name="troubleshooting"> </a>Resolução de problemas
* **Configuração inicial**: Quando a implementação inicial do serviço de Gestão API numa subnet não for bem sucedida, é aconselhável primeiro implantar uma máquina virtual na mesma sub-rede. Na próxima área de trabalho remota na máquina virtual e valide se há conectividade com um de cada recurso abaixo em sua assinatura do Azure
    * Blob de armazenamento do Azure
    * Base de Dados SQL do Azure
    * Tabela de armazenamento do Azure

  > [!IMPORTANT]
  > Depois de validar a conectividade, certifique-se de remover todos os recursos implantados na sub-rede antes de implantar o gerenciamento de API na sub-rede.

* **Atualizações Incrementais**: Ao efazer alterações na sua rede, consulte a [API NetworkStatus,](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/networkstatus)para verificar se o serviço de Gestão API não perdeu acesso a nenhum dos recursos críticos, dos quais depende. O status de conectividade deve ser atualizado a cada 15 minutos.

* **Links de Navegação**de Recursos : Ao implantar na subnet vnet estilo Gestor de Recursos, a API Management reserva a subnet, criando uma Ligação de navegação de recursos. Se a sub-rede já contiver um recurso de um fornecedor diferente, a implantação **falhará**. Da mesma forma, ao mover um serviço de gerenciamento de API para uma sub-rede diferente ou excluí-lo, removeremos esse link de navegação do recurso.

## <a name="subnet-size"></a> Requisito de tamanho de subnet
O Azure reserva alguns endereços IP dentro de cada sub-rede e esses endereços não podem ser usados. O primeiro e o último endereços IP das sub-redes são reservados para a conformidade do protocolo, juntamente com mais três endereços usados para os serviços do Azure. Para mais informações, consulte [Existem restrições à utilização de endereços IP dentro destas subredes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Além dos endereços IP usados pela infraestrutura de VNET do Azure, cada instância de gerenciamento de API na sub-rede usa dois endereços IP por unidade de SKU Premium ou um endereço IP para a SKU do desenvolvedor. Cada instância reserva um endereço IP adicional para o balanceador externo de carga. Ao implantar em vnet interna, ele requer um endereço IP adicional para o balanceador de carga interno.

Dado o cálculo acima do tamanho mínimo da subnet, em que a Gestão API pode ser implantada é /29 que dá três endereços IP utilizáveis.

## <a name="routing"></a> Encaminhamento
+ Um endereço IP público com balanceamento de carga (VIP) será reservado para fornecer acesso a todos os pontos de extremidade de serviço.
+ Um endereço IP de um intervalo de IP de sub-rede (DIP) será usado para acessar recursos na vnet e um endereço IP público (VIP) será usado para acessar recursos fora da vnet.
+ O endereço IP público com balanceamento de carga pode ser encontrado na folha visão geral/Essentials no portal do Azure.

## <a name="limitations"> </a>Limitações
* Uma sub-rede que contém instâncias de gerenciamento de API não pode conter nenhum outro tipo de recurso do Azure.
* A sub-rede e o serviço de gerenciamento de API devem estar na mesma assinatura.
* Uma sub-rede que contém instâncias de gerenciamento de API não pode ser movida entre assinaturas.
* Para implantações de gerenciamento de API de várias regiões configuradas no modo de rede virtual interna, os usuários são responsáveis por gerenciar o balanceamento de carga em várias regiões, pois elas possuem o roteamento.
* A conectividade de um recurso em uma VNET emparelhada globalmente em outra região para o serviço de gerenciamento de API no modo interno não funcionará devido à limitação da plataforma. Para mais informações, ver Recursos numa rede virtual não pode comunicar com o [equilíbrio de carga interna azure em rede virtual peered](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)

## <a name="control-plane-ips"></a> Controlo de endereços IP do avião

Os endereços IP são divididos pelo **Azure Environment**. Ao permitir pedidos de entrada endereço IP marcado com **a Global** deve ser listado em branco juntamente com o endereço IP específico **da Região.**

| **Ambiente Azure**|   **Região**|  **Endereço IP**|
|-----------------|-------------------------|---------------|
| Azure Public| Centro-Sul dos EUA (Global)| 104.214.19.224|
| Azure Public| Centro-Norte dos EUA (Global)| 52.162.110.80|
| Azure Public| E.U.A. Centro-Oeste| 52.253.135.58|
| Azure Public| Coreia do Sul Central| 40.82.157.167|
| Azure Public| Oeste do Reino Unido| 51.137.136.0|
| Azure Public| Oeste do Japão| 40.81.185.8|
| Azure Public| E.U.A. Centro-Norte| 40.81.47.216|
| Azure Public| Sul do Reino Unido| 51.145.56.125|
| Azure Public| Oeste da Índia| 40.81.89.24|
| Azure Public| E.U.A. Leste| 52.224.186.99|
| Azure Public| Europa Ocidental| 51.145.179.78|
| Azure Public| Leste do Japão| 52.140.238.179|
| Azure Public| França Central| 40.66.60.111|
| Azure Public| Leste do Canadá| 52.139.80.117|
| Azure Public| Emirados Unidos norte| 20.46.144.85|
| Azure Public| Sul do Brasil| 191.233.24.179|
| Azure Public| Sudeste asiático| 40.90.185.46|
| Azure Public| África do Sul Norte| 102.133.130.197|
| Azure Public| Canadá Central| 52.139.20.34|
| Azure Public| Sul da Coreia do Sul| 40.80.232.185|
| Azure Public| Índia Central| 13.71.49.1|
| Azure Public| E.U.A. Oeste| 13.64.39.16|
| Azure Public| Sudeste da Austrália| 20.40.160.107|
| Azure Public| Austrália Central| 20.37.52.67|
| Azure Public| Sul da Índia| 20.44.33.246|
| Azure Public| E.U.A. Central| 13.86.102.66|
| Azure Public| Leste da Austrália| 20.40.125.155|
| Azure Public| E.U.A.Oeste 2| 51.143.127.203|
| Azure Public| Leste dos EUA 2 EUAP| 52.253.229.253|
| Azure Public| EUA Central EUAP| 52.253.159.160|
| Azure Public| E.U.A. Centro-Sul| 20.188.77.119|
| Azure Public| E.U.A. Leste 2| 20.44.72.3|
| Azure Public| Europa do Norte| 52.142.95.35|
| Azure Public| Ásia Oriental| 52.139.152.27|
| Azure Public| Sul de França| 20.39.80.2|
| Azure Public| Suíça Oeste| 51.107.96.8|
| Azure Public| Austrália Central 2| 20.39.99.81|
| Azure Public| Central dos Emirados Emirados Unidos| 20.37.81.41|
| Azure Public| Suíça Norte| 51.107.0.91|
| Azure Public| África do Sul Ocidental| 102.133.0.79|
| Azure Public| Alemanha West Central| 51.116.96.0|
| Azure Public| Alemanha Norte| 51.116.0.0|
| Azure Public| Noruega Leste| 51.120.2.185|
| Azure Public| Noroeste da Noruega| 51.120.130.134|
| Azure China 21Vianet| China Norte (Global)| 139.217.51.16|
| Azure China 21Vianet| China Leste (Global)| 139.217.171.176|
| Azure China 21Vianet| China Norte| 40.125.137.220|
| Azure China 21Vianet| China Leste| 40.126.120.30|
| Azure China 21Vianet| China Norte 2| 40.73.41.178|
| Azure China 21Vianet| China Leste 2| 40.73.104.4|
| Azure Government| USGov Virginia (Global)| 52.127.42.160|
| Azure Government| USGov Texas (Global)| 52.127.34.192|
| Azure Government| USGov Virginia| 52.227.222.92|
| Azure Government| USGov Iowa| 13.73.72.21|
| Azure Government| USGov Arizona| 52.244.32.39|
| Azure Government| USGov Texas| 52.243.154.118|
| Azure Government| USDoD central| 52.182.32.132|
| Azure Government| USDoD East| 52.181.32.192|

## <a name="related-content"> </a>Conteúdo relacionado
* [Ligar uma Rede Virtual para apoiar usando vpn Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Conectando uma Rede Virtual de diferentes modelos de implementação](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Como usar o Inspetor da API para rastrear chamadas na Gestão da API azure](api-management-howto-api-inspector.md)
* [Rede Virtual Frequentemente feita perguntas](../virtual-network/virtual-networks-faq.md)
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
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/security-overview.md#service-tags
