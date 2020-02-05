---
title: 'Tutorial: migrar do Google Maps para o mapas do Azure | Mapas do Microsoft Azure'
description: Um tutorial sobre como migrar do Google Maps para mapas do Microsoft Azure. Orientações orienta você sobre como alternar para APIs e SDKs do Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 1f6f282406c6813b2b126c300f21bda21e8f9464
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988979"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>Migrar do Google Maps para mapas do Azure

Este tutorial fornece informações sobre como migrar aplicativos Web, móveis e baseados em servidor do Google Maps para a plataforma de mapas do Microsoft Azure. Este tutorial inclui exemplos de código comparativa, sugestões de migração e práticas recomendadas para migrar para mapas do Azure.

## <a name="azure-maps-platform-overview"></a>Visão geral da plataforma do Azure Maps

O Azure Maps fornece aos desenvolvedores de todos os setores recursos geoespaciais poderosos, empacotados com os dados de mapa regularmente atualizados para fornecer contexto geográfico para aplicativos Web e móveis. O Azure Maps tem um conjunto de APIs de API compatível com a API. Essas APIs rest oferecem Mapas, Pesquisa, Encaminhamento, Tráfego, Fusos Horários, Geolocalização, Geofencing, Dados do Mapa, Meteorologia, Mobilidade e Operações Espaciais. As operações são acompanhadas por SDKs Web e Android para facilitar o desenvolvimento, flexível e portátil em várias plataformas.

## <a name="high-level-platform-comparison"></a>Comparação de plataforma de alto nível

A tabela seguinte fornece uma lista de alto nível de funcionalidades do Azure Maps, que correspondem às funcionalidades do Google Maps. Esta lista não mostra todas as funcionalidades do Azure Maps. Os Mapas Azure adicionais incluem acessibilidade, apis geofencing, isochrones, operações espaciais, acesso ao azulejo do mapa direto, serviços de lote e comparações de cobertura de dados (isto é, cobertura de imagens).

| Recurso do Google Maps         | Suporte ao Azure Maps                     |
|-----------------------------|:--------------------------------------:|
| Web SDK                     | ✓                                      |
| SDK Android                 | ✓                                      |
| iOS SDK                     | Planeado                                |
| APIs de serviço REST           | ✓                                      |
| Direções (roteamento)        | ✓                                      |
| Matriz de Distância             | ✓                                      |
| Elevação                   | Planeado                                |
| Geocodificação (encaminhar/reverter) | ✓                                      |
| Geolocalização                 | N/A                                    |
| Pesquisa de locais               | ✓                                      |
| Detalhes de locais              | N/A – site & número de telefone disponível |
| Fotos de locais               | N/A                                    |
| Preenchimento automático          | ✓                                      |
| Mapas estáticos                 | ✓                                      |
| Exibição de rua estática          | N/A                                    |
| Fuso Horário                   | ✓                                      |
| API de mapas inseridos           | N/A                                    |
| Mapear URLs                    | N/A                                    |

O Google Maps fornece a autenticação básica baseada em chaves. O Azure Maps fornece tanto a autenticação básica baseada em chaves como a autenticação altamente segura, azure Ative Directory.

## <a name="licensing-considerations"></a>Considerações sobre licenciamento

Ao migrar para o Azure Maps do Google Maps, os pontos a seguir devem ser considerados em relação ao licenciamento.

- O Azure Maps cobra pelo uso de mapas interativos, que são baseados no número de telhas de mapas carregadas, mas o Google Maps cobra para o carregamento do controlo do mapa. Nos SDKs interativos do Azure Maps, os blocos de mapa são armazenados em cache automaticamente para reduzir o custo do desenvolvedor. Uma transação do Azure Maps é gerada para cada 15 blocos de mapa que são carregados. Os SDKs interativos do Azure Maps usam blocos de 512 pixels e, em média, gera uma ou menos transações por exibição de página.
- Muitas vezes é muito mais rentável substituir imagens estáticas de mapas dos serviços web do Google Maps pelo Azure Maps Web SDK. O Azure Maps Web SDK utiliza azulejos de mapas e, a menos que o utilizador faça panelas e zooms o mapa, muitas vezes gerarão apenas uma fração de uma transação por carga de mapa. O SDK da Web do Azure Maps tem opções para desabilitar o movimento panorâmico e o zoom. Além disso, o SDK da Web do Azure Maps fornece muito mais opções de visualização do que um serviço Web de mapa estático.
- O mapas do Azure permite que os dados de sua plataforma sejam armazenados no Azure. Ele também pode ser armazenado em cache em outro lugar por até seis meses, de acordo com os [termos de uso](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Aqui estão alguns recursos relacionados para mapas do Azure:

- [Página de preços do Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- O [Azure mapeia o termo de uso](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (incluído nos termos do Microsoft Online Services)
- [Escolha o tipo de preço certo no Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Plano de migração sugerido

Este é um plano de migração de alto nível.

1. Faça o inventário do que o Google Maps SDKs e serviços que seu aplicativo está usando e verifique se o Azure Maps fornece SDKs e serviços alternativos para você migrar para o.
2. Crie uma assinatura do Azure (se você ainda não tiver uma) em [https://azure.com](https://azure.com).
3. Crie uma conta do Azure Maps ([documentação](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) e chave de autenticação ou Azure Active Directory ([documentação](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)).
4. Migre o código do aplicativo.
5. Teste seu aplicativo migrado.
6. Implante seu aplicativo migrado para produção.

## <a name="azure-maps-technical-resources"></a>Recursos técnicos do Azure Maps

Aqui está uma lista de recursos técnicos úteis para mapas do Azure.

- Visão geral: [https://azure.com/maps](https://azure.com/maps)
- Documentação: [https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Exemplos de código do SDK Web: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Fóruns para desenvolvedores: [https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Vídeos: [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blog: [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Comentários do mapas do Azure (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)

## <a name="migration-support"></a>Suporte para migração

Os desenvolvedores podem procurar suporte à migração por meio dos [fóruns](https://aka.ms/AzureMapsForums) ou por meio de uma das muitas opções de suporte do Azure: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Passos seguintes

Saiba como migrar seu aplicativo do Google Maps com estes artigos:

> [!div class="nextstepaction"]
> [Migrar um aplicativo Web](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Migrar um aplicativo Android](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Migrar um serviço Web](migrate-from-google-maps-web-services.md)
