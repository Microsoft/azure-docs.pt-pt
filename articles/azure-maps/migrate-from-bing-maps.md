---
title: 'Tutorial: Migrar de Bing Maps para Azure Maps Microsoft Azure Maps'
description: Um tutorial sobre como migrar de Bing Maps para Microsoft Azure Maps. A orientação acompanha-o como mudar para Azure Maps APIs e SDKs.
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: ab4b4e0ab7d0474dc4fe7692cfe3c46835095f45
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873749"
---
# <a name="tutorial---migrate-from-bing-maps-to-azure-maps"></a>Tutorial - Migrar de Bing Maps para Azure Maps

Este guia fornece informações sobre como migrar aplicações baseadas na web, mobile e servidor, desde o Bing Maps até à plataforma Azure Maps. Este guia inclui amostras de código comparativas, sugestões de migração e boas práticas para migrar para a Azure Maps.

## <a name="azure-maps-platform-overview"></a>Visão geral da plataforma Azure Maps

O Azure Maps fornece aos desenvolvedores de todas as indústrias poderosas capacidades geoespaciais, repletas de dados de mapeamento mais recentes disponíveis para fornecer contexto geográfico para aplicações web e móveis. Azure Maps é um conjunto compatível com API Azure One de APIs rest para mapas, pesquisa, encaminhamento, tráfego, fusos horários, Geofencing, Map Data, Weather Data, e muitos mais serviços acompanhados por SDKs Web e Android para tornar o desenvolvimento fácil, flexível e portátil em várias plataformas. [O Azure Maps também está disponível no Power BI.](power-bi-visual-getting-started.md)

## <a name="high-level-platform-comparison"></a>Comparação de plataforma de alto nível

A tabela seguinte fornece uma lista de alto nível de funcionalidades do Bing Maps e o suporte relativo para essas funcionalidades no Azure Maps. Esta lista não inclui funcionalidades adicionais do Azure Maps, tais como acessibilidade, APIs de geofencing, serviços de tráfego, operações espaciais, acesso de azulejos de mapa direto e serviços de lote.

| Recurso Bing Maps                     | Suporte Azure Maps |
|---------------------------------------|:------------------:|
| Web SDK                               | ✓                  |
| SDK Android                           | ✓                  |
| iOS SDK                               | Planeado            |
| UWP SDK                               | Planeado            |
| WPF SDK                               | Planeado            |
| APIs de serviço de descanso                     | ✓                  |
| Sugestão Automática                           | ✓                  |
| Instruções (incluindo o camião)          | ✓                  |
| Matriz de distância                       | ✓                  |
| Elevações                            | Planeado            |
| Imagens - Mapa Estático                  | ✓                  |
| Metadados de imagens                      | ✓                  |
| Isochrones                            | ✓                  |
| Insights locais                        | ✓                  |
| Pesquisa local                          | ✓                  |
| Reconhecimento de Localização                  | ✓                  |
| Localizações (geocoding para a frente/reverso) | ✓                  |
| Rotas de Itinerário Otimizado            | Planeado            |
| Encaixe nas estradas                         | ✓                  |
| Serviços de Dados Espaciais (SDS)           | Parcial            |
| Fuso Horário                             | ✓                  |
| Incidentes de Trânsito                     | ✓                  |
| Mapas orientados por configuração             | N/D                |

O Bing Maps fornece uma autenticação básica baseada em chaves. O Azure Maps fornece a autenticação básica baseada em chaves, bem como a autenticação altamente segura do Azure Ative Directory.

## <a name="licensing-considerations"></a>Considerações de licenciamento

Ao migrar para a Azure Maps a partir de Bing Maps, deve ser considerado o seguinte no que diz respeito ao licenciamento.

-   O Azure Maps cobra pelo uso de mapas interativos com base no número de azulejos de mapas carregados, enquanto bing Maps cobra para o carregamento do controlo do mapa (sessões). No Azure Maps, os azulejos do mapa são automaticamente cached para reduzir o custo para o desenvolvedor. Uma transação Azure Maps é gerada por cada 15 azulejos de mapa que são carregados. Os SDKs Azure Maps interativos utilizam azulejos de 512 pixels e, em média, geram uma ou menos transações por visualização de página.

-   O Azure Maps permite que os dados da sua plataforma sejam armazenados no Azure. Também pode ser colocado em cache em outro lugar por um máximo de seis meses, de acordo com os [termos de utilização](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Aqui estão alguns recursos relacionados com licenciamento para Azure Maps:

-   [Página de preços do Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
-   [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
-   [Termo de utilização do Azure Maps](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (incluído nos Termos dos Serviços Online da Microsoft)
-   [Escolha o nível de preços certo em Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Plano de migração sugerido

Segue-se um plano de migração de alto nível.

1.  Faça o inventário do que bing Maps SDKs e serviços que a sua aplicação está a usar e verifique se o Azure Maps fornece SDKs e serviços alternativos para que possa migrar.
2.  Crie uma subscrição Azure (se já não tiver uma) em <https://azure.com> .
3.  Crie uma conta Azure Maps[(documentação)](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)e chave de autenticação ou Diretório Ativo Azure[(documentação).](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)
4.  Migrar o teu código de aplicação.
5.  Teste a sua aplicação migratória.
6.  Coloque a sua aplicação migratória para a produção.

## <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps

Para criar uma conta Azure Maps e ter acesso à plataforma Azure Maps, siga estes passos:

1. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
2. Inicie sessão no [portal do Azure](https://portal.azure.com/).
3. Criar uma [conta Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys). 
4. [Obtenha a sua chave de subscrição Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication#view-authentication-details) ou configurar a autenticação do Azure Ative Directory para uma maior segurança.

## <a name="azure-maps-technical-resources"></a>Recursos técnicos Azure Maps

Aqui está uma lista de recursos técnicos úteis para o Azure Maps.

-   Descrição geral: https://azure.com/maps
-   Documentação: <https://aka.ms/AzureMapsDocs>
-   Amostras de código Web SDK: <https://aka.ms/AzureMapsSamples>
-   Fóruns de Desenvolvimento: <https://aka.ms/AzureMapsForums>
-   Vídeos: <https://aka.ms/AzureMapsVideos>
-   Blog: <https://aka.ms/AzureMapsBlog>
-   Feedback do Azure Maps (UserVoice): <https://aka.ms/AzureMapsFeedback>

## <a name="migration-support"></a>Apoio à migração

Os desenvolvedores podem procurar apoio migratório através dos [fóruns](https://aka.ms/AzureMapsForums) ou através de uma das muitas opções de suporte do Azure: <https://azure.microsoft.com/support/options/>

## <a name="new-terminology"></a>Nova terminologia 

Segue-se uma lista de termos comuns do Bing Maps e que são conhecidos por um termo diferente em Azure Maps.

| Termo Bing Maps                    | Termo Azure Maps                                                |
|-----------------------------------|----------------------------------------------------------------|
| Aérea                            | Satélite ou Antena                                            |
| Instruções                        | Também pode ser referido como Encaminhamento                             |
| Entidades                          | Geometrias ou Características                                         |
| `EntityCollection`                | Fonte de dados ou camada                                           |
| `Geopoint`                        | Posição                                                       |
| `GeoXML`                          | Ficheiros XML no módulo IO espacial                             |
| Sobreposição de solo                    | Camada de imagem                                                    |
| Híbrido (em referência ao tipo de mapa) | Satélite com estradas                                           |
| Infobox                           | Popup                                                          |
| Localização                          | Posição                                                       |
| `LocationRect`                    | Caixa de delimitação                                                   |
| Tipo de Mapa                          | Estilo de mapa                                                      |
| Barra de navegação                    | Picker de estilo de mapa, controlo de zoom, controlo de tom, controlo de bússola |
| Pino de empurrar                           | Camada de bolha, camada de símbolo ou marcador HTML                      |

## <a name="next-steps"></a>Passos seguintes

Saiba os detalhes de como migrar a sua aplicação Bing Maps com estes artigos:

> [!div class="nextstepaction"]
> [Migrar uma aplicação Web](migrate-from-bing-maps-web-app.md)

> [!div class="nextstepaction"]
> [Migrar um serviço Web](migrate-from-bing-maps-web-services.md)
