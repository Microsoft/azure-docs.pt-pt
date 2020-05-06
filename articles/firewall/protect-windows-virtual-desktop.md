---
title: Utilize firewall azure para proteger o Windows Virtual Desktop
description: Saiba como usar o Firewall Azure para proteger as implementações do Windows Virtual Desktop
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: 86b30b644da929f10f5d7c9642d5f89fbd29a7fa
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864079"
---
# <a name="use-azure-firewall-to-protect-window-virtual-desktop-deployments"></a>Utilize firewall Azure para proteger as implementações de ambiente de trabalho virtual da janela

O Windows Virtual Desktop é um serviço de virtualização de computadores e aplicativos que funciona no Azure. Quando um utilizador final se conecta a um ambiente de ambiente de trabalho virtual do Windows, a sua sessão é executada por uma piscina de anfitriões. Uma piscina de anfitriões é uma coleção de máquinas virtuais Azure que se registam no Windows Virtual Desktop como anfitriões de sessão. Estas máquinas virtuais funcionam na sua rede virtual e estão sujeitas aos controlos de segurança da rede virtual. Precisam de acesso à Internet de saída ao serviço de desktop virtual windows para funcionarcorretamente e podem também necessitar de acesso à Internet para os utilizadores finais. A Firewall Azure pode ajudá-lo a bloquear o ambiente e filtrar o tráfego de saída.

[![Arquitetura](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) de ambiente de trabalho virtual Windows](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

Siga as diretrizes deste artigo para fornecer proteção adicional para o seu pool de anfitriões do Windows Virtual Desktop utilizando o Firewall Azure.

## <a name="prerequisites"></a>Pré-requisitos


 - Um ambiente de ambiente de trabalho virtual do Windows e piscina de hospedada.

   Para mais informações, consulte [Tutorial: Crie uma piscina de anfitriões utilizando o Azure Marketplace](../virtual-desktop/create-host-pools-azure-marketplace.md) e [crie uma piscina anfitriã com um modelo de Gestor](../virtual-desktop/virtual-desktop-fall-2019/create-host-pools-arm-template.md)de Recursos Azure .

Para saber mais sobre os ambientes de ambiente de trabalho virtual do Windows, consulte o ambiente de ambiente de [trabalho virtual do Windows](../virtual-desktop/environment-setup.md).

## <a name="host-pool-outbound-access-to-windows-virtual-desktop"></a>Acesso de saída do pool de hospedas ao Windows Virtual Desktop

As máquinas virtuais Azure que cria para o Windows Virtual Desktop devem ter acesso a vários Nomes de Domínio Totalmente Qualificados (FQDNs) para funcionar corretamente. O Azure Firewall fornece uma etiqueta FQDN virtual do Windows Para simplificar esta configuração. Utilize os seguintes passos para permitir o tráfego da plataforma de ambiente de trabalho virtual windows de saída:

- Implemente o Firewall Azure e configure a sua sub-rede de suporte de ambiente de trabalho virtual do Windows , user Defined Route (UDR) para direcionar todo o tráfego através da Firewall Azure. A sua rota padrão agora aponta para a firewall.
- Crie uma coleção de regras de aplicação e adicione uma regra para ativar a tag FQDN *do WindowsVirtualDesktop.* A gama de endereços IP de origem é a rede virtual do pool anfitrião, o protocolo é **https**, e o destino é **WindowsVirtualDesktop**.

- O conjunto de contas de ônibus de armazenamento e serviço necessários para o seu pool de anfitriões do Windows Virtual Desktop é específico para a implementação, pelo que ainda não foi capturado na etiqueta FQDN do WindowsVirtualDesktop. Pode abordar esta questão de uma das seguintes formas:

   - Permita o acesso https da sua subnet de piscina anfitriãpara *xt.blob.core.windows.net, *eh.servicebus.windows.net e *xt.table.core.windows.net. Estes FQDNs wildcard permitem o acesso necessário, mas são menos restritivos.
   - Utilize a seguinte consulta de análise de registo para listar as FQDNs exatamente necessárias e, em seguida, permitir-lhes explicitamente nas suas regras de aplicação de firewall:
   ```
   AzureDiagnostics
   | where Category == "AzureFirewallApplicationRule"
   | search "Deny"
   | search "gsm*eh.servicebus.windows.net" or "gsm*xt.blob.core.windows.net" or "gsm*xt.table.core.windows.net"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" *
   | project TimeGenerated,Protocol,FQDN
   ```

- Criar uma coleção de regras de rede adicione as seguintes regras:

   - Permitir o DNS – permita o tráfego do seu endereço IP privado da ADDS para * para as portas TCP e UDP 53.
   - Permitir KMS – permita o tráfego das suas máquinas virtuais do Windows Virtual Desktop para a porta TCP do Windows Activation Service 1688. Para obter mais informações sobre os endereços IP do destino, consulte falhas de [ativação do Windows no cenário de túnel forçado](../virtual-machines/troubleshooting/custom-routes-enable-kms-activation.md#solution).

> [!NOTE]
> Algumas implementações podem não precisar de regras de DNS, por exemplo, os controladores de domínio de diretório Ativo Azure encaminharem consultas de DNS para O DNS Azure em 168.63.129.16.

## <a name="host-pool-outbound-access-to-the-internet"></a>Piscina anfitrião acesso de saída à Internet

Dependendo das necessidades da sua organização, poderá querer ativar um acesso seguro à Internet para os seus utilizadores finais. Nos casos em que a lista de destinos permitidos esteja bem definida (por exemplo, o acesso do [Office 365)](https://docs.microsoft.com/Office365/Enterprise/office-365-ip-web-service)pode utilizar a aplicação azure firewall e as regras de rede para configurar o acesso necessário. Esta via de tráfego de utilizador final diretamente para a Internet para melhor desempenho.

Se pretender filtrar o tráfego de Internet do utilizador de saída utilizando um portal web seguro existente no local, pode configurar navegadores web ou outras aplicações que executem no pool de anfitriões do Windows Virtual Desktop com uma configuração de procuração explícita. Por exemplo, consulte [como utilizar as opções da linha de comando do Microsoft Edge para configurar as definições de procuração](https://docs.microsoft.com/deployedge/edge-learnmore-cmdline-options-proxy-settings). Estas definições de procuração apenas influenciam o acesso à Internet do utilizador final, permitindo que a plataforma de ambiente de trabalho virtual do Windows desalore diretamente através do Firewall Azure.

## <a name="additional-considerations"></a>Considerações adicionais

Poderá ser necessário configurar regras adicionais de firewall, dependendo dos seus requisitos:

- Acesso ao servidor NTP

   Por predefinição, as máquinas virtuais que executam o Windows ligam-se a time.windows.com sobre a porta UDP 123 para sincronização do tempo. Crie uma regra de rede para permitir este acesso, ou para um servidor de tempo que utiliza no seu ambiente.


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o Windows Virtual Desktop: [O que é o Windows Virtual Desktop?](../virtual-desktop/overview.md)