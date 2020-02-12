---
title: Azure Internet Analyzer | Microsoft Docs
description: Saiba mais sobre o Azure Internet Analyzer
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: overview
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 3d9d1240e31d40a3d9c75d56314acc1721d90b99
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501947"
---
# <a name="what-is-internet-analyzer-preview"></a>O que é o Internet Analyzer? (Pré-visualização)

O Internet Analyzer é uma plataforma de avaliação do lado do cliente para testar como as alterações na infraestrutura de rede afetam o desempenho de seus clientes. Independentemente de você estar migrando do local para o Azure ou avaliando um novo serviço do Azure, o Internet Analyzer permite que você aprenda com os dados de seus usuários e a análise avançada da Microsoft para melhor compreender e otimizar sua arquitetura de rede com o Azure – antes de você migrá.

O Internet Analyzer usa um pequeno cliente JavaScript inserido em seu aplicativo Web para medir a latência dos usuários finais para o conjunto selecionado de destinos de rede, chamamos _pontos de extremidade_. O Internet Analyzer permite que você configure vários testes lado a lado, permitindo que você avalie uma variedade de cenários à medida que sua infraestrutura e necessidades do cliente evoluem. O Internet Analyzer fornece pontos de extremidade personalizados e pré-configurados, fornecendo a conveniência e a flexibilidade para tomar decisões de desempenho confiáveis para seus usuários finais. 


> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="quick--customizable-tests"></a>Testes de & personalizáveis rapidamente

O Internet Analyzer aborda as perguntas relacionadas ao desempenho para migração na nuvem, implantação em uma nova ou mais regiões do Azure ou teste de novas plataformas de fornecimento de conteúdo e aplicativo no Azure, como [Azure front door](https://azure.microsoft.com/services/frontdoor/) e [Microsoft Azure CDN ](https://azure.microsoft.com/services/cdn/). 

Cada teste que você cria no Internet Analyzer é composto por dois pontos de extremidade: ponto de extremidades A e ponto de extremidade B. o desempenho do ponto de extremidade B é analisado em relação ao ponto de extremidade A. 

Você pode configurar seu próprio ponto de extremidade personalizado ou selecionar entre uma variedade de pontos de extremidades do Azure pré-configurados. Os pontos de extremidade personalizados devem ser usados para avaliar as cargas de trabalho locais, suas instâncias em outros provedores de nuvem ou suas configurações personalizadas do Azure. Os testes podem ser compostos por dois pontos de extremidade personalizados; no entanto, pelo menos um ponto de extremidade personalizado deve ser hospedado no Azure. Os pontos de extremidade pré-configurados do Azure são uma maneira rápida e fácil de avaliar o desempenho de plataformas de rede do Azure populares, como a porta frontal do Azure, o Gerenciador de tráfego do Azure e a CDN do Azure. 

Durante a visualização, os seguintes pontos de extremidade pré-configurados estão disponíveis: 

* **Regiões do Azure**
    * Sul do Brasil
    * Índia Central
    * E.U.A. Central
    * Ásia Leste
    * E.U.A. Leste
    * Oeste do Japão
    * Europa do Norte
    * Norte da África do Sul
    * Ásia Sudeste 
    * Norte dos E.A.U.
    * Oeste do Reino Unido  
    * Europa Ocidental
    * E.U.A. Oeste 
    * E.U.A. Oeste 2
* **Várias combinações de regiões do Azure** 
    * Leste dos EUA, sul do Brasil 
    * Leste dos EUA, Ásia Oriental 
    * Europa Ocidental, sul do Brasil
    * Europa Ocidental, Sudeste Asiático
    * Europa Ocidental, Norte dos EAU
    * Oeste dos EUA, leste dos EUA 
    * Oeste dos EUA, Europa Ocidental
    * Oeste dos EUA, Norte dos EAU
    * Europa Ocidental, Norte dos EAU, Sudeste Asiático
    * Oeste dos EUA, Europa Ocidental, Ásia Oriental
    * Oeste dos EUA, Europa Setentrional, Sudeste Asiático, Norte dos EAU, norte da África do Sul 
* **Azure + Azure front door** -implantado em qualquer uma ou várias combinações de regiões do Azure listadas acima
* **Azure + CDN do Azure da Microsoft** -implantado em qualquer combinação de região única do Azure listada acima
* **Azure + Azure Traffic Manager** -implantado em qualquer combinação de várias regiões do Azure listada acima

## <a name="suggested-test-scenarios"></a>Cenários de teste sugeridos 

Para ajudá-lo a tomar as melhores decisões de desempenho para seus clientes, o Internet Analyzer permite que você avalie dois pontos de extremidade para sua população específica de usuários finais. 

Embora o Internet Analyzer possa responder a uma infinidade de perguntas, algumas das mais comuns são: 
* Qual é o impacto no desempenho da migração para a nuvem? 
    * *Teste sugerido: personalizado (sua infraestrutura local atual) versus Azure (qualquer ponto de extremidade pré-configurado)*
* Qual é o valor de colocar meus dados na borda versus em um data center? 
    *  *Teste sugerido: Azure vs. Azure front door, Azure vs. CDN do Azure da Microsoft*
* Qual é o benefício de desempenho da porta frontal do Azure?
    *  *Teste sugerido: Custom/Azure/CDN versus Azure front door*
* Qual é o benefício de desempenho da CDN do Azure da Microsoft? 
    *  *Teste sugerido: personalizado/Azure/AFD versus CDN do Azure da Microsoft*
* Como a CDN do Azure da Microsoft se empilha? 
    *  *Teste sugerido: personalizado (outro ponto de extremidade da CDN) versus CDN do Azure da Microsoft*
* Qual é a melhor nuvem para sua população de usuário final em cada região? 
    *  *Teste sugerido: personalizado (outro serviço de nuvem) versus Azure (qualquer ponto de extremidade pré-configurado)*

## <a name="how-it-works"></a>Como funciona

Para usar o Internet Analyzer, configure um recurso do Internet Analyzer no portal do Microsoft Azure e instale o pequeno cliente JavaScript em seu aplicativo. O cliente mede a latência dos usuários finais para os pontos de extremidade selecionados baixando uma imagem de um pixel por HTTPS. Depois de coletar medidas de latência, o cliente envia os dados de medida para o Internet Analyzer.

Quando um usuário visita o aplicativo Web, o cliente JavaScript seleciona dois pontos de extremidade para medir em todos os testes configurados. Para cada ponto de extremidade, o cliente executa uma medição _fria_ e _quente_ . A medição _fria_ incorre em latência adicional ao lado da latência de rede pura entre o usuário e o ponto de extremidade, como resolução DNS, handshake de conexão TCP e negociação SSL/TLS. A medição a _quente_ ocorre logo após a conclusão da medida _fria_ e tira proveito do gerenciamento de conexão TCP persistente dos navegadores modernos para obter uma medida precisa da latência de ponta a ponta. Quando suportado pelo navegador do usuário, a API de tempo de recurso do W3C é usada para o tempo de medição preciso. Atualmente, somente as medidas de latência passiva são usadas para análise.

![arquitetura](./media/ia-overview/architecture.png)


## <a name="scorecards"></a>Scorecard 

Quando um teste é iniciado, os dados de telemetria ficam visíveis no recurso do Internet Analyzer na guia Scorecard. Esses dados são sempre agregados. Use os filtros a seguir para alterar a exibição dos dados que você vê: 

* **Teste:** Selecione o teste para o qual você gostaria de exibir os resultados. Os dados de teste aparecem quando há dados suficientes para concluir a análise – na maioria dos casos, dentro de 24 horas. 
* **Período de tempo & data de término:** O Internet Analyzer gera três scorecards diariamente – cada Scorecard reflete um período de agregação diferente – as 24 horas anteriores (dia), os sete dias anteriores (semana) e os 30 dias anteriores (mês). Use o filtro "data de término" para selecionar o período de tempo que você deseja ver. 
* **País:** Use este filtro para exibir dados específicos para usuários finais que residem em um país. O filtro global mostra dados em todas as regiões geográficas.  

Mais informações sobre scorecards podem ser encontradas na página [interpretando o Scorecard](internet-analyzer-scorecard.md) . 


## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar seu primeiro recurso do Internet Analyzer](internet-analyzer-create-test-portal.md).
* Leia as [perguntas frequentes do Internet Analyzer](internet-analyzer-faq.md). 
