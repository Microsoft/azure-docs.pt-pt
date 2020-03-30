---
title: Visão geral da gateway de gestão da API Azure auto-hospedada [ Microsoft Docs
description: Saiba como o gateway de Gestão Azure API auto-hospedado ajuda as organizações a gerir APIs nos ambientes híbridos e multicloud.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513721"
---
# <a name="self-hosted-api-management-gateway-overview"></a>Visão geral do gateway de gestão da API auto-hospedado

Este artigo explica como a funcionalidade de gateway auto-hospedada permite a gestão híbrida e multi-cloud aPi, apresenta a sua arquitetura de alto nível, e destaca as suas capacidades fundamentais.

> [!NOTE]
> A funcionalidade de gateway auto-hospedada está em pré-visualização. Durante a pré-visualização, o gateway auto-hospedado só está disponível nos níveis Developer e Premium sem custos adicionais. O nível de desenvolvimento está limitado a uma única implementação de gateway auto-hospedada.

## <a name="hybrid-and-multi-cloud-api-management"></a>Gestão híbrida e multi-nuvem da API

A funcionalidade de gateway auto-hospedada expande o suporte de Gestão API para ambientes híbridos e multi-nuvem e permite às organizações gerir de forma eficiente e segura APIs alojadas no local e através de nuvens de um único serviço de Gestão API em Azure.

Com o gateway auto-hospedado, os clientes têm a flexibilidade para implementar uma versão contentorizada da componente de gateway da API Management para os mesmos ambientes onde acolhem as suas APIs. Todos os gateways auto-hospedados são geridos a partir do serviço de Gestão API com os que são federados, proporcionando assim aos clientes a visibilidade e experiência de gestão unificada em todas as APIs internas e externas. A colocação dos gateways perto das APIs permite aos clientes otimizar os fluxos de tráfego da API e abordar os requisitos de segurança e conformidade.

Cada serviço de Gestão API é composto pelos seguintes componentes-chave:

-   O plano de gestão, exposto como API, usado para configurar o serviço através do portal Azure, PowerShell, e outros mecanismos suportados.
-   Gateway (ou avião de dados) é responsável por proxying pedidos de API, aplicação de políticas e recolha de telemetria
-   Portal de desenvolvimento usado por desenvolvedores para descobrir, aprender e bordo para usar as APIs

Por padrão, todos estes componentes são implantados em Azure, fazendo com que todo o tráfego de API (mostrado como setas pretas sólidas na imagem abaixo) flua através do Azure, independentemente do local onde as despesas de implementação das APIs estejam alojadas. A simplicidade operacional deste modelo tem a ver com o custo do aumento da latência, das questões de conformidade e, em alguns casos, das taxas adicionais de transferência de dados.

![Fluxo de tráfego da API sem gateways auto-hospedados](media/self-hosted-gateway-overview/without-gateways.png)

A implantação de gateways auto-hospedados nos mesmos ambientes que as implementações da API e adicioná-las ao serviço de Gestão API permite que o tráfego da API flua diretamente para as APIs de backend, o que melhora a latência, otimiza os custos de transferência de dados e permite cumprimento, mantendo os benefícios de ter um único ponto de gestão e descoberta de todas as APIs dentro da organização, independentemente do local onde as suas implementações são hospedadas.

![Fluxo de tráfego da API com gateways auto-hospedados](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Embalagem e características

O gateway auto-hospedado é uma versão contentorizada e funcionalmente equivalente do gateway gerido implantado para o Azure como parte de todos os serviços de Gestão API. O gateway auto-hospedado está disponível como um recipiente Docker baseado em Linux do Registo de Contentores da Microsoft. Pode ser implantado para Docker, Kubernetes ou qualquer outra solução de orquestração de contentores que funciona num ambiente de trabalho, num cluster de servidores ou em infraestruturas em nuvem.

> [!IMPORTANT]
> Algumas funcionalidades disponíveis no gateway gerido ainda não estão disponíveis na pré-visualização. Mais notavelmente: Iniciar sessão na política do Hub de Eventos, integração de tecidode serviço, http/2 a jusante. Não há nenhum plano para disponibilizar uma cache incorporada no gateway auto-hospedado.

## <a name="connectivity-to-azure"></a>Conectividade com Azure

O gateway auto-hospedado requer conectividade TCP/IP de saída para Azure na porta 443. Cada gateway auto-hospedado tem de ser associado a um único serviço de Gestão API e é configurado através do seu plano de gestão. Gateway auto-hospedado usa conectividade com Azure para:

-   Relatando o seu estado enviando mensagens de batimentos cardíacos a cada minuto
-   Verificação regular (a cada 10 segundos) e aplicação de atualizações de configuração sempre que estão disponíveis
-   Envio de registos e métricas de pedidos para o Monitor Azure, se configurado para fazê-lo
-   Envio de eventos para Insights de Aplicação, se definido para fazê-lo

Quando a conectividade com o Azure se perder, o gateway auto-hospedado não poderá receber atualizações de configuração, reportar o seu estado ou fazer upload de telemetria.

O gateway auto-hospedado foi projetado para "falhar estática" e pode sobreviver à perda temporária de conectividade para Azure. Pode ser implantado com ou sem a configuração local de reserva ligada. No caso anterior, os gateways auto-hospedados guardarão regularmente uma cópia de configuração de cópia de segurança num volume persistente ligado ao recipiente ou à cápsula.

Quando a cópia de segurança da configuração é desligada e a conectividade com o Azure é interrompida:

-   Os gateways auto-hospedados que estão em execução continuarão a funcionar usando uma cópia em memória da configuração
-   Portas de entrada auto-hospedadas paradas não serão capazes de começar

Quando a cópia de segurança da configuração é ligada e a conectividade com o Azure é interrompida:

-   Os gateways auto-hospedados que estão em execução continuarão a funcionar usando uma cópia em memória da configuração
-   Gateways auto-hospedados parados começarão a usar uma cópia de cópia de reserva da configuração

Quando a conectividade for restaurada, cada gateway auto-hospedado afetado pela paralisação reconectar-se-á automaticamente com o seu serviço de Gestão API associado e descarregará todas as atualizações de configuração que ocorreram enquanto o gateway estava "offline".

## <a name="next-steps"></a>Passos seguintes

-   [Leia um livro branco para obter fundos adicionais sobre este tópico](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Desloque a porta de entrada auto-hospedada para Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [Implementar porta de entrada auto-hospedada para Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
