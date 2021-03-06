---
title: Gerir quintas
description: Descreve como gerir quintas
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: 050b3b4d67eda9b6c9b4621c014e3e6baad34053
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173805"
---
# <a name="manage-farms"></a>Gerir terrenos agrícolas

Você pode gerir suas fazendas em Azure FarmBeats. Este artigo fornece a informação sobre como criar quintas, instalar dispositivos, sensores e drones que o ajudam a gerir as suas quintas.

## <a name="create-farms"></a>Criar quintas

Utilize os passos seguintes:

1. Faça login no Acelerador de Fazenda, a página **Farms** exibe.
    A página **Farms** exibe a lista de explorações no caso de já terem sido criadas em subscrição.

    Aqui está a imagem da amostra:

    ![Screenshot que mostra a página Farms.](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. Selecione **Create Farm** e forneça **nome,** **colheitas** e **endereço.**
3. No Limite de **Definição de Fazenda**, (campo obrigatório) selecione **mark on Map** ou **Paste GeoJSON code**.

Aqui estão as duas formas de definir um limite agrícola:

1. **Marca no Mapa**: Utilize a ferramenta de controlo do mapa para desenhar e marcar o limite da quinta. Para marcar os limites,  ![ o Screenshot mostra o ícone do lápis para desenhar limites no mapa e marcar os ](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png) limites exatos.

    ![Screenshot que mostra os limites desenhados num mapa.](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **Código De Pasta GeoJson**: O GeoJSON é um formato para codificar estruturas geográficas de dados, utilizando a Notação de Objetos JavaScript (JSON). Esta opção exibe uma caixa de texto onde uma cadeia GeoJSON pode ser inserida para marcar os limites da quinta. Também pode criar o código GeoJSON a partir de GeoJSON.io.
Utilize as pontas de ferramentas para ajudar a preencher as informações.

    ![Screenshot que realça a opção Código Pasta GeoJson no ecrã Create Farm.](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  **Selecione Submeter** para criar uma fazenda. Uma nova quinta é criada e exibida na página **Farms.**

## <a name="view-farm"></a>Ver quinta

A página da lista farm apresenta uma lista de quintas criadas. Selecione uma quinta para ver a lista de:

 - **Contagem do dispositivo** — exibe o número e o estado dos dispositivos implantados dentro da exploração.
 - **Mapa** — mapa da quinta com os dispositivos implantados na quinta.
 - **Telemetria** — exibe a telemetria dos sensores implantados na quinta.
 - **Mapas de Precisão mais recentes** — apresenta o mais recente mapa de índices de satélite (EVI, NDWI), mapa de calor de humidade do solo e mapa de colocação de sensores.

## <a name="edit-farm"></a>Fazenda de edição

A página **Farms** apresenta uma lista de quintas criadas.

1.  Selecione uma quinta para ver e editar a quinta.
2.  **Selecione EditAr Farm** para editar as informações da quinta. Na janela Detalhes da **Fazenda,** pode editar **Nome,** **Colheitas,** **Endereço** e definir campos **de fronteira agrícola.**

    ![Project Farm Beats](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. **Selecione Enviar** para guardar os detalhes editados.

## <a name="delete-farm"></a>Apagar quinta

A página **Farms** apresenta uma lista de quintas criadas. Utilize os seguintes passos para eliminar uma exploração:

1.  Selecione uma fazenda da lista para apagar detalhes da fazenda.
2.  Selecione **Delete Farm** para apagar a quinta.

    ![Screenshot que mostra o ecrã Delete Farm e realça o botão Eliminar.](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > Quando se apaga uma quinta, os dispositivos e mapas associados à quinta não são eliminados. Qualquer informação sobre a fazenda associada ao dispositivo e mapas não será relevante. Pode continuar a visualizar dispositivos, telemetria e mapas a partir do serviço FarmBeats.


## <a name="next-steps"></a>Passos seguintes

Agora que criou a sua quinta, aprenda a [obter dados de sensores](get-sensor-data-from-sensor-partner.md) fluindo para a sua fazenda.
