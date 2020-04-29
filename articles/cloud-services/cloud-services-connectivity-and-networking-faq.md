---
title: Problemas de conectividade e rede
titleSuffix: Azure Cloud Services
description: Este artigo lista as perguntas frequentes sobre conectividade e networking para os Serviços Cloud Microsoft Azure.
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
ms.openlocfilehash: 7caeba0e88f63106eae80f7142b5d65463f8d7a7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77019405"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de conectividade e networking para serviços de nuvem Azure: Perguntas frequentes (PERGUNTAS)

Este artigo inclui perguntas frequentes sobre questões de conectividade e networking para [os Serviços Azure Cloud.](https://azure.microsoft.com/services/cloud-services) Para obter informações sobre tamanho, consulte a [página de tamanho VM](cloud-services-sizes-specs.md)dos Serviços cloud .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Não posso reservar um IP num serviço de nuvem multi-VIP.
Em primeiro lugar, certifique-se de que a instância da máquina virtual para a qual tenta reservar o IP está ligada. Em segundo lugar, certifique-se de que utiliza IPs reservados tanto para a encenação como para as implantações de produção. *Não* altere as definições enquanto a implementação estiver a atualizar.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Como uso o Ambiente de Trabalho Remoto quando tenho um NSG?
Adicione regras ao NSG que permitem o tráfego nos portos **3389** e **20000**. O Ambiente de Trabalho Remoto utiliza a porta **3389**. As instâncias de serviço em nuvem são equilibradas em carga, por isso não pode controlar diretamente a que instância se conectar. Os agentes *RemoteForwarder* e *RemoteAccess* gerem o tráfego do Protocolo de Ambiente de Trabalho Remoto (RDP) e permitem ao cliente enviar um cookie RDP e especificar uma instância individual para se ligar. Os agentes *RemoteForwarder* e *RemoteAccess* exigem que a porta **20000** esteja aberta, que pode ser bloqueada se tiver um NSG.

## <a name="can-i-ping-a-cloud-service"></a>Posso fazer um serviço de nuvem?

Não, não usando o protocolo "ping"/ICMP normal. O protocolo ICMP não é permitido através do equilibrador de carga Azure.

Para testar a conectividade, recomendamos que faça um ping de porta. Enquanto o Ping.exe utiliza o ICMP, pode utilizar outras ferramentas, como PSPing, Nmap e telnet, para testar a conectividade com uma porta TCP específica.

Para mais informações, consulte [Utilize os pings de porta em vez do ICMP para testar a conectividade Azure VM](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Como posso evitar receber milhares de acessos de endereços IP desconhecidos que possam indicar um ataque malicioso ao serviço de nuvem?
O Azure implementa uma segurança de rede multicamadapara proteger os seus serviços de plataforma contra ataques de negação de serviço distribuídos (DDoS). O sistema de defesa Azure DDoS faz parte do processo de monitorização contínua do Azure, que é continuamente melhorado através de testes de penetração. Este sistema de defesa DDoS foi concebido para resistir não só aos ataques vindos de fora, mas também a outros inquilinos do Azure. Para mais informações, consulte a [segurança da rede Azure.](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf)

Também pode criar uma tarefa de arranque para bloquear seletivamente alguns endereços IP específicos. Para mais informações, consulte [bloquear um endereço IP específico](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Quando tento pôr rdp na minha instância de serviço na nuvem, recebo a mensagem "A conta de utilizador expirou."
Pode obter a mensagem de erro "Esta conta de utilizador expirou" quando contornar a data de validade configurada nas definições do RDP. Pode alterar a data de validade do portal seguindo estes passos:

1. Inscreva-se no [portal Azure,](https://portal.azure.com)vá ao seu serviço na nuvem e selecione o separador **Remote Desktop.**

2. Selecione o slot de implantação **de Produção** ou **Encenação.**

3. Alterar a data **Expira e,** em seguida, guardar a configuração.

Agora deve ser capaz de rdp para a sua máquina.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Por que motivo o Balanceador de Carga do Azure não balanceia o tráfego equitativamente?
Para obter informações sobre como funciona um equilibrador de carga interna, consulte o novo modo de distribuição do [Azure Load Balancer](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

O algoritmo de distribuição utilizado é um hash de 5-tuple (fonte IP, porta de origem, IP de destino, porta de destino e tipo de protocolo) para mapear o tráfego para servidores disponíveis. Só proporciona a cisma numa sessão de transporte. Os pacotes na mesma sessão de TCP ou UDP são direcionados para a mesma instância IP (DIP) do datacenter por trás do ponto final equilibrado em carga. Quando o cliente fecha e reabre a ligação ou inicia uma nova sessão a partir da mesma fonte IP, a porta de origem muda e faz com que o tráfego vá para um ponto final de DIP diferente.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Como posso redirecionar o tráfego de entrada para o URL padrão do meu serviço de nuvem para um URL personalizado?

O módulo de reescrita url do IIS pode ser usado para redirecionar o \*tráfego que vem ao URL padrão para o serviço de nuvem (por exemplo, .cloudapp.net) para algum nome/URL personalizado. Uma vez que o módulo DE Reescrita URL está ativado em funções web por padrão e as suas regras estão configuradas no web.config da aplicação, está sempre disponível no VM independentemente de reboots/reimagens. Para mais informações, consulte:

- [Criar regras de reescrita para o módulo de reescrita url](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Remover uma ligação predefinida](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Como posso bloquear/desativar o tráfego de entrada para o URL padrão do meu serviço de nuvem?

Pode evitar a entrada de tráfego no URL/nome padrão \*do seu serviço na nuvem (por exemplo, .cloudapp.net). Defino o cabeçalho do anfitrião para\.um nome DNS personalizado (por exemplo, www MyCloudService.com) sob a configuração de ligação do site no ficheiro de definição de serviço em nuvem (*.csdef), conforme indicado:

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

Como este encadernação do cabeçalho do anfitrião é aplicado através do ficheiro csdef, o serviço só é acessível através do nome personalizado "www.MyCloudService.com". Todos os pedidos de entrada para o domínio "*.cloudapp.net" falham sempre. Se utilizar uma sonda SLB personalizada ou um equilibrante de carga interna no serviço, bloquear o URL/nome padrão do serviço pode interferir com o comportamento de sondagem.

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Como posso garantir que o endereço IP virado para o público de um serviço na nuvem nunca mude?

Para garantir que o endereço IP virado para o público do seu serviço na nuvem (também conhecido como VIP) nunca muda para que possa ser habitualmente listado por alguns clientes específicos, recomendamos que tenha um IP reservado associado a ele. Caso contrário, o IP virtual fornecido pelo Azure é transferido da sua subscrição caso apague a implementação. Para uma operação de troca VIP bem sucedida, necessita de IPs reservados individuais para slots de produção e encenação. Sem eles, a operação de troca falha. Para reservar um endereço IP e associá-lo ao seu serviço na nuvem, consulte estes artigos:

- [Reserve o endereço IP de um serviço de nuvem existente](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
- [Associar um IP reservado a um serviço na nuvem utilizando um ficheiro de configuração de serviço](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

Se tiver mais de um exemplo para os seus papéis, associar o RIP ao seu serviço de nuvem não deve causar qualquer tempo de inatividade. Em alternativa, pode adicionar a gama IP do seu datacenter Azure a uma lista de autorizações. Pode encontrar todas as gamas Azure IP no [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

Este ficheiro contém as gamas de endereços IP (incluindo cálculo, SQL e gamas de armazenamento) utilizadas nos datacenters do Azure. Um ficheiro atualizado é publicado semanalmente que reflete as gamas atualmente implementadas e quaisquer alterações futuras nas gamas IP. As novas gamas que aparecem no ficheiro não são usadas nos centros de dados durante pelo menos uma semana. Descarregue o novo ficheiro .xml todas as semanas e realize as alterações necessárias no seu site para identificar corretamente os serviços em funcionamento no Azure. Os utilizadores do Azure ExpressRoute poderão notar que este ficheiro foi utilizado para atualizar o anúncio bGP do espaço Azure na primeira semana de cada mês.

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Como posso usar redes virtuais do Azure Resource Manager com serviços na nuvem?

Os serviços em nuvem não podem ser colocados em redes virtuais do Gestor de Recursos Do Azure. Redes virtuais do Gestor de Recursos e redes virtuais de implantação clássica podem ser conectadas através do peering. Para mais informações, consulte o peering da [rede Virtual.](../virtual-network/virtual-network-peering-overview.md)


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Como posso obter a lista de iPs públicos usados pelos meus Serviços de Nuvem?

Você pode usar o seguinte script PS para obter a lista de IPs públicos para Serviços cloud sob a sua subscrição

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
