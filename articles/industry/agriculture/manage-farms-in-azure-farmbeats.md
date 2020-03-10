---
title: Gerir Quintas
description: Descreve como gerir quintas
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 09144c4c35ab911b60931849807123608f2c3cdd
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385157"
---
# <a name="manage-farms"></a>Gerir terrenos agrícolas

Você pode gerir suas fazendas em Azure FarmBeats. Este artigo fornece informações sobre como criar quintas, instalar dispositivos, sensores e drones que o ajudam a gerir as suas quintas.

## <a name="create-farms"></a>Criar quintas

Utilize os passos seguintes:

1. Login no Acelerador de Fazendas, a página **Farms** exibe.
    A página **Farms** apresenta a lista de quintas no caso de já terem sido criadas por subscrição.

    Aqui está a imagem da amostra:

    ![Batidas da Fazenda do Projeto](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. Selecione **Criar Quinta** e fornecer **Nome,** **Colheitas** e **Endereço**.
3. No **Limite da Fazenda Define**, (campo obrigatório) selecione **ou Marca no mapa** ou **código Paste GeoJSON**.

Aqui estão as duas formas de definir um limite agrícola:

1. **Marca no mapa**: Utilize a ferramenta de controlo do mapa para desenhar e marcar o limite da quinta. Para marcar os limites, ![Project Farm Bate](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png) e marcar os limites exatos.

    ![Batidas da Fazenda do Projeto](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **Código GeoJson**da Pasta : O GeoJSON é um formato para codificar estruturas geográficas de dados, utilizando a Notação de Objetos JavaScript (JSON). Esta opção exibe uma caixa de texto onde uma cadeia GeoJSON pode ser inserida para marcar os limites da fazenda. Também pode criar código GeoJSON a partir de GeoJSON.io.
Utilize as pontas de ferramentas para ajudar a preencher a informação.

    ![Batidas da Fazenda do Projeto](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  Selecione **Submeter** para criar uma quinta. Uma nova quinta é criada e exibida na página **quinta.**

## <a name="view-farm"></a>Ver fazenda

A página da lista farm apresenta uma lista de quintas criadas. Selecione uma quinta para ver a lista de:

 - A **contagem do dispositivo** - apresenta o número e o estado dos dispositivos implantados dentro da exploração.
 - **Mapa** — mapa da quinta com os dispositivos implantados na quinta.
 - **Telemetria** — exibe a telemetria dos sensores implantados na quinta.
 - **Os mais recentes mapas** de precisão — exibem o mais recente mapa de índices de satélite (EVI, NDWI), mapa de calor da humidade do solo e mapa de colocação de sensores.

## <a name="edit-farm"></a>Editar quinta

A página **Farms** apresenta uma lista de quintas criadas.

1.  Selecione uma quinta para ver e editar a quinta.
2.  Selecione **Editar Quinta** para editar as informações da quinta. Na janela Detalhes da **Fazenda,** pode editar **Nome**, **Colheitas,** **Endereço,** e definir campos **de fronteira agrícola.**

    ![Batidas da Fazenda do Projeto](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. Selecione **Submeter** para guardar os detalhes editados.

## <a name="delete-farm"></a>Eliminar quinta

A página **Farms** apresenta uma lista de quintas criadas. Utilize os seguintes passos para eliminar uma exploração:

1.  Selecione uma quinta da lista para apagar detalhes da quinta.
2.  **Selecione Delete Farm** para apagar a quinta.

    ![Batidas da Fazenda do Projeto](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > Quando se elimina uma quinta, os dispositivos e mapas associados à quinta não são apagados. Qualquer informação agrícola associada ao dispositivo e mapas não será relevante. Pode continuar a ver dispositivos, telemetria e mapas do serviço FarmBeats.


## <a name="next-steps"></a>Passos Seguintes

Agora que criou a sua quinta, aprenda a obter dados de [sensores](get-sensor-data-from-sensor-partner.md) fluindo para a sua fazenda.
