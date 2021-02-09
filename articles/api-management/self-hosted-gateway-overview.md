---
title: Visão geral de gateway auto-hospedada | Microsoft Docs
description: Saiba como a funcionalidade de gateway auto-hospedada da Azure API Management ajuda as organizações a gerir as APIs em ambientes híbridos e multicloud.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/25/2021
ms.author: apimpm
ms.openlocfilehash: 48abce693ca22163c0a1742ba71faf36fc6156a1
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989099"
---
# <a name="self-hosted-gateway-overview"></a>Descrição geral do gateway autoalojado

Este artigo explica como a funcionalidade de gateway auto-hospedada da Azure API Management permite a gestão híbrida e multi-nuvem da API, apresenta a sua arquitetura de alto nível e destaca as suas capacidades.

## <a name="hybrid-and-multi-cloud-api-management"></a>Gestão de API híbrida e multi-nuvem

A funcionalidade de gateway auto-hospedada expande o suporte da API Management para ambientes híbridos e multi-nuvens e permite que as organizações gerem de forma eficiente e segura as APIs hospedadas no local e através das nuvens a partir de um único serviço de Gestão API em Azure.

Com o gateway auto-hospedado, os clientes têm a flexibilidade para implantar uma versão contentorizada da componente de gateway de gestão API para os mesmos ambientes onde acolhem as suas APIs. Todos os gateways auto-hospedados são geridos a partir do serviço de Gestão API com o que são federados, proporcionando assim aos clientes a visibilidade e experiência de gestão unificada em todas as APIs internas e externas. A colocação dos gateways perto das APIs permite que os clientes otimizem os fluxos de tráfego da API e abordem os requisitos de segurança e conformidade.

Cada serviço de Gestão API é composto pelos seguintes componentes-chave:

-   O avião de gestão, exposto como API, usado para configurar o serviço através do portal Azure, PowerShell, e outros mecanismos suportados.
-   Gateway (ou plano de dados) é responsável por procurar pedidos de API, aplicar políticas e recolher telemetria
-   Portal de desenvolvedores usado pelos desenvolvedores para descobrir, aprender e a bordo para usar as APIs

Por padrão, todos estes componentes são implantados em Azure, fazendo com que todo o tráfego API (mostrado como setas pretas sólidas na imagem abaixo) flua através de Azure, independentemente do local onde estão hospedados os backends que implementam as APIs. A simplicidade operacional deste modelo tem o custo do aumento da latência, das questões de conformidade e, em alguns casos, das taxas adicionais de transferência de dados.

![Fluxo de tráfego da API sem portais auto-hospedados](media/self-hosted-gateway-overview/without-gateways.png)

A implementação de gateways auto-hospedados nos mesmos ambientes onde as implementações da API de backend são hospedadas permite que o tráfego de API flua diretamente para as APIs de backend, o que melhora a latência, otimiza os custos de transferência de dados, e permite o cumprimento, mantendo os benefícios de ter um único ponto de gestão, observabilidade e descoberta de todas as APIs dentro da organização, independentemente do local onde as suas implementações estão hospedadas.

![Fluxo de tráfego da API com gateways auto-hospedados](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Embalagem e características

O gateway auto-hospedado é uma versão contentorizada e funcionalmente equivalente do gateway gerido implantado para Azure como parte de todos os serviços de Gestão da API. O gateway auto-hospedado está disponível como um [recipiente](https://aka.ms/apim/sputnik/dhub) Docker baseado em Linux do Registo de Contentores da Microsoft. Pode ser implantado para Docker, Kubernetes, ou qualquer outra solução de orquestração de contentores que esteja a funcionar num cluster de servidores em instalações, infraestruturas em nuvem, ou para fins de avaliação e desenvolvimento, num computador pessoal.

A seguinte funcionalidade encontrada nos gateways geridos não está **disponível** nos gateways auto-hospedados:

- Registos do Azure Monitor
- Versão upstream (backend side) versão TLS e gestão de cifra
- Validação de certificados de servidor e cliente usando [certificados de raiz ca enviados](api-management-howto-ca-certificates.md) para o serviço de Gestão API. Para obter mais informações, consulte [a validação do Certificado no gateway auto-hospedado.](api-management-howto-mutual-certificates-for-clients.md#certificate-validation-in-self-hosted-gateway)
- Integração com o [Tecido de Serviço](../service-fabric/service-fabric-api-management-overview.md)
- Recomeço de sessão TLS
- Renegociação do certificado de cliente. Isto significa que, para a [autenticação](api-management-howto-mutual-certificates-for-clients.md) do certificado de cliente para trabalhar, os consumidores de API devem apresentar os seus certificados como parte do aperto de mão TLS inicial. Para garantir isso, ative a definição do certificado de cliente de negociação ao configurar um nome de anfitrião personalizado de gateway auto-hospedado.
- Cache embutido. Consulte este [documento](api-management-howto-cache-external.md) para saber sobre a utilização de cache externo em gateways auto-hospedados.

## <a name="connectivity-to-azure"></a>Conectividade ao Azure

Gateways auto-hospedados requerem conectividade TCP/IP de saída para Azure na porta 443. Cada gateway auto-hospedado deve ser associado a um único serviço de Gestão API e é configurado através do seu plano de gestão. Gateway auto-hospedado usa conectividade para Azure para:

-   Reportar o seu estado enviando mensagens de batimentocardíaco a cada minuto
-   Regularmente verificando (a cada 10 segundos) e aplicando atualizações de configuração sempre que estiverem disponíveis
-   Envio de registos e métricas de pedido para Azure Monitor, se configurado para fazê-lo
-   Envio de eventos para Application Insights, se definido para fazê-lo

Quando a conectividade com o Azure for perdida, o gateway auto-hospedado não poderá receber atualizações de configuração, reportar o seu estado ou carregar telemetria.

O gateway auto-hospedado foi projetado para "falhar estático" e pode sobreviver à perda temporária de conectividade com Azure. Pode ser implantado com ou sem backup de configuração local. No primeiro caso, os gateways auto-hospedados guardam regularmente uma cópia de backup da mais recente configuração descarregada num volume persistente ligado ao seu recipiente ou vagem.

Quando a cópia de segurança de configuração é desligada e a conectividade com o Azure é interrompida:

-   Os gateways auto-hospedados continuarão a funcionar usando uma cópia na memória da configuração
-   Gateways parados não serão capazes de começar

Quando a cópia de segurança de configuração é ligada e a conectividade com o Azure é interrompida:

-   Os gateways auto-hospedados continuarão a funcionar usando uma cópia na memória da configuração
-   Gateways auto-hospedados parados serão capazes de começar a usar uma cópia de backup da configuração

Quando a conectividade for restaurada, cada gateway auto-hospedado afetado pela paralisação reconectará automaticamente com o seu serviço de Gestão API associado e descarregará todas as atualizações de configuração que ocorreram enquanto o gateway estava "offline".

## <a name="next-steps"></a>Passos seguintes

-   [Leia um papel branco para mais informações sobre este tópico](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Implementar porta de entrada auto-hospedada para Docker](how-to-deploy-self-hosted-gateway-docker.md)
-   [Implementar porta de entrada auto-hospedada para Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
