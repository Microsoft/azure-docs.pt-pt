---
title: Visão geral do gateway auto-hospedado [ Microsoft Docs
description: Saiba como a funcionalidade de gateway auto-hospedada da Azure API Management ajuda as organizações a gerir APIs em ambientes híbridos e multicloud.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: b560b02544eeb96167e68ed305d4d9942d2b1e0f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232977"
---
# <a name="self-hosted-gateway-overview"></a>Descrição geral do gateway autoalojado

Este artigo explica como a funcionalidade de gateway auto-hospedada da Azure API Management permite a gestão híbrida e multi-cloud aPi, apresenta a sua arquitetura de alto nível e destaca as suas capacidades.

## <a name="hybrid-and-multi-cloud-api-management"></a>Gestão híbrida e multi-nuvem da API

A funcionalidade de gateway auto-hospedada expande o suporte de Gestão API para ambientes híbridos e multi-nuvem e permite às organizações gerir de forma eficiente e segura APIs alojadas no local e através de nuvens de um único serviço de Gestão API em Azure.

Com o gateway auto-hospedado, os clientes têm a flexibilidade para implementar uma versão contentorizada da componente de gateway da API Management para os mesmos ambientes onde acolhem as suas APIs. Todos os gateways auto-hospedados são geridos a partir do serviço de Gestão API com os que são federados, proporcionando assim aos clientes a visibilidade e experiência de gestão unificada em todas as APIs internas e externas. A colocação dos gateways perto das APIs permite aos clientes otimizar os fluxos de tráfego da API e abordar os requisitos de segurança e conformidade.

Cada serviço de Gestão API é composto pelos seguintes componentes-chave:

-   O plano de gestão, exposto como API, usado para configurar o serviço através do portal Azure, PowerShell, e outros mecanismos suportados.
-   Gateway (ou avião de dados) é responsável por proxying pedidos de API, aplicação de políticas e recolha de telemetria
-   Portal de desenvolvimento usado por desenvolvedores para descobrir, aprender e bordo para usar as APIs

Por padrão, todos estes componentes são implantados em Azure, fazendo com que todo o tráfego de API (mostrado como setas pretas sólidas na imagem abaixo) flua através do Azure, independentemente do local onde as despesas de implementação das APIs estejam alojadas. A simplicidade operacional deste modelo tem a ver com o custo do aumento da latência, das questões de conformidade e, em alguns casos, das taxas adicionais de transferência de dados.

![Fluxo de tráfego da API sem gateways auto-hospedados](media/self-hosted-gateway-overview/without-gateways.png)

A implantação de gateways auto-hospedados nos mesmos ambientes onde as implementações de API de backend são hospedadas permite que o tráfego de API flua diretamente para as APIs de backend, o que melhora a latência, otimiza os custos de transferência de dados, e permite o cumprimento, mantendo os benefícios de ter um único ponto de gestão, observabilidade e descoberta de todas as APIs dentro da organização, independentemente de onde as suas implementações estão alojadas.

![Fluxo de tráfego da API com gateways auto-hospedados](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Embalagem e características

O gateway auto-hospedado é uma versão contentorizada e funcionalmente equivalente do gateway gerido implantado para o Azure como parte de todos os serviços de Gestão API. O gateway auto-hospedado está disponível como um [recipiente](https://aka.ms/apim/sputnik/dhub) Docker baseado em Linux do Registo de Contentores da Microsoft. Pode ser implantado para Docker, Kubernetes ou qualquer outra solução de orquestração de contentores que funciona num cluster de servidores em instalações, infraestruturas de nuvem, ou para fins de avaliação e desenvolvimento, num computador pessoal.

A seguinte funcionalidade encontrada nos gateways geridos não está **disponível** nos gateways auto-hospedados:

- Registos do Azure Monitor
- Versão TLS a montante (backend) e gestão de cifras
- Validação de certificados de servidor e cliente utilizando [certificados de raiz CA enviados](api-management-howto-ca-certificates.md) para o serviço de Gestão API. Para adicionar suporte para CA personalizado, adicione uma camada à imagem de recipiente de gateway auto-hospedada que instala o certificado de raiz da AC.
- Integração com o [Tecido de Serviço](../service-fabric/service-fabric-api-management-overview.md)
- Recomeço de sessão TLS
- Renegociação do certificado de cliente. Isto significa que, para que a [autenticação](api-management-howto-mutual-certificates-for-clients.md) do certificado de cliente funcione, os consumidores de API devem apresentar os seus certificados como parte do aperto de mão inicial do TLS. Para garantir isso, ative a definição de certificado de cliente de negociação ao configurar um nome de anfitrião personalizado de gateway auto-hospedado.
- Cache embutido. Consulte este [documento](api-management-howto-cache-external.md) para aprender sobre a utilização de cache externa em gateways auto-hospedados.

## <a name="connectivity-to-azure"></a>Conectividade com Azure

Os gateways auto-hospedados requerem conectividade TCP/IP de saída para Azure na porta 443. Cada gateway auto-hospedado deve ser associado a um único serviço de Gestão API e é configurado através do seu plano de gestão. Gateway auto-hospedado usa conectividade com Azure para:

-   Relatando o seu estado enviando mensagens de batimentos cardíacos a cada minuto
-   Verificação regular (a cada 10 segundos) e aplicação de atualizações de configuração sempre que estão disponíveis
-   Envio de registos e métricas de pedidos para o Monitor Azure, se configurado para fazê-lo
-   Envio de eventos para Insights de Aplicação, se definido para fazê-lo

Quando a conectividade com o Azure se perder, o gateway auto-hospedado não poderá receber atualizações de configuração, reportar o seu estado ou fazer upload de telemetria.

O gateway auto-hospedado foi projetado para "falhar estática" e pode sobreviver à perda temporária de conectividade com Azure. Pode ser implantado com ou sem cópia de segurança de configuração local. No caso anterior, os gateways auto-hospedados guardarão regularmente uma cópia de reserva da configuração mais recente descarregada num volume persistente ligado ao seu recipiente ou casulo.

Quando a cópia de segurança da configuração é desligada e a conectividade com o Azure é interrompida:

-   Executar gateways auto-hospedados continuará a funcionar usando uma cópia na memória da configuração
-   Portas de entrada auto-hospedadas paradas não serão capazes de começar

Quando a cópia de segurança da configuração é ligada e a conectividade com o Azure é interrompida:

-   Executar gateways auto-hospedados continuará a funcionar usando uma cópia na memória da configuração
-   Gateways auto-hospedados parados serão capazes de começar a usar uma cópia de cópia de reserva da configuração

Quando a conectividade for restaurada, cada gateway auto-hospedado afetado pela paralisação reconectar-se-á automaticamente com o seu serviço de Gestão API associado e descarregará todas as atualizações de configuração que ocorreram enquanto o gateway estava "offline".

## <a name="next-steps"></a>Passos seguintes

-   [Leia um livro branco para obter fundos adicionais sobre este tópico](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Desloque a porta de entrada auto-hospedada para Docker](how-to-deploy-self-hosted-gateway-docker.md)
-   [Implementar porta de entrada auto-hospedada para Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
