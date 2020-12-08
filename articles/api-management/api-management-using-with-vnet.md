---
title: Como utilizar a Gestão de API do Azure com redes virtuais
description: Saiba como configurar uma ligação a uma rede virtual na Azure API Management e aceder aos serviços web através dela.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/22/2020
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: 7af15552a489f36d87204bfefe47e579cc19f6dc
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96778819"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Como utilizar a Gestão de API do Azure com redes virtuais
As Redes Virtuais (VNETs) do Azure permitem-lhe colocar quaisquer recursos do Azure numa rede encaminhável sem Internet para a qual controla o acesso. Estas redes podem então ser ligadas às suas redes no local utilizando várias tecnologias VPN. Para saber mais sobre as Redes Virtuais Azure comece com a informação aqui: [Azure Virtual Network Overview](../virtual-network/virtual-networks-overview.md).

A Azure API Management pode ser implementada dentro da rede virtual (VNET), para que possa aceder a serviços de backend dentro da rede. O portal de desenvolvedores e gateway API, pode ser configurado para ser acessível a partir da Internet ou apenas dentro da rede virtual.

> [!NOTE]
> O URL do documento de importação da API deve ser alojado num endereço de Internet acessível ao público.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para executar os passos descritos neste artigo, deve ter:

+ Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Um caso APIM. Para obter mais informações, consulte [Criar uma instância de Gestão API Azure.](get-started-create-service-instance.md)

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>Ativar a ligação VNET

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Ativar a conectividade VNET utilizando o portal Azure

1. Vá ao [portal Azure](https://portal.azure.com) para encontrar a sua instância de gestão da API. Procure e selecione **serviços de Gestão de API.**

2. Escolha a sua instância de Gestão API.

3. Selecione **Rede virtual**.
4. Configurar o caso de Gestão da API a ser implantado dentro de uma rede Virtual.

    :::image type="content" source="media/api-management-using-with-vnet/api-management-menu-vnet.png" alt-text="Selecione rede virtual no portal Azure.":::
    
5. Selecione o tipo de acesso pretendido:

    * **Off**: Este é o padrão. A API Management não é implantada numa rede virtual.

    * **Externo**: O portal de gateway e developer da API Management são acessíveis a partir da internet pública através de um equilibrador de carga externo. O gateway pode aceder a recursos dentro da rede virtual.

        ![Peering público][api-management-vnet-public]

    * **Interna**: O portal de gateway e developer da API Management só são acessíveis a partir da rede virtual através de um equilibrador de carga interno. O gateway pode aceder a recursos dentro da rede virtual.

        ![Peering privado][api-management-vnet-private]

6. Se selecionou **Externa** ou **Interna,** verá uma lista de todas as regiões onde o seu serviço de Gestão de API é prestado. Escolha uma **localização** e, em seguida, escolha a sua **rede Virtual** e **sub-rede.** A lista de rede virtual é preenchida com redes virtuais clássicas e gestoras de recursos disponíveis nas suas subscrições Azure que estão configuradas na região que está a configurar.

    > [!IMPORTANT]
    > Ao implementar uma instância de Gestão API da Azure para um Gestor de Recursos VNET, o serviço deve estar numa sub-rede dedicada que não contenha outros recursos, exceto para instâncias de Gestão API da Azure. Se for feita uma tentativa de implantar uma instância de Gestão API da Azure para uma sub-rede VNET do Gestor de Recursos que contenha outros recursos, a implementação falhará.

    Em seguida, selecione **Aplicar**. A página **de rede Virtual** da sua instância de Gestão de API é atualizada com as suas novas escolhas de rede virtual e sub-rede.

    :::image type="content" source="media/api-management-using-with-vnet/api-management-using-vnet-select.png" alt-text="Definições de rede virtual no portal.":::

7. Na barra de navegação superior, **selecione Guardar** e, em seguida, selecione **Aplicar a configuração da rede**.

> [!NOTE]
> O endereço VIP da instância de Gestão da API será alterado cada vez que o VNET estiver ativado ou desativado.
> O endereço VIP também será alterado quando a Gestão da API for transferida de **Externa** para **Interna,** ou vice-versa.
>

> [!IMPORTANT]
> Se remover a API Management de um VNET ou alterar a que está implantada, o VNET anteriormente utilizado pode permanecer bloqueado até seis horas. Durante este período não será possível eliminar o VNET ou implantar-lhe um novo recurso. Este comportamento é válido para os clientes que usam a versão API 2018-01-01 e mais cedo. Os clientes que utilizam a versão API 2019-01-01 e, mais tarde, o VNET é libertado assim que o serviço de Gestão da API associado for eliminado.

## <a name="deploy-api-management-into-external-vnet"></a><a name="deploy-apim-external-vnet"> </a>Implementar a Gestão da API em VNET Externo

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-external-vnet%2Fazuredeploy.json)

* **Criar um serviço de Gestão API dentro de um VNET**: Utilize o cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) para criar um serviço de Gestão API Azure dentro de um VNET.

* **Implementar um serviço de Gestão API existente dentro de um VNET**: Utilize a [Cmdlet Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) para mover um serviço de Gestão API Azure existente dentro de uma Rede Virtual.

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>Conecte-se a um serviço web hospedado dentro de uma Rede Virtual
Depois de o seu serviço de Gestão API estar ligado ao VNET, aceder aos serviços de backend no seu interior não é diferente de aceder a serviços públicos. Basta digitar o endereço IP local ou o nome de anfitrião (se um servidor DNS estiver configurado para o VNET) do seu serviço web no campo URL do **serviço Web** ao criar uma nova API ou editar um existente.

![Adicionar API da VPN][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>Problemas de configuração de rede comuns
Segue-se uma lista de problemas comuns de configuração errada que podem ocorrer durante a implantação do serviço de Gestão API numa Rede Virtual.

* **Configuração personalizada do servidor DNS**: O serviço de Gestão API depende de vários serviços Azure. Quando a API Management é hospedada num VNET com um servidor DNS personalizado, precisa de resolver os nomes de anfitrião desses serviços Azure. Por favor, siga [esta](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) orientação sobre a configuração personalizada do DNS. Consulte a tabela das portas abaixo e outros requisitos de rede para referência.

> [!IMPORTANT]
> Se planeia utilizar um(s) Server(s) DNS personalizado para o VNET, deverá alterá-lo **antes** de implantar um serviço de Gestão API no mesmo. Caso contrário, é necessário atualizar o serviço de Gestão API sempre que alterar o(s) Servidor DNS,s, executando a [Operação de Configuração de Rede Aplicada](/rest/api/apimanagement/2019-12-01/apimanagementservice/applynetworkconfigurationupdates)

* **Os portos necessários para a Gestão da API**: Tráfego de entrada e saída na Sub-rede em que a Gestão da API é implantada pode ser controlado utilizando [o Grupo de Segurança da Rede][Network Security Group]. Se alguma destas portas não estiver disponível, a API Management pode não funcionar corretamente e pode tornar-se inacessível. Ter uma ou mais destas portas bloqueadas é outra questão comum de má configuração quando se utiliza a API Management com um VNET.

<a name="required-ports"> </a> Quando uma instância de serviço de Gestão API é hospedada num VNET, as portas da tabela seguinte são utilizadas.

| Fonte / Porto de Destino | Direção          | Protocolo de transporte |   [Etiquetas de serviço](../virtual-network/network-security-groups-overview.md#service-tags) <br> Fonte / Destino   | Finalidade \* ( )                                                 | Tipo de Rede Virtual |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | Entrada            | TCP                | INTERNET / VIRTUAL_NETWORK            | Comunicação do cliente à API Management                      | Externo             |
| * / 3443                     | Entrada            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Ponto final de gestão para o portal Azure e PowerShell         | & Internos Externos  |
| * / 443                  | Saída           | TCP                | VIRTUAL_NETWORK / Armazenamento             | **Dependência do Armazenamento Azure**                             | & Internos Externos  |
| * / 443                  | Saída           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Diretório Ativo Azure](api-management-howto-aad.md) (quando aplicável)                   | & Internos Externos  |
| * / 1433                     | Saída           | TCP                | VIRTUAL_NETWORK / SQL                 | **Acesso aos pontos finais do Azure SQL**                           | & Internos Externos  |
| * / 5671, 5672, 443          | Saída           | TCP                | VIRTUAL_NETWORK / EventHub            | Dependência da política e do monitor do [Log para o Event Hub](api-management-howto-log-event-hubs.md) | & Internos Externos  |
| * / 445                      | Saída           | TCP                | VIRTUAL_NETWORK / Armazenamento             | Dependência da Azure File Share para [GIT](api-management-configuration-repository-git.md)                      | & Internos Externos  |
| * / 443, 12000                     | Saída           | TCP                | VIRTUAL_NETWORK / AzureCloud            | Extensão de Saúde e Monitorização         | & Internos Externos  |
| * / 1886, 443                     | Saída           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Publicar [Registos e Métricas de Diagnóstico,](api-management-howto-use-azure-monitor.md) [Saúde de Recursos](../service-health/resource-health-overview.md) e Insights de [Aplicação](api-management-howto-app-insights.md)                   | & Internos Externos  |
| * / 25, 587, 25028                       | Saída           | TCP                | VIRTUAL_NETWORK / INTERNET            | Ligue ao Relé SMTP para envio de e-mails                    | & Internos Externos  |
| * / 6381 - 6383              | Entrada & Saída | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Acesso Ao Serviço Redis para políticas [cache](api-management-caching-policies.md) entre máquinas         | & Internos Externos  |
| * / 4290              | Entrada & Saída | UDP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Sync Counters para políticas [de limite de](api-management-access-restriction-policies.md#LimitCallRateByKey) taxa entre máquinas         | & Internos Externos  |
| * / *                        | Entrada            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Balanceador de carga de infraestrutura Azure                          | & Internos Externos  |

>[!IMPORTANT]
> Os Portos para os quais o *Objetivo* é **arrojado** são necessários para que o serviço de Gestão da API seja implantado com sucesso. O bloqueio das outras portas, no entanto, provocará **uma degradação** na capacidade de utilização e **monitorização do serviço de funcionamento e fornecerá o SLA comprometido**.

+ **Funcionalidade TLS**: Para permitir a construção e validação da cadeia de certificados TLS/SSL, o serviço de Gestão API necessita de conectividade de rede de saída para ocsp.msocsp.com, mscrl.microsoft.com e crl.microsoft.com. Esta dependência não é necessária, se qualquer certificado que você carregar para a API Management contiver toda a cadeia para a raiz de CA.

+ **ACESSO DNS**: O acesso de saída na porta 53 é necessário para comunicação com servidores DNS. Se existir um servidor DNS personalizado na outra extremidade de um gateway VPN, o servidor DNS deve ser acessível a partir da sub-rede que hospeda a API Management.

+ **Métricas e Monitorização da Saúde**: Conectividade da rede de saída para os pontos finais de monitorização do Azure, que resolvem nos seguintes domínios. Como mostrado na tabela, estes URLs estão representados sob a etiqueta de serviço AzureMonitor para utilização com grupos de segurança de rede.

    | Ambiente Azure | Pontos Finais                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Público      | <ul><li>gcs.prod.monitoring.core.windows.net **new**</li><li>prod.warmpath.msftcloudes.com a **ser depreciada**</li><li>global.prod.microsoftmetrics.com **new**</li><li>global.metrics.nsatc.net a **ser depreciada**</li><li>shoebox2.prod.microsoftmetrics.com **new**</li><li>shoebox2.metrics.nsatc.net a **ser depreciada**</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com **new**</li><li>prod3.metrics.nsatc.net a **ser depreciada**</li><li>prod3-black.prod.microsoftmetrics.com **new**</li><li>prod3-black.prod3.metrics.nsatc.net a **ser depreciada**</li><li>prod3-red.prod.microsoftmetrics.com **new**</li><li>prod3-red.prod3.metrics.nsatc.net a **ser depreciada**</li><li>gcs.prod.warm.ingestion.monitoring.azure.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>global.prod.microsoftmetrics.com **new**</li><li>global.metrics.nsatc.net a **ser depreciada**</li><li>shoebox2.prod.microsoftmetrics.com **new**</li><li>shoebox2.metrics.nsatc.net a **ser depreciada**</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com **new**</li><li>prod3.metrics.nsatc.net a **ser depreciada**</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.us</li></ul>                                                                                                                                                                                                                                                |
    | Azure China 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>global.prod.microsoftmetrics.com **new**</li><li>global.metrics.nsatc.net a **ser depreciada**</li><li>shoebox2.prod.microsoftmetrics.com **new**</li><li>shoebox2.metrics.nsatc.net a **ser depreciada**</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com **new**</li><li>prod3.metrics.nsatc.net a **ser depreciada**</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.cn</li></ul>                                                                                                                                                                                                                                                |

  >[!IMPORTANT]
  > A mudança de clusters acima com a zona do **DNS (nsatc.net** para **.microsoftmetrics.com** é sobretudo uma alteração de DNS. Endereço IP do cluster não será alterado.

+ **Tags de serviço regionais**: As regras NSG que permitem a conectividade de saída às etiquetas de serviço De Armazenamento, SQL e Event Hubs podem utilizar as versões regionais das tags correspondentes à região que contém o exemplo de Gestão da API (por exemplo, Storage.WestUS para uma instância de Gestão de API na região oeste dos EUA). Nas implantações multi-regiões, o NSG em cada região deve permitir o tráfego das etiquetas de serviço para aquela região e para a região primária.

+ **Relé SMTP**: Conectividade de rede de saída para o Relé SMTP, que se resolve sob o hospedeiro `smtpi-co1.msn.com` `smtpi-ch1.msn.com` , `smtpi-db3.msn.com` e `smtpi-sin.msn.com``ies.global.microsoft.com`

+ **Portal de desenvolvimento CAPTCHA**: Conectividade de rede de saída para o CAPTCHA do portal de desenvolvimento, que resolve sob os anfitriões `client.hip.live.com` e `partner.hip.live.com` .

+ **Portal Azure Diagnostics**: Para ativar o fluxo de registos de diagnóstico a partir do portal Azure ao utilizar a extensão de Gestão API a partir de dentro de uma Rede Virtual, é necessário aceder `dc.services.visualstudio.com` à porta 443. Isto ajuda na resolução de problemas que pode enfrentar ao utilizar a extensão.

+ **Azure Load Balancer**: Permitir o pedido de entrada da Tag de Serviço `AZURE_LOAD_BALANCER` não é um requisito para o SKU, uma vez que `Developer` apenas implantamos uma unidade de Compute por trás. Mas a entrada de [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) torna-se crítica ao escalar para sku mais alto como `Premium` , como falha da Sonda de Saúde do Balanceador de Carga, falha uma implantação.

+ **Insights de Aplicação**: Se a monitorização do [Azure Application Insights](api-management-howto-app-insights.md) estiver ativada na Gestão da API, então precisamos de permitir a conectividade de saída ao ponto final da [Telemetria](../azure-monitor/app/ip-addresses.md#outgoing-ports) a partir da Rede Virtual. 

+ **Forçar o tráfego de túneis para as instalações Firewall Utilizando a Rota Expressa ou o Aparelho Virtual de Rede**: Uma configuração comum do cliente é definir a sua própria rota padrão (0.0.0.0/0) que força todo o tráfego da sub-rede delegada pela API Management a fluir através de uma firewall no local ou para um aparelho virtual da Rede. Este fluxo de tráfego invariavelmente quebra a conectividade com a Azure API Management porque o tráfego de saída está bloqueado no local, ou NAT'd para um conjunto irreconhecível de endereços que já não funcionam com vários pontos finais Azure. A solução requer que faça algumas coisas:

  * Ativar os pontos finais de serviço na sub-rede em que o serviço de Gestão API é implantado. [Os pontos finais][ServiceEndpoints] de serviço precisam de ser ativados para Azure Sql, Azure Storage, Azure EventHub e Azure ServiceBus. Ativar pontos finais diretamente da sub-rede delegada da API Management para estes serviços permite-lhes utilizar a rede de espinha dorsal microsoft Azure, fornecendo o encaminhamento ideal para o tráfego de serviços. Se utilizar os Pontos Finais de Serviço com uma Api Management forçada, o tráfego de serviços acima da Azure não é forçado a fazer um túnel. O outro tráfego de dependência do serviço de gestão da API é forçado a fazer um túnel e não pode ser perdido ou o serviço de Gestão da API não funcionaria corretamente.
    
  * Todo o tráfego de aviões de controlo da Internet para o ponto final de gestão do seu serviço de Gestão API é encaminhado através de um conjunto específico de IPs de entrada hospedado pela API Management. Quando o tráfego for for for escavado, as respostas não irão mapear simetricamente para estes IPs de entrada. Para ultrapassar a limitação, precisamos adicionar as seguintes rotas definidas pelo utilizador[(UDRs][UDRs]) para orientar o tráfego de volta para Azure, definindo o destino destas rotas hospedeiras para "Internet". O conjunto de IPs de entrada para controlo tráfego de avião é documentado [Endereços IP do plano de controlo](#control-plane-ips)

  * Para outras dependências de serviços de gestão da API que são forçadas a fazer túneis, deve haver uma maneira de resolver o nome de hospedeiro e chegar ao ponto final. Estes incluem
      - Métricas e Monitorização da Saúde
      - Diagnóstico do portal Azure
      - Relé SMTP
      - Portal de desenvolvedores CAPTCHA

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>Resolução de problemas
* **Configuração inicial**: Quando a implantação inicial do serviço de Gestão API numa sub-rede não for bem sucedida, é aconselhável colocar primeiro uma máquina virtual na mesma sub-rede. Próximo ambiente de trabalho remoto na máquina virtual e valide que há conectividade com um de cada recurso abaixo na sua subscrição Azure
    * Bolha de armazenamento Azure
    * Base de Dados SQL do Azure
    * Mesa de armazenamento Azure

  > [!IMPORTANT]
  > Depois de validar a conectividade, certifique-se de remover todos os recursos implantados na sub-rede, antes de colocar a API Management na sub-rede.

* **Verifique o estado da conectividade da rede**: Depois de implementar a API Management na sub-rede, utilize o portal para verificar a conectividade do seu caso em dependências como o Azure Storage. No portal, no menu esquerdo, em **Implementação e infraestrutura,** selecione **Estado de conectividade da Rede**.

   :::image type="content" source="media/api-management-using-with-vnet/verify-network-connectivity-status.png" alt-text="Verificar o estado da conectividade da rede no portal":::

    * Selecione **Necessário** para rever a conectividade aos serviços Azure necessários para a Gestão da API. Uma falha indica que a instância é incapaz de realizar operações nucleares para gerir as APIs.
    * Selecione **Opcional** para rever a conectividade aos serviços opcionais. Qualquer falha indica apenas que a funcionalidade específica não funcionará (por exemplo, SMTP). Uma falha pode levar à degradação da capacidade de utilização e monitorização da instância de Gestão da API e fornecer o SLA comprometido.

Para resolver problemas de conectividade, reveja [os problemas de configuração da rede comuns](#network-configuration-issues) e corrija as definições de rede necessárias.

* **Atualizações Incrementais**: Ao escruissar a sua rede, consulte a [NetworkStatus API,](/rest/api/apimanagement/2019-12-01/networkstatus)para verificar se o serviço de Gestão API não perdeu o acesso a nenhum dos recursos críticos, dos quais depende. O estado de conectividade deve ser atualizado a cada 15 minutos.

* **Links de navegação de recursos**: Ao implantar na sub-rede vnet do estilo Resource Manager, a API Management reserva a sub-rede, criando uma ligação de navegação de recursos. Se a sub-rede já contiver um recurso de um fornecedor diferente, a implementação **falhará**. Da mesma forma, quando mover um serviço de Gestão API para uma sub-rede diferente ou eliminá-lo, removeremos essa ligação de navegação de recursos.

## <a name="subnet-size-requirement"></a><a name="subnet-size"> </a> Requisito do tamanho da sub-rede
O Azure reserva alguns endereços IP dentro de cada sub-rede, e estes endereços não podem ser utilizados. Os primeiros e últimos endereços IP das sub-redes estão reservados para a conformidade com o protocolo, juntamente com mais três endereços utilizados para os serviços Azure. Para mais informações, consulte [existem restrições à utilização de endereços IP dentro destas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Para além dos endereços IP utilizados pela infraestrutura Azure VNET, cada instância de Gestão Api na sub-rede utiliza dois endereços IP por unidade de SKU Premium ou um endereço IP para o Desenvolvedor SKU. Cada instância reserva um endereço IP adicional para o balançador de carga externo. Ao ser implantado na rede virtual Interna, requer um endereço IP adicional para o equilibrador de carga interno.

Dado o cálculo acima do tamanho mínimo da sub-rede, em que a Gestão da API pode ser implementada é /29 que dá três endereços IP utilizáveis.

Cada unidade de escala adicional da Gestão da API requer mais dois endereços IP.

## <a name="routing"></a><a name="routing"> </a> Encaminhamento
+ Um endereço IP público equilibrado de carga (VIP) será reservado para fornecer acesso a todos os pontos finais de serviço.
+ Um endereço IP de uma gama IP de sub-rede (DIP) será usado para aceder a recursos dentro da vnet e um endereço IP público (VIP) será usado para aceder a recursos fora da rede.
+ O endereço IP público equilibrado pode ser encontrado na lâmina Overview/Essentials no portal Azure.

## <a name="limitations"></a><a name="limitations"> </a>Limitações
* Uma sub-rede que contenha instâncias de Gestão API não pode conter quaisquer outros tipos de recursos Azure.
* A sub-rede e o serviço de Gestão API devem estar na mesma subscrição.
* Uma sub-rede que contenha instâncias de Gestão da API não pode ser movida através de subscrições.
* Para implementações de gestão de API multi-regiões configuradas no modo de rede virtual Interna, os utilizadores são responsáveis pela gestão do equilíbrio da carga em várias regiões, uma vez que são donos do encaminhamento.
* A conectividade de um recurso num VNET globalmente espreitado noutra região para o serviço de Gestão API em modo Interno não funcionará devido à limitação da plataforma. Para obter mais informações, consulte [Recursos numa rede virtual não pode comunicar com o equilibrador interno de carga Azure na rede virtual espreguiçadida](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"> </a> Endereços IP do plano de controlo

Os endereços IP são divididos pelo **Azure Environment**. Ao permitir pedidos de entrada, o endereço IP marcado com **a Global** deve ser permitido juntamente com o Endereço IP específico da **Região.**

| **Ambiente Azure**|   **Região**|  **Endereço IP**|
|-----------------|-------------------------|---------------|
| Azure Público| South Central US (Global)| 104.214.19.224|
| Azure Público| Norte Central DOS EUA (Global)| 52.162.110.80|
| Azure Público| E.U.A. Centro-Oeste| 52.253.135.58|
| Azure Público| Coreia do Sul Central| 40.82.157.167|
| Azure Público| Oeste do Reino Unido| 51.137.136.0|
| Azure Público| Oeste do Japão| 40.81.185.8|
| Azure Público| E.U.A. Centro-Norte| 40.81.47.216|
| Azure Público| Sul do Reino Unido| 51.145.56.125|
| Azure Público| Oeste da Índia| 40.81.89.24|
| Azure Público| E.U.A. Leste| 52.224.186.99|
| Azure Público| Europa Ocidental| 51.145.179.78|
| Azure Público| Leste do Japão| 52.140.238.179|
| Azure Público| França Central| 40.66.60.111|
| Azure Público| Leste do Canadá| 52.139.80.117|
| Azure Público| Uae Norte| 20.46.144.85|
| Azure Público| Sul do Brasil| 191.233.24.179|
| Azure Público| Sudeste do Brasil| 191.232.18.181|
| Azure Público| Sudeste Asiático| 40.90.185.46|
| Azure Público| Norte da África do Sul| 102.133.130.197|
| Azure Público| Canadá Central| 52.139.20.34|
| Azure Público| Sul da Coreia do Sul| 40.80.232.185|
| Azure Público| Índia Central| 13.71.49.1|
| Azure Público| E.U.A. Oeste| 13.64.39.16|
| Azure Público| Sudeste da Austrália| 20.40.160.107|
| Azure Público| Austrália Central| 20.37.52.67|
| Azure Público| Sul da Índia| 20.44.33.246|
| Azure Público| E.U.A. Central| 13.86.102.66|
| Azure Público| Leste da Austrália| 20.40.125.155|
| Azure Público| E.U.A. Oeste 2| 51.143.127.203|
| Azure Público| Leste DOS EUA 2| 52.253.229.253|
| Azure Público| EUA Central EUAP| 52.253.159.160|
| Azure Público| E.U.A. Centro-Sul| 20.188.77.119|
| Azure Público| E.U.A. Leste 2| 20.44.72.3|
| Azure Público| Europa do Norte| 52.142.95.35|
| Azure Público| Ásia Leste| 52.139.152.27|
| Azure Público| Sul de França| 20.39.80.2|
| Azure Público| Suíça Oeste| 51.107.96.8|
| Azure Público| Austrália Central 2| 20.39.99.81|
| Azure Público| Centro dos Emirados Árabes Unidos| 20.37.81.41|
| Azure Público| Suíça Norte| 51.107.0.91|
| Azure Público| Oeste da África do Sul| 102.133.0.79|
| Azure Público| Alemanha Centro-Oeste| 51.116.96.0|
| Azure Público| Alemanha Norte| 51.116.0.0|
| Azure Público| Leste da Noruega| 51.120.2.185|
| Azure Público| Oeste da Noruega| 51.120.130.134|
| Azure China 21Vianet| China Norte (Global)| 139.217.51.16|
| Azure China 21Vianet| China Leste (Global)| 139.217.171.176|
| Azure China 21Vianet| Norte da China| 40.125.137.220|
| Azure China 21Vianet| Leste da China| 40.126.120.30|
| Azure China 21Vianet| China Norte 2| 40.73.41.178|
| Azure China 21Vianet| China Leste 2| 40.73.104.4|
| Azure Government| USGov Virginia (Global)| 52.127.42.160|
| Azure Government| USGov Texas (Global)| 52.127.34.192|
| Azure Government| USGov Virginia| 52.227.222.92|
| Azure Government| USGov Iowa| 13.73.72.21|
| Azure Government| USGov Arizona| 52.244.32.39|
| Azure Government| USGov Texas| 52.243.154.118|
| Azure Government| USDoD Central| 52.182.32.132|
| Azure Government| USDod Leste| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>Conteúdo relacionado
* [Ligar uma Rede Virtual para fazer backend usando o Vpn Gateway](../vpn-gateway/design.md#s2smulti)
* [Ligação de uma Rede Virtual de diferentes modelos de implantação](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Como usar o Inspetor da API para rastrear chamadas na Azure API Management](api-management-howto-api-inspector.md)
* [Rede Virtual Perguntas frequentes](../virtual-network/virtual-networks-faq.md)
* [Etiquetas de serviço](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
