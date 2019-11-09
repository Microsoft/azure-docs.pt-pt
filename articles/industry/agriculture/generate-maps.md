---
title: Gerar mapas
description: Descreve como gerar mapas no FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 635431fb87e5f164f92ab4b7a1027ee96e9d801a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891015"
---
# <a name="generate-maps"></a>Gerar mapas

Usando o Azure FarmBeats, você pode gerar os seguintes mapas usando imagens de satélite e entradas de dados de sensor. O Maps ajuda você a exibir a localização geográfica do seu farm e identificar o local apropriado para seus dispositivos.

  -  **Mapa de posicionamento de sensor** – fornece recomendações sobre quantos sensores usar e onde colocá-los em um farm.
  - **Mapa de índices satélite** – mostra o índice de vegetação e o índice de água para um farm.
  - **Mapa de umidade de solo** – mostra a distribuição de umidade de solo, combinando dados de satélite e dados de sensor.

## <a name="sensor-placement-maps"></a>Mapas de posicionamento do sensor

O mapa de posicionamento do sensor FarmBeats ajuda você com o posicionamento dos sensores de umidade de solo. A saída do mapa consiste em uma lista de coordenadas para a implantação do sensor. As entradas desses sensores são usadas juntamente com imagens satélite para gerar o calor de umidade de solo.  

Esse mapa é derivado ao segmentar o canopy como visto em várias datas ao longo do ano, até mesmo Bare-solo e edifícios fazem parte do Canopy. Você pode remover sensores que não são necessários no local. Este mapa é para orientação e você pode alterar a posição e os números ligeiramente com base no seu conhecimento personalizado (adicionar sensores não retornará os resultados do mapa de calor de umidade de solo, mas há uma possibilidade de deterioração na precisão do mapa de calor se o número do sensor for reduzido).  

## <a name="before-you-begin"></a>Antes de começar  

Antes de tentar gerar um mapa de posicionamento de sensor, verifique o seguinte:

- O tamanho do farm deve ser maior que um acre.
- O número de cenas de sentinela sem nuvem deve ser maior que seis para o intervalo de datas selecionado.  
- Pelo menos seis cenas de sentinela sem nuvem devem ter NDVI > = 0,3.

    > [!NOTE]
    > O Sentinel permite apenas dois threads simultâneos por usuário. Como resultado, os trabalhos serão colocados na fila e poderão levar mais tempo para serem concluídos.

### <a name="dependencies-on-sentinel"></a>Dependências no sentinela  

Veja a seguir as dependências do Sentinel:

- Dependemos do desempenho de sentinela para baixar imagens satélite. Verifique o status de desempenho do Sentinela e [as atividades](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)de manutenção.
- O Sentinel permite apenas dois [threads de downloads](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) simultâneos por usuário.
- A geração de mapa de precisão será afetada pela [cobertura do Sentinel e pela frequência de revisitação]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage).

## <a name="create-sensor-placement-map"></a>Criar mapa de posicionamento do sensor
A seção detalha os procedimentos para criar mapas de posicionamento de sensor.

> [!NOTE]
> Você pode iniciar o posicionamento do sensor na página **mapas** ou no menu suspenso **gerar mapas de precisão** na página **detalhes do farm** .

Utilize os passos seguintes:

1. Na home page, vá para **mapas** no menu de navegação à esquerda.
2. Selecione **criar mapas** e selecione o **posicionamento do sensor** no menu suspenso.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Selecione o **posicionamento do sensor**.
  A janela de posicionamento do sensor é exibida.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Selecione um farm no menu suspenso **farm** .  
   Para pesquisar e selecionar seu farm, você pode rolar na lista suspensa ou digitar o nome do farm na caixa de texto.
5. Se você quiser gerar um mapa para o último ano, selecione a opção **recomendado** .
6. Se você quiser gerar um mapa para um intervalo de datas personalizado, selecione **selecionar intervalo de datas**e forneça a data de início e de término para a qual você deseja gerar o mapa de posicionamento do sensor.
7. Selecione **gerar mapas**.
 Uma mensagem de confirmação com detalhes do trabalho é exibida.

  Para obter informações sobre o status do trabalho, consulte a seção **exibir trabalhos**. Se o status do trabalho mostrar *falha*, uma mensagem de erro detalhada será exibida na dica de ferramenta do status de *falha* .. Nesse caso, repita as etapas listadas acima e tente novamente.

  Se o problema persistir, consulte a seção [solucionar problemas](troubleshoot-project-farmbeats.md) ou contate o [Fórum do Azure FarmBeats para obter suporte](https://aka.ms/FarmBeatsMSDN) com os logs relevantes.

### <a name="view-and-download-sensor-placement-map"></a>Exibir e baixar o mapa de posicionamento do sensor

Utilize os passos seguintes:

1. Na home page, vá para **mapas** no menu de navegação à esquerda.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecione **Filtrar** na navegação à esquerda da janela.
  A janela **filtro** exibe os critérios de pesquisa.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Selecione **tipo**, **Data** e **nome** no menu suspenso e, em seguida, selecione **aplicar** para pesquisar o mapa que você deseja exibir.
  A data em que o trabalho foi criado é mostrada no formato type_farmname_YYYY-MM-DD.
4. Percorra a lista de mapas disponíveis usando as barras de navegação no final da página.
5. Selecione o mapa que você deseja exibir. Uma janela pop-up exibe a visualização do mapa selecionado.
6. Selecione **baixar**e baixe o arquivo geojson de coordenadas do sensor.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Mapa de índices satélite

As seções a seguir explicam os procedimentos envolvidos na criação e exibição do mapa de índices satélites.

> [!NOTE]
> Você pode iniciar o mapa de índices satélite na página **mapas** ou no menu suspenso **gerar mapas de precisão** na página de **detalhes do farm** .

O FarmBeats fornece a capacidade de gerar índice de vegetação de diferença normalizada (NDVI), índice vegetação aprimorado (EVI) e mapas de índice de água de diferença normalizada (NDWI) para farms. Esses índices ajudam a determinar como o corte está crescendo no momento ou cresceu no passado e os níveis de água representativos no solo.


> [!NOTE]
> Uma imagem de Sentinela é necessária para os dias para os quais o mapa é gerado.


## <a name="create-satellite-indices-map"></a>Criar mapa de índices satélite

Utilize os passos seguintes:

1. Na home page, vá para **mapas** no menu de navegação à esquerda.
2. Selecione **criar mapas** e selecione **índices satélites** no menu suspenso.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. Selecione **índices satélite**.
  A janela de índices satélite é exibida.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Selecione um farm no menu suspenso.  
   Para pesquisar e selecionar seu farm, você pode rolar na lista suspensa ou digitar o nome do farm.   
5. Se você quiser gerar um mapa para a última semana, selecione **a opção esta semana** .
6. Se você quiser gerar um mapa para o intervalo de datas personalizado, selecione **selecionar intervalo de datas**e forneça a data de início e de término para a qual você deseja gerar o mapa de índices satélite.
7. Selecione **gerar mapas**.
    Uma mensagem de confirmação com detalhes do trabalho é exibida.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Para obter informações sobre o status do trabalho, consulte a seção **exibir trabalhos**. Se o status do trabalho mostrar *falha*, uma mensagem de erro detalhada será exibida na dica de ferramenta do status de *falha* . Nesse caso, repita as etapas listadas acima e tente novamente.

    Se o problema persistir, consulte a seção [solucionar problemas](troubleshoot-project-farmbeats.md) ou contate o [Fórum do Azure FarmBeats para obter suporte](https://aka.ms/FarmBeatsMSDN) com os logs relevantes.

### <a name="view-and-download-map"></a>Exibir e baixar o mapa

Utilize os passos seguintes:

1. Na home page, vá para **mapas** no menu de navegação à esquerda.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecione **Filtrar** na navegação à esquerda da janela, a janela **filtro** exibe os critérios de pesquisa.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Selecione **tipo**, **Data** e **nome** no menu suspenso e, em seguida, selecione **aplicar** para pesquisar o mapa que você deseja exibir.
  A data em que o trabalho foi criado é mostrada no formato type_farmname_YYYY-MM-DD.

4. Percorra a lista de mapas disponíveis usando as barras de navegação no final da página.
5. Para cada combinação de **nome** e **Data**do farm, os três mapas a seguir estão disponíveis:
    - NDVI
    - EVI
    - NDWI
6. Selecione o mapa que você deseja exibir. Uma janela pop-up exibe a visualização do mapa selecionado.
7. Selecione **baixar** menu suspenso para selecionar o formato de download e o mapa será baixado e armazenado em sua pasta local no computador.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="get-soil-moisture-heatmap"></a>Obter calor de umidade de solo

A umidade de solo é a água que é mantida nos espaços entre as partículas de solo. A calor de umidade de solo ajuda você a entender os dados de umidade de solo em qualquer profundidade, em alta resolução em seus farms. Para gerar um calor de umidade de solo preciso e utilizável, uma implantação uniforme de sensores é necessária, em que todos os sensores são do mesmo provedor. Provedores diferentes terão diferenças na maneira como a umidade de solo é medida junto com as diferenças na calibragem. O calor é gerado para uma profundidade específica usando os sensores implantados nessa profundidade.

### <a name="before-you-begin"></a>Antes de começar  

Antes de tentar gerar um calor de umidade de solo, verifique o seguinte:

- Pelo menos três sensores de umidade de solo devem ser implantados. A Microsoft recomenda que você não tente criar um mapa de umidade de solo antes que os sensores sejam implantados e associados ao farm.  
- A geração de calor de umidade de solo é influenciada pela cobertura de caminho do Sentinel, pela nuvem e pela nuvem. Pelo menos uma cena de sentinela livre de nuvem deve estar disponível nos últimos 120 dias, a partir do dia em que o mapa de umidade de solo foi solicitado.
- Pelo menos metade dos sensores implantados no farm deve estar online e ter dados transmitidos para o Hub de dados.
- O calor deve ser gerado usando medidas de sensor do mesmo provedor.


## <a name="create-soil-moisture-heatmap"></a>Criar calor de umidade de solo

Utilize os passos seguintes:

1. Na home page, vá para **mapas** no menu de navegação à esquerda para exibir a página mapas.
2. Selecione **criar mapas** e selecione **umidade de solo** no menu suspenso.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Selecione **umidade de solo**.
    A janela umidade de solo é exibida.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Selecione um farm no menu suspenso **farm** .  
   Para pesquisar e selecionar seu farm, você pode rolar a partir da lista suspensa ou digitar o nome do farm no menu suspenso selecionar Farm.
5. No menu suspenso **selecionar medida do sensor de umidade de solo** , selecione a medida do sensor de umidade de solo (profundidade) para a qual você deseja gerar o mapa.
Para localizar a medida do sensor, vá para **sensores**, selecione qualquer sensor de umidade do solo. Em seguida, na seção **Propriedades do sensor** , use o valor em relação ao **nome da medida**
6. Se você quiser gerar para **hoje** ou **esta semana**, selecione uma das opções.
7. Se você quiser gerar um intervalo de datas personalizado, selecione **selecionar intervalo de datas**e forneça a data de início e de término para a qual você deseja gerar o mapa de umidade de solo.
8. Selecione **gerar mapas**.
 Uma mensagem de confirmação com detalhes do trabalho é exibida.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Para obter informações sobre o status do trabalho, consulte a seção **exibir trabalhos**. Se o status do trabalho mostrar *falha*, uma mensagem de erro detalhada será exibida na dica de ferramenta do status de *falha* . Nesse caso, repita as etapas listadas acima e tente novamente.

    Se o problema persistir, consulte a seção [solucionar problemas](troubleshoot-project-farmbeats.md) ou contate o [Fórum do Azure FarmBeats para obter suporte](https://aka.ms/FarmBeatsMSDN) com os logs relevantes.

### <a name="view-and-download-map"></a>Exibir e baixar o mapa

Utilize os passos seguintes:

1. Na home page, vá para **mapas** no menu de navegação à esquerda.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecione **Filtrar** na navegação à esquerda da janela, a janela **filtro** é exibida de onde você pode pesquisar mapas.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Selecione **tipo**, **Data** e **nome** no menu suspenso e, em seguida, selecione **aplicar** para pesquisar o mapa que você deseja exibir. A data em que o trabalho foi criado é mostrada no formato type_farmname_YYYY-MM-DD.
4. Selecione o ícone de **classificação** ao lado dos cabeçalhos de tabela para classificar de acordo com o farm, data, criado em, ID do trabalho e tipo de trabalho.
5. Percorra a lista de mapas disponíveis usando os botões de navegação no final da página.
6. Selecione o mapa que você deseja exibir. Uma janela pop-up exibe a visualização do mapa selecionado.
7. Selecione **baixar** menu suspenso para selecionar o formato de download e o mapa será baixado e armazenado na pasta especificada.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
