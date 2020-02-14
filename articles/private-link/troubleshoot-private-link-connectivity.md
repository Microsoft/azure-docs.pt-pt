---
title: Problemas de sessão de conectividade Azure Private Link
description: Orientação passo a passo para diagnosticar conectividade de ligação privada
services: private-link
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 0d26ad6802e551523875dcad13066fdbdbf39ada
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191046"
---
# <a name="troubleshoot-private-link-service-connectivity-problems"></a>Resolver problemas de conectividade do Serviço Private Link

Este guia fornece orientações passo a passo para validar e diagnosticar conectividade para a configuração do seu serviço de ligação privada. 

O Azure Private Link permite-lhe aceder aos Serviços Azure PaaS (por exemplo, Armazenamento Azure, Azure Cosmos DB e SQL Database) e a Azure acolheu serviços de clientes/parceiros sobre um Ponto Final Privado na sua rede virtual. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Também pode criar o seu próprio Serviço de Link Privado na sua rede virtual (VNet) e entregá-lo em privado aos seus clientes. 

Pode ativar o seu serviço atrás do Azure Standard Load Balancer para acesso a Private Link. Os consumidores do seu serviço podem criar um ponto final privado dentro da sua rede virtual e mapeá-lo para este serviço para aceder em privado.

Aqui estão os cenários de conectividade que estão disponíveis com o serviço Private Link
- rede virtual da mesma região 
- redes virtuais regionalmente peered
- redes virtuais globalmente peered
- clientes no local sobre circuitos VPN ou Express Route

## <a name="deployment-troubleshooting"></a>Resolução de problemas de implantação

Reveja as informações sobre as políticas de [rede dedesactivação no serviço](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) de ligação privada para casos de resolução de problemas em que não é possível selecionar o endereço IP de origem a partir da subnet da sua escolha para o seu Serviço de Ligação Privada.

Certifique-se de que a definição **privadaLinkServiceNetworkPolicies** está desativada para a sub-rede a partir da qual está a selecionar o endereço IP de origem.

## <a name="diagnosing-connectivity-problems"></a>Diagnosticar problemas de conectividade

Se estiver a ter problemas de conectividade com a configuração da ligação privada, veja os passos listados abaixo para se certificar de que todas as configurações habituais são como esperado.

1. Reveja a configuração do Serviço de Link Privado navegando no recurso 

    a) Ir ao **Private Link Center**

      ![Centro de Ligação Privada](./media/private-link-tsg/private-link-center.png)

    b) Selecione **Serviços de Ligação Privada** a partir do painel de navegação esquerdo

      ![Serviços de Ligação Privada](./media/private-link-tsg/private-link-service.png)

    c) Filtrar e selecionar o serviço de ligação privada que pretende diagnosticar

    d) Rever as ligações de ponto final privado
     - Certifique-se de que o ponto final privado de que procura conectividade está listado com o estado de ligação **aprovado.** 
     - Se **estiver pendente,** selecione-o e aprove. 

       ![Conexões de endpoint privados](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Navegue até ao ponto final privado do que está a ligar, clicando no nome. Certifique-se de que o estado da ligação mostra **aprovado**.

       ![Visão geral da ligação de ponto final privado](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Uma vez aprovados ambos os lados, tente a conectividade novamente.

    e) Rever **as pseudónimos** do separador overview e id de **recursos** do separador Propriedades 
     - Certifique-se de que o **Id alias / recurso** corresponde ao **Alias / Id** de Recursos que está a utilizar para criar um ponto final privado para este serviço. 

       ![Verificar pseudónimos](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Verificar id de recursos](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f) Rever a visibilidade (Secção Geral) informações
     - Certifique-se de que a sua subscrição se enquadra no âmbito da **Visibilidade**

       ![Verificar visibilidade](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g) Rever informações sobre o balanceador de carga (visão geral)
     - Pode navegar para carregar o equilíbrio clicando na ligação do equilibrante de carga

       ![Verificar o balanceador de carga](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Certifique-se de que as definições do Balanceor de Carga estão configuradas de acordo com as suas expectativas
       - Rever a configuração IP frontend
       - Rever Piscinas de Backend
       - Rever regras de equilíbrio de carga

       ![Verificar propriedades do balancere de carga](./media/private-link-tsg/pls-ilb-properties.png)

     - Certifique-se de que o Balancer de Carga está a funcionar de acordo com as definições acima
       - Selecione um VM em qualquer sub-rede que não seja a sub-rede onde o pool de backend do Balancer de Carga está disponível
       - Tente aceder à extremidade frontal do equilibrador de carga a partir de VM acima
       - Se a ligação fizer o pool de backend de acordo com as regras de equilíbrio de carga, o seu equilibrador de carga está operacional
       - Também pode rever a Métrica do Equilíbrio de Carga através do Monitor Azure para ver se os dados estão a fluir através do equilibrista de carga

2. Use [**o Monitor Azure**](https://docs.microsoft.com/azure/azure-monitor/overview) para rever os dados está a fluir

    a) No recurso Private Link SErvice, selecione **Métricas**
     - Selecione **bytes-in** ou **bytes-out**
     - Os dados de revisão estão a fluir quando se tenta ligar ao Serviço de Ligação Privada. Espere um atraso de cerca de 10 minutos.

       ![Verifique as métricas do serviço de ligação privada](./media/private-link-tsg/pls-metrics.png)

3. Contacte a equipa [de Suporte Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) se o seu problema ainda estiver por resolver e o problema de conectividade ainda existir. 

## <a name="next-steps"></a>Passos seguintes

 * [Criar um Serviço de Ligação Privada (CLI)](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)

 * [Guia de resolução de problemas de Endpoint privado](troubleshoot-private-endpoint-connectivity.md)
