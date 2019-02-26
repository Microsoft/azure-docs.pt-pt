---
title: Problemas de conectividade e rede para perguntas frequentes do Microsoft Azure Cloud Services | Documentos da Microsoft
description: Este artigo apresenta uma lista de perguntas mais frequentes sobre a conectividade e funcionamento em rede para serviços Cloud do Microsoft Azure.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2018
ms.author: genli
ms.openlocfilehash: 2a46879a6882e6d45e4a7ccce59e4a02feea9005
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805591"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Conectividade e problemas de rede para serviços Cloud do Azure: Perguntas mais frequentes (FAQ)

Este artigo contém perguntas freqüentes sobre problemas de conectividade e rede para [serviços Cloud do Azure](https://azure.microsoft.com/services/cloud-services). Para informações de tamanho, consulte a [página de tamanho de VM dos serviços Cloud](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Eu não é possível reservar um IP num serviço cloud vários VIPS.
Em primeiro lugar, certifique-se de que a instância de máquina virtual que tentar reservar o IP está ativada. Em segundo lugar, certifique-se de que utiliza IPs reservados para implementações de teste e produção. *Isso não é possível* alterar as definições, enquanto a implementação está a ser atualizado.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Como posso usar o ambiente de trabalho remoto quando tenho um NSG?
Adicionar regras ao NSG que permitam o tráfego nas portas **3389** e **20000**. Ambiente de trabalho remoto utiliza a porta **3389**. As instâncias de serviço cloud são carga balanceada, então diretamente não é possível controlar qual a instância para ligar ao. O *RemoteForwarder* e *RemoteAccess* agentes gerir o tráfego de protocolo RDP (Remote Desktop) e permitir que o cliente enviar um cookie RDP e especifique uma instância individual para ligar a. O *RemoteForwarder* e *RemoteAccess* agentes exigem porta **20000** seja aberta, que poderá ser bloqueado se tiver um NSG.

## <a name="can-i-ping-a-cloud-service"></a>Eu ping um serviço em nuvem?

Não, não ao utilizar o normal de "ping" / protocolo ICMP. O protocolo ICMP não é permitido através do Balanceador de carga do Azure.

Para testar a conectividade, recomendamos que faça um ping de porta. Enquanto Ping.exe usa ICMP, pode utilizar outras ferramentas, como o PSPing, Nmap e telnet, para testar a conectividade a uma porta TCP específica.

Para obter mais informações, consulte [utilizar pings de porta em vez de ICMP para testar a conectividade de VM do Azure](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Como posso impedir que recebem centenas de hits de endereços IP desconhecidos, que podem indicar um ataque malicioso ao serviço cloud?
O Azure implementa uma segurança de rede multicamada para proteger os seus serviços de plataforma contra ataques de (DDoS) distribuídos denial-of-service. O sistema de defesa contra DDoS do Azure faz parte contínuo processo de monitorização do Azure, que é continuamente melhorado através de testes de penetração. Este sistema de defesa contra DDoS foi concebido para resistirem não só os ataques de fora, mas também de outros inquilinos do Azure. Para obter mais informações, consulte [segurança de rede do Azure](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Também pode criar uma tarefa de arranque para bloquear seletivamente alguns endereços IP específicos. Para obter mais informações, consulte [bloquear um endereço IP específico](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Quando tento RDP para a minha instância de serviço em nuvem, recebo a mensagem "a conta de utilizador expirou."
Poderá receber a mensagem de erro "Esta conta de utilizador expirou" quando ignorar a data de expiração que está configurada nas definições do RDP. Pode alterar a data de expiração do portal, seguindo estes passos:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com), aceda ao seu serviço cloud e selecione o **ambiente de trabalho remoto** separador.

2. Selecione o **produção** ou **teste** bloco de implementação.

3. Alteração da **expira em** data e, em seguida, guarde a configuração.

Agora deve ser capaz de RDP para o seu computador.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Por que é o Balanceador de carga do Azure não balanceia o tráfego equitativamente?
Para obter informações sobre o funcionamento do Balanceador de carga interno, consulte [novo modo de distribuição do Balanceador de carga do Azure](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

O algoritmo de distribuição utilizado é uma 5 cadeias de identificação (IP de origem, porta de origem, IP de destino, porta de destino e o tipo de protocolo) hash para mapear o tráfego para servidores disponíveis. Ele fornece persistência apenas dentro de uma sessão de transporte. Pacotes na mesma sessão TCP ou UDP são direcionados para a mesma instância de IP (DIP) do Centro de dados por trás do ponto final com balanceamento de carga. Quando o cliente fecha e reabra a ligação ou inicia uma nova sessão do mesmo IP de origem, a porta de origem é alterado e faz com que o tráfego Ir para um ponto de extremidade diferente do DIP.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Como pode redirecionar o tráfego de entrada para o URL predefinido do meu serviço em nuvem para um URL personalizado?

O URL Rewrite module do IIS pode ser utilizado para redirecionar o tráfego que vem para o URL predefinido para o serviço cloud (por exemplo, \*. cloudapp.net) para alguma URL/nome personalizado. Como o URL Rewrite module está ativado nas funções da web por predefinição e suas regras estão configuradas no Web. config da aplicação, está sempre disponível na VM, independentemente de reinicializações/recria a imagem. Para obter mais informações, consulte:

- [Criar regras de reescrita para o URL Rewrite module](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Remover uma ligação padrão](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Como pode posso bloquear/desativar o tráfego de entrada para o URL predefinido do meu serviço em nuvem?

Pode impedir que o tráfego de entrada para o padrão de URL/nome do seu serviço cloud (por exemplo, \*. cloudapp.net). Defina o cabeçalho de anfitrião para um nome DNS personalizado (por exemplo, www.MyCloudService.com) em configuração de ligação de site no ficheiro de definição (*.csdef) do serviço de nuvem, conforme indicado:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
    <WebRole name="MyWebRole" vmsize="Small">
        <Sites>
            <Site name="Web">
            <Bindings>
                <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" />
            </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
</ServiceDefinition>
```

Uma vez que este enlace de cabeçalho de anfitrião é imposta pelo ficheiro csdef, o serviço é acessível apenas através do nome personalizado "www.MyCloudService.com." Todos os pedidos recebidos para a "*. cloudapp.net" domínio sempre falhará. Se utilizar uma sonda SLB personalizada ou um balanceador de carga interno no serviço, o padrão de bloqueio URL/nome do serviço pode interferir com o comportamento de pesquisa.

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Como posso fazer-se de que o endereço IP públicos de um serviço cloud nunca mudará?

Para certificar-se de que o endereço IP destinado ao público do seu serviço cloud (também conhecido como um VIP) nunca mudará para que possa ser convencionalmente na lista de permissões por alguns clientes específicos, é recomendável que tem um IP reservado associado ao mesmo. Caso contrário, o IP virtual fornecido pelo Azure é desalocado da sua subscrição, se eliminar a implementação. Para a operação de troca de VIP efetuada com êxito, terá de IPs reservados individuais para a produção e blocos de teste. Sem eles, a operação de troca falhará. Reservar um endereço IP e associá-lo com o seu serviço de nuvem, veja estes artigos:

- [Reservar o endereço IP de um serviço cloud existente](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [Associar um IP reservado a um serviço cloud utilizando um ficheiro de configuração de serviço](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

Se tiver mais de uma instância para as funções, a associação de RIP ao seu serviço cloud não deve causar qualquer período de inatividade. Em alternativa, pode adicionar o intervalo IP do seu datacenter do Azure. Pode encontrar todos os intervalos de IP do Azure com o [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

Este ficheiro contém os intervalos de endereços do IP (incluindo intervalos de computação, SQL e armazenamento) utilizados nos datacenters do Azure. Um ficheiro atualizado é publicado semanalmente que reflete os intervalos implementados atualmente e as alterações futuras para os intervalos de IP. Os novo intervalos que aparecem no ficheiro não são utilizados nos centros de dados para, pelo menos, uma semana. Transfira o ficheiro. XML novo todas as semanas e efetue as alterações necessárias no seu site para identificar corretamente os serviços em execução no Azure. Os utilizadores do ExpressRoute do Azure poderão constatar que este ficheiro utilizado para atualizar o anúncio BGP do espaço Azure na primeira semana de cada mês.

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Como posso utilizar redes virtuais do Azure Resource Manager com os serviços cloud?

Serviços cloud não não possível colocar em redes virtuais do Azure Resource Manager. Redes virtuais do Gestor de recursos e redes virtuais de implementação clássica, podem ser ligadas através de peering. Para obter mais informações, consulte [peering de rede Virtual](../virtual-network/virtual-network-peering-overview.md).


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Como posso obter a lista de IPs públicos utilizados pelo meus serviços Cloud?

É possível usar o seguinte PS script para obter a lista de IPs públicos para serviços em nuvem na sua subscrição

```powershell
$services = Get-AzureService  | Group-Object -Property ServiceName

foreach ($service in $services)
{
    "Cloud Service '$($service.Name)'"

    $deployment = Get-AzureDeployment -ServiceName $service.Name
    "VIP - " +  $deployment.VirtualIPs[0].Address
    "================================="
}
```
