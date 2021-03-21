---
title: Reprodução de gravações de vários dias - Azure
description: Neste tutorial, você aprenderá a usar APIs do Azure Media Service para reproduzir uma gravação de vídeo contínua de vários dias.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 81a778b40649c1318b3738a289f0db37fd35376a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492793"
---
# <a name="tutorial-playback-of-multi-day-recordings"></a>Tutorial: Reprodução de gravações de vários dias  

Este tutorial baseia-se no tutorial contínuo de gravação de [vídeo](continuous-video-recording-concept.md) (CVR). Neste tutorial, você aprenderá a usar APIs do Azure Media Service para reproduzir uma gravação de vídeo contínua de vários dias a partir da nuvem. 

Isto é útil para cenários como a segurança pública, onde é necessário manter uma gravação das imagens de uma câmara durante vários dias (ou semanas), e há uma necessidade ocasional de assistir a partes específicas dessa filmagem.

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Execute a aplicação de amostra que demonstra como pode navegar no conteúdo de uma gravação de vários dias
> * Ver partes selecionadas dessa gravação

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida  

Recomenda-se que leia as seguintes páginas de documentação:

* [Análise de vídeo ao vivo na visão geral do IoT Edge](overview.md)
* [Análise de vídeo ao vivo na terminologia IoT Edge](terminology.md)
* [Conceito de gráfico de mídia](media-graph-concept.md)
* [Gravação de vídeo contínua](continuous-video-recording-concept.md) 
* [Como guiar: Reprodução de gravações](playback-recordings-how-to.md)
* [Tutorial: Gravação contínua de vídeo](continuous-video-recording-tutorial.md)

## <a name="prerequisites"></a>Pré-requisitos

* Complete o [tutorial cvr](continuous-video-recording-tutorial.md). Embora este tutorial e as APIs relevantes discutidas em [Tutorial: Gravação contínua de vídeo](continuous-video-recording-tutorial.md) se aplique a gravações que sejam de 5 minutos ou mais, recomenda-se que grave 5 horas de vídeo, se não mais. As APIs utilizadas para navegar nas gravações são melhor demonstradas com gravações longas.
* Recomendamos que execute este tutorial enquanto o [Tutorial: Gravação contínua de vídeo](continuous-video-recording-tutorial.md) ainda está em execução – ou seja, ainda está a gravar vídeo para a nuvem.

## <a name="run-the-sample"></a>Executar o exemplo 

Como parte do tutorial da [CVR,](continuous-video-recording-tutorial.md)teria criado uma conta de Media Service. Para este tutorial, terá de ter acesso completo à API a essa conta. Pode utilizar os passos em [Obter credenciais para aceder à API dos Serviços de Mídia](../latest/access-api-howto.md?tabs=portal) para criar um principal serviço. Você deve ser capaz de obter um bloco JSON do portal Azure que se parece com isto:

```
{
    "AadClientId": "<<INSERT_AZURE_AD_APP_ID_HERE>>",
    "AadSecret": "<<INSERT_AZURE_AD_APP_SECRET_HERE>>",
    "AadTenantDomain": "<<YOUR_TENANT_DOMAIN>>",
    "AadTenantId": "<<YOUR_TENANT_ID>>",
    "AccountName": "<<YOUR_ACCOUNT_NAME>>",
    "Location": "<<YOUR_REGION_NAME>>",
    "ResourceGroup": "<<YOUR_RESOURCE_GROUP>>",
    "SubscriptionId": "<<YOUR_SUBSCRIPTION_ID>>",
    "ArmAadAudience": "https://management.core.windows.net",
    "ArmEndpoint": "https://management.azure.com"
}
```

Em seguida, no código Visual Studio, aberto src/ams-asset-player. Esta pasta contém os ficheiros necessários para este tutorial. Abra a appsettings.jsem ficheiro e copie o seu conteúdo num novo ficheiro, appsettings.development.jsligado. Faça as seguintes edições para o recém-criado appsettings.development.jsem:

```
  "AMS" : {
    "subscriptionId" : "Use value of SubscriptionId above",
    "resourceGroup" : "Use value of ResourceGroup above",
    "accountId" : "Use value of AccountName above",
    "aadTenantId" : "Use value of AadTenantId above",
    "aadClientId" : "Use value of AadClientId above",
    "aadSecret" : "Use value of AadSecret above"
} 
```

1. No Código do Estúdio Visual, abra o **separador Extensões** (ou prima Ctrl+Shift+X) e procure por Azure IoT Hub.
1. Clique no direito e selecione **Definições de extensão**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Definições de extensão":::
1. Procure e ative "Mostrar Mensagem Verbose".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Mostrar mensagem verbose":::
1. <!--In Visual Studio Code, you can click-->Clique no ícone Executar à esquerda (ou Ctrl+Shift+D) para apresentar as aplicações disponíveis para executar:

    ![A screenshot mostra um menu no Código do Estúdio Visual com o item de execução selecionado.](./media/playback-multi-day-recordings-tutorial/run.png)
1. Selecione a aplicação AMS Asset Player a partir da caixa drop down, como mostrado abaixo, e bata em F5 para começar a depurar.

    ![A screenshot mostra um menu no Código do Estúdio Visual com o Ams Asset Player selecionado.](./media/playback-multi-day-recordings-tutorial/debug.png)

A aplicação da amostra irá construir e lançar a sua aplicação de navegador padrão, e abrir a página AMS Asset Player.

> [!NOTE]
> Dependendo das definições de segurança do seu navegador, poderá ver uma mensagem de aviso. Uma vez que a página web está a funcionar localmente, pode optar por ignorar o aviso.

O Leitor de Ativos AMS pede-lhe que introduza o nome de um ativo do Media Service. Deve utilizar o nome do Ativo que utilizou para gravar vídeos em [Tutorial: Gravação contínua de vídeo](continuous-video-recording-tutorial.md).

Ao escrever o nome do ativo e bater, o código do jogador carregará o URL de streaming. Para mais informações, consulte [Como-a-Guia: Reprodução de gravações](playback-recordings-how-to.md). Se, como recomendado, ainda estiver a gravar para o ativo, o leitor irá detetar isso e tentará fazer a reprodução para a parte mais recente do vídeo gravado. Pode ver a horatamp (em UTC) na parte superior esquerda do leitor. Na imagem abaixo, note como o botão "Live" é selecionado.

![Fluxo](./media/playback-multi-day-recordings-tutorial/assetplayer1.png)
 
No lado direito do leitor, pode ver os controlos para navegar no arquivo. Os anos, meses e datas deste controlo são preenchidos utilizando a API dos Media disponível documentada em [Como-Guia: Reprodução de gravações](playback-recordings-how-to.md).
Quando expandir o dia, se tiver deixado o Tutorial CVR funcionar durante várias horas, verá um resultado como este:

![Procurar arquivo](./media/playback-multi-day-recordings-tutorial/results.png)

A fonte para o feed de vídeo no tutorial é um ficheiro MKV. Quando o simulador RSTP (ver [Live555 Media Server)](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)chega ao fim do ficheiro, termina o fluxo. O nó de origem RTSP no gráfico de mídia deteta-o e restabelece a ligação, e o vídeo retoma. Entre cada extremidade do ficheiro e reconectar,.

![Resultados](./media/playback-multi-day-recordings-tutorial/assetplayer2.png)
 
Quando clica em qualquer uma das entradas da lista, a aplicação cria um URL de streaming com o filtro apropriado, tal como https://{hostname}/{locatorId}/content.ism/manifest(formato=mpd-time-csf,startTime=YYYY-MM-DDTHH:MM:SS). O leitor carregará este URL e a reprodução começará no início de tempo desejado.

Em alternativa, pode utilizar horários de início e de fim específicos, através dos controlos na parte inferior da página. Pode utilizar os resultados da chamada AvailableMedia, conforme listado no lado direito, como um guia para os valores permitidos dos tempos de início e fim. As restrições detalhadas nos filtros startTime e endTime estão documentadas em [Como-a-Guia: Reprodução de gravações](playback-recordings-how-to.md). Depois de selecionar os valores de tempo, ao clicar em Enviar, a aplicação carregará o leitor com um URL de streaming como: https://{hostname}/{locatorId}/content.ism/manifest (formato=mpd-time-csf,startTime=YYYY-MM-DDTHH:MM:SS,endTime= YYYY-MM-DDTHH:MMTHH:MM:SS) A reprodução começará no início desejado.

## <a name="next-steps"></a>Passos seguintes

[Gravação de vídeo baseada em eventos para nuvem e reprodução da nuvem](event-based-video-recording-tutorial.md)
