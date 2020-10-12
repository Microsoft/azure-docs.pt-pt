---
title: Trabalhe com mapas interiores no Azure Maps Creator
description: Este artigo introduz conceitos que se aplicam aos serviços do Criador Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: ad1b7ae08e74f455190c44a813dde44b0b683014
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91311364"
---
# <a name="creator-for-indoor-maps"></a>Criador para mapas interiores

Este artigo introduz conceitos e ferramentas que se aplicam ao Criador de Mapas Azure. Recomendamos que leia este artigo antes de começar a usar a API e a SDK do Criador de Mapas Azure.

Pode utilizar o Criador para desenvolver aplicações com funcionalidades de mapas baseadas em dados do mapa interior. Este artigo descreve o processo de upload, conversão, criação e utilização dos dados do seu mapa. Todo o fluxo de trabalho é ilustrado no diagrama abaixo.

![Fluxo de trabalho de dados do mapa do criador](./media/creator-indoor-maps/workflow.png)

## <a name="create-azure-maps-creator"></a>Criar Criador de Mapas Azure

Para utilizar os serviços do Criador, o Azure Maps Creator tem de ser criado numa conta Azure Maps. Para obter informações sobre como criar o Criador de Mapas Azure em Azure Maps, consulte [Manage Azure Maps Creator](how-to-manage-creator.md).

## <a name="upload-a-drawing-package"></a>Faça upload de um pacote de desenho

O Criador recolhe dados de mapas interiores convertendo um pacote de desenho carregado. O pacote de desenho representa uma instalação construída ou remodelada. Para obter informações sobre os requisitos do pacote de desenho, consulte [os requisitos do pacote de desenho](drawing-requirements.md).

Utilize a [Azure Maps Data Upload API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) para carregar um pacote de desenho.  Após um upload bem sucedido, a API de upload de dados devolverá um identificador de dados do utilizador ( `udid` ). O `udid` será usado no próximo passo para converter o pacote carregado em dados de mapas internos.

## <a name="convert-a-drawing-package"></a>Converter um pacote de desenho

O [serviço de conversão Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion) converte um pacote de desenho carregado em dados de mapas interiores. O serviço de Conversão também valida o pacote. As questões de validação são classificadas em dois tipos: erros e avisos. Se forem detetados erros, o processo de conversão falha. Se forem detetados avisos, a conversão terá sucesso. No entanto, recomenda-se que reveja e resolva todos os avisos. Um aviso significa que parte da conversão foi ignorada ou fixada automaticamente. Não resolver os avisos pode resultar em erros nestes últimos processos. Para obter mais informações, consulte [avisos e erros do pacote de desenho](drawing-conversion-error-codes.md).

Quando ocorre um erro, o serviço De Conversão fornece um link para a aplicação web autónoma do Visualizador de Erros de [Desenho Azure Maps.](drawing-error-visualizer.md) Pode utilizar o Visualizador de Erro de Desenho para inspecionar [os avisos e erros](drawing-conversion-error-codes.md) do pacote de desenho que ocorreram durante o processo de conversão. Uma vez corrigidos os erros, pode então tentar carregar e converter a embalagem.

## <a name="create-indoor-map-data"></a>Criar dados de mapas interiores

A Azure Maps Creator fornece três serviços:

* [Serviço dataset](https://docs.microsoft.com/rest/api/maps/dataset/createpreview).
Utilize o serviço Dataset para criar um conjunto de dados a partir de dados de pacotes de desenho convertidos.
* [Serviço de azulejos.](https://docs.microsoft.com/rest/api/maps/tileset/createpreview)
Utilize o serviço Tileset para criar uma representação baseada em vetores de um conjunto de dados. As aplicações podem usar um teesto para apresentar uma visão baseada em azulejos visuais do conjunto de dados.
* [Serviço estado de recurso](https://docs.microsoft.com/rest/api/maps/featurestate). Utilize o serviço Estado recurso para suportar o estilo dinâmico do mapa. O estilo dinâmico do mapa permite que as aplicações reflitam eventos em tempo real em espaços fornecidos pelo sistema IoT.

### <a name="datasets"></a>Conjuntos de dados

Um conjunto de dados é uma coleção de recursos do mapa interior. As características do mapa interior representam instalações definidas num pacote de desenho convertido. Depois de criar um conjunto de dados com o [serviço Dataset,](https://docs.microsoft.com/rest/api/maps/dataset/createpreview)pode criar qualquer número de [azulejos](#tilesets) ou [estados de recurso](#feature-statesets).

O [serviço Dataset](https://docs.microsoft.com/rest/api/maps/dataset/createpreview) permite que os desenvolvedores, a qualquer momento, adicionem ou removam as instalações a um conjunto de dados existente. Para obter mais informações sobre como atualizar um conjunto de dados existente utilizando a API, consulte as opções do apêndice no [serviço Dataset](https://docs.microsoft.com/rest/api/maps/dataset/createpreview). Para obter um exemplo de como atualizar um conjunto de dados, consulte [a Manutenção de Dados](#data-maintenance).

### <a name="tilesets"></a>Azulejos

Um azulejo é uma coleção de dados vetoriais que representa um conjunto de azulejos uniformes. Os desenvolvedores podem usar o [serviço Tileset](https://docs.microsoft.com/rest/api/maps/tileset/createpreview) para criar azulejos a partir de um conjunto de dados.

Para refletir diferentes fases de conteúdo, pode criar vários azulejos a partir do mesmo conjunto de dados. Por exemplo, você poderia fazer um azulejo com móveis e equipamentos, e outro azulejo sem móveis e equipamentos.  Pode optar por gerar um azulejo com as mais recentes atualizações de dados, e uma sem as mais recentes atualizações de dados.

Além dos dados vetoriais, o telha fornece metadados para otimização de renderização de mapas. Por exemplo, os metadados de telha contém um nível de zoom min e max para o teesto. Os metadados também fornecem uma caixa de limites que define a extensão geográfica do teseta. A caixa de delimitação permite que uma aplicação desaponte programáticamente o ponto central correto. Para obter mais informações sobre metadados de azulejos, consulte [a Tileset List API](https://docs.microsoft.com/rest/api/maps/tileset/listpreview).

Uma vez criado um teesto, pode ser recuperado pelo [serviço Render V2](#render-v2-service).

Se um teesto ficar desatualizado e deixar de ser útil, pode apagar o teesto. Para obter mais informações sobre como eliminar os azulejos, consulte [a Manutenção de Dados.](#data-maintenance)

>[!NOTE]
>Um teesto é independente do conjunto de dados a partir do qual foi criado. Se criar teestos a partir de um conjunto de dados e, posteriormente, atualizar esse conjunto de dados, os azulejos não serão atualizados. Para refletir as alterações num conjunto de dados, é necessário criar novos azulejos. Da mesma forma, se eliminar um teesto, o conjunto de dados não será afetado.

### <a name="feature-statesets"></a>Estados-membros de recurso

Os estados de recurso são coleções de propriedades dinâmicas *(estados)* atribuídas a funcionalidades de conjunto de dados, tais como salas ou equipamentos. Um exemplo de um *estado* pode ser temperatura ou ocupação. Cada *estado* é um par chave/valor contendo o nome da propriedade, o valor e o tempo da última atualização.

O [serviço Estado de Recurso](https://docs.microsoft.com/rest/api/maps/featurestate/createstatesetpreview) permite criar e gerir um conjunto de recursos para um conjunto de dados. O estado é definido por um ou mais *estados.* Cada característica, como um quarto, pode ter um *estado* ligado a ele.

O valor de cada *estado* num stateet pode ser atualizado ou recuperado por dispositivos IoT ou outras aplicações.  Por exemplo, utilizando a [API de Atualização do Estado de Recurso,](https://docs.microsoft.com/rest/api/maps/featurestate/updatestatespreview)os dispositivos que medem a ocupação do espaço podem registar sistematicamente a mudança de estado de uma sala.

Uma aplicação pode usar um stateet de recurso para renderizar dinamicamente as funcionalidades numa instalação de acordo com o seu estado atual e o seu estilo de mapa. Para obter mais informações sobre a utilização de recursos para funcionalidades de estilo num mapa de renderização, consulte [o Módulo SDK da Web Interior](#indoor-maps-module).

>[!NOTE]
>Tal como os azulejos, a alteração de um conjunto de dados não afeta o estado de característica existente e a eliminação de um estadoet de recurso não terá qualquer efeito no conjunto de dados a que está anexado.

## <a name="using-indoor-maps"></a>Usando mapas interiores

### <a name="render-v2-service"></a>Serviço renderizador V2

O Azure Maps [Render V2 service-Get Map Tile API](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) foi estendido para suportar azulejos criadores.

[Render V2 service-Get Map State Tile API](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) permite que as aplicações solicitem azulejos. Os azulejos podem então ser integrados num controlo de mapas ou SDK. Para um exemplo de um controlo de mapas que utiliza o serviço Render V2, consulte [o Módulo mapa interior](#indoor-maps-module).

### <a name="web-feature-service-api"></a>Serviço de Recursos Web API

Os conjuntos de dados podem ser consultados através da API do [Serviço de Recursos Web (WFS).](https://docs.microsoft.com/rest/api/maps/wfs) O WFS segue as [funcionalidades API do Consórcio Geoespacial Aberto.](http://docs.opengeospatial.org/DRAFTS/17-069r1.html) A API do WFS permite-lhe consultar funcionalidades dentro do próprio conjunto de dados. Por exemplo, você pode usar WFS para encontrar todas as salas de reuniões de tamanho médio de uma determinada instalação e nível de piso.

### <a name="indoor-maps-module"></a>Módulo de Mapas do interior

O [Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/) inclui o módulo De Mapas Interiores. Este módulo oferece funcionalidades alargadas à biblioteca Azure Maps *Map Control.* O módulo Indoor Maps torna os mapas interiores criados no Criador. Integra widgets como *o apanhador de pisos,* que ajuda os utilizadores a visualizar os diferentes pisos.

O módulo Mapas Interiores permite criar aplicações web que integram dados de mapas interiores com outros [serviços Azure Maps.](https://docs.microsoft.com/azure/azure-maps/) As configurações de aplicações mais comuns podem incluir a adição de conhecimento a mapas interiores de outros mapas, tais como estradas, imagens, clima e trânsito.

O módulo Indoor Maps também suporta o estilo dinâmico do mapa. Para um walk-through passo-a-passo sobre como implementar o estilo dinâmico de estados de recurso numa aplicação, consulte [Como Usar o Módulo de Mapa Interior](how-to-use-indoor-module.md)

### <a name="azure-maps-integration"></a>Integração do Azure Maps

À medida que começa a desenvolver soluções para mapas interiores, pode descobrir formas de integrar as capacidades existentes do Azure Maps. Por exemplo, o rastreio de ativos ou cenários de segurança poderiam ser implementados utilizando a [API de Geofence Azure Maps](https://docs.microsoft.com/rest/api/maps/spatial/postgeofence) com mapas interiores do Criador. A API de Geofence poderia ser utilizada para determinar, por exemplo, se um trabalhador entra ou sai de áreas interiores específicas. Para mais informações sobre como ligar o Azure Maps com a telemetria IoT está disponível [aqui.](tutorial-iot-hub-maps.md)

### <a name="data-maintenance"></a>Manutenção de Dados

 A Azure Maps Creator List, Update e Delete API permite-lhe listar, atualizar e eliminar os seus conjuntos de dados, tesets e estados de funcionalidade.

>[!NOTE]
>Sempre que analisar uma lista de itens e decidir eliminá-los, deve considerar o impacto dessa eliminação em todas as API ou aplicações dependentes. Por exemplo, se eliminar um teesto que está atualmente a ser utilizado por uma aplicação através do [Render V2 - Get Map Tile API](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview), eliminando esse azulejo resultaria numa falha de aplicação para tornar esse teesto.

### <a name="example-updating-a-dataset"></a>Exemplo: Atualizar um conjunto de dados

O exemplo a seguir mostra como atualizar um conjunto de dados, criar um novo teesto e apagar um teesto antigo.

1. Siga os passos no [pacote De desenho](#upload-a-drawing-package) e [converta uma](#convert-a-drawing-package) embalagem de desenho para carregar e converter o novo pacote de desenho.

2. Utilize o [Conjunto de Dados Criar API](https://docs.microsoft.com/rest/api/maps/dataset/createpreview) para anexar os dados convertidos ao conjunto de dados existente.

3. Utilize o [Tileset Create API](https://docs.microsoft.com/rest/api/maps/tileset/createpreview) para gerar um novo teesto a partir do conjunto de dados atualizado. Guarde o novo azulejoid para o passo 4.

4. Atualize o identificador de azulejos na sua aplicação para permitir a visualização do conjunto de dados do campus atualizado. Se o teesto antigo já não estiver a ser utilizado, pode eliminá-lo.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Criar um mapa interior do Criador](tutorial-creator-indoor-maps.md)
