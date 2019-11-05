---
title: Visão geral do gateway de gerenciamento de API do Azure auto-hospedado | Microsoft Docs
description: Saiba como o gateway de gerenciamento de API do Azure auto-hospedado ajuda as organizações a gerenciar APIs nos ambientes híbridos e de nuvem.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 415f0e209e607a863d715b1a66a2435603a662f0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513721"
---
# <a name="self-hosted-api-management-gateway-overview"></a>Visão geral do gateway de gerenciamento de API auto-hospedado

Este artigo explica como o recurso de gateway auto-hospedado permite o gerenciamento de API híbrida e de várias nuvens, apresenta sua arquitetura de alto nível e destaca seus recursos fundamentais.

> [!NOTE]
> O recurso de gateway auto-hospedado está em versão prévia. Durante a visualização, o gateway auto-hospedado está disponível apenas nas camadas desenvolvedor e Premium sem custo adicional. A camada de desenvolvedor é limitada a uma única implantação de gateway de hospedagem interna.

## <a name="hybrid-and-multi-cloud-api-management"></a>Gerenciamento de API híbrida e de várias nuvens

O recurso de gateway auto-hospedado expande o suporte ao gerenciamento de API para ambientes híbridos e de várias nuvens e permite que as organizações gerenciem com eficiência e segurança as APIs hospedadas localmente e entre nuvens de um único serviço de gerenciamento de API no Azure.

Com o gateway auto-hospedado, os clientes têm a flexibilidade de implantar uma versão em contêiner do componente do gateway de gerenciamento de API nos mesmos ambientes em que hospedam suas APIs. Todos os gateways de hospedagem interna são gerenciados do serviço de gerenciamento de API com o qual são federados, fornecendo aos clientes a experiência de gerenciamento unificada e de visibilidade em todas as APIs internas e externas. Colocar os gateways próximos às APIs permite que os clientes otimizem fluxos de tráfego de API e atendam aos requisitos de segurança e conformidade.

Cada serviço de gerenciamento de API é composto pelos seguintes componentes principais:

-   Plano de gerenciamento, exposto como uma API, usado para configurar o serviço por meio do portal do Azure, do PowerShell e de outros mecanismos com suporte.
-   O gateway (ou o plano de dados) é responsável por solicitações de proxy de API, aplicação de políticas e coleta de telemetria
-   Portal do desenvolvedor usado por desenvolvedores para descobrir, aprender e carregar para usar as APIs

Por padrão, todos esses componentes são implantados no Azure, causando todo o tráfego da API (mostrado como setas pretas sólidas na imagem abaixo) para fluir pelo Azure, independentemente de onde os back-ends implementando as APIs estejam hospedados. A simplicidade operacional desse modelo é o custo de maior latência, problemas de conformidade e, em alguns casos, taxas de transferência de dados adicionais.

![Fluxo de tráfego de API sem gateways hospedados internamente](media/self-hosted-gateway-overview/without-gateways.png)

Implantar gateways auto-hospedados nos mesmos ambientes que as implementações de API de back-end e adicioná-las ao serviço de gerenciamento de API permite que o tráfego de API flua diretamente para as APIs de back-end, o que melhora a latência, otimiza os custos de transferência de dados e habilita conformidade enquanto mantém os benefícios de ter um único ponto de gerenciamento e descoberta de todas as APIs dentro da organização, independentemente de onde suas implementações estão hospedadas.

![Fluxo de tráfego de API com gateways auto-hospedados](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Empacotamento e recursos

O gateway auto-hospedado é uma versão em contêiner e funcionalmente equivalente do gateway gerenciado implantado no Azure como parte de cada serviço de gerenciamento de API. O gateway auto-hospedado está disponível como um contêiner do Docker baseado em Linux do registro de contêiner da Microsoft. Ele pode ser implantado no Docker, kubernetes ou qualquer outra solução de orquestração de contêiner em execução em uma área de trabalho, cluster de servidores ou infraestrutura de nuvem.

> [!IMPORTANT]
> Algumas funcionalidades disponíveis no gateway gerenciado ainda não estão disponíveis na versão prévia. Mais notavelmente: log na política do hub de eventos, integração de Service Fabric, downstream HTTP/2. Não há nenhum plano para disponibilizar um cache interno no gateway auto-hospedado.

## <a name="connectivity-to-azure"></a>Conectividade com o Azure

O gateway auto-hospedado requer conectividade TCP/IP de saída para o Azure na porta 443. Cada gateway auto-hospedado deve ser associado a um único serviço de gerenciamento de API e configurado por meio de seu plano de gerenciamento. O gateway auto-hospedado usa a conectividade com o Azure para:

-   Relatando seu status enviando mensagens de pulsação a cada minuto
-   Verificando regularmente (a cada 10 segundos) e aplicando atualizações de configuração sempre que elas estiverem disponíveis
-   Enviando métricas e logs de solicitação para Azure Monitor, se configurado para fazer isso
-   Enviando eventos para Application Insights, se definido como fazer isso

Quando a conectividade com o Azure for perdida, o gateway auto-hospedado não poderá receber atualizações de configuração, relatar seu status ou carregar a telemetria.

O gateway auto-hospedado é projetado para "falhar de forma estática" e pode sobreviver à perda temporária de conectividade com o Azure. Ele pode ser implantado com ou sem o backup de configuração local ativado. No primeiro caso, os gateways auto-hospedados salvarão regularmente uma cópia de backup da configuração em um volume persistente anexado ao contêiner ou ao Pod.

Quando o backup de configuração é desativado e a conectividade com o Azure é interrompida:

-   Gateways auto-hospedados que estão em execução continuarão a funcionar usando uma cópia na memória da configuração
-   Os gateways de hospedagem interna interrompida não poderão ser iniciados

Quando o backup de configuração é ativado e a conectividade com o Azure é interrompida:

-   Gateways auto-hospedados que estão em execução continuarão a funcionar usando uma cópia na memória da configuração
-   Gateways auto-hospedados interrompidos começarão a usar uma cópia de backup da configuração

Quando a conectividade for restaurada, cada gateway autohospedado afetado pela interrupção será reconectado automaticamente com seu serviço de gerenciamento de API associado e baixará todas as atualizações de configuração que ocorreram enquanto o gateway estava "offline".

## <a name="next-steps"></a>Passos seguintes

-   [Leia um White Paper para obter mais informações sobre este tópico](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Implantar o gateway auto-hospedado no Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [Implantar o gateway auto-hospedado no kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
