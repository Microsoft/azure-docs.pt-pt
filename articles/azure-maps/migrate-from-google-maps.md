---
title: Tutorial - Migrar do Google Maps para Azure Maps Microsoft Azure Maps
description: Tutorial sobre como migrar do Google Maps para o Microsoft Azure Maps. A orientação acompanha-o como mudar para Azure Maps APIs e SDKs.
author: rbrundritt
ms.author: richbrun
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: ee7eda58c211ca570b052d55c813999e62b95fde
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876316"
---
# <a name="tutorial---migrate-from-google-maps-to-azure-maps"></a>Tutorial - Migrar do Google Maps para Azure Maps

Este artigo fornece informações sobre como migrar aplicações baseadas na web, mobile e servidor do Google Maps para a plataforma Microsoft Azure Maps. Este tutorial inclui amostras de código comparativas, sugestões de migração e boas práticas para migrar para a Azure Maps.

## <a name="azure-maps-platform-overview"></a>Visão geral da plataforma Azure Maps

O Azure Maps fornece aos desenvolvedores de todas as indústrias poderosas capacidades geoespaciais. As capacidades são embaladas com dados de mapas atualizados regularmente para fornecer contexto geográfico para aplicações web e móveis. Azure Maps tem um conjunto de APIs de REST Azure One API. As APIs REST oferecem Gráficos renderizando, Pesquisa, Encaminhamento, Tráfego, Fusos Horários, Geolocalização, Geofencing, Mapas, Meteorologia, Mobilidade e Operações Espaciais. As operações são acompanhadas por SDKs Web e Android para facilitar o desenvolvimento, flexível e portátil em várias plataformas.

## <a name="high-level-platform-comparison"></a>Comparação de plataforma de alto nível

A tabela fornece uma lista de alto nível de funcionalidades do Azure Maps, que correspondem às funcionalidades do Google Maps. Esta lista não mostra todas as funcionalidades do Azure Maps. As funcionalidades adicionais do Azure Maps incluem: acessibilidade, geofencing, isochrones, operações espaciais, acesso direto a azulejos, serviços de lote e comparações de cobertura de dados (isto é, cobertura de imagens).

| Funcionalidade do Google Maps         | Suporte Azure Maps                     |
|-----------------------------|:--------------------------------------:|
| Web SDK                     | ✓                                      |
| SDK Android                 | ✓                                      |
| iOS SDK                     | Planeado                                |
| APIs de serviço de descanso           | ✓                                      |
| Instruções (Encaminhamento)        | ✓                                      |
| Matriz de distância             | ✓                                      |
| Elevação                   | Planeado                                |
| Geocoding (Para a frente/reverso) | ✓                                      |
| Geolocalização                 | N/D                                    |
| Estradas mais próximas               | ✓                                      |
| Pesquisa de lugares               | ✓                                      |
| Detalhes dos locais              | N/A – site & número de telefone disponível |
| Fotos de lugares               | N/D                                    |
| Colocar Autocompleto          | ✓                                      |
| Estalar para a estrada                | ✓                                      |
| Limites de velocidade                | ✓                                      |
| Mapas estáticos                 | ✓                                      |
| Vista de rua estática          | N/D                                    |
| Fuso Horário                   | ✓                                      |
| API incorporada de mapas           | N/D                                    |
| URLs de mapa                    | N/D                                    |

O Google Maps fornece uma autenticação básica baseada em chaves. O Azure Maps fornece a autenticação básica baseada em chaves e a autenticação do Azure Ative Directory. A autenticação do Azure Ative Directory fornece mais funcionalidades de segurança, em comparação com a autenticação básica baseada em chaves.

## <a name="licensing-considerations"></a>Considerações de licenciamento

Ao migrar para o Azure Maps do Google Maps, considere os seguintes pontos sobre o licenciamento.

- Azure Maps cobra pelo uso de mapas interativos, que se baseia no número de azulejos de mapas carregados. Por outro lado, o Google Maps cobra por carregar o controlo do mapa. Nos SDKs Azure Maps interativos, os azulejos do mapa são automaticamente cached para reduzir o custo de desenvolvimento. Uma transação Azure Maps é gerada por cada 15 azulejos de mapa que são carregados. O Azure Maps SDKs interac interactive usa azulejos de 512 pixels e, em média, gera uma ou menos transações por visualização de página.
- Muitas vezes, é mais rentável substituir imagens de mapas estáticos dos serviços web do Google Maps com o Azure Maps Web SDK. O Azure Maps Web SDK utiliza azulejos de mapas. A menos que o utilizador faça o painel e faça zoom no mapa, o serviço gera frequentemente apenas uma fração de uma transação por carga de mapa. A Azure Maps web SDK tem opções para desativar panorâmicas e zooming, se desejar. Além disso, a web SDK Azure Maps fornece muito mais opções de visualização do que o serviço web de mapa estático.
- O Azure Maps permite que os dados da sua plataforma sejam armazenados no Azure. Além disso, os dados podem ser cached em outro lugar por até seis meses, de acordo com os [termos de utilização](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Aqui estão alguns recursos relacionados para Azure Maps:

- [Página de preços do Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Termo de utilização do Azure Maps](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (incluído nos Termos dos Serviços Online da Microsoft)
- [Escolha o nível de preços certo em Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Plano de migração sugerido

Segue-se um plano de migração de alto nível.

1. Faça o inventário dos SDKs e serviços do Google Maps que a sua aplicação utiliza. Verifique se o Azure Maps fornece SDKs e serviços alternativos.
2. Se ainda não tiver um, crie uma subscrição Azure em [https://azure.com](https://azure.com) .
3. Crie uma conta Azure Maps[(documentação)](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)e chave de autenticação ou Diretório Ativo Azure[(documentação).](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)
4. Migrar o teu código de aplicação.
5. Teste a sua aplicação migratória.
6. Coloque a sua aplicação migratória para a produção.

## <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps

Para criar uma conta Azure Maps e ter acesso à plataforma Azure Maps, siga estes passos:

1. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
2. Inicie sessão no [portal do Azure](https://portal.azure.com/).
3. Criar uma [conta Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys). 
4. [Obtenha a sua chave de subscrição Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication#view-authentication-details) ou configurar a autenticação do Azure Ative Directory para uma maior segurança.

## <a name="azure-maps-technical-resources"></a>Recursos técnicos Azure Maps

Aqui está uma lista de recursos técnicos úteis para o Azure Maps.

- Visão geral: [https://azure.com/maps](https://azure.com/maps)
- Documentação: [https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Amostras de código Web SDK: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Fóruns de Desenvolvimento: [https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Vídeos: [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blog: [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Blog Tecnológico: [https://aka.ms/AzureMapsTechBlog](https://aka.ms/AzureMapsTechBlog)
- Feedback do Azure Maps (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)
- [Caderno jupyter Azure Maps](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)

## <a name="migration-support"></a>Apoio à migração

Os desenvolvedores podem procurar apoio migratório através dos [fóruns](https://aka.ms/AzureMapsForums) ou através de uma das muitas opções de suporte do Azure: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

Pode aprender a migrar a sua aplicação Google Maps utilizando: 

[Migrar uma aplicação Android](migrate-from-google-maps-android-app.md) 

[Migrar um serviço Web](migrate-from-google-maps-web-services.md) 

[Migrar uma aplicação Web](migrate-from-google-maps-web-app.md)