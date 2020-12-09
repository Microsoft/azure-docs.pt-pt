---
title: Começando com o Azure Maps Power BI visual Microsoft Azure Maps
description: Neste artigo, você vai aprender a usar o visual do Microsoft Azure Maps para Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a563a7776e84289e38743057778e8fe10fd17503
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904708"
---
# <a name="getting-started-with-the-azure-maps-power-bi-visual"></a>Começando com o Azure Maps Power BI visual

<Token>**APLICA-SE A:** ![ Marca verde de verificação. ](media/power-bi-visual/yes.png) Serviço de BI de energia para **_consumidores_* _ ![ Marca de verificação verde. ](media/power-bi-visual/yes.png) Serviço de Power BI para designers & desenvolvedores ![ Marca de verificação verde. ](media/power-bi-visual/yes.png) Power BI Desktop ![ X indicando não. ](media/power-bi-visual/no.png) Requer licença Pro ou Premium</Token>

Este artigo mostra como utilizar o visual do Microsoft Azure Maps para Power BI.

> [!NOTE]
> Este visual pode ser criado e visualizado tanto no Power BI Desktop como no serviço Power BI. Os passos e as ilustrações neste artigo referem-se ao Power BI Desktop.

O Azure Maps visual para Power BI fornece um rico conjunto de visualizações de dados para dados espaciais em cima de um mapa. Estima-se que mais de 80% dos dados empresariais tenham um contexto de localização. O visual Azure Maps pode ser usado para obter insights sobre como este contexto de localização se relaciona e influencia os seus dados de negócio.

![Power BI desktop com o Azure Maps visualizando dados de negócio](media/power-bi-visual/azure-maps-visual-hero.png)

## <a name="what-is-sent-to-azure"></a>O que é enviado para Azure?

O Azure Maps conecta-se ao serviço de nuvem alojado no Azure para recuperar dados de localização, como imagens de mapas e coordenadas que são usadas para criar a visualização do mapa. 

-   Os detalhes sobre a área em que o mapa está focado são enviados para Azure para recuperar imagens necessárias para tornar a tela do mapa (também conhecida como azulejos de mapa). 
-   Os dados nos baldes de Localização, Latitude e Longitude podem ser enviados para Azure para recuperar as coordenadas do mapa (um processo chamado geocoding). 
-   Os dados de telemetria podem ser recolhidos sobre a saúde do visual (ou seja, relatórios de acidentes), se a opção de telemetria no Power BI estiver ativada.

Além dos cenários acima descritos, nenhum outro dado sobreposto no mapa é enviado para os servidores do Azure Maps. Toda a renderização de dados ocorre localmente dentro do cliente.

Você, ou o seu administrador, poderá ter de atualizar a sua firewall para permitir o acesso à plataforma Azure Maps que utiliza o seguinte URL.

> `https://atlas.microsoft.com`

Para saber mais sobre privacidade e termos de utilização relacionados com o visual do Azure Maps consulte [a Microsoft Azure Legal Information](https://azure.microsoft.com/support/legal/).

## <a name="azure-maps-visual-preview-behavior-and-requirements"></a>Comportamento e requisitos visuais do Azure Maps (Pré-visualização)

Existem algumas considerações e requisitos para _ *Azure Maps** visual. :

-   O **visual Azure Maps** (Pré-visualização) deve ser ativado no Power BI Desktop. Para ativar **o visual do Azure Maps,** selecione **File** &gt; **opções de ficheiros e** &gt; **opções de** &gt; **visualização,** selecione a caixa de verificação **Azure Maps Visual.** Se o visual do Azure Maps não estiver disponível depois de o fazer, é provável que um interruptor de administração de inquilinos no Portal do Administrador precise de ser ativado.
-   O conjunto de dados deve ter campos que contenham **informações de latitude** e **longitude.** A geocoding dos campos de localização será adicionada numa futura atualização.
-   O controlo de lendas incorporado para Power BI não aparece atualmente nesta pré-visualização. Será adicionado numa futura atualização.

## <a name="use-the-azure-maps-visual-preview"></a>Utilize o visual Azure Maps (Pré-visualização)

Uma vez ativado o visual **Azure Maps,** selecione o ícone **Azure Maps** a partir do painel **visualizações.**

![Botão visual Azure Maps no painel visualizações](media/power-bi-visual/azure-maps-in-visualizations-pane.png)

Power BI cria uma tela de design visual Azure Maps vazia. Durante a pré-visualização, é apresentado um aviso adicional.

![Power BI desktop com o Azure Maps visual carregado no seu estado inicial](media/power-bi-visual/visual-initial-load.png)

Tome os seguintes passos para carregar o visual do Azure Maps:

1.  No painel **Fields,** arraste campos de dados que contenham latitude e longitude coordenam informações nos baldes **de Latitude** e/ou **Longitude.** Estes são os dados mínimos necessários para carregar o visual do Azure Maps.
    
    > [!div class="mx-imgBorder"]
    > ![Azure Maps pontos de exibição visual como bolhas no mapa após latitude e longitude campos fornecidos](media/power-bi-visual/bubble-layer.png)

2.  Para colorir os dados baseados na categorização, arraste um campo categórico para o balde **da Lenda** do painel **Fields.** Neste exemplo, estamos a usar a coluna **AdminDistrict** (também conhecida como estado ou província).  
    
    > [!div class="mx-imgBorder"]
    > ![Azure Maps pontos de exibição visual como bolhas coloridas no mapa após o campo da lenda fornecido](media/power-bi-visual/bubble-layer-with-legend-color.png)

    > [!NOTE]
    > O controlo de lendas incorporado para Power BI não aparece atualmente nesta pré-visualização. Será adicionado numa futura atualização.

3.  Para escalar os dados de forma relativamente, arraste uma medida para o balde **de tamanho** do painel **Fields.** Neste exemplo, estamos a usar a coluna **Sales.**  
    
    > [!div class="mx-imgBorder"]
    > ![Azure Maps pontos de exibição visual como bolhas coloridas e escaladas no mapa após o campo de tamanho fornecido.](media/power-bi-visual/bubble-layer-with-legend-color-and-size.png)

4.  Utilize as opções no painel **de formato** para personalizar a forma como os dados são renderizados. A imagem a seguir é o mesmo mapa acima, mas com as camadas de bolha preenchem a opção de transparência definida em 50% e a opção de contorno de alto contraste ativada.  
    
    > [!div class="mx-imgBorder"]
    > ![Azure Maps pontos de exibição visual como bolhas no mapa com um estilo personalizado](media/power-bi-visual/bubble-layer-styled.png)

## <a name="fields-pane-buckets"></a>Baldes de painel de campos

Os seguintes baldes de dados estão disponíveis no painel **Fields** do Azure Maps visual.

| Campo     | Descrição  |
|-----------|--------------|
| Latitude  | O campo utilizado para especificar o valor de latitude dos pontos de dados. Os valores de latitude devem estar entre -90 e 90 graus decimais em formato decimal.  |
| Longitude | O campo utilizado para especificar o valor de longitude dos pontos de dados. Os valores de longitude devem estar entre -180 e 180 graus decimais em formato decimal.  |
| Legenda    | O campo usado para categorizar os dados e atribuir uma cor única para pontos de dados em cada categoria. Quando este balde estiver cheio, aparecerá uma secção **de cores de dados** no painel **de formato** que permite ajustes nas cores. |
| Tamanho      | A medida utilizada para dimensionamento relativo de pontos de dados no mapa.   |
| Descrições  | Campos de dados adicionais que são apresentados em pontas de ferramentas quando as formas são pairadas. |

## <a name="map-settings"></a>Definições do mapa

A secção de **definições** do Mapa do painel de formato fornece opções para personalizar a forma como o mapa é apresentado e reage a atualizações.

| Definições             | Descrição  |
|---------------------|--------------|
| Zoom automático           | Automaticamente amplia o mapa para os dados carregados através do painel **Fields** do visual. À medida que os dados mudam, o mapa atualizará a sua posição em conformidade. Quando o deslizador está na posição **Off,** são apresentadas definições adicionais de visualização do mapa para a vista do mapa predefinido. |
| Embrulho mundial          | Permite ao utilizador fazer o mapa horizontalmente infinitamente. |
| Picker de estilo        | Adiciona um botão ao mapa que permite aos leitores do relatório alterar o estilo do mapa. |
| Controlos de navegação | Adiciona botões ao mapa como outro método para permitir que os leitores do relatório zoom, rotação e alteração do tom do mapa. Para mais informações, consulte este documento sobre [como navegar no mapa](map-accessibility.md#navigating-the-map) para obter detalhes sobre todas as diferentes formas de os utilizadores poderem navegar no mapa. |
| Estilo de mapa           | O estilo do mapa. Para mais informações, consulte este documento para obter mais informações sobre [os estilos de mapa suportados.](supported-map-styles.md) |

### <a name="map-view-settings"></a>Definições de visualização do mapa

Se o deslizador **de zoom Automático** estiver na posição **Off,** são apresentadas as seguintes definições e permitem ao utilizador especificar as informações de visualização do mapa predefinida.

| Definições          | Descrição   |
|------------------|---------------|
| Zoom             | O nível de zoom padrão do mapa. Pode ser um número entre 0 e 22. |
| Latitude central  | A latitude padrão no centro do mapa. |
| Longitude central | A longitude padrão no centro do mapa. |
| Rumo          | A orientação padrão do mapa em graus, onde 0 é norte, 90 é leste, 180 é sul, e 270 é oeste. Pode ser qualquer número entre 0 e 360. |
| Pitch            | A inclinação padrão do mapa em graus entre 0 e 60, onde 0 está olhando diretamente para o mapa. |

## <a name="considerations-and-limitations"></a>Considerações e Limitações

O visual Azure Maps está disponível nos seguintes serviços e aplicações:

| Serviço/Aplicação                              | Disponibilidade |
|------------------------------------------|--------------|
| Power BI Desktop                         | Sim          |
| Serviço Power BI (app.powerbi.com)       | Sim          |
| Aplicações móveis do Power BI             | Sim          |
| Publicar na Web do Power BI                  | Não           |
| Power BI Embedded                        | Não           |
| Incorporação do serviço Power BI (PowerBI.com) | Sim          |

O suporte para serviços/aplicações de Power BI adicionais será adicionado em futuras atualizações.

**Onde está disponível o Azure Maps?**

Neste momento, o Azure Maps está atualmente disponível em todos os países e regiões, com exceção dos seguintes:

- China
- Coreia do Sul

Para obter detalhes de cobertura para os diferentes serviços Azure Maps que alimentam este visual, consulte o documento [de informação de cobertura geográfica.](geographic-coverage.md)

**Quais os navegadores web suportados pelo visual Azure Maps?**

Consulte esta documentação para obter informações sobre [os navegadores suportados pela Azure Maps Web SDK.](supported-browsers.md)

**Quantos pontos de dados posso visualizar?**

Este visual suporta até 30.000 pontos de dados.

**Os endereços ou outras cordas de localização podem ser usados neste visual?**

A pré-visualização inicial deste visual apenas suporta valores de latitude e longitude em graus decimais. Uma futura atualização irá adicionar suporte para endereços e outras cordas de localização.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o visual Azure Maps Power BI:

> [!div class="nextstepaction"]
> [Compreender camadas no Azure Maps Power BI visual](power-bi-visual-understanding-layers.md)

> [!div class="nextstepaction"]
> [Gerencie o visual Azure Maps dentro da sua organização](power-bi-visual-manage-access.md)

Personalize o visual:

> [!div class="nextstepaction"]
> [Sugestões e truques para formatação de cor no Power BI](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Personalizar os títulos, fundos e legendas das visualizações](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)