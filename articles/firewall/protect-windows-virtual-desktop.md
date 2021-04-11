---
title: Utilize o Azure Firewall para proteger o Windows Virtual Desktop
description: Saiba como utilizar o Azure Firewall para proteger as implementações de desktop virtual do Windows
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: d5320f44aa5d922cea852ab09e5141fad277e2b0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566031"
---
# <a name="use-azure-firewall-to-protect-window-virtual-desktop-deployments"></a>Utilizar o Azure Firewall para proteger as implementações do Windows Virtual Desktop

O Windows Virtual Desktop é um serviço de virtualização de desktop e aplicações que funciona no Azure. Quando um utilizador final se conecta a um ambiente de ambiente de trabalho virtual do Windows, a sessão é executada por uma piscina de anfitrião. Uma piscina de anfitriões é uma coleção de máquinas virtuais Azure que se registam no Windows Virtual Desktop como anfitriões de sessão. Estas máquinas virtuais funcionam na sua rede virtual e estão sujeitas aos controlos de segurança da rede virtual. Precisam de acesso à Internet de saída ao serviço de desktop virtual do Windows para funcionarem corretamente e podem também necessitar de acesso à Internet de saída para os utilizadores finais. O Azure Firewall pode ajudá-lo a bloquear o seu ambiente e a filtrar o tráfego de saída.

[![Arquitetura virtual do Windows Desktop ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png)](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

Siga as diretrizes deste artigo para fornecer proteção adicional para o seu conjunto de anfitriões virtual windows desktop usando Azure Firewall.

## <a name="prerequisites"></a>Pré-requisitos


 - Um ambiente de ambiente de trabalho virtual do Windows implantado e uma piscina de anfitriões.

   Para obter mais informações, consulte [Tutorial: Crie uma piscina de anfitriões utilizando o Azure Marketplace](../virtual-desktop/create-host-pools-azure-marketplace.md) e [Crie uma piscina de anfitriões com um modelo de Gestor de Recursos Azure](../virtual-desktop/virtual-desktop-fall-2019/create-host-pools-arm-template.md).

Para saber mais sobre ambientes de ambiente de trabalho virtual do Windows consulte [o ambiente de ambiente de trabalho virtual do Windows.](../virtual-desktop/environment-setup.md)

## <a name="host-pool-outbound-access-to-windows-virtual-desktop"></a>Host pool acesso de saída ao Windows Virtual Desktop

As máquinas virtuais Azure que cria para o Windows Virtual Desktop devem ter acesso a vários Nomes de Domínios Totalmente Qualificados (FQDNs) para funcionarem corretamente. O Azure Firewall fornece uma Tag FQDN virtual do Windows Desktop para simplificar esta configuração. Utilize os seguintes passos para permitir o tráfego da plataforma virtual de ambiente de trabalho virtual do Windows:

- Implemente o Azure Firewall e configuure a sua sub-rede de suporte virtual do Windows Desktop para a rota definida pelo utilizador (UDR) para encaminhar todo o tráfego através da Firewall Azure. A sua rota padrão aponta agora para a firewall.
- Crie uma recolha de regras de aplicação e adicione uma regra para ativar a tag FQDN *do WindowsVirtualDesktop.* A gama de endereços IP de origem é a rede virtual do pool anfitrião, o protocolo é **https**, e o destino é **WindowsVirtualDesktop**.

- O conjunto de contas de ônibus de armazenamento e serviço necessários para o seu pool de anfitriões virtual windows virtual é específico de implementação, por isso ainda não foi capturado na etiqueta FQDN do WindowsVirtualDesktop. Pode abordar esta questão de uma das seguintes formas:

   - Permitir o acesso https da sub-rede da piscina do anfitrião a *xt.blob.core.windows.net, *eh.servicebus.windows.net e *xt.table.core.windows.net. Estes FQDNs wildcard permitem o acesso necessário, mas são menos restritivos.
   - Utilize a seguinte consulta de análise de registo para listar as FQDNs exatas e, em seguida, permitir-lhes explicitamente nas suas regras de aplicação de firewall:
   ```
   AzureDiagnostics
   | where Category == "AzureFirewallApplicationRule"
   | search "Deny"
   | search "gsm*eh.servicebus.windows.net" or "gsm*xt.blob.core.windows.net" or "gsm*xt.table.core.windows.net"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" *
   | project TimeGenerated,Protocol,FQDN
   ```

- Criar uma coleção de regras de rede adicione as seguintes regras:

   - Permitir DNS – permitir o tráfego do seu endereço IP privado ADDS para * para as portas TCP e UDP 53.
   - Permitir KMS – permitir o tráfego das máquinas virtuais do Windows Desktop para a porta TCP 1688 do Windows Activation Service. Para obter mais informações sobre os endereços IP de destino, consulte [a ativação do Windows falha no cenário de túneis forçados](/troubleshoot/azure/virtual-machines/custom-routes-enable-kms-activation#solution).

> [!NOTE]
> Algumas implementações podem não precisar de regras DNS, por exemplo, os controladores de domínio do Azure Ative Directory encaminham as consultas dns para Azure DNS em 168.63.129.16.

## <a name="host-pool-outbound-access-to-the-internet"></a>Hospedar piscina de acesso à Internet

Dependendo das necessidades da sua organização, pode querer permitir um acesso seguro à Internet para os seus utilizadores finais. Nos casos em que a lista de destinos permitidos esteja bem definida (por exemplo, [acesso ao Microsoft 365),](/microsoft-365/enterprise/microsoft-365-ip-web-service)pode utilizar a aplicação Estrutural do Azure Firewall e as regras de rede para configurar o acesso necessário. Isto encaminha o tráfego de utilizadores finais diretamente para a Internet para melhor desempenho.

Se pretender filtrar o tráfego de Internet do utilizador de saída utilizando um portal web seguro existente no local, pode configurar navegadores web ou outras aplicações em execução no pool do anfitrião do Windows Virtual Desktop com uma configuração explícita de procuração. Por exemplo, consulte [como utilizar as opções da linha de comando do Microsoft Edge para configurar as definições de procuração](/deployedge/edge-learnmore-cmdline-options-proxy-settings). Estas definições de procuração apenas influenciam o acesso à Internet do utilizador final, permitindo que a plataforma virtual do Windows ultrapasse o tráfego diretamente através do Azure Firewall.

## <a name="additional-considerations"></a>Considerações adicionais

Poderá ser necessário configurar regras adicionais de firewall, dependendo dos seus requisitos:

- Acesso ao servidor NTP

   Por predefinição, as máquinas virtuais que executam o Windows conectam-se a time.windows.com sobre a porta UDP 123 para sincronização de tempo. Crie uma regra de rede para permitir este acesso, ou para um servidor de tempo que utiliza no seu ambiente.


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o Windows Virtual Desktop: [O que é o Windows Virtual Desktop?](../virtual-desktop/overview.md)