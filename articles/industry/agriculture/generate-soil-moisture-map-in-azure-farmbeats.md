---
title: Gerar mapa de calor de humidade do solo
description: Descreve como gerar mapa de calor de humidade do solo em Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: dd8d688355e0f71ce77cdbc6012d788c7b16d825
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173788"
---
# <a name="generate-soil-moisture-heatmap"></a>Gerar mapa de calor de humidade do solo

A humidade do solo é a água que é mantida nos espaços entre partículas do solo.O mapa de calor da humidade do solo ajuda-o a compreender os dados de humidade a qualquer profundidade e em alta resolução dentro das suas quintas. Para gerar um mapa de calor preciso e utilizável do solo, é necessária uma colocação uniforme de sensores do mesmo fornecedor. Diferentes fornecedores terão diferenças na forma como a humidade do solo é medida juntamente com as diferenças de calibração. O Mapa de Calor é gerado para uma determinada profundidade utilizando os sensores implantados a esta profundidade.

Este artigo descreve o processo de geração de um mapa de calor de humidade do solo para a sua quinta, utilizando o Acelerador Azure FarmBeats. Neste artigo, aprenderá a:

- [Criar Quintas](#create-a-farm)
- [Atribuir sensores a Quintas](#get-soil-moisture-sensor-data-from-partner)
- [Gerar mapa de calor de humidade do solo](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>Antes de começar

Certifique-se do seguinte:  

- Uma subscrição do Azure.
- Um exemplo de Azure FarmBeats.
- Para a quinta estão disponíveis três sensores mínimos de humidade do solo.

## <a name="create-a-farm"></a>Criar uma quinta

Uma quinta é uma área geográfica de interesse para a qual pretende criar um mapa de calor de humidade do solo. Você pode criar uma fazenda usando a [API farms](https://aka.ms/FarmBeatsDatahubSwagger) ou no [FarmsBeats Accelerator UI](manage-farms-in-azure-farmbeats.md#create-farms)

## <a name="deploy-sensors"></a>Implementar sensores

Deve colocar fisicamente sensores de humidade do solo na quinta. Pode adquirir sensores de humidade do solo a qualquer um dos nossos parceiros aprovados - [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/) e [Teralytic](https://teralytic.com/). Deve coordenar-se com o seu fornecedor de sensores para fazer a configuração física na sua quinta.

## <a name="get-soil-moisture-sensor-data-from-partner"></a>Obtenha dados do sensor de humidade do solo do parceiro

À medida que os sensores começam a ser transmitidos, os dados para o painel de dados do parceiro permitem os dados em Azure FarmBeats. Isto pode ser feito a partir da aplicação do parceiro.

Por exemplo, se tiver adquirido sensores Davis, iniciará sessão na sua conta de ligação meteorológica e fornecerá as credenciais necessárias para permitir o streaming de dados em Azure FarmBeats. Para obter as credenciais necessárias, siga as instruções a partir dos [dados](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners)do sensor .

Assim que introduzir as suas credenciais e **selecionar Submeter-se** à aplicação do parceiro, pode ter os dados a fluir para a Azure FarmBeats.

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>Atribuir sensores de humidade do solo à quinta

Uma vez que tenha ligado a sua conta de sensores ao Azure FarmBeats, tem de atribuir os sensores de humidade do solo à quinta de interesse.

1.  Na página inicial, selecione **Farms** do menu, é apresentada a página da lista **Farms.**
2.  Selecione **MyFarm**  >  **Add Devices**.
3.  A janela **Add Devices** exibe. Selecione qualquer dispositivo que esteja ligado aos sensores de humidade do solo para a sua quinta.

    ![Screenshot que mostra o ecrã de Add Devices.](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Selecione **Dispositivos de Adicionar**.     

## <a name="generate-soil-moisture-heatmap"></a>Gerar mapa de calor de humidade do solo

Este passo é criar um emprego ou uma operação de longa duração que gere o mapa de calor da humidade do solo para a sua quinta.

1.  Na página inicial, vá a **Quintas** a partir do menu de navegação à esquerda para ver a página das quintas.
2.  Selecione **MyFarm**.
3.  Na página Detalhes da **Fazenda,** **selecione 'Gerar Mapa de Precisão'.**
4.  A partir do menu suspenso, selecione **Humidade do Solo**.
5.  Na janela de humidade do **solo,** selecione **This Week**.
6.  Na Medida de **Sensor** de Humidade do **Solo Select,** insira a medida que pretende utilizar para o mapa.
    Para encontrar a medida do sensor, nos **Sensores,** selecione qualquer sensor de humidade do solo. Nas **propriedades sensoriais,** utilize o valor **do Nome da Medida.**

    ![Screenshot que mostra o ecrã de humidade do solo.](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  Selecione **'Gerar Mapas'.**
    É apresentada uma mensagem de confirmação com detalhes do trabalho. Para mais informações, consulte o Estatuto de Emprego em Emprego.

    >[!NOTE]
    > O trabalho leva cerca de 3 a 4 horas para ser concluído.

### <a name="download-the-soil-moisture-heatmap"></a>Descarregue o mapa de calor da humidade do solo

Utilize os passos seguintes:

1. Na página **Jobs,** verifique o **Estado do Trabalho** para o trabalho que criou no último procedimento.
2. Quando o estado do trabalho mostrar **Sucesso**, selecione **Mapas** no menu.
3. Procure o mapa no dia em que foi criado no formato <> moisture_MyFarm_YYYY-MM-DD.
4. Selecione um mapa na coluna **Nome,** uma janela pop-up aparece com a pré-visualização do mapa selecionado.
5. Selecione **Transferir**. O mapa é descarregado e armazenado na pasta local do seu computador.

    ![Project Farm Beats](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>Passos seguintes

Agora que gerou com sucesso um heatmap de humidade do solo, aprenda a [gerar colocação de sensores](generate-maps-in-azure-farmbeats.md#sensor-placement-map) e [ingere dados históricos de telemetria.](ingest-historical-telemetry-data-in-azure-farmbeats.md) 
