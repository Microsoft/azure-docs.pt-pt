---
title: O que é o Azure Austrália? | Microsoft Docs
description: Orientação sobre como configurar o Azure nas regiões australianas para atender aos requisitos específicos da política, regulamentos e legislações do governo australiano.
author: Galey801
ms.service: azure-australia
ms.topic: overview
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: a6bd4a8747ba4aa592914fc970acd756803dd15e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570881"
---
# <a name="what-is-azure-australia"></a>O que é o Azure Austrália?

No 2014, o Azure foi iniciado na Austrália, com duas regiões; Leste da Austrália (Sydney) e sudeste da Austrália (Melbourne). Em abril de 2018, duas novas regiões do Azure localizadas em Camberra – Austrália Central e Austrália Central 2, foram iniciadas. As regiões da Austrália Central e da Austrália Central 2 são projetadas intencionalmente para atender às necessidades da infraestrutura nacional fundamental e governamental, além de oferecer conectividade e flexibilidade especiais para que você possa localizar seus sistemas ao lado da nuvem, com níveis de segurança e resiliência esperadas apenas de redes secretas confidenciais. O Azure Austrália é uma plataforma para a transformação digital do governo e da infraestrutura nacional essencial – e a única nuvem de missão crítica disponível na Austrália projetada especificamente para essas necessidades.

Há requisitos específicos do governo australiano para se conectar, consumir e operar em [Microsoft Azure Austrália](https://azure.microsoft.com/global-infrastructure/australia/) para sistemas e dados do governo australiano. Os recursos nesta página também fornecem diretrizes gerais aplicáveis a todos os clientes com um foco específico na configuração e na operação seguras.

Consulte a página da Austrália do [portal de confiança do serviço da Microsoft](https://aka.ms/au-irap) para obter informações atuais sobre as avaliações, a certificação e a inclusão do IRAP (segurança de informações do assessor) do Azure na lista de serviços de nuvem certificados (CCSL). Na página da Austrália, você também encontrará outros conselhos da Microsoft específicos para provedores de infraestrutura crítica e governamental.

## <a name="principles-for-securing-customer-data-in-azure-australia"></a>Princípios para proteger os dados do cliente na Austrália do Azure

O Azure Austrália fornece uma variedade de recursos e serviços que você pode usar para criar soluções de nuvem para atender às suas necessidades de dados regulamentados/controlados. Uma solução de cliente compatível não é nada mais do que a implementação efetiva dos recursos prontos para uso do Azure na Austrália, juntamente com uma prática de segurança de dados sólida.

Quando você hospeda uma solução na Austrália do Azure, a Microsoft lida com muitos desses requisitos no nível de infraestrutura de nuvem.

O diagrama a seguir mostra o modelo de defesa detalhada do Azure. Por exemplo, a Microsoft fornece DDoS de infraestrutura de nuvem básica, juntamente com recursos do cliente, como dispositivos de segurança ou serviços de DDoS Premium para necessidades de aplicativos específicas do cliente.

![texto alternativo](media/defenceindepth.png)

Esses artigos descrevem os princípios fundamentais para proteger seus serviços e aplicativos, com orientações e práticas recomendadas sobre como aplicar esses princípios. Em outras palavras, como os clientes devem fazer uso inteligente da Austrália do Azure para atender às obrigações e responsabilidades necessárias para uma solução que lida com informações confidenciais governamentais e classificadas.

Há duas categorias de documentação fornecidas para as agências governamentais da Austrália que estão migrando para o Azure.

## <a name="security-in-azure-australia"></a>Segurança na Austrália do Azure

Identidade, controle de acesso baseado em função, proteção de dados por meio de criptografia e gerenciamento de direitos, e monitoramento e controle de configuração efetivos são elementos fundamentais que você precisa implementar. Nesta seção, há uma série de artigos explicando os recursos internos do Azure e como eles se relacionam com o ISM e o ASD Essential 8.

Esses artigos podem ser acessados por meio do menu em *conceitos-> segurança na Austrália do Azure*.

## <a name="gateways-in-azure-australia"></a>Gateways na Austrália do Azure

Outra etapa importante para agências governamentais é o estabelecimento de recursos de segurança do perímetro. Esses recursos são chamados de SIG (gateways de Internet seguros) e, ao usar o Azure, é sua responsabilidade garantir que essas proteções estejam em vigor. A Microsoft não opera um SIG; no entanto, combinando nossos serviços de rede de borda que protegem todos os clientes e serviços específicos implantados em seu ambiente do Azure, você pode operar um recurso equivalente.

Esses artigos podem ser acessados por meio do menu em *conceitos-> gateways na Austrália do Azure*.

## <a name="next-steps"></a>Passos Seguintes

* Se o foco principal estiver protegendo seus dados no Azure, comece com a [segurança de dados](secure-your-data.md)
* Se o foco da chave estiver criando um gateway no Azure, comece com a [auditoria, o registro e a visibilidade do gateway](gateway-log-audit-visibility.md).
