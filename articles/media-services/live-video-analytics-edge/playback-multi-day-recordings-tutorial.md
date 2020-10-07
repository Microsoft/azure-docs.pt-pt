---
title: Reprodução de gravações de vários dias - Azure
description: Neste tutorial, você aprenderá a usar APIs do Azure Media Service para reproduzir uma gravação de vídeo contínua de vários dias.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 1ec9260be7241057478b06446ac2aa53c14bcb47
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803437"
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

Em seguida, no código Visual Studio, aberto src/ams-asset-player. Esta pasta contém os ficheiros necessários para este tutorial. Abra a appsettings.jsem ficheiro e copie o seu conteúdo num novo ficheiro, appsettings.development.jsligado. Faça as seguintes edições para este último ficheiro:

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
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Definições de extensão" é selecionado.

![Fluxo](./media/playback-multi-day-recordings-tutorial/assetplayer1.png)
 
No lado direito do leitor, pode ver os controlos para navegar no arquivo. Os anos, meses e datas deste controlo são preenchidos utilizando a API dos Media disponível documentada em [Como-Guia: Reprodução de gravações](playback-recordings-how-to.md).
Quando expandir o dia, se tiver deixado o Tutorial CVR funcionar durante várias horas, verá um resultado como este:

![Procurar arquivo](./media/playback-multi-day-recordings-tutorial/results.png)

A fonte para o feed de vídeo no tutorial é um ficheiro MKV. Quando o simulador RSTP (ver [Live555 Media Server)](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)chega ao fim do ficheiro, termina o fluxo. O nó de origem RTSP no gráfico de mídia deteta-o e restabelece a ligação, e o vídeo retoma. Entre cada extremidade do ficheiro e reconectar,.

![Results](./media/playback-multi-day-recordings-tutorial/assetplayer2.png)
 
Quando clica em qualquer uma das entradas da lista, a aplicação cria um URL de streaming com o filtro apropriado, tal como https://{hostname}/{locatorId}/content.ism/manifest(formato=mpd-time-csf,startTime=YYYY-MM-MMTHH:MM:SS). O leitor carregará este URL e a reprodução começará no início de tempo desejado.

Em alternativa, pode utilizar horários de início e de fim específicos, através dos controlos na parte inferior da página. Pode utilizar os resultados da chamada AvailableMedia, conforme listado no lado direito, como um guia para os valores permitidos dos tempos de início e fim. As restrições detalhadas nos filtros startTime e endTime estão documentadas em [Como-a-Guia: Reprodução de gravações](playback-recordings-how-to.md). Depois de selecionar os valores de tempo, ao clicar em Enviar, a aplicação carregará o leitor com um URL de streaming como: https://{hostname}/{locatorId}/content.ism/manifest(formato=mpd-time-csf,startTime=YYYYY-MM-DDTHH:MM:SS,endTime= YYYY-MM-DDTHH:MM:MM:SS) A reprodução começará no início desejado.

## <a name="next-steps"></a>Passos seguintes

[Gravação de vídeo baseada em eventos para nuvem e reprodução da nuvem](event-based-video-recording-tutorial.md)
