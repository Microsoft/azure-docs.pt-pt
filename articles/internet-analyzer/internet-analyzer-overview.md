---
title: Azure Internet Analyzer | Microsoft Docs
description: Saiba mais sobre o Azure Internet Analyzer
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: overview
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 2512092c69390ce604c26ab6c5dd000afafc7c05
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91360893"
---
# <a name="what-is-internet-analyzer-preview"></a>O que é o Analisador de Internet? (Pré-visualização)

O Internet Analyzer é uma plataforma de medição do lado do cliente para testar como as mudanças na infraestrutura de rede afetam o desempenho dos seus clientes. Quer esteja a migrar do local para o Azure ou a avaliar um serviço do Azure novo, o Internet Analyzer permite-lhe utilizar os dados dos seus utilizadores e das análises avançadas da Microsoft para melhor compreender e otimizar a sua arquitetura de rede com o Azure, antes de migrar.

O Internet Analyzer utiliza um pequeno cliente JavaScript incorporado na sua aplicação Web para medir a latência dos seus utilizadores finais até ao seu conjunto selecionado de destinos de rede, _chamamos pontos finais_. O Internet Analyzer permite-lhe configurar vários testes lado a lado, permitindo-lhe avaliar uma variedade de cenários à medida que a sua infraestrutura e necessidades de clientes evoluem. O Internet Analyzer fornece pontos finais personalizados e pré-configurados, proporcionando-lhe a conveniência e flexibilidade para tomar decisões de desempenho fidedignas para os seus utilizadores finais. 


> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="quick--customizable-tests"></a>Testes rápidos & personalizáveis

O Internet Analyzer aborda questões relacionadas com o desempenho para a migração em nuvem, implantando-se em novas ou adicionais regiões do Azure, ou testando novas plataformas de aplicação e entrega de conteúdos em Azure, como [Azure Front Door](https://azure.microsoft.com/services/frontdoor/) e [Microsoft Azure CDN](https://azure.microsoft.com/services/cdn/). 

Cada teste que cria no Internet Analyzer é composto por dois pontos finais — Endpoint A e Endpoint B. O desempenho do Endpoint B é analisado em relação ao Ponto Final A. 

Pode configurar o seu próprio ponto final personalizado ou selecionar a partir de uma variedade de pontos finais Azure pré-configurados. Os pontos finais personalizados devem ser usados para avaliar as cargas de trabalho no local, as suas instâncias em outros fornecedores de nuvem ou as suas configurações Azure personalizadas. Os ensaios podem ser compostos por dois pontos finais personalizados; no entanto, pelo menos um ponto final personalizado deve ser hospedado em Azure. Os pontos finais pré-configurados do Azure são uma forma rápida e fácil de avaliar o desempenho de plataformas populares de networking Azure, tais como Azure Front Door, Azure Traffic Manager e Azure CDN. 

Durante a pré-visualização, estão disponíveis os seguintes pontos finais pré-configurados: 

* **Regiões do Azure**
    * Sul do Brasil
    * Índia Central
    * E.U.A. Central
    * Ásia Leste
    * E.U.A. Leste
    * Oeste do Japão
    * Europa do Norte
    * Norte da África do Sul
    * Sudeste Asiático 
    * Uae Norte
    * Oeste do Reino Unido  
    * Europa Ocidental
    * E.U.A. Oeste 
    * E.U.A. Oeste 2
* **Múltiplas combinações da região de Azure** 
    * East US, Brasil Sul 
    * Leste dos EUA, Ásia Oriental 
    * West Europe
    * Europa Ocidental, Sudeste Asiático
    * West Europe, UAE North
    * Eua Ocidentais, Leste dos EUA 
    * Eua Ocidentais, Europa Ocidental
    * West US, UAE North
    * Europa Ocidental, Emirados Árabes Unidos Norte, Sudeste Asiático
    * Eua Ocidentais, Europa Ocidental, Ásia Oriental
    * Eua Ocidentais, Norte da Europa, Sudeste Asiático, Emirados Árabes Unidos Norte, África do Sul Norte 
* **Azure + Azure Front Door** - implantado em qualquer combinação única ou múltipla da região de Azure listada acima
* **Azure + Azure CDN da Microsoft** - implantado em qualquer combinação da região de Azure listada acima
* **Azure + Azure Traffic Manager** - implantado em qualquer combinação múltipla da região de Azure listada acima

## <a name="suggested-test-scenarios"></a>Cenários de teste sugeridos 

Para ajudá-lo a tomar as melhores decisões de desempenho para os seus clientes, o Internet Analyzer permite-lhe avaliar dois pontos finais para a sua população específica de utilizadores finais. 

Enquanto o Analisador de Internet pode responder a uma infinidade de perguntas, algumas das mais comuns são: 
* Qual é o impacto de desempenho da migração para a nuvem? 
    * *Teste sugerido: Custom (a sua infraestrutura atual no local) vs. Azure (qualquer ponto final pré-configurado)*
* Qual é o valor de colocar os meus dados no limite vs. num centro de dados? 
    *  *Teste sugerido: Azure vs. Azure Front Door, Azure vs. Azure CDN da Microsoft*
* Qual é o benefício de desempenho de Azure Front Door?
    *  *Teste sugerido: Custom/ Azure/ CDN vs. Azure Front Door*
* Qual é o benefício de desempenho da Azure CDN da Microsoft? 
    *  *Teste sugerido: Custom/ Azure/ AFD vs. Azure CDN da Microsoft*
* Como é que o Azure CDN da Microsoft se empilha? 
    *  *Teste sugerido: Personalizado (outro ponto final CDN) vs. Azure CDN da Microsoft*
* Qual é a melhor nuvem para a sua população de utilizadores finais em cada região? 
    *  *Teste sugerido: Personalizado (outro serviço em nuvem) vs. Azure (qualquer ponto final pré-configurado)*

## <a name="how-it-works"></a>Como funciona

Para utilizar o Internet Analyzer, crie um recurso Analisador de Internet no portal Microsoft Azure e instale o pequeno cliente JavaScript na sua aplicação. O cliente mede a latência dos seus utilizadores finais para os seus pontos finais selecionados, descarregando uma imagem de um pixel sobre HTTPS. Após a recolha de medições de latência, o cliente envia os dados de medição para o Analisador de Internet.

Quando um utilizador visita a aplicação Web, o cliente JavaScript seleciona dois pontos finais para medir em todos os testes configurados. Para cada ponto final, o cliente realiza uma medição _fria_ e _quente._ A medição _a frio_ incorre em latência adicional ao lado da latência pura da rede entre o utilizador e o ponto final, como a resolução de DNS, o aperto de mão de ligação TCP e a negociação SSL/TLS. A medição _quente_ segue-se logo após a conclusão da medição _a frio_ e aproveita a gestão persistente da ligação TCP dos navegadores modernos para obter uma medida precisa da latência de ponta a ponta. Quando suportado pelo navegador do utilizador, a API de tempo de recurso W3C é utilizada para um tempo preciso de medição. Atualmente, apenas medições quentes de latência são usadas para análise.

![O diagrama mostra um utilizador final a ligar-se a um servidor de aplicações com o cliente incorporado e aos dois pontos finais na internet a partir de várias opções. O utilizador envia as medições para o Analisador de Internet.](./media/ia-overview/architecture.png)


## <a name="scorecards"></a>Cartões de pontos 

Uma vez iniciado o teste, os dados de telemetria são visíveis no seu recurso Internet Analyzer no separador Marcador. Estes dados são sempre agregados. Utilize os seguintes filtros para alterar a visão dos dados que vê: 

* **Teste:** Selecione o teste para o qual gostaria de ver os resultados. Os dados do teste aparecem quando há dados suficientes para completar a análise – na maioria dos casos, dentro de 24 horas. 
* **Prazo & data de fim:** O Internet Analyzer gera três cartões de pontuação diários – cada cartão de pontuação reflete um período de agregação diferente – as 24 horas anteriores (dia), os sete dias anteriores (semana) e os 30 dias anteriores (mês). Utilize o filtro "Data final" para selecionar o período de tempo que pretende ver. 
* **País:** Utilize este filtro para visualizar dados específicos dos utilizadores finais que residem num país. O filtro global mostra dados em todas as geografias.  

Mais informações sobre cartões de pontuação podem ser encontradas na página De interpretação do [seu cartão de pontuação.](internet-analyzer-scorecard.md) 


## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar o seu primeiro recurso de Analisador de Internet.](internet-analyzer-create-test-portal.md)
* Leia o [Analisador de Internet FAQ.](internet-analyzer-faq.md) 
