---
title: Como utilizar a Azure API Management com redes virtuais
description: Saiba como configurar uma ligação a uma rede virtual na Azure API Management e aceda a serviços web através dela.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/05/2020
ms.author: apimpm
ms.openlocfilehash: c5a1aaac0edea1e5ab2e6cdf35f91f61eed23db5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374963"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Como utilizar a Azure API Management com redes virtuais
As Redes Virtuais (VNETs) do Azure permitem-lhe colocar quaisquer recursos do Azure numa rede encaminhável sem Internet para a qual controla o acesso. Estas redes podem então ser ligadas às suas redes no local utilizando várias tecnologias VPN. Para saber mais sobre as Redes Virtuais Azure comece com a informação aqui: [Visão geral da rede virtual Azure](../virtual-network/virtual-networks-overview.md).

A Azure API Management pode ser implantada dentro da rede virtual (VNET), para que possa aceder a serviços de backend dentro da rede. O portal de desenvolvimento e o portal API podem ser configurados para serem acessíveis quer a partir da Internet quer apenas dentro da rede virtual.

> [!NOTE]
> O URL do documento de importação da API deve ser alojado num endereço de internet acessível ao público.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para realizar os passos descritos neste artigo, deve ter:

+ Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Um caso DaPIM. Para mais informações, consulte Criar uma instância de [Gestão API Azure.](get-started-create-service-instance.md)

## <a name="enable-vpn"> </a>Ativar a ligação VNET

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Ativar a conectividade VNET utilizando o portal Azure

1. Vá ao [portal Azure](https://portal.azure.com) para encontrar a sua instância de gestão da API. Procure e selecione **serviços de Gestão API.**

2. Escolha a sua instância de Gestão API.

3. Selecione **Rede virtual**.
4. Configure a instância de Gestão API a ser implantada dentro de uma rede Virtual.

    ![Menu de rede virtual da Gestão API][api-management-using-vnet-menu]
5. Selecione o tipo de acesso desejado:

    * **Off**: Este é o padrão. A API Management não é implantada numa rede virtual.

    * **Externo**: O portal de gestão da API e o portal de desenvolvimento estão acessíveis a partir da internet pública através de um equilibrador de carga externo. O portal pode aceder a recursos dentro da rede virtual.

        ![Peering público][api-management-vnet-public]

    * **Interna**: O portal de gestão da API e o portal de desenvolvimento só estão acessíveis a partir da rede virtual através de um equilibrador de carga interna. O portal pode aceder a recursos dentro da rede virtual.

        ![Peering privado][api-management-vnet-private]

6. Se selecionou **Externa** ou **Interna,** verá uma lista de todas as regiões onde o seu serviço de Gestão API está aprovisionado. Escolha um **Local**e, em seguida, escolha a sua **rede Virtual** e **subnet**. A lista de redes virtuais é povoada com redes virtuais clássicas e de Recursos disponíveis nas suas subscrições Azure que estão configuradas na região que está a configurar.

    > [!IMPORTANT]
    > Ao implantar uma instância de Gestão API Azure para um VNET gestor de recursos, o serviço deve estar numa subnet dedicada que não contenha outros recursos, exceto para instâncias de Gestão de API Azure. Se for feita uma tentativa de implantar uma instância de Gestão API Azure para uma subnet VNET do Gestor de Recursos que contenha outros recursos, a implementação falhará.

    Em seguida, selecione **Aplicar**. A página de **rede Virtual** da sua instância de Gestão API é atualizada com as suas novas opções de rede virtual e sub-rede.

    ![Selecione VPN][api-management-setup-vpn-select]

7. Na barra de navegação superior, selecione **Guardar**, e, em seguida, selecione **Aplicar a configuração da rede**.

> [!NOTE]
> O endereço VIP da instância de Gestão API mudará cada vez que o VNET estiver ativado ou desativado.
> O endereço VIP também mudará quando a API Management for transferida de **Externa** para **Interna**, ou vice-versa.
>

> [!IMPORTANT]
> Se remover a Gestão API de um VNET ou alterar o que está implantado, o VNET usado anteriormente pode permanecer bloqueado por até seis horas. Durante este período não será possível eliminar o VNET ou implantar um novo recurso para o mesmo. Este comportamento é verdade para os clientes que usam a versão api 2018-01-01 e anteriormente. Os clientes que utilizam a api-versão 2019-01-01 e, mais tarde, o VNET é libertado assim que o serviço de Gestão aPI associado é eliminado.

## <a name="enable-vnet-powershell"> </a>Ativar a ligação VNET utilizando cmdlets PowerShell
Também pode ativar a conectividade VNET utilizando os cmdlets PowerShell.

* Criar um serviço de **Gestão API dentro de um VNET:** Use o cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) para criar um serviço de Gestão API Azure dentro de um VNET.

* **Implemente um serviço de Gestão API existente dentro**de um VNET : Utilize a cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) para mover um serviço de Gestão de API Azure existente dentro de uma Rede Virtual.

## <a name="connect-vnet"> </a>Ligue-se a um serviço web hospedado dentro de uma Rede virtual
Depois de o seu serviço de Gestão API estar ligado ao VNET, aceder a serviços de backend dentro dele não é diferente de aceder a serviços públicos. Basta digitar o endereço IP local ou o nome do anfitrião (se um servidor DNS estiver configurado para o VNET) do seu serviço web no campo URL do **serviço Web** ao criar um novo API ou editar um existente.

![Adicione API da VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Problemas comuns de configuração da rede
Segue-se uma lista de problemas comuns de configuração que podem ocorrer ao implementar o serviço de Gestão API numa Rede Virtual.

* **Configuração personalizada**do servidor DNS : O serviço de Gestão API depende de vários serviços Azure. Quando a API Management é hospedada num VNET com um servidor DNS personalizado, precisa de resolver os nomes de anfitriões desses serviços Azure. Por favor, siga [esta](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) orientação sobre a configuração personalizada do DNS. Consulte a tabela das portas abaixo e outros requisitos de rede para referência.

> [!IMPORTANT]
> Se planeia utilizar um (s) Servidor(s) Personalizado dNS para o VNET, deverá instalá-lo **antes** de implementar um serviço de Gestão API no mesmo. Caso contrário, precisa de atualizar o serviço de Gestão API sempre que alterar o Servidor(s) DNS, executando a Operação de [Configuração](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/ApplyNetworkConfigurationUpdates) da Rede Aplicada

* **Os portos necessários para**a Gestão da API : O tráfego de entrada e saída para a subnet em que a API Management é implantada pode ser controlado através do Grupo de Segurança da [Rede][Network Security Group]. Se alguma destas portas não estiver disponível, a API Management pode não funcionar corretamente e pode tornar-se inacessível. Ter uma ou mais destas portas bloqueadas é outra questão comum de configuração errada ao utilizar a Gestão API com um VNET.

<a name="required-ports"></a> Quando uma instância de serviço de Gestão API é alojada num VNET, as portas da tabela seguinte são utilizadas.

| Fonte / Porta de Destino(s) | Direção          | Protocolo de transporte |   [Etiquetas de serviço](../virtual-network/security-overview.md#service-tags) <br> Fonte / Destino   | Finalidade (*)                                                 | Tipo de Rede Virtual |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / 80, 443                  | Entrada            | TCP                | INTERNET / VIRTUAL_NETWORK            | Comunicação do cliente à API Management                      | Externo             |
| * / 3443                     | Entrada            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Ponto final de gestão para portal Azure e Powershell         | Externa e Interna  |
| * / 80, 443                  | Saída           | TCP                | VIRTUAL_NETWORK / Armazenamento             | **Dependência do armazenamento azure**                             | Externa e Interna  |
| * / 80, 443                  | Saída           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | Diretório Ativo Azure (se aplicável)                   | Externa e Interna  |
| * / 1433                     | Saída           | TCP                | VIRTUAL_NETWORK / SQL                 | **Acesso a pontos finais Azure SQL**                           | Externa e Interna  |
| * / 5671, 5672, 443          | Saída           | TCP                | VIRTUAL_NETWORK / EventHub            | Dependência para log to event hub política e agente de monitorização | Externa e Interna  |
| * / 445                      | Saída           | TCP                | VIRTUAL_NETWORK / Armazenamento             | Dependência da partilha de ficheiros Azure para o GIT                      | Externa e Interna  |
| * / 1886                     | Saída           | TCP                | VIRTUAL_NETWORK / INTERNET            | Necessário para publicar o estado de saúde à Saúde dos Recursos          | Externa e Interna  |
| * / 443                     | Saída           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Publicar Registos e Métricas de Diagnóstico                        | Externa e Interna  |
| * / 25                       | Saída           | TCP                | VIRTUAL_NETWORK / INTERNET            | Ligue-se ao Relé SMTP para envio de e-mails                    | Externa e Interna  |
| * / 587                      | Saída           | TCP                | VIRTUAL_NETWORK / INTERNET            | Ligue-se ao Relé SMTP para envio de e-mails                    | Externa e Interna  |
| * / 25028                    | Saída           | TCP                | VIRTUAL_NETWORK / INTERNET            | Ligue-se ao Relé SMTP para envio de e-mails                    | Externa e Interna  |
| * / 6381 - 6383              | Entrada e saída | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Access Azure Cache for Redis Instances between RoleInstances          | Externa e Interna  |
| * / *                        | Entrada            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Equilibrador de carga de infraestrutura Azure                          | Externa e Interna  |

>[!IMPORTANT]
> As Portas para as quais o *Objetivo* é **arrojado** são necessárias para que o serviço de Gestão DaPi seja implantado com sucesso. No entanto, o bloqueio das outras portas provocará degradação na capacidade de utilização e monitorização do serviço de funcionamento.

+ **Funcionalidade SSL**: Para permitir a construção e validação da cadeia de certificados SSL, o serviço de Gestão API necessita de conectividade de rede de saída para ocsp.msocsp.com, mscrl.microsoft.com e crl.microsoft.com. Esta dependência não é necessária, se qualquer certificado que enviar para a API Management contenha a cadeia completa para a raiz da AC.

+ **Acesso DNS**: O acesso de saída na porta 53 é necessário para a comunicação com servidores DNS. Se existir um servidor DNS personalizado na outra extremidade de um gateway VPN, o servidor DNS deve ser acessível a partir da subnet que acolhe a Gestão API.

+ **Métricas e Monitorização da Saúde**: Conectividade da rede de saída para os pontos finais de monitorização do Azure, que se resolvem nos seguintes domínios:

    | Ambiente Azure | Pontos Finais                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li>gcs.prod.monitoring.core.windows.net(**novo)**</li><li>prod.warmpath.msftcloudes.com a**ser depreciado**</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li><li>prod3-black.prod3.metrics.nsatc.net</li><li>prod3-red.prod3.metrics.nsatc.net</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`.warm.ingestion.msftcloudes.com onde está eastus2.warm.ingestion.msftcloudes.com `East US 2`</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |
    | Azure China 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |

+ **Relé SMTP**: Conectividade de rede de saída para o Relé SMTP, que se resolve sob o `smtpi-co1.msn.com`hospedeiro , `smtpi-ch1.msn.com`, `smtpi-db3.msn.com`, `smtpi-sin.msn.com` e `ies.global.microsoft.com`

+ **Portal de desenvolvimento CAPTCHA**: Conectividade de rede de saída para o CAPTCHA do portal de desenvolvimento, que se resolve sob os anfitriões `client.hip.live.com` e `partner.hip.live.com`.

+ **Portal Azure Diagnósticos**: Para permitir o fluxo de registos de diagnóstico do portal Azure ao utilizar a extensão de Gestão API a partir de dentro de uma Rede Virtual, é necessário o acesso de saída a `dc.services.visualstudio.com` na porta 443. Isto ajuda a resolver problemas que poderá enfrentar ao utilizar a extensão.

+ Forçar o tráfego de **túneis para firewall on-prem Utilizando a Rota Expressa ou o Aparelho Virtual**da Rede : Uma configuração comum do cliente é definir a sua própria rota padrão (0.0.0.0/0) que obriga todo o tráfego da subnet delegada da API Management a fluir através de uma firewall no local ou para um aparelho virtual da Rede. Este fluxo de tráfego invariavelmente quebra a conectividade com a Azure API Management porque o tráfego de saída está bloqueado no local, ou NAT'd a um conjunto irreconhecível de endereços que já não funcionam com vários pontos finais Azure. A solução requer que faça algumas coisas:

  * Ativar pontos finais de serviço na subnet em que o serviço de Gestão API é implantado. [Os pontos finais][ServiceEndpoints] de serviço precisam de ser ativados para azure Sql, Azure Storage, Azure EventHub e Azure ServiceBus. Permitir pontos finais diretamente da subnet delegada da API Management para estes serviços permite-lhes utilizar a rede de espinha dorsal microsoft Azure, proporcionando um encaminhamento ideal para o tráfego de serviços. Se utilizar os Pontos Finais de Serviço com um túnel forçado a Api Management, o tráfego de serviços do Azure acima não é forçado a fazer túneis. O outro tráfego de dependência do serviço de gestão da API é forçado a fazer túneis e não pode ser perdido ou o serviço de Gestão aPi não funcionaria corretamente.
    
  * Todo o tráfego de aviões de controlo da Internet para o ponto final de gestão do seu serviço de Gestão API são encaminhados através de um conjunto específico de IPs de entrada hospedados pela API Management. Quando o tráfego for acionado por túneis, as respostas não irão mapear simetricamente estes IPs de origem de entrada. Para ultrapassar a limitação, precisamos de adicionar as seguintes rotas definidas pelo utilizador[(UDRs)][UDRs]para orientar o tráfego de volta para Azure, definindo o destino destas rotas hospedeiras para "Internet". O conjunto de IPs de entrada para controlo O tráfego de avião é documentado [Controlo De destinos IP do avião](#control-plane-ips)

  * Para outras dependências de serviço de Gestão API que são túneis de força, deve haver uma maneira de resolver o nome de anfitrião e chegar ao ponto final. Estes incluem
      - Métricas e Monitorização da Saúde
      - Diagnósticos do portal Azure
      - Relé SMTP
      - Portal de desenvolvimento CAPTCHA

## <a name="troubleshooting"> </a>Resolução de problemas
* **Configuração inicial**: Quando a implementação inicial do serviço de Gestão API numa subnet não for bem sucedida, é aconselhável primeiro implantar uma máquina virtual na mesma sub-rede. Próximo ambiente de trabalho remoto na máquina virtual e valida que há conectividade com um de cada recurso abaixo na sua subscrição azure
    * Bolha de armazenamento azure
    * Base de Dados SQL do Azure
    * Mesa de armazenamento azure

  > [!IMPORTANT]
  > Depois de ter validado a conectividade, certifique-se de remover todos os recursos implantados na sub-rede, antes de implementar a Gestão API na sub-rede.

* **Atualizações Incrementais**: Ao efazer alterações na sua rede, consulte a [API NetworkStatus,](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/networkstatus)para verificar se o serviço de Gestão API não perdeu acesso a nenhum dos recursos críticos, dos quais depende. O estado de conectividade deve ser atualizado a cada 15 minutos.

* **Links de Navegação**de Recursos : Ao implantar na subnet vnet estilo Gestor de Recursos, a API Management reserva a subnet, criando uma Ligação de navegação de recursos. Se a sub-rede já contiver um recurso de um fornecedor diferente, a implantação **falhará**. Da mesma forma, quando você move um serviço de Gestão API para uma subnet diferente ou eliminá-lo, removeremos essa ligação de navegação de recursos.

## <a name="subnet-size"></a> Requisito de tamanho de subnet
O Azure reserva alguns endereços IP dentro de cada subnet, e estes endereços não podem ser usados. Os primeiros e últimos endereços IP das subnets estão reservados para conformidade com o protocolo, juntamente com mais três endereços utilizados para os serviços Azure. Para mais informações, consulte [Existem restrições à utilização de endereços IP dentro destas subredes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Para além dos endereços IP utilizados pela infraestrutura Azure VNET, cada instância de Gestão Api na subnet utiliza dois endereços IP por unidade de Premium SKU ou um endereço IP para o Developer SKU. Cada instância reserva um endereço IP adicional para o equilibrador de carga externo. Ao ser implantado na vnet interna, requer um endereço IP adicional para o equilibrante interno de carga.

Dado o cálculo acima do tamanho mínimo da subnet, em que a Gestão API pode ser implantada é /29 que dá três endereços IP utilizáveis.

## <a name="routing"></a> Encaminhamento
+ Um endereço IP público equilibrado em carga (VIP) será reservado para fornecer acesso a todos os pontos finais do serviço.
+ Um endereço IP de uma gama IP sub-rede (DIP) será usado para aceder a recursos dentro da vnet e um endereço IP público (VIP) será usado para aceder a recursos fora da vnet.
+ O endereço IP público equilibrado de carga pode ser encontrado na lâmina Overview/Essentials no portal Azure.

## <a name="limitations"> </a>Limitações
* Uma subrede que contenha instâncias de Gestão API não pode conter outros tipos de recursos Azure.
* A sub-rede e o serviço de Gestão API devem estar na mesma subscrição.
* Uma sub-rede que contenha instâncias de Gestão API não pode ser movida através de subscrições.
* Para implementações multi-regiões da API Management configuradas no modo de rede virtual interna, os utilizadores são responsáveis pela gestão do equilíbrio de carga em várias regiões, uma vez que possuem o encaminhamento.
* A conectividade de um recurso num VNET globalmente peered em outra região para o serviço de Gestão API em modo Interno não funcionará devido à limitação da plataforma. Para mais informações, ver Recursos numa rede virtual não pode comunicar com o [equilíbrio de carga interna azure em rede virtual peered](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)

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
| Azure Public| Ásia Sudeste| 40.90.185.46|
| Azure Public| África do Sul Norte| 102.133.130.197|
| Azure Public| Canadá Central| 52.139.20.34|
| Azure Public| Sul da Coreia do Sul| 40.80.232.185|
| Azure Public| Índia Central| 13.71.49.1|
| Azure Public| E.U.A. Oeste| 13.64.39.16|
| Azure Public| Austrália Sudeste| 20.40.160.107|
| Azure Public| Austrália Central| 20.37.52.67|
| Azure Public| Sul da Índia| 20.44.33.246|
| Azure Public| E.U.A. Central| 13.86.102.66|
| Azure Public| Leste da Austrália| 20.40.125.155|
| Azure Public| E.U.A.Oeste 2| 51.143.127.203|
| Azure Public| LESTE DOS EUA 2 EUAP| 52.253.229.253|
| Azure Public| EUA Centrais EUA| 52.253.159.160|
| Azure Public| E.U.A. Centro-Sul| 20.188.77.119|
| Azure Public| E.U.A. Leste 2| 20.44.72.3|
| Azure Public| Europa do Norte| 52.142.95.35|
| Azure Public| Ásia Leste| 52.139.152.27|
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
| Azure Government| USDoD Central| 52.182.32.132|
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
