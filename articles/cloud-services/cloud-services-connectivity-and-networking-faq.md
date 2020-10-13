---
title: Problemas de conectividade e rede
titleSuffix: Azure Cloud Services
description: Este artigo lista as perguntas frequentes sobre conectividade e networking para o Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/23/2018
ms.author: genli
ms.openlocfilehash: 2a27161ca9a218b1f7c0e3fb51c9935438d9778e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91533422"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Questões de conectividade e networking para serviços em nuvem Azure: Perguntas frequentes (PERGUNTAS Frequentes)

Este artigo inclui perguntas frequentes sobre questões de conectividade e networking para [a Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Para obter informações sobre o tamanho, consulte a página de [tamanho dos Serviços cloud VM](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Não posso reservar um IP num serviço de nuvem multi-VIP.
Em primeiro lugar, certifique-se de que a caixa de máquina virtual para a qual tenta reservar o IP está ligada. Em segundo lugar, certifique-se de que utiliza iPs reservados tanto para a encenação como para as instalações de produção. *Não* altere as definições enquanto a colocação estiver a ser melhorada.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Como uso o Remote Desktop quando tenho um NSG?
Adicione regras ao NSG que permitem o tráfego nos portos **3389** e **20000**. O Ambiente de Trabalho Remoto utiliza a porta **3389**. As instâncias de serviço em nuvem são equilibradas, por isso não é possível controlar diretamente a que instância se ligar. Os agentes *RemoteForwarder* e *RemoteAccess* gerem o tráfego do Remote Desktop Protocol (RDP) e permitem ao cliente enviar um cookie RDP e especificar uma instância individual para se ligar. Os agentes *RemoteForwarder* e *RemoteAccess* exigem que a porta **20000** esteja aberta, o que pode ser bloqueado se tiver um NSG.

## <a name="can-i-ping-a-cloud-service"></a>Posso ver um serviço de nuvem?

Não, não usando o protocolo normal "ping"/ICMP. O protocolo ICMP não é permitido através do equilibrador de carga Azure.

Para testar a conectividade, recomendamos que faça um ping de porta. Enquanto Ping.exe utiliza ICMP, pode utilizar outras ferramentas, como PSPing, Nmap e telnet, para testar a conectividade com uma porta TCP específica.

Para obter mais informações, consulte [utilize pings de porta em vez de ICMP para testar a conectividade Azure VM](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Como posso evitar receber milhares de acessos de endereços IP desconhecidos que possam indicar um ataque malicioso ao serviço de nuvem?
O Azure implementa uma segurança de rede multicamadas para proteger os seus serviços de plataforma contra ataques de negação de serviço distribuídos (DDoS). O sistema de defesa Azure DDoS faz parte do processo de monitorização contínua da Azure, que é continuamente melhorado através de testes de penetração. Este sistema de defesa DDoS foi concebido para resistir não só a ataques vindos de fora, mas também de outros inquilinos do Azure. Para mais informações, consulte [a segurança da rede Azure.](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf)

Também pode criar uma tarefa de arranque para bloquear seletivamente alguns endereços IP específicos. Para mais informações, consulte [bloquear um endereço IP específico.](cloud-services-startup-tasks-common.md#block-a-specific-ip-address)

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Quando tento rdp para a minha instância de serviço na nuvem, recebo a mensagem "A conta de utilizador expirou."
Pode receber a mensagem de erro "Esta conta de utilizador expirou" quando contornar a data de validade que está configurada nas definições do PDR. Pode alterar a data de validade a partir do portal seguindo estes passos:

1. Inscreva-se no [portal Azure,](https://portal.azure.com)vá ao seu serviço de cloud e selecione o **separador Ambiente de Trabalho Remoto.**

2. Selecione a ranhura **de implementação de Produção** ou **encenação.**

3. Altere o **Expira na** data e, em seguida, guarde a configuração.

Agora deve ser capaz de rdp para a sua máquina.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Por que motivo o Balanceador de Carga do Azure não balanceia o tráfego equitativamente?
Para obter informações sobre o funcionamento de um equilibrador de carga interno, consulte [o novo modo de distribuição do Azure Load Balancer](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

O algoritmo de distribuição utilizado é um haxixe de 5 tuple (origem IP, porta de origem, IP de destino, porta de destino e tipo de protocolo) para mapear o tráfego para os servidores disponíveis. Só proporciona adesão dentro de uma sessão de transporte. Os pacotes na mesma sessão de TCP ou UDP são direcionados para a mesma instância IP (DIP) do datacenter por trás do ponto final equilibrado de carga. Quando o cliente fecha e reabre a ligação ou inicia uma nova sessão a partir da mesma fonte IP, a porta de origem muda e faz com que o tráfego vá para um ponto final DIP diferente.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Como posso redirecionar o tráfego de entrada para o URL padrão do meu serviço de nuvem para um URL personalizado?

O módulo de reescrita de URL do IIS pode ser usado para redirecionar o tráfego que vem para o URL padrão para o serviço na nuvem (por exemplo, \* .cloudapp.net) para algum nome/URL personalizado. Como o módulo url rewrite é ativado em funções web por padrão e as suas regras estão configuradas na web.config da aplicação, está sempre disponível no VM independentemente de reboots/reimagens. Para mais informações, consulte:

- [Crie regras de reescrita para o módulo de reescrita de URL](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Remover um link predefinido](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Como posso bloquear/desativar o tráfego de entrada para o URL padrão do meu serviço na nuvem?

Pode impedir a entrada de tráfego no URL/nome predefinido do seu serviço na nuvem (por exemplo, \* .cloudapp.net). Desabrague o cabeçalho do anfitrião para um nome DNS personalizado (por exemplo, www \. MyCloudService.com) na configuração de encadernação do site no ficheiro de definição de serviço de nuvem (*.csdef), conforme indicado:

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

Como esta ligação do cabeçalho do anfitrião é aplicada através do ficheiro csdef, o serviço é acessível apenas através do nome personalizado "www.MyCloudService.com". Todos os pedidos de entrada para o domínio "*.cloudapp.net" falham sempre. Se utilizar uma sonda SLB personalizada ou um equilibrador de carga interno no serviço, bloquear o URL/nome padrão do serviço pode interferir com o comportamento de sondagem.

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Como posso garantir que o endereço IP virado para o público de um serviço de nuvem nunca muda?

Para garantir que o endereço IP virado para o público do seu serviço na nuvem (também conhecido como VIP) nunca muda para que possa ser habitualmente aprovado por alguns clientes específicos, recomendamos que tenha um IP reservado associado ao mesmo. Caso contrário, o IP virtual fornecido pela Azure é transabilitado a partir da sua subscrição se eliminar a implementação. Para uma operação de troca VIP bem sucedida, precisa de IPs reservados individuais para slots de produção e de paragem. Sem eles, a operação de troca falha. Para reservar um endereço IP e associá-lo ao seu serviço na nuvem, consulte estes artigos:

- [Reserve o endereço IP de um serviço de nuvem existente](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
- [Associar um IP reservado a um serviço de nuvem utilizando um ficheiro de configuração de serviço](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

Se tiver mais de um exemplo para as suas funções, associar o RIP ao seu serviço de nuvem não deve causar qualquer tempo de inatividade. Em alternativa, pode adicionar a gama IP do seu datacenter Azure a uma lista de autorizações. Pode encontrar todas as gamas Azure IP no [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

Este ficheiro contém os intervalos de endereços IP (incluindo gamas de cálculo, SQL e armazenamento) utilizados nos datacenters Azure. Um ficheiro atualizado é publicado semanalmente que reflete as gamas atualmente implementadas e quaisquer alterações futuras nas gamas IP. As novas gamas que aparecem no ficheiro não são utilizadas nos datacenters durante pelo menos uma semana. Descarregue o novo ficheiro .xml todas as semanas e execute as alterações necessárias no seu site para identificar corretamente os serviços em execução em Azure. Os utilizadores do Azure ExpressRoute podem notar que este ficheiro foi utilizado para atualizar o anúncio do BGP do espaço Azure na primeira semana de cada mês.

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Como posso utilizar redes virtuais do Azure Resource Manager com serviços na nuvem?

Os serviços em nuvem não podem ser colocados em redes virtuais do Azure Resource Manager. As redes virtuais do Gestor de Recursos e as redes virtuais de implantação clássica podem ser conectadas através do espreitamento. Para obter mais informações, consulte [a rede Virtual a espreitar.](../virtual-network/virtual-network-peering-overview.md)


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Como posso obter a lista de iPs públicos usados pelos meus Serviços Cloud?

Você pode usar o script ps seguindo para obter a lista de IPs públicos para Serviços cloud sob a sua subscrição

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
