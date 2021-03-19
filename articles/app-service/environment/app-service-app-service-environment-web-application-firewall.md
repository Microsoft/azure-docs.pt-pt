---
title: Configure uma WAF
description: Saiba como configurar uma firewall de aplicação web (WAF) em frente ao seu Ambiente de Serviço de Aplicações, seja com o Azure Application Gateway ou com uma WAF de terceiros.
author: ccompy
ms.assetid: a2101291-83ba-4169-98a2-2c0ed9a65e8d
ms.topic: tutorial
ms.date: 03/03/2018
ms.author: stefsch
ms.custom: mvc, seodec18
ms.openlocfilehash: 354568fa3ab3816b643a8f08305ab55868a9b0b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90973714"
---
# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>Configurar uma Firewall de Aplicações Web (WAF) para o Ambiente de Serviço de Aplicações
## <a name="overview"></a>Descrição Geral

As firewalls de aplicações Web (WAF) ajudam a proteger as suas aplicações Web ao inspecionarem o tráfego Web de entrada para bloquear injeções de SQL, Scripting Entre Sites, carregamentos de malware, DDoS de aplicações e outros ataques. Também inspecionam as respostas dos servidores Web do back-end quanto a Prevenção de Perda de Dados (DLP). Em combinação com o isolamento e o dimensionamento adicional que os Ambientes de Serviço de Aplicações proporcionam, as firewalls oferecem um ambiente ideal para alojar aplicações Web criticas das empresas que têm de conseguir suportar pedidos maliciosos e altos volumes de tráfego. O Azure proporciona uma capacidade de WAF com o [Gateway de Aplicação](../../application-gateway/overview.md).  Para saber como integrar o Ambiente de Serviço de Aplicações num Gateway de Aplicação, leia o documento [Integrate your ILB ASE with an Application Gateway](./integrate-with-application-gateway.md) (Integrar o ASE de ILB num Gateway de Aplicação).

Para além do Gateway de Aplicação do Azure, existem muitas opções no mercado, como o [Barracuda WAF for Azure](https://www.barracuda.com/programs/azure), que estão disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf?tab=PlansAndPrice). O resto deste documento centra-se em integrar o seu Ambiente de Serviço de Aplicações num dispositivo Barracuda WAF.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Configuração
Neste documento, vamos configurar o Ambiente de Serviço de Aplicações por trás de várias instâncias com carga balanceada do Barracuda WAF, de modo a que apenas o tráfego do WAF possa chegar ao Ambiente de Serviço de Aplicações e não esteja acessível a partir do DMZ. Também temos o Gestor de Tráfego do Azure à frente das instâncias do Barracuda WAF para fazer o balanceamento de carga entre os datacenters e as regiões do Azure. Um diagrama de alto nível da configuração teria o aspeto da imagem seguinte:

![O diagrama mostra um Gestor de Tráfego Azure opcional que liga a instâncias do Firewall de Aplicações Web, conectando-se à Rede A C L para permitir apenas o tráfego a partir da firewall num Ambiente de Serviço de Aplicações que contém Web, A P I e Mobile App para duas regiões.][Architecture] 

> [!NOTE]
> Com a introdução do [suporte de ILB para o Ambiente de Serviço de Aplicações](app-service-environment-with-internal-load-balancer.md), pode configurar o ASE para não ser acessível a partir do DMZ e apenas acessível à rede privada. 
> 
> 

## <a name="configuring-your-app-service-environment"></a>Configurar o Ambiente de Serviço de Aplicações
Para configurar um Ambiente de Serviço de Aplicações, veja a [nossa documentação](app-service-web-how-to-create-an-app-service-environment.md) sobre o assunto. Quando tiver um Ambiente de Serviço de Aplicações criado, pode criar Aplicações Web, Aplicações API e [Aplicações Móveis](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) nesse ambiente, as quais estarão protegidas por trás do WAF que vamos configurar na próxima secção.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Configurar o Serviço Cloud Barracuda WAF
A Barracuda tem um [artigo detalhado](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) sobre a implementação do respetivo WAF em máquinas virtuais no Azure. Contudo, uma vez que pretendemos ter redundância e não introduzir um ponto único de falha, recomendamos que implemente pelo menos duas instâncias de VMs do WAF no mesmo Serviço Cloud quando seguir essas instruções.

### <a name="adding-endpoints-to-cloud-service"></a>Adicionar Pontos Finais ao Serviço Cloud
Quando tiver duas ou mais instâncias de VMs do WAF no seu Serviço Cloud, pode utilizar o [portal do Azure](https://portal.azure.com/) para adicionar pontos finais HTTP e HTTPS que vão ser utilizados pela sua aplicação, conforme mostrado na imagem abaixo:

![Configurar o Ponto Final][ConfigureEndpoint]

Se as suas aplicações utilizarem outros pontos finais, certifique-se de que os adiciona a esta lista também. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>Configurar o Barracuda WAF através do respetivo Portal de Gestão
O Barracuda WAF utiliza a Porta TCP 8000 para configuração através do respetivo portal de gestão. Se tiver várias instâncias de VMs do WAF, terá de repetir os passos aqui descritos para cada instância de VM. 

> [!NOTE]
> Depois de concluída a configuração do WAF, remova o ponto final TCP/8000 de todas as VMs do WAF, para manter este último seguro.
> 
> 

Adicione o ponto final de gestão, conforme mostrado na imagem seguinte, para configurar o Barracuda WAF.

![Adicionar o Ponto Final de Gestão][AddManagementEndpoint]

Utilize um browser para navegar para o ponto final de gestão do seu Serviço Cloud. Se o nome do seu Serviço Cloud for test.cloudapp.net, navegaria para `http://test.cloudapp.net:8000` para aceder a este ponto final. Veria uma página de início de sessão igual à da imagem seguinte, onde poderia iniciar sessão com as credenciais que especificou na fase de configuração da VM do WAF.

![Página de Início de Sessão de Gestão][ManagementLoginPage]

Depois de iniciar sessão, deverá ver um dashboard, como o da imagem seguinte, que apresenta estatísticas básicas sobre a proteção do WAF.

![Dashboard de Gestão][ManagementDashboard]

Clicar no separador **Services** (Serviços) permite-lhe configurar o WAF para os serviços que aquele está a proteger. Para obter mais detalhes sobre como configurar o Barracuda WAF, veja a [documentação da Barracuda](https://techlib.barracuda.com/waf/getstarted1). No exemplo seguinte, foi configurada uma aplicação do Serviço de Aplicações que serve o tráfego em HTTP e HTTPS.

![Gestão Adicionar Serviços][ManagementAddServices]

> [!NOTE]
> Dependendo da configuração das suas aplicações e das funcionalidades que estão a ser utilizadas no seu Ambiente de Serviço de Aplicações, é necessário encaminhar o tráfego para portas TCP que não sejam 80 e 443, por exemplo, se tiver configuração IP TLS para uma aplicação de Serviço de Aplicações. Para obter uma lista das portas de rede utilizadas em Ambientes de Serviço de Aplicações, veja a secção Portas de Rede da [documentação Control Inbound Traffic](app-service-app-service-environment-control-inbound-traffic.md) (Controlar o Tráfego de Entrada).
> 
> 

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Configurar o Gestor de Tráfego do Microsoft Azure (OPCIONAL)
Se a sua aplicação estiver disponível em várias regiões, recomenda-se que faça o balanceamento de carga da mesma atrás do [Gestor de Tráfego do Azure](../../traffic-manager/traffic-manager-overview.md). Para tal, pode adicionar um ponto final no [portal do Azure](https://portal.azure.com) com o nome do Serviço Cloud do seu WAF no perfil do Gestor de Tráfego, conforme mostrado na imagem seguinte. 

![Ponto Final do Gestor de Tráfego][TrafficManagerEndpoint]

Se a sua aplicação precisar de autenticação, confirme que tem algum recurso que não precise de qualquer autenticação, para que o Gestor de Tráfego emita um ping para a disponibilidade da mesma. Pode configurar o URL na página **Configuração** do [portal do Azure](https://portal.azure.com), conforme mostrado na imagem seguinte:

![Configurar o Gestor de Tráfego][ConfigureTrafficManager]

Para reencaminhar os pings do Gestor de Tráfego do WAF para a aplicação, tem de configurar as Traduções de Web sites no Barracuda WAF para reencaminhar o tráfego para a aplicação, conforme mostrado no exemplo seguinte:

![Traduções de Web sites][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Proteger o Tráfego para o Ambiente de Serviço de Aplicações com Grupos de Segurança de Rede (NSG)
Siga a [documentação Control Inbound Traffic](app-service-app-service-environment-control-inbound-traffic.md) para ver detalhes sobre como limitar o tráfego para o Ambiente de Serviço de Aplicações a partir do WAF ao utilizar apenas o endereço VIP do seu Serviço Cloud. Aqui está uma amostra do comando PowerShell para executar esta tarefa para a porta TCP 80.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
```

Substitua SourceAddressPrefix pelo endereço IP Virtual (VIP) do Serviço Cloud do WAF.

> [!NOTE]
> Se eliminar e recriar o Serviço Cloud, o VIP é alterado. Confirme que atualiza o endereço IP no grupo de recurso de rede se o fizer. 
> 
> 

<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png