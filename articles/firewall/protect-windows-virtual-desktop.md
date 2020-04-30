---
title: Utilize firewall azure para proteger o Windows Virtual Desktop
description: Saiba como usar o Firewall Azure para proteger as implementações do Windows Virtual Desktop
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: victorh
ms.openlocfilehash: c5d7281d50c151722303b48b2b28a597eec72d79
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254177"
---
# <a name="use-azure-firewall-to-protect-window-virtual-desktop-deployments"></a>Utilize firewall Azure para proteger as implementações de ambiente de trabalho virtual da janela

O Windows Virtual Desktop (WVD) é um serviço de virtualização de computadores e aplicações que funciona no Azure. Quando um utilizador final se conecta a um ambiente de ambiente de trabalho virtual do Windows, a sua sessão é executada por uma piscina de anfitriões. Uma piscina de anfitriões é uma coleção de máquinas virtuais Azure que se registam no Windows Virtual Desktop como anfitriões de sessão. Estas máquinas virtuais funcionam na sua rede virtual e estão sujeitas aos controlos de segurança da rede virtual. Precisam de acesso à Internet de saída ao serviço WVD para funcionarem corretamente e podem também necessitar de acesso à Internet para os utilizadores finais. A Firewall Azure pode ajudá-lo a bloquear o ambiente e filtrar o tráfego de saída.

[![Arquitetura](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) de ambiente de trabalho virtual Windows](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

Siga as diretrizes deste artigo para fornecer proteção adicional para o seu pool de anfitriões WVD usando o Firewall Azure.

## <a name="prerequisites"></a>Pré-requisitos


 - Um ambiente WVD implantado e piscina de hospedeiro.

   Para mais informações, consulte [Tutorial: Crie uma piscina de anfitriões utilizando o Azure Marketplace](../virtual-desktop/create-host-pools-azure-marketplace.md) e [crie uma piscina anfitriã com um modelo de Gestor](../virtual-desktop/create-host-pools-arm-template.md)de Recursos Azure .

Para saber mais sobre os ambientes WVD consulte o ambiente de ambiente de [trabalho virtual do Windows](../virtual-desktop/environment-setup.md).

## <a name="host-pool-outbound-access-to-windows-virtual-desktop"></a>Acesso de saída do pool de hospedas ao Windows Virtual Desktop

As máquinas virtuais Azure que cria para o Windows Virtual Desktop devem ter acesso a vários Nomes de Domínio Totalmente Qualificados (FQDNs) para funcionar corretamente. O Azure Firewall fornece uma etiqueta FQDN virtual do Windows Para simplificar esta configuração. Utilize os seguintes passos para permitir o tráfego da plataforma WVD de saída:

- Implemente o Firewall Azure e configure a sua subnet de piscina wVD User Defined Route (UDR) para encaminhar todo o tráfego através da Firewall Azure. A sua rota padrão agora aponta para a firewall.
- Crie uma coleção de regras de aplicação e adicione uma regra para ativar a tag FQDN *do WindowsVirtualDesktop.* A gama de endereços IP de origem é a rede virtual do pool anfitrião, o protocolo é **https**, e o destino é **WindowsVirtualDesktop**.

- O conjunto de contas de ônibus de armazenamento e serviço necessários para o seu pool de anfitriões WVD é específico para a implementação, por isso ainda não é capturado na etiqueta FQDN do WindowsVirtualDesktop. Pode abordar esta questão de uma das seguintes formas:

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
   - Permitir KMS – permita o tráfego das suas máquinas virtuais WVD para a porta TCP do Serviço de Ativação do Windows 1688. Para obter mais informações sobre os endereços IP do destino, consulte falhas de [ativação do Windows no cenário de túnel forçado](../virtual-machines/troubleshooting/custom-routes-enable-kms-activation.md#solution).

> [!NOTE]
> Algumas implementações podem não precisar de regras de DNS, por exemplo, os controladores de domínio de diretório Ativo Azure encaminharem consultas de DNS para O DNS Azure em 168.63.129.16.

## <a name="host-pool-outbound-access-to-the-internet"></a>Piscina anfitrião acesso de saída à Internet

Dependendo das necessidades da sua organização, poderá querer ativar um acesso seguro à Internet para os seus utilizadores finais. Nos casos em que a lista de destinos permitidos esteja bem definida (por exemplo, o acesso do [Office 365)](https://docs.microsoft.com/Office365/Enterprise/office-365-ip-web-service)pode utilizar a aplicação azure firewall e as regras de rede para configurar o acesso necessário. Esta via de tráfego de utilizador final diretamente para a Internet para melhor desempenho.

Se pretender filtrar o tráfego de Internet do utilizador de saída utilizando um portal web seguro existente no local, pode configurar navegadores web ou outras aplicações que executem no pool de anfitriões wVD com uma configuração de procuração explícita. Por exemplo, consulte [como utilizar as opções da linha de comando do Microsoft Edge para configurar as definições de procuração](https://docs.microsoft.com/deployedge/edge-learnmore-cmdline-options-proxy-settings). Estas definições de procuração apenas influenciam o acesso à Internet do utilizador final, permitindo que a plataforma WVD desaperte o tráfego diretamente através do Firewall Azure.

## <a name="additional-considerations"></a>Considerações adicionais

Poderá ser necessário configurar regras adicionais de firewall, dependendo dos seus requisitos:

- Acesso ao servidor NTP

   Por predefinição, as máquinas virtuais que executam o Windows ligam-se a time.windows.com sobre a porta UDP 123 para sincronização do tempo. Crie uma regra de rede para permitir este acesso, ou para um servidor de tempo que utiliza no seu ambiente.


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o Windows Virtual Desktop: [O que é o Windows Virtual Desktop?](../virtual-desktop/overview.md)