---
title: Analisador de Internet Azure [ Microsoft Docs
description: Saiba mais sobre o Analisador de Internet Azure
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: overview
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 3d9d1240e31d40a3d9c75d56314acc1721d90b99
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "73501947"
---
# <a name="what-is-internet-analyzer-preview"></a>O que é O Analisador de Internet? (Pré-visualização)

O Internet Analyzer é uma plataforma de medição do lado do cliente para testar como as mudanças na infraestrutura de rede afetam o desempenho dos seus clientes. Quer esteja a migrar do local para o Azure ou a avaliar um serviço do Azure novo, o Internet Analyzer permite-lhe utilizar os dados dos seus utilizadores e das análises avançadas da Microsoft para melhor compreender e otimizar a sua arquitetura de rede com o Azure, antes de migrar.

O Internet Analyzer utiliza um pequeno cliente JavaScript incorporado na sua aplicação Web para medir a latência dos seus utilizadores finais para o seu conjunto selecionado de destinos de rede, chamamos _pontos finais_. O Internet Analyzer permite-lhe configurar vários testes lado a lado, permitindo-lhe avaliar uma variedade de cenários à medida que a sua infraestrutura e as necessidades do cliente evoluem. O Internet Analyzer fornece pontos finais personalizados e pré-configurados, proporcionando-lhe a conveniência e flexibilidade para tomar decisões de desempenho confiáveis para os seus utilizadores finais. 


> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="quick--customizable-tests"></a>Testes rápidos & personalizáveis

O Internet Analyzer aborda questões relacionadas com o desempenho para a migração na nuvem, implantando para novas ou adicionais regiões Azure, ou testando novas plataformas de aplicação e entrega de conteúdos em Azure, como [Azure Front Door](https://azure.microsoft.com/services/frontdoor/) e [Microsoft Azure CDN.](https://azure.microsoft.com/services/cdn/) 

Cada teste que cria no Analisador de Internet é composto por dois pontos finais — Endpoint A e Endpoint B. O desempenho do Endpoint B é analisado em relação ao Ponto Final A. 

Pode configurar o seu próprio ponto final personalizado ou selecionar a partir de uma variedade de pontos finais Azure reconfigurados. Os pontos finais personalizados devem ser utilizados para avaliar as cargas de trabalho no local, as suas instâncias noutros fornecedores de nuvem ou as suas configurações personalizadas do Azure. Os testes podem ser compostos por dois pontos finais personalizados; no entanto, pelo menos um ponto final personalizado deve ser hospedado em Azure. Os pontos finais do Azure reconfigurados são uma forma rápida e fácil de avaliar o desempenho de plataformas populares de networking Azure, como a Porta Frontal Azure, o Gestor de Tráfego Azure e o Azure CDN. 

Durante a pré-visualização, estão disponíveis os seguintes pontos finais pré-configurados: 

* **Regiões do Azure**
    * Sul do Brasil
    * Índia Central
    * E.U.A. Central
    * Ásia Leste
    * E.U.A. Leste
    * Oeste do Japão
    * Europa do Norte
    * África do Sul Norte
    * Ásia Sudeste 
    * Emirados Unidos norte
    * Oeste do Reino Unido  
    * Europa ocidental
    * E.U.A. Oeste 
    * E.U.A.Oeste 2
* **Múltiplas combinações da região de Azure** 
    * Leste dos EUA, Brasil Sul 
    * Leste dos EUA, Ásia Oriental 
    * Europa Ocidental, Brasil Sul
    * Europa Ocidental, Sudeste Asiático
    * Europa Ocidental, Emirados Unidos
    * Oeste dos EUA, Leste dos EUA 
    * Oeste dos EUA, Europa Ocidental
    * Oeste dos EUA, Emirados Unidos
    * Europa Ocidental, Emirados Unidos norte, Sudeste Asiático
    * Oeste dos EUA, Europa Ocidental, Ásia Oriental
    * Oeste DOS EUA, Norte da Europa, Sudeste Asiático, Emirados Unidos norte, África do Sul Norte 
* **Porta frontal Azure + Azure** - implantada em qualquer combinação única ou múltipla da região de Azure listada acima
* **Azure + Azure CDN da Microsoft** - implantado em qualquer combinação única da região azure listada acima
* **Azure + Azure Traffic Manager** - implantado em qualquer combinação de várias regiões de Azure listadas acima

## <a name="suggested-test-scenarios"></a>Cenários de teste sugeridos 

Para ajudá-lo a tomar as melhores decisões de desempenho para os seus clientes, o Internet Analyzer permite-lhe avaliar dois pontos finais para a sua população específica de utilizadores finais. 

Enquanto o Analisador de Internet pode responder a uma infinidade de perguntas, algumas das mais comuns são: 
* Qual é o impacto do desempenho da migração para a nuvem? 
    * *Teste sugerido: Personalizado (a sua atual infraestrutura no local) vs. Azure (qualquer ponto final pré-reconfigurado)*
* Qual é o valor de colocar os meus dados no limite vs. num centro de dados? 
    *  *Teste sugerido: Azure vs. Azure Front Door, Azure vs. Azure CDN da Microsoft*
* Qual é o benefício de desempenho da Porta da Frente Azure?
    *  *Teste sugerido: Personalizado/ Azure/ CDN vs. Porta da Frente Azure*
* Qual é o benefício de desempenho do Azure CDN da Microsoft? 
    *  *Teste sugerido: Custom/ Azure/ AFD vs. Azure CDN da Microsoft*
* Como é que o Azure CDN da Microsoft se acumula? 
    *  *Teste sugerido: Personalizado (outro ponto final da CDN) vs. Azure CDN da Microsoft*
* Qual é a melhor nuvem para a sua população de utilizadores finais em cada região? 
    *  *Teste sugerido: Personalizado (outro serviço na nuvem) vs. Azure (qualquer ponto final pré-reconfigurado)*

## <a name="how-it-works"></a>Como funciona

Para utilizar o Analisador de Internet, crie um recurso de Analisador de Internet no portal Microsoft Azure e instale o pequeno cliente JavaScript na sua aplicação. O cliente mede a latência dos seus utilizadores finais para os seus pontos finais selecionados, descarregando uma imagem de um pixel em HTTPS. Depois de recolher medições de latência, o cliente envia os dados de medição para o Analisador de Internet.

Quando um utilizador visita a aplicação Web, o cliente JavaScript seleciona dois pontos finais para medir em todos os testes configurados. Para cada ponto final, o cliente realiza uma medição _fria_ e _quente._ A medição _a frio_ incorre em latência adicional ao lado da latência pura da rede entre o utilizador e o ponto final, como a resolução dNS, o aperto de mão de ligação TCP e a negociação SSL/TLS. A medição _quente_ segue logo após a medição _a frio_ terminar e aproveita a persistente gestão da ligação TCP dos navegadores modernos para obter uma medida precisa de latência de ponta a ponta. Quando suportado pelo navegador do utilizador, o API de tempo de recursos W3C é utilizado para um timing preciso de medição. Atualmente, apenas são utilizadas medições quentes de latência para análise.

![arquitetura](./media/ia-overview/architecture.png)


## <a name="scorecards"></a>Cartões de pontuação 

Uma vez iniciado um teste, os dados de telemetria são visíveis no seu recurso De Analisador de Internet sob o separador Scorecard. Estes dados são sempre agregados. Utilize os seguintes filtros para alterar a visão dos dados que vê: 

* **Teste:** Selecione o teste para o qual gostaria de ver os resultados. Os dados do teste aparecem uma vez que há dados suficientes para completar a análise – na maioria dos casos, dentro de 24 horas. 
* **Período & data de fim:** O Internet Analyzer gera três cartões de pontuação diários – cada cartão de pontuação reflete um período de tempo de agregação diferente – as 24 horas anteriores (dia), os sete dias anteriores (semana) e os 30 dias anteriores (mês). Utilize o filtro "Data Final" para selecionar o período de tempo que pretende ver. 
* **País:** Utilize este filtro para visualizar dados específicos para os utilizadores finais residentes num país. O filtro global mostra dados em todas as geografias.  

Mais informações sobre cartões de pontuação podem ser encontradas na página de Interpretação do seu cartão de [pontuação.](internet-analyzer-scorecard.md) 


## <a name="next-steps"></a>Passos seguintes

* Aprenda a [criar o seu primeiro recurso de Analisador](internet-analyzer-create-test-portal.md)de Internet.
* Leia o FaQ do [Analisador](internet-analyzer-faq.md)de Internet . 
