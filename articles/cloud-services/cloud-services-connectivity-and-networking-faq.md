---
title: Problemas de conectividade e rede para Serviços de Nuvem do Microsoft Azure perguntas frequentes | Microsoft Docs
description: Este artigo lista as perguntas frequentes sobre conectividade e rede para Serviços de Nuvem do Microsoft Azure.
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
ms.openlocfilehash: 513803adec71e0e2c9578d762c5f4c110ed7086f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384491"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de conectividade e rede para serviços de nuvem do Azure: Perguntas frequentes (FAQs)

Este artigo inclui perguntas frequentes sobre problemas de conectividade e rede para os [serviços de nuvem do Azure](https://azure.microsoft.com/services/cloud-services). Para obter informações de tamanho, consulte a [página tamanho da VM dos serviços de nuvem](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Não consigo reservar um IP em um serviço de nuvem de vários VIPs.
Primeiro, verifique se a instância de máquina virtual para a qual você está tentando reservar o IP está ativada. Em segundo lugar, certifique-se de usar IPs reservados para as implantações de preparo e produção. *Não* altere as configurações enquanto a implantação estiver sendo atualizada.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Como fazer usar Área de Trabalho Remota quando tenho um NSG?
Adicione regras ao NSG que permitem o tráfego nas portas **3389** e **20000**. Área de Trabalho Remota usa a porta **3389**. As instâncias de serviço de nuvem têm balanceamento de carga, portanto, não é possível controlar diretamente a instância à qual se conectar. Os agentes *RemoteForwarder* e *RemoteAccess* gerenciam o tráfego de protocolo RDP (RDP) e permitem que o cliente envie um cookie RDP e especifique uma instância individual à qual se conectar. Os agentes *RemoteForwarder* e *RemoteAccess* exigem que a porta **20000** esteja aberta, o que poderá ser bloqueado se você tiver um NSG.

## <a name="can-i-ping-a-cloud-service"></a>Posso executar ping em um serviço de nuvem?

Não, não usando o protocolo/ICMP normal "ping". O protocolo ICMP não é permitido por meio do Azure Load Balancer.

Para testar a conectividade, recomendamos que você faça um ping de porta. Enquanto o ping. exe usa ICMP, você pode usar outras ferramentas, como PSPing, nmap e Telnet, para testar a conectividade com uma porta TCP específica.

Para obter mais informações, consulte [usar pings de porta em vez de ICMP para testar a conectividade de VM do Azure](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Como fazer impedir o recebimento de milhares de ocorrências de endereços IP desconhecidos que podem indicar um ataque mal-intencionado ao serviço de nuvem?
O Azure implementa uma segurança de rede de várias camadas para proteger seus serviços de plataforma contra ataques de DDoS (negação de serviço distribuído). O sistema de defesa contra DDoS do Azure faz parte do processo de monitoramento contínuo do Azure, que é continuamente melhorado por meio de testes de penetração. Esse sistema de defesa contra DDoS é projetado para resistir não apenas a ataques externos, mas também de outros locatários do Azure. Para obter mais informações, consulte [segurança de rede do Azure](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Você também pode criar uma tarefa de inicialização para bloquear seletivamente alguns endereços IP específicos. Para obter mais informações, consulte [bloquear um endereço IP específico](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Quando tento usar o RDP para minha instância do serviço de nuvem, recebo a mensagem "a conta de usuário expirou".
Você pode receber a mensagem de erro "esta conta de usuário expirou" quando você ignora a data de validade configurada em suas configurações de RDP. Você pode alterar a data de validade no portal seguindo estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com), vá para o serviço de nuvem e selecione a guia **área de trabalho remota** .

2. Selecione o slot de implantação de **produção** ou de **preparo** .

3. Altere a data **expira em** e, em seguida, salve a configuração.

Agora você deve ser capaz de RDP para seu computador.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Por que Azure Load Balancer não está equilibrando o tráfego igualmente?
Para obter informações sobre como funciona um balanceador de carga interno, consulte [Azure Load Balancer novo modo de distribuição](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

O algoritmo de distribuição usado é um hash de 5 tuplas (IP de origem, porta de origem, IP de destino, porta de destino e tipo de protocolo) para mapear o tráfego para os servidores disponíveis. Ele fornece adesão somente dentro de uma sessão de transporte. Os pacotes na mesma sessão TCP ou UDP são direcionados para a mesma instância de DIP (IP do Datacenter) por trás do ponto de extremidade com balanceamento de carga. Quando o cliente fecha e reabre a conexão ou inicia uma nova sessão do mesmo IP de origem, a porta de origem é alterada e faz com que o tráfego vá para um ponto de extremidade DIP diferente.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Como posso redirecionar o tráfego de entrada para a URL padrão do meu serviço de nuvem para uma URL personalizada?

O módulo de reescrita de URL do IIS pode ser usado para redirecionar o tráfego que chega à URL padrão para o serviço de \*nuvem (por exemplo,. cloudapp.net) para algum nome/URL personalizado. Como o módulo de reescrita de URL é habilitado em funções Web por padrão e suas regras são configuradas no Web. config do aplicativo, ela está sempre disponível na VM, independentemente das reinicializações/recriações de imagem. Para obter mais informações, consulte:

- [Criar regras de reescrita para o módulo de reescrita de URL](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Remover um link padrão](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Como bloquear/desabilitar o tráfego de entrada para a URL padrão do meu serviço de nuvem?

Você pode impedir o tráfego de entrada para a URL/nome padrão do seu serviço de nuvem ( \*por exemplo,. cloudapp.net). Defina o cabeçalho de host para um nome DNS personalizado (por exemplo,\.www MyCloudService.com) em configuração de associação de site no arquivo de definição do serviço de nuvem (*. csdef), conforme indicado:

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

Como essa associação de cabeçalho de host é imposta por meio do arquivo csdef, o serviço é acessível somente por meio do nome personalizado "www.MyCloudService.com". Todas as solicitações de entrada para o domínio "*. cloudapp.net" sempre falham. Se você usar uma investigação SLB personalizada ou um balanceador de carga interno no serviço, o bloqueio da URL/nome padrão do serviço poderá interferir no comportamento de investigação.

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Como posso garantir que o endereço IP voltado para o público de um serviço de nuvem nunca seja alterado?

Para garantir que o endereço IP voltado para o público do seu serviço de nuvem (também conhecido como VIP) nunca seja alterado para que possa ser normalmente na lista de permissões por alguns clientes específicos, recomendamos que você tenha um IP reservado associado a ele. Caso contrário, o IP virtual fornecido pelo Azure será desalocado da sua assinatura se você excluir a implantação. Para uma operação de permuta de VIP bem-sucedida, você precisa de IPs reservados individuais para slots de produção e de preparo. Sem eles, a operação de permuta falha. Para reservar um endereço IP e associá-lo ao seu serviço de nuvem, consulte estes artigos:

- [Reservar o endereço IP de um serviço de nuvem existente](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [Associar um IP reservado a um serviço de nuvem usando um arquivo de configuração de serviço](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

Se você tiver mais de uma instância para suas funções, a associação de RIP ao seu serviço de nuvem não deverá causar nenhum tempo de inatividade. Como alternativa, você pode colocar o intervalo IP do seu datacenter do Azure na lista de permissões. Você pode encontrar todos os intervalos de IP do Azure no [centro de download da Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

Esse arquivo contém os intervalos de endereços IP (incluindo os intervalos de computação, SQL e de armazenamento) usados em data centers do Azure. Um arquivo atualizado é publicado semanalmente que reflete os intervalos atualmente implantados e as alterações futuras nos intervalos de IP. Novos intervalos que aparecem no arquivo não são usados nos data centers por pelo menos uma semana. Baixe o novo arquivo. XML a cada semana e execute as alterações necessárias no seu site para identificar corretamente os serviços em execução no Azure. Os usuários do Azure ExpressRoute podem observar que esse arquivo costumava atualizar o anúncio de BGP do espaço do Azure na primeira semana de cada mês.

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Como posso usar Azure Resource Manager redes virtuais com os serviços de nuvem?

Os serviços de nuvem não podem ser colocados em Azure Resource Manager redes virtuais. As redes virtuais do Resource Manager e as redes virtuais de implantação clássica podem ser conectadas por meio de emparelhamento. Para obter mais informações, consulte [emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md).


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Como posso obter a lista de IPs públicos usados pelos meus serviços de nuvem?

Você pode usar o script do PS a seguir para obter a lista de IPs públicos para serviços de nuvem em sua assinatura

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
