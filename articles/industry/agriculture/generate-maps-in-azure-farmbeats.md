---
title: Gerar mapas
description: Este artigo descreve como gerar mapas em Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: d0176fd337abd37f97a6dbe5cf7c68ccf94114d0
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173869"
---
# <a name="generate-maps"></a>Gerar mapas

Utilizando o Azure FarmBeats, pode gerar os seguintes mapas utilizando imagens de satélite e entradas de dados de sensores. Os mapas ajudam-no a ver a localização geográfica da sua quinta e a identificar a localização adequada para os seus dispositivos.

  - **Mapa de colocação de sensores**: Fornece recomendações sobre quantos sensores usar e onde colocá-los numa fazenda.
  - **Mapa dos índices de satélite**: Mostra o índice de vegetação e o índice de água para uma exploração.
  - **Mapa de calor da humidade do solo**: Mostra a distribuição da humidade do solo através da fusão de dados de satélite e de sensores.

## <a name="sensor-placement-map"></a>Mapa de colocação de sensores

Um mapa de colocação de sensores FarmBeats ajuda-o na colocação de sensores de humidade do solo. A saída do mapa consiste numa lista de coordenadas para a implantação de sensores. As entradas destes sensores são usadas juntamente com imagens de satélite para gerar o mapa de calor da humidade do solo.

Este mapa é derivado segmentando a capota como visto ao longo de várias datas ao longo do ano. Até o solo e os edifícios são parte do dossel. Pode remover sensores que não são necessários no local. Este mapa é para orientação, e você pode alterar a posição e os números ligeiramente com base no seu conhecimento personalizado. Adicionar sensores não vai regredir os resultados do mapa de calor da humidade do solo, mas há a possibilidade de deterioração da precisão do mapa de calor se o número do sensor for reduzido.

## <a name="before-you-begin"></a>Antes de começar

Encontre os seguintes pré-requisitos antes de tentar gerar um mapa de colocação de sensores:

- O tamanho da fazenda deve ser de mais de um hectare.
- O número de cenas sentinelas sem nuvens deve ser superior a seis para o intervalo de datas selecionado.
- Pelo menos seis cenas de Sentinela sem nuvens devem ter um Índice normalizado de vegetação de diferença (NDVI) superior ou igual a 0,3.

    > [!NOTE]
    > O Sentinel permite apenas duas linhas simultâneas por utilizador. Como resultado, os empregos ficam em fila e podem demorar mais tempo a ser concluídos.

### <a name="dependencies-on-sentinel"></a>Dependências de Sentinela

As seguintes dependências dizem respeito ao Sentinel:

- Dependemos do desempenho do Sentinel para descarregar imagens de satélite. Verifique o estado de desempenho do Sentinel e [as atividades](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)de manutenção.
- O Sentinel permite apenas dois [fios de transferências simultâneos](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) por utilizador.
- A geração de mapas de precisão é afetada pela [cobertura do Sentinel e pela frequência de revisitação.]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)

## <a name="create-a-sensor-placement-map"></a>Criar um mapa de colocação de sensores
Esta secção detalha os procedimentos para a criação de mapas de colocação de sensores.

> [!NOTE]
> Pode iniciar um mapa de colocação de sensores a partir da página **Do Mapa** ou do menu de drop-down Do Generate **Precision Maps** na página Detalhes da **Fazenda.**

Siga estes passos.

1. Na página inicial, vá ao **Maps** a partir do menu de navegação à esquerda.
2. Selecione **Criar Mapas** e selecione **Sensor Placement** a partir do menu suspenso.

    ![Selecione colocação de sensores](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Depois de selecionar **a colocação do sensor,** aparece a janela **de colocação** do sensor.

    ![Janela de colocação de sensores](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Selecione uma fazenda no menu de entrega da **Fazenda.**
   Para pesquisar e selecionar a sua quinta, pode deslocar-se na lista de drop-down ou introduzir o nome da quinta na caixa de texto.
5. Para gerar um mapa para o ano passado, selecione **Recomendado**.
6. Para gerar um mapa para um intervalo de datas personalizado, selecione a opção **Selecione Date Range**. Introduza a data de início e de fim para a qual pretende gerar o mapa de Colocação do Sensor.
7. Selecione **'Gerar Mapas'.**
 Aparece uma mensagem de confirmação com detalhes do trabalho.

  Para obter informações sobre o estado do trabalho, consulte a secção **Ver Empregos**. Se o estado do trabalho mostrar *Falha,* aparece uma mensagem de erro detalhada na ponta da ferramenta do estado *falhado.* Neste caso, repita os passos anteriores e tente novamente.

  Se o problema persistir, consulte a secção [Troubleshoot](troubleshoot-azure-farmbeats.md) ou contacte o [fórum Azure FarmBeats para obter apoio](https://aka.ms/FarmBeatsMSDN) com registos relevantes.

### <a name="view-and-download-a-sensor-placement-map"></a>Ver e descarregar um mapa de colocação de sensores

Siga estes passos.

1. Na página inicial, vá ao **Maps** a partir do menu de navegação à esquerda.

    ![Selecione Mapas do menu de navegação à esquerda](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecione **Filtro** a partir da navegação esquerda da janela.
  A janela **Filter** apresenta critérios de pesquisa.

    ![Janela do filtro](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Selecione **Valores de Tipo,** **Data** e **Nome** nos menus suspensos. Em seguida, **selecione Aplicar** para procurar o mapa que deseja visualizar.
  A data em que o trabalho foi criado é mostrada no formato type_farmname_YYYY-MM-DD.
4. Percorra a lista de mapas disponíveis utilizando as barras de navegação no final da página.
5. Selecione o mapa que pretende visualizar. Uma janela pop-up apresenta a pré-visualização do mapa selecionado.
6. Selecione **Download** e descarregue o ficheiro GeoJSON das coordenadas do sensor.

    ![Pré-visualização do mapa de colocação de sensores](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Mapa dos índices de satélite

As secções seguintes explicam os procedimentos envolvidos na criação e visualização de um mapa de índices de satélite.

> [!NOTE]
> Pode iniciar um mapa de índices de satélite a partir da página **Maps** ou a partir do menu de drop-down **Do Generate Precision Maps** na página Detalhes da **Fazenda.**

O FarmBeats fornece-lhe a capacidade de gerar mapas NDVI, Enhanced Vegetation Index (EVI) e mapas do Índice de Água de Diferença Normalizada (NDWI) para explorações agrícolas. Estes índices ajudam a determinar como a cultura está atualmente a crescer, ou cresceu no passado, e os níveis representativos da água no solo.


> [!NOTE]
> É necessária uma imagem Sentinela para os dias para os quais o mapa é gerado.


## <a name="create-a-satellite-indices-map"></a>Criar um mapa de índices de satélite

Siga estes passos.

1. Na página inicial, vá ao **Maps** a partir do menu de navegação à esquerda.
2. Selecione **Create Maps** e selecione **Índices de Satélite** do menu suspenso.

    ![Selecione Índices de Satélite do menu suspenso](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. Depois de selecionar **índices de satélite,** aparece a janela **dos índices de** satélite.

    ![Janela de índices de satélite](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Selecione uma quinta a partir do menu suspenso.
   Para pesquisar e selecionar a sua quinta, pode deslocar-se na lista de drop-down ou introduzir o nome da quinta.   
5. Para gerar um mapa para a última semana, selecione **This Week**.
6. Para gerar um mapa para um intervalo de datas personalizado, selecione a opção **Selecione Date Range**. Insira a data de início e de fim para a qual pretende gerar o mapa de Índices de Satélite.
7. Selecione **'Gerar Mapas'.**
    Aparece uma mensagem de confirmação com detalhes do trabalho.

    ![Mensagem de confirmação do mapa de índices de satélite](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Para obter informações sobre o estado do trabalho, consulte a secção **Ver Empregos**. Se o estado do trabalho mostrar *Falha,* aparece uma mensagem de erro detalhada na ponta da ferramenta do estado *falhado.* Neste caso, repita os passos anteriores e tente novamente.

    Se o problema persistir, consulte a secção [Troubleshoot](troubleshoot-azure-farmbeats.md) ou contacte o [fórum Azure FarmBeats para obter apoio](https://aka.ms/FarmBeatsMSDN) com registos relevantes.

### <a name="view-and-download-a-map"></a>Ver e baixar um mapa

Siga estes passos.

1. Na página inicial, vá ao **Maps** a partir do menu de navegação à esquerda.

    ![Selecione Mapas](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecione **Filtro** a partir da navegação esquerda da janela. A janela **Filter** apresenta critérios de pesquisa.

    ![A janela do filtro apresenta critérios de pesquisa](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Selecione **Valores de Tipo,** **Data** e **Nome** nos menus suspensos. Em seguida, **selecione Aplicar** para procurar o mapa que deseja visualizar.
  A data em que o trabalho foi criado é mostrada no formato type_farmname_YYYY-MM-DD.

4. Percorra a lista de mapas disponíveis utilizando as barras de navegação no final da página.
5. Para cada combinação de Nome e **Data** da **Fazenda,** estão disponíveis os seguintes três mapas:
    - NDVI
    - EVI
    - NDWI
6. Selecione o mapa que pretende visualizar. Uma janela pop-up apresenta a pré-visualização do mapa selecionado.
7. Selecione **Descarregue** no menu drop-down para selecionar o formato de descarregamento. O mapa é descarregado e armazenado na sua pasta local no seu computador.

    ![Visualização do mapa de índices de satélite selecionados](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>Mapa de calor da humidade do solo

A humidade do solo é a água que está guardada nos espaços entre partículas do solo.O mapa de calor da humidade do solo ajuda-o a compreender os dados de humidade do solo a qualquer profundidade, em alta resolução dentro da sua quinta. Para gerar um mapa de calor preciso e utilizável da humidade do solo, é necessária uma colocação uniforme de sensores. Todos os sensores devem ser do mesmo fornecedor. Diferentes fornecedores têm diferenças na forma como a humidade do solo é medida juntamente com as diferenças de calibração. O mapa de calor é gerado para uma determinada profundidade utilizando os sensores implantados a essa profundidade.

### <a name="before-you-begin"></a>Antes de começar

Conheça os seguintes pré-requisitos antes de tentar gerar um mapa de calor da humidade do solo:

- Devem ser implantados pelo menos três sensores de humidade do solo. Não tente criar um mapa de calor da humidade do solo antes que os sensores sejam implantados e associados à quinta.
- Gerar um mapa de calor da humidade do solo é influenciado pela cobertura do caminho do Sentinel, cobertura de nuvens e sombra de nuvem. Pelo menos uma Cena Sentinela sem nuvens deve estar disponível nos últimos 120 dias, a partir do dia em que foi solicitado o mapa de calor da humidade do solo.
- Pelo menos metade dos sensores implantados na quinta devem estar online e ter dados a transmitir para o datahub.
- O mapa térmico deve ser gerado utilizando medidas de sensor do mesmo fornecedor.


## <a name="create-a-soil-moisture-heatmap"></a>Criar um mapa de calor da humidade do solo

Siga estes passos.

1. Na página inicial, vá ao **Maps** a partir do menu de navegação à esquerda para ver a página do **Maps.**
2. Selecione **Create Maps** e selecione **a Humidade** do Solo no menu suspenso.

    ![Selecione a humidade do solo do menu suspenso](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Depois de selecionar **a Humidade do Solo,** aparece a janela de humidade do **solo.**

    ![Janela de humidade do solo](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Selecione uma fazenda no menu de entrega da **Fazenda.**
   Para pesquisar e selecionar a sua quinta, pode deslocar-se a partir da lista de drop-down ou introduzir o nome da quinta no menu **Select farm** drop-down.
5. No menu **Select Soil Moisture Sensor Measure** drop-down, selecione a medida do sensor de humidade do solo (profundidade) para a qual pretende gerar o mapa.
Para encontrar a medida do sensor, vá aos **Sensores** e selecione qualquer sensor de humidade do solo. Em seguida, na secção **Propriedades sensoriais,** utilize o valor em **Nome da Medida**.
6. Para gerar um mapa para **Hoje** ou **Esta Semana,** selecione uma das opções.
7. Para gerar um mapa para um intervalo de datas personalizado, selecione a opção **Selecione Date Range**. Introduza a data de início e de fim para a qual pretende gerar o mapa de calor da humidade do solo.
8. Selecione **'Gerar Mapas'.**
 Aparece uma mensagem de confirmação com detalhes do trabalho.

   ![Mensagem de confirmação do mapa de humidade do solo](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Para obter informações sobre o estado do trabalho, consulte a secção **Ver Empregos**. Se o estado do trabalho mostrar *Falha,* aparece uma mensagem de erro detalhada na ponta da ferramenta do estado *falhado.* Neste caso, repita os passos anteriores e tente novamente.

    Se o problema persistir, consulte a secção [Troubleshoot](troubleshoot-azure-farmbeats.md) ou contacte o [fórum Azure FarmBeats para obter apoio](https://aka.ms/FarmBeatsMSDN) com registos relevantes.

### <a name="view-and-download-a-map"></a>Ver e baixar um mapa

Siga estes passos.

1. Na página inicial, vá ao **Maps** a partir do menu de navegação à esquerda.

    ![Ir ao Maps](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecione **Filtro** a partir da navegação esquerda da janela. A janela **filter** é exibida de onde pode procurar mapas.

    ![Selecione Filtro da navegação à esquerda](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Selecione **Valores de Tipo,** **Data** e **Nome** nos menus suspensos. Em seguida, **selecione Aplicar** para procurar o mapa que deseja visualizar. A data em que o trabalho foi criado é mostrada no formato type_farmname_YYYY-MM-DD.
4. Selecione o ícone **'Ordenar'** ao lado dos cabeçalhos de mesa para classificar de acordo com **a Quinta**, **Data**, **Criado,** **ID de Trabalho** e Tipo de **Trabalho**.
5. Percorra a lista de mapas disponíveis utilizando os botões de navegação no final da página.
6. Selecione o mapa que pretende visualizar. Uma janela pop-up apresenta a pré-visualização do mapa selecionado.
7. Selecione **Descarregue** no menu drop-down para selecionar o formato de descarregamento. O mapa é descarregado e armazenado na pasta especificada.

    ![Pré-visualização do mapa de calor da humidade do solo](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
