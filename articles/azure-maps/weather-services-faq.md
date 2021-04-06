---
title: Os serviços de clima do Microsoft Azure Maps (Pré-visualização) frequentemente questionados (FAQ)
description: Encontre resposta a perguntas comuns sobre os dados e funcionalidades dos serviços de meteorologia do Azure Maps (Preview).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9c8e971b4fda313ffede58455dd6d057d6848ce4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678134"
---
# <a name="azure-maps-weather-services-preview-frequently-asked-questions-faq"></a>Serviços meteorológicos Azure Maps (Pré-visualização) frequentemente questionados (FAQ)

> [!IMPORTANT]
> Os serviços Azure Maps Weather estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo responde a perguntas comuns sobre os dados e [funcionalidades dos serviços climáticos](/rest/api/maps/weather) Azure Maps. Os seguintes tópicos são abordados:

* Fontes de dados e modelos de dados
* Cobertura e disponibilidade dos serviços meteorológicos
* Frequência de atualização de dados
* Desenvolvimento com Azure Maps SDKs
* Opções para visualizar dados meteorológicos, incluindo a integração do Microsoft Power BI

## <a name="data-sources-and-data-models"></a>Fontes de dados e modelos de dados

**Como é que o Azure Maps fornece dados meteorológicos?**

O Azure Maps é construído com a colaboração de parceiros de tecnologia de mobilidade e localização de classe mundial, incluindo a AccuWeather, que fornece os dados meteorológicos subjacentes. Para ler o anúncio da colaboração da Azure Maps com a AccuWeather, ver [Rain ou shine: Azure Maps Weather Services trará insights para a sua empresa.](https://azure.microsoft.com/blog/rain-or-shine-azure-maps-weather-services-will-bring-insights-to-your-enterprise/)

A AccuWeather tem informação meteorológica e ambiental em tempo real disponível em qualquer parte do mundo, em grande parte devido às suas parcerias com numerosas agências meteorológicas nacionais e outros arranjos de propriedade. Abaixo é fornecida uma lista destas informações fundamentais.

* Observações de superfície globais publicamente disponíveis de agências governamentais
* Conjuntos de dados de observação de superfícies proprietários de governos e empresas privadas
* Dados de radar de alta resolução para mais de 40 países/regiões
* Dados de relâmpagos globais em tempo real
* Avisos meteorológicos emitidos pelo Governo para mais de 60 países/regiões e territórios
* Dados de satélites de satélites meteorológicos geoestacionários que cobrem o mundo inteiro
* Mais de 150 modelos de previsão numérica, incluindo modelação interna, proprietária, modelos governamentais como o Sistema Global de Previsão (GFS) dos EUA, e modelos únicos de downscaleed fornecidos por empresas privadas
* Observações de qualidade do ar
* Observações dos departamentos de transporte

Dezenas de milhares de observações de superfície, juntamente com outros dados, são incorporadas para criar e influenciar as condições atuais disponibilizadas aos utilizadores. Isto inclui não só conjuntos de dados padrão disponíveis livremente, mas também observações únicas obtidas a partir de serviços meteorológicos nacionais em muitos países/regiões, incluindo Índia, Brasil e Canadá e outros inputs proprietários. Estes conjuntos de dados únicos aumentam a resolução espacial e temporal dos dados atuais da condição para os nossos utilizadores. 

Estes conjuntos de dados são revistos em tempo real para precisão para o Sistema de Previsão Digital, que utiliza os algoritmos de inteligência artificial proprietários da AccuWeather para modificar continuamente as previsões, garantindo que incorporam sempre os dados mais recentes e maximizando assim a sua precisão contínua.

**Que modelos criam dados de previsão meteorológica?**

Numerosos sistemas de orientação de previsão meteorológica são utilizados para formular previsões globais. São utilizados mais de 150 modelos de previsão numérica todos os dias, tanto os conjuntos de dados externos como os internos. Isto inclui modelos governamentais como o European Centre ECMWF e o U.S. Global Forecast System (GFS). Além disso, a AccuWeather incorpora modelos proprietários de alta resolução que baixam as previsões para locais específicos e domínios regionais estratégicos para prever o tempo com maior precisão. Os algoritmos únicos de mistura e ponderação da AccuWeather foram desenvolvidos ao longo das últimas décadas. Estes algoritmos aproveitam idealmente as numerosas entradas de previsão para fornecer previsões altamente precisas.

## <a name="weather-services-preview-coverage-and-availability"></a>Cobertura e disponibilidade de serviços meteorológicos (Pré-visualização)

**Que tipo de cobertura posso esperar para diferentes países/regiões?**

A cobertura do serviço meteorológico varia por país/região. Todas as funcionalidades não estão disponíveis em todos os países/regiões. Para mais informações, consulte [a documentação da cobertura.](./weather-coverage.md)

## <a name="data-update-frequency"></a>Frequência de atualização de dados

**Com que frequência os dados das Condições Correntes são atualizados?**

Os dados das Condições Correntes são atualizados aproximadamente uma vez por hora, mas podem ser atualizados com mais frequência com condições de mudança rápida – como grandes alterações de temperatura, alterações nas condições do céu, alterações de precipitação, e assim por diante. A maioria das estações de observação em todo o mundo reportam muitas vezes por hora à medida que as condições mudam. No entanto, algumas áreas ainda só serão atualizadas uma, duas ou quatro vezes por hora em intervalos programados.  

O Azure Maps caches os dados das Condições Atuais até 10 minutos para ajudar a capturar a frequência de atualização quase em tempo real dos dados à medida que ocorrem. Para ver quando a resposta em cache expira e evitar exibir dados desatualizados, pode aproveitar as informações do Cabeçalho expirado no cabeçalho HTTP da resposta API do Azure Maps.

**Com que frequência são atualizados os dados de Previsão Diária e Horária?**

Os dados de Previsão Diária e Horária são atualizados várias vezes por dia, à medida que são recebidas observações atualizadas.  Por exemplo, se uma temperatura alta/baixa prevista for ultrapassada, os nossos dados de Previsão ajustar-se-ão no ciclo de atualização seguinte. Isto pode acontecer em intervalos diferentes, mas normalmente acontece dentro de uma hora. Muitas condições meteorológicas repentinas podem causar uma alteração de dados de previsão. Por exemplo, numa tarde quente de verão, uma tempestade isolada pode emergir subitamente, trazendo forte cobertura de nuvens e chuva. A tempestade isolada pode efetivamente baixar a temperatura até 10 graus. Este novo valor de temperatura terá impacto nas Previsões Horárias e Diárias para o resto do dia e, como tal, será atualizado nos nossos conjuntos de dados.

As APIs de previsão de mapas Azure estão em cache até 30 minutos. Para ver quando a resposta em cache expira e evitar exibir dados desatualizados, pode aproveitar as informações do Cabeçalho expirado no cabeçalho HTTP da resposta API do Azure Maps. Recomendamos a atualização, se necessário, com base num caso específico de utilização do produto e UI (interface de utilizador).

## <a name="developing-with-azure-maps-sdks"></a>Desenvolvimento com Azure Maps SDKs

**O Azure Maps Web SDK suporta de forma nativa a integração dos serviços meteorológicos (Pré-visualização)?**

O Azure Maps Web SDK fornece um módulo de serviços. O módulo de serviços é uma biblioteca auxiliar que facilita a utilização dos serviços Azure Maps REST em aplicações web ou Node.js. utilizando JavaScript ou TypeScript. Para começar, consulte a nossa [documentação.](./how-to-use-services-module.md)

**O Azure Maps Android SDK suporta de forma nativa a integração dos serviços meteorológicos (Preview) ?**

O Azure Maps Android SDKs suporta camadas de azulejos Mercator, que podem ter notação x/y/zoom, notação de chave quad ou notação de caixa de limites EPSG 3857.

Planeamos criar um módulo de serviços para Java/Android semelhante ao módulo Web SDK. O módulo de serviços Android facilitará o acesso a todos os serviços do Azure Maps numa aplicação Java ou Android.  

## <a name="data-visualizations"></a>Visualizações de dados  

**O Azure Maps Power BI Visual suporta azulejos meteorológicos Azure Maps?**

Sim. Para aprender a migrar radares e azulejos de satélite infravermelhos para o visual do Microsoft Power BI, consulte [Adicione uma camada de azulejo ao power bi visual](./power-bi-visual-add-tile-layer.md). 

**Como interpreto as cores usadas para radares e azulejos de satélite?**

O [artigo do conceito](./weather-services-concepts.md#radar-and-satellite-imagery-color-scale) Azure Maps Weather inclui um guia para ajudar a interpretar as cores usadas para azulejos de radar e satélite. O artigo cobre amostras de cores e códigos de cores HEX.
 
**Posso criar animações de radar e azulejos por satélite?**

Sim. Além de radares em tempo real e azulejos de satélite, os clientes do Azure Maps podem solicitar azulejos passados e futuros para melhorar visualizações de dados com sobreposições de mapas. Isto pode ser feito chamando diretamente [Get Map Tile v2 API](/rest/api/maps/renderv2/getmaptilepreview) ou solicitando azulejos através da web SDK Azure Maps. As telhas de radar estão fornecidas até 1,5 horas no passado, e por até 2 horas no futuro. Os azulejos estão disponíveis em intervalos de 5 minutos. Os azulejos infravermelhos são fornecidos até 3 horas no passado, e estão disponíveis em intervalos de 10 minutos. Para obter mais informações, consulte a amostra de código de animação de azulejos [meteorológicos](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Animated%20tile%20layer)de código de código de código de código de origem aberta.  

**Oferece ícones para diferentes condições meteorológicas?**

Sim. Pode encontrar ícones e respetivos códigos [aqui.](./weather-services-concepts.md#weather-icons) Note que apenas algumas das APIs do serviço meteorológico (pré-visualização), tais como  [Get Current Conditions API,](/rest/api/maps/weather/getcurrentconditionspreview)devolva o *iconCode* na resposta. Para obter mais informações, consulte a amostra de código de [código](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Get%20current%20weather%20at%20a%20location)de código de código aberto das Condições Meteorológicas Atuais .

## <a name="next-steps"></a>Passos seguintes

Se esta FAQ não responder à sua pergunta, pode contactar-nos através dos seguintes canais (por ordem de escalada):

* A secção de comentários deste artigo.
* [MSFT Q&Página A para Azure Maps](/answers/topics/azure-maps.html).
* Suporte microsoft. Para criar um novo pedido de suporte, no [portal Azure,](https://portal.azure.com/)no separador Ajuda, selecione o botão de suporte **Ajuda +** e, em seguida, selecione Novo pedido **de suporte**.
* [Azure Maps UserVoice](https://feedback.azure.com/forums/909172-azure-maps) para submeter pedidos de funcionalidades.

Saiba como solicitar dados meteorológicos em tempo real e previstos utilizando os serviços Azure Maps Weather (Preview):
> [!div class="nextstepaction"]
> [Solicitar dados meteorológicos em tempo real ](how-to-request-weather-data.md)

Artigo de conceitos Azure Maps Weather services (Preview):
> [!div class="nextstepaction"]
> [Conceitos dos Serviços de meteorologia](weather-coverage.md)

Explore a documentação da API dos serviços Azure Maps Weather (Preview):

> [!div class="nextstepaction"]
> [Serviços do Azure Maps](/rest/api/maps/weather)