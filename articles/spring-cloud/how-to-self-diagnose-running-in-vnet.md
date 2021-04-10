---
title: Como auto-diagnosticar Azure Spring Cloud VNET
description: Aprenda a auto-diagnosticar e resolver problemas em Azure Spring Cloud em execução em VNET.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: 5407213b62902326d53b73e42ee3af1ba9b11524
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878445"
---
# <a name="self-diagnose-running-azure-spring-cloud-in-vnet"></a>Auto-diagnóstico executando Azure Spring Cloud em VNET
O diagnóstico Azure Spring Cloud suporta aplicações interativas de resolução de problemas em redes virtuais sem configuração. O diagnóstico Azure Spring Cloud identifica problemas e guia-o para informações que ajudam a resolver problemas e resolvê-los.

## <a name="navigate-to-the-diagnostics-page"></a>Navegue para a página de diagnósticos
O procedimento a seguir inicia o diagnóstico de aplicações em rede.
1. Inicie sessão no portal do Azure.
1. Vá à sua página de visão geral da nuvem de primavera Azure.
1. Selecione **diagnosticar e resolver problemas** no menu no painel de navegação à esquerda.
1. Selecione a terceira categoria, **Networking**.

   ![Título de autodiagnósto](media/spring-cloud-self-diagnose-vnet/self-diagostic-title.png)

## <a name="view-a-diagnostic-report"></a>Ver um relatório de diagnóstico
Depois de clicar na categoria **networking,** pode ver duas questões relacionadas com networking específicas da sua Nuvem de primavera Azure injetada VNet: **Resolução DNS** e **Tráfego de Saída Obrigatório**.

   ![Opções de autodiagnóscgnó](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-req-outbound-options.png)

Selecione o seu problema de destino para ver o relatório de diagnóstico. Será apresentado um resumo dos diagnósticos, tais como: 

* *O recurso foi removido.*
* *O recurso não é implantado na sua própria rede virtual.*

Alguns resultados contêm documentação relacionada. Diferentes sub-redes mostrarão os resultados separadamente.

## <a name="dns-resolution"></a>Resolução DNS 
Se selecionar **resolução dns**, os resultados indicarão se existem problemas dns com aplicações.  As aplicações saudáveis são listadas da seguinte forma:

* *Problemas dns resolvidos sem problemas na sub-rede 'subnet01'.*
* *Problemas de DNS resolvidos sem problemas na sub-rede 'subnet02'.*

O exemplo do relatório de diagnóstico que se segue indica que a saúde da aplicação é desconhecida. O prazo de apresentação do relatório não inclui o momento em que o estado de saúde foi reportado.  Assuma que o tempo de fim de contexto é *2021-03-03T04:20:00Z*. O último TIMETAMP na Tabela de **Resolução de DNS** é *2021-03-03T03:39:00Z,* no dia anterior. O registo de verificação de saúde pode não ter sido enviado por causa de uma rede bloqueada. 

Os resultados do estado de saúde desconhecidos contêm documentação relacionada.  Pode clicar no suporte do ângulo esquerdo para ver o visor de queda.

   ![DNS desconhecido](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-unknown.png)

Se configurar mal o seu recorde privado de DNS Zone, obterá um resultado crítico como: `Failed to resolve the Private DNS in subnet xxx` . 

Nas renderizações da Tabela de **Resolução DNS,** encontrará a informação de mensagem de pormenor a partir da qual pode verificar o seu config.

## <a name="required-outbound-traffic"></a>Tráfego de saída obrigatório 

Se selecionar **Tráfego de Saída Obrigatório,** os resultados indicarão se existem problemas de tráfego de saída com aplicações.  As aplicações saudáveis são listadas da seguinte forma:

* *Tráfego de saída obrigatório resolvido sem problemas na sub-rede 'subnet01'.
* *Tráfego de saída obrigatório resolvido sem problemas na sub-rede 'subnet02'.

Se qualquer sub-rede estiver bloqueada pelas regras NSG ou firewall, e se não tiver bloqueado o registo, encontrará as seguintes falhas. Pode verificar se ignorou quaisquer [Responsabilidades do Cliente.](spring-cloud-vnet-customer-responsibilities.md)
    
   ![O ponto final falhou](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-failed.png)

Se não houver dados dentro de `Required Outbound Traffic Table Renderings` 30 minutos, o resultado será `health status unknown` . Talvez a sua rede esteja bloqueada ou o serviço de registos esteja encerrado.

   ![Ponto final de diagnóstico desconhecido](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-unknown.png)

## <a name="see-also"></a>Ver também
* [Como auto-diagnosticar Azure Spring Cloud](spring-cloud-howto-self-diagnose-solve.md)