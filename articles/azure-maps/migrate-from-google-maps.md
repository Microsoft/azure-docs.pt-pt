---
title: 'Tutorial: Migrar do Google Maps para o Azure Maps Microsoft Azure Maps'
description: Um tutorial sobre como migrar do Google Maps para o Microsoft Azure Maps. A orientação acompanha-o através de como mudar para APIs e SDKs do Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 7b73923b7fc32ae83bfc8405d074835c02031a63
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77913706"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>Migrar do Google Maps para o Azure Maps

Este tutorial fornece informações sobre como migrar aplicações web, móveis e baseadas em servidores do Google Maps para a plataforma Microsoft Azure Maps. Este tutorial inclui amostras de código comparativos, sugestões de migração e boas práticas para migrar para o Azure Maps.

## <a name="azure-maps-platform-overview"></a>Visão geral da plataforma Azure Maps

O Azure Maps fornece aos desenvolvedores de todas as indústrias poderosas capacidades geoespaciais. As capacidades estão repletas de dados de mapas atualizados regularmente para fornecer contexto geográfico para aplicações web e móveis. O Azure Maps tem um conjunto de APIs de API compatível com a API. As APIs restantes oferecem mapas renderização, pesquisa, encaminhamento, tráfego, fusos horários, geolocalização, geofencing, dados do mapa, meteorologia, mobilidade e operações espaciais. As operações são acompanhadas por SDKs Web e Android para facilitar o desenvolvimento, flexível e portátil em várias plataformas.

## <a name="high-level-platform-comparison"></a>Comparação de plataforma de alto nível

A tabela fornece uma lista de alto nível de funcionalidades do Azure Maps, que correspondem às funcionalidades do Google Maps. Esta lista não mostra todas as funcionalidades do Azure Maps. As funcionalidades adicionais do Azure Maps incluem: acessibilidade, geofencing, isochrones, operações espaciais, acesso ao azulejo do mapa direto, serviços de lote e comparações de cobertura de dados (isto é, cobertura de imagens).

| Recurso do Google Maps         | Suporte do Azure Maps                     |
|-----------------------------|:--------------------------------------:|
| Web SDK                     | ✓                                      |
| SDK Android                 | ✓                                      |
| iOS SDK                     | Planeado                                |
| SERVIÇO DE REPOUSO APIs           | ✓                                      |
| Instruções (Encaminhamento)        | ✓                                      |
| Matriz de Distância             | ✓                                      |
| Elevação                   | Planeado                                |
| Geocodificação (Para a frente/para trás) | ✓                                      |
| Geolocalização                 | N/D                                    |
| Estradas mais próximas               | ✓                                      |
| Pesquisa de locais               | ✓                                      |
| Detalhes dos locais              | N/A - número de telefone & do site disponível |
| Fotos de lugares               | N/D                                    |
| Lugar Autocompleto          | ✓                                      |
| Snap to Road                | ✓                                      |
| Limites de velocidade                | ✓                                      |
| Mapas Estáticos                 | ✓                                      |
| Vista de rua estática          | N/D                                    |
| Fuso Horário                   | ✓                                      |
| Mapas Incorporados API           | N/D                                    |
| URLs de mapa                    | N/D                                    |

O Google Maps fornece autenticação básica baseada em chaves. O Azure Maps fornece autenticação básica baseada em chaves e autenticação do Diretório Ativo Azure. A autenticação do Diretório Ativo Azure fornece mais funcionalidades de segurança, em comparação com a autenticação básica baseada em chaves.

## <a name="licensing-considerations"></a>Considerações de licenciamento

Ao migrar para o Azure Maps do Google Maps, considere os seguintes pontos sobre o licenciamento.

- O Azure Maps cobra pelo uso de mapas interativos, que se baseiam no número de telhas de mapas carregadas. Por outro lado, o Google Maps cobra por carregar o controlo do mapa. Nos SDKs interativos do Azure Maps, os azulejos do mapa são automaticamente cached para reduzir o custo de desenvolvimento. Uma transação do Azure Maps é gerada por cada 15 telhas de mapas que são carregadas. Os SDKs interativos do Azure Maps utilizam azulejos de 512 pixels e, em média, gera mato ou menos transações por visualização de página.
- Muitas vezes, é mais rentável substituir imagens estáticas de mapas dos serviços web do Google Maps pelo Azure Maps Web SDK. O Azure Maps Web SDK utiliza azulejos de mapas. A menos que o utilizador faça panelas e zoom o mapa, o serviço gera frequentemente apenas uma fração de uma transação por carga de mapa. O Azure Maps web SDK tem opções para desativar a panorâmica e o zooming, se desejar. Além disso, o Web SDK do Azure Maps fornece muito mais opções de visualização do que o serviço web de mapas estáticos.
- O Azure Maps permite que os dados da sua plataforma sejam armazenados no Azure. Além disso, os dados podem ser cacheados em outros lugares por um máximo de seis meses, de acordo com os [termos de utilização](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Aqui estão alguns recursos relacionados para o Azure Maps:

- [Página de preços do Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Termo de utilização do Azure Maps](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (incluído nos Termos de Serviços Online da Microsoft)
- [Escolha o nível de preços certos no Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Plano de migração sugerido

Segue-se um plano de migração de alto nível.

1. Faça o inventário dos SDKs e serviços do Google Maps que a sua aplicação utiliza. Verifique se o Azure Maps fornece SDKs e serviços alternativos.
2. Se ainda não tem uma, crie uma [https://azure.com](https://azure.com)subscrição Azure em .
3. Criar uma conta Azure Maps[(documentação)](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)e chave de autenticação ou Diretório Ativo Azure[(documentação).](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)
4. Migra o teu código de candidatura.
5. Teste a sua aplicação migratória.
6. Implante a sua aplicação migrada para a produção.

## <a name="azure-maps-technical-resources"></a>Recursos técnicos do Azure Maps

Aqui está uma lista de recursos técnicos úteis para o Azure Maps.

- Visão geral:[https://azure.com/maps](https://azure.com/maps)
- Documentação:[https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Amostras de código Web SDK:[https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Fóruns de Desenvolvimento:[https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Vídeos:[https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blog:[https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Blog tecnológico:[https://aka.ms/AzureMapsTechBlog](https://aka.ms/AzureMapsTechBlog)
- Feedback do Azure Maps (UserVoice):[https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)
- [Caderno De Jupyter do Azure Maps] [https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook]

## <a name="migration-support"></a>Apoio à migração

Os desenvolvedores podem procurar apoio migratório através dos [fóruns](https://aka.ms/AzureMapsForums) ou através de uma das muitas opções de suporte azure:[https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Passos seguintes

Conheça os detalhes de como migrar a sua aplicação do Google Maps com estes artigos:

> [!div class="nextstepaction"]
> [Migrar uma aplicação Web](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Migrar uma aplicação Android](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Migrar um serviço Web](migrate-from-google-maps-web-services.md)
