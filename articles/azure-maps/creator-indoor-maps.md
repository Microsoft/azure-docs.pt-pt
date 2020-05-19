---
title: Trabalho com mapas interiores no Criador do Azure Maps
description: Este artigo introduz conceitos que se aplicam aos serviços do Criador do Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 34a1495d1a14e35bc5a94bfc01f4034c6fd6de72
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598396"
---
# <a name="creator-for-indoor-maps"></a>Criador de mapas interiores

Este artigo introduz conceitos e ferramentas que se aplicam ao Criador do Azure Maps. Recomendamos que leia este artigo antes de começar a usar o Azure Maps Creator API e SDK.

Pode utilizar o Criador para desenvolver aplicações com funcionalidades de mapas baseadas em dados de mapas interiores. Este artigo descreve o processo de upload, conversão, criação e utilização dos dados do seu mapa. Todo o fluxo de trabalho é ilustrado no diagrama abaixo.

![Fluxo de trabalho de dados do mapa do criador](./media/creator-indoor-maps/workflow.png)

## <a name="create-azure-maps-creator"></a>Criar criador de mapas azure

Para utilizar os serviços do Criador, o Criador do Azure Maps deve ser criado numa conta Azure Maps. Para obter informações sobre como criar o Criador do Azure Maps no Azure Maps, consulte [Manage Azure Maps Creator](how-to-manage-creator.md).

## <a name="upload-a-drawing-package"></a>Faça upload de um pacote de desenho

O Criador recolhe dados do mapa interior através da conversão de um pacote de desenho carregado. O pacote de desenho representa uma instalação construída ou remodelada. Para obter informações sobre os requisitos do pacote de desenho, consulte [os requisitos do pacote de desenho.](drawing-requirements.md)

Utilize a API de upload de [dados do Azure Maps](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) para fazer o upload de um pacote de desenho.  Após um upload bem sucedido, a API de upload de dados devolverá um identificador de dados do utilizador `udid` ( ). O `udid` será utilizado no próximo passo para converter o pacote carregado em dados de mapas interiores.

## <a name="convert-a-drawing-package"></a>Converter um pacote de desenho

O serviço de [conversão do Azure Maps](https://docs.microsoft.com/rest/api/maps/data/conversion) converte um pacote de desenho carregado em dados de mapas interiores. O serviço de Conversão também valida o pacote. As questões de validação são classificadas em dois tipos: erros e avisos. Se forem detetados erros, o processo de conversão falha. Se os avisos forem detetados, a conversão terá sucesso. No entanto, recomenda-se que reveja e resolva todos os avisos. Um aviso significa que parte da conversão foi ignorada ou fixada automaticamente. A não resolução dos avisos pode resultar em erros nos últimos processos. Para mais informações, consulte [advertências e erros do pacote de desenho.](drawing-conversion-error-codes.md)

Quando ocorre um erro, o serviço de Conversão fornece uma ligação à aplicação web autónoma do [Azure Maps Drawing Error Visualizer.](drawing-error-visualizer.md) Pode utilizar o Visualizador de Erro de Desenho para inspecionar [os avisos e erros](drawing-conversion-error-codes.md) do pacote de desenho ocorridos durante o processo de conversão. Depois de corrigir os erros, pode então tentar carregar e converter o pacote.

## <a name="create-indoor-map-data"></a>Criar dados de mapas interiores

O Azure Maps Creator presta três serviços:

* [Serviço](https://docs.microsoft.com/rest/api/maps/dataset/createpreview)de conjunto de dados .
Utilize o serviço Dataset para criar um conjunto de dados a partir de um pacote de desenho convertido.
* [Serviço tileset.](https://docs.microsoft.com/rest/api/maps/tileset/createpreview)
Utilize o serviço Tileset para criar uma representação baseada em vetores de um conjunto de dados. As aplicações podem usar um azulejo para apresentar uma visão visual baseada em azulejos do conjunto de dados.
* [Serviço de Estado de Recurso](https://docs.microsoft.com/rest/api/maps/featurestate). Utilize o serviço Feature State para suportar o estilo dinâmico do mapa. O estilo dinâmico do mapa permite que as aplicações reflitam eventos em tempo real em espaços fornecidos pelo sistema IoT.

### <a name="datasets"></a>Conjuntos de Dados

Um conjunto de dados é uma coleção de características do mapa interior. As características do mapa interior representam instalações definidas num pacote de desenho convertido. Depois de criar um conjunto de dados com o [serviço Dataset,](https://docs.microsoft.com/rest/api/maps/dataset/createpreview)pode criar qualquer número de [tilesets](#tilesets) ou conjuntos de [funcionalidades](#feature-statesets).

O [serviço Dataset](https://docs.microsoft.com/rest/api/maps/dataset/createpreview) permite que os desenvolvedores, a qualquer momento, adicionem ou removam as instalações a um conjunto de dados existente. Para obter mais informações sobre como atualizar um conjunto de dados existente utilizando a API, consulte as opções de apêndice no [serviço Dataset](https://docs.microsoft.com/rest/api/maps/dataset/createpreview). Para um exemplo de como atualizar um conjunto de dados, consulte a [Manutenção de Dados](#data-maintenance).

### <a name="tilesets"></a>Azulejos

Um azulejo é uma coleção de dados vetoriais que representa um conjunto de azulejos uniformes. Os desenvolvedores podem usar o [serviço Tileset](https://docs.microsoft.com/rest/api/maps/tileset/createpreview) para criar azulejos a partir de um conjunto de dados.

Para refletir diferentes fases de conteúdo, pode criar vários azulejos a partir do mesmo conjunto de dados. Por exemplo, você poderia fazer um azulejo com mobiliário e equipamento, e outro azulejo sem móveis e equipamentos.  Pode optar por gerar um tileset com as mais recentes atualizações de dados, e um sem as mais recentes atualizações de dados.

Além dos dados vetoriais, o azulejo fornece metadados para otimização de renderização de mapas. Por exemplo, os metadados de azulejos contêm um nível de zoom min e max para o azulejo. Os metadados também fornecem uma caixa de delimitação que define a extensão geográfica do azulejo. A caixa de delimitação permite que uma aplicação deajuste programáticamente o ponto central correto. Para obter mais informações sobre metadados de azulejos, consulte [a Tileset List API](https://docs.microsoft.com/rest/api/maps/tileset/listpreview).

Uma vez criado um azulejo, pode ser recuperado pelo [serviço Render V2](#render-v2-service).

Se um azulejo ficar desatualizado e já não for útil, pode eliminar o azulejo. Para obter mais informações sobre como eliminar os azulejos, consulte a [Manutenção de Dados](#data-maintenance).

>[!NOTE]
>Um azulejo é independente do conjunto de dados a partir do qual foi criado. Se criar azulejos a partir de um conjunto de dados e, posteriormente, atualizar esse conjunto de dados, os tilesets não serão atualizados. Para refletir as mudanças num conjunto de dados, tem de criar novos azulejos. Da mesma forma, se eliminar um tileset, o conjunto de dados não será afetado.

### <a name="feature-statesets"></a>Conjuntos de estados de recurso

Os estados de recurso são coleções de propriedades dinâmicas (*estados*) atribuídas a funcionalidades de conjunto de dados, como quartos ou equipamentos. Um exemplo de um *estado* pode ser a temperatura ou a ocupação. Cada *estado* é um par chave/valor contendo o nome da propriedade, o valor e o carimbo temporal da última atualização.

O [serviço Feature State](https://docs.microsoft.com/rest/api/maps/featurestate/createstatesetpreview) permite criar e gerir um conjunto de funcionalidades para um conjunto de dados. O estado é definido por um ou mais *estados.* Cada característica, como uma sala, pode ter um *estado* ligado a ele.

O valor de cada *estado* num estado pode ser atualizado ou recuperado por dispositivos IoT ou outras aplicações.  Por exemplo, utilizando a [API update state update](https://docs.microsoft.com/rest/api/maps/featurestate/updatestatespreview)feature, os dispositivos que medem a ocupação espacial podem sistematicamente registar a mudança de estado de uma sala.

Uma aplicação pode usar um conjunto de estado de funcionalidade para tornar dinamicamente funcionalidades numa instalação de acordo com o seu estado atual e respetivo estilo de mapa. Para obter mais informações sobre a utilização de funcionalidades de estados para modelar funcionalidades num mapa de renderização, consulte o [Módulo SDK da Web Interior](#indoor-maps-module).

>[!NOTE]
>Tal como os tilesets, a alteração de um conjunto de dados não afeta o estado da funcionalidade existente e a exclusão de um conjunto de estados de funcionalidade não terá qualquer efeito no conjunto de dados a que está anexado.

## <a name="using-indoor-maps"></a>Usando mapas interiores

### <a name="render-v2-service"></a>Serviço Render V2

O Azure Maps [Render V2 service-Get Map Tile API](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) foi estendido para apoiar os tilesets do Criador.

[Render V2 service-Get Map State Tile API](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) permite aplicações para solicitar tilesets. Os azulejos podem então ser integrados num controlo de mapas ou SDK. Para um exemplo de um controlo de mapa que utiliza o serviço Render V2, consulte [o Módulo mapas interiores](#indoor-maps-module).

### <a name="web-feature-service-api"></a>API do Serviço de Recurso Web

Os conjuntos de dados podem ser consultados utilizando o Serviço de [Funcionalidadeweb (WFS) API](https://docs.microsoft.com/rest/api/maps/wfs). O WFS segue as funcionalidades da [API do Consórcio Geoespacial Aberto.](http://docs.opengeospatial.org/DRAFTS/17-069r1.html) A API WFS permite-lhe consultar funcionalidades dentro do conjunto de dados em si. Por exemplo, você pode usar WFS para encontrar todas as salas de reuniões de tamanho médio de uma determinada instalação e nível de piso.

### <a name="indoor-maps-module"></a>Módulo De Mapas Interiores

O [Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/) inclui o módulo Indoor Maps. Este módulo oferece funcionalidades estendidas à biblioteca de Controlo de *Mapas* Do Mapa do Mapa do Azure Maps. O módulo Indoor Maps torna mapas interiores criados em Criador. Integra widgets como *o apanhador*de pisos, que ajuda os utilizadores a visualizar os diferentes andares.

O módulo Indoor Maps permite-lhe criar aplicações web que integram dados de mapas interiores com outros [serviços do Azure Maps.](https://docs.microsoft.com/azure/azure-maps/) As configurações de aplicações mais comuns poderiam incluir a adição de conhecimento a mapas interiores de outros mapas, tais como estrada, imagens, clima e trânsito.

O módulo Indoor Maps também suporta o estilo dinâmico do mapa. Para um passo passo a passo sobre como implementar o estilo dinâmico de funcionalidade sinuoso numa aplicação, consulte [Como Utilizar o Módulo de Mapa Interior](how-to-use-indoor-module.md)

### <a name="azure-maps-integration"></a>Integração do Azure Maps

À medida que começa a desenvolver soluções para mapas interiores, pode descobrir formas de integrar as capacidades existentes do Azure Maps. Por exemplo, cenários de rastreio de ativos ou de segurança poderiam ser implementados utilizando a [API De Geofence Azure Maps](https://docs.microsoft.com/rest/api/maps/spatial/postgeofence) com mapas interiores do Criador. A API Geofence poderia ser utilizada para determinar, por exemplo, se um trabalhador entra ou deixa áreas interiores específicas. Para mais informações sobre como ligar o Azure Maps à telemetria IoT está disponível [aqui](tutorial-iot-hub-maps.md).

### <a name="data-maintenance"></a>Manutenção de Dados

 A Lista de Criadores de Mapas Azure, atualização e Eliminação a API permite-lhe listar, atualizar e eliminar os seus conjuntos de dados, azulejos e funcionalidades.

>[!NOTE]
>Sempre que analisar uma lista de itens e decidir eliminá-los, deve considerar o impacto dessa eliminação em todas as API ou aplicações dependentes. Por exemplo, se eliminar um azulejo que está atualmente a ser utilizado por uma aplicação através da [Render V2 - Get Map Tile API,](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview)apagar esse azulejo resultaria numa falha de aplicação para renderizar esse azulejo.

### <a name="example-updating-a-dataset"></a>Exemplo: Atualizar um conjunto de dados

O exemplo que se segue mostra como atualizar um conjunto de dados, criar um novo azulejo e apagar um azulejo antigo.

1. Siga os passos no [pacote Descarregamento de um desenho](#upload-a-drawing-package) e [converta uma](#convert-a-drawing-package) secção de pacote de desenho para carregar e converter o novo pacote de desenho.

2. Utilize o [Dataset Create API](https://docs.microsoft.com/rest/api/maps/dataset/createpreview) para anexar os dados convertidos ao conjunto de dados existente.

3. Utilize a [API Tileset Create](https://docs.microsoft.com/rest/api/maps/tileset/createpreview) para gerar um novo azulejo a partir do conjunto de dados atualizado. Guarde o novo tilesetId para o passo 4.

4. Atualize o identificador de azulejos na sua aplicação para permitir a visualização do conjunto de dados do campus atualizado. Se o azulejo velho já não estiver a ser utilizado, pode eliminá-lo.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Criar um mapa interior do Criador](tutorial-creator-indoor-maps.md)
