---
title: Gerar mapa de calor da humidade do solo
description: Descreve como gerar mapa de calor da humidade do solo em Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a2115e9c1601c86cce8857c10baf12b91cc2b997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482572"
---
# <a name="generate-soil-moisture-heatmap"></a>Gerar mapa de calor da humidade do solo

A humidade do solo é a água que é mantida nos espaços entre partículas de solo.O Heatmap da Humidade do Solo ajuda-o a compreender os dados de humidade a qualquer profundidade e em alta resolução dentro das suas quintas. Para gerar um mapa de calor preciso e utilizável para a humidade do solo, é necessária uma implantação uniforme de sensores do mesmo fornecedor. Diferentes fornecedores terão diferenças na forma como a humidade do solo é medida juntamente com as diferenças de calibração. O Heatmap é gerado para uma profundidade particular utilizando os sensores implantados a esta profundidade.

Este artigo descreve o processo de geração de um Mapa de Calor da Humidade do Solo para a sua quinta, utilizando o Acelerador Azure FarmBeats. Neste artigo, aprenderá a:

- [Criar Quintas](#create-a-farm)
- [Atribuir sensores a Fazendas](#get-soil-moisture-sensor-data-from-partner)
- [Gerar mapa de calor da humidade do solo](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>Antes de começar

Certifique-se do seguinte:  

- Uma subscrição do Azure.
- Um exemplo de Azure FarmBeats.
- Um mínimo de três sensores de humidade do solo estão disponíveis para a exploração.

## <a name="create-a-farm"></a>Criar uma quinta

Uma quinta é uma área geográfica de interesse para a qual você quer criar um mapa de calor de humidade do solo. Você pode criar uma fazenda usando a [Farms API](https://aka.ms/FarmBeatsDatahubSwagger) ou no [FarmsBeats Accelerator UI](manage-farms-in-azure-farmbeats.md#create-farms)

## <a name="deploy-sensors"></a>Implantar sensores

Deve colocar fisicamente sensores de humidade do solo na quinta. Você pode comprar sensores de humidade do solo de qualquer um dos nossos parceiros aprovados - [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/) e [Teralytic](https://teralytic.com/). Deve coordenar-se com o seu fornecedor de sensores para fazer a configuração física na sua quinta.

## <a name="get-soil-moisture-sensor-data-from-partner"></a>Obtenha dados do sensor de humidade do solo do parceiro

À medida que os sensores começam a ser transmitidos, os dados no painel de dados do parceiro, permitem os dados em Azure FarmBeats. Isto pode ser feito a partir da aplicação do parceiro.

Por exemplo, se tiver adquirido sensores Davis, iniciará sessão na sua conta de ligação meteorológica e fornecerá as credenciais necessárias para permitir o streaming de dados no Azure FarmBeats. Para obter as credenciais necessárias, siga as instruções de [Obter dados](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners)do sensor .

Assim que introduzir as suas credenciais e selecionar **Submeter** na aplicação do parceiro, pode ter os dados a fluir para o Azure FarmBeats.

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>Atribuir sensores de humidade do solo à quinta

Depois de ter ligado a sua conta de sensor a Azure FarmBeats, tem de atribuir os sensores de humidade do solo à quinta de interesses.

1.  Na página inicial, selecione **Farms** do menu, a página da lista **de Quintas** é exibida.
2.  Selecione **MyFarm** > **Add Devices**.
3.  Os visores da janela **Add Devices.** Selecione qualquer dispositivo ligado aos sensores de humidade do solo para a sua exploração.

    ![Batidas da Fazenda do Projeto](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Selecione **Adicionar Dispositivos**.     

## <a name="generate-soil-moisture-heatmap"></a>Gerar mapa de calor da humidade do solo

Este passo é criar um emprego ou uma operação de longa duração que gere o Mapa de Calor da Humidade do Solo para a sua quinta.

1.  Na página inicial, vá a **Quintas** do menu de navegação à esquerda para ver a página da quinta.
2.  Selecione **MyFarm**.
3.  Na página Detalhes da **Fazenda,** **selecione Generate Precision Map**.
4.  A partir do menu suspenso, selecione Humidade do **Solo**.
5.  Na janela humidade do **solo,** selecione **This Week**.
6.  Na medida de **sensor**de humidade do **solo seleta,** introduza a medida que pretende utilizar para o mapa.
    Para encontrar a medida do sensor, em **Sensores,** selecione qualquer sensor de humidade do solo. Nas propriedades do **sensor,** utilize o valor **de nome de medida.**

    ![Batidas da Fazenda do Projeto](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  Selecione **Generate Maps**.
    É apresentada uma mensagem de confirmação com detalhes do trabalho. Para mais informações, consulte o Estatuto de Emprego em Empregos.

    >[!NOTE]
    > O trabalho leva cerca de 3 a 4 horas para ser concluído.

### <a name="download-the-soil-moisture-heatmap"></a>Descarregue o mapa de calor da humidade do solo

Utilize os passos seguintes:

1. Na página **Jobs,** verifique o Estado de **Trabalho** para o trabalho que criou no último procedimento.
2. Quando o estado de trabalho mostrar **sucesso,** selecione **Maps** no menu.
3. Procure o mapa no dia em que foi criado no formato <> de moisture_MyFarm_YYYY-MM-DD.
4. Selecione um mapa na coluna **Nome,** uma janela pop-up com a pré-visualização do mapa selecionado.
5. Selecione **Transferir**. O mapa é descarregado e armazenado na pasta local do seu computador.

    ![Batidas da Fazenda do Projeto](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>Passos seguintes

Agora que gerou com sucesso um Mapa de Calor da Humidade do Solo, aprenda a [gerar colocação](generate-maps-in-azure-farmbeats.md#sensor-placement-map) de sensores e ingerir dados históricos de [telemetria.](ingest-historical-telemetry-data-in-azure-farmbeats.md) 
