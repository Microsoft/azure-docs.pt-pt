---
title: Tutorial - Monitor vídeo usando o Azure IoT Central video analytics - modelo de aplicação de deteção de objetos e movimentos
description: Este tutorial mostra como usar os dashboards na análise de vídeo - modelo de aplicação de deteção de objetos e movimentos para gerir as suas câmaras e monitorizar o vídeo.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: 1c1ddeb8cafd8aa7584da48a715139c5d12eeb10
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874803"
---
# <a name="tutorial-monitor-and-manage-a-video-analytics---object-and-motion-detection-application"></a>Tutorial: Monitorar e gerir uma aplicação de vídeo - aplicação de deteção de objetos e movimentos

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Adicione câmaras de deteção de objetos e movimentos à sua aplicação IoT Central.
> * Gerencie os seus streams de vídeo e reproduza-os quando forem detetados eventos interessantes.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deve completar:

* Um dos anteriores [Criar uma aplicação de análise de vídeo ao vivo no Azure IoT Central](./tutorial-video-analytics-create-app-yolo-v3.md) ou criar uma análise de vídeo em tutoriais [Azure IoT Central (OpenVINO). &trade; ](tutorial-video-analytics-create-app-openvino.md)
* Um dos anteriores [Criar uma instância IoT Edge para análise de vídeo ao vivo (Linux VM)](tutorial-video-analytics-iot-edge-vm.md) ou criar uma instância IoT Edge para tutoriais [de análise de vídeo ao vivo (Intel NUC).](tutorial-video-analytics-iot-edge-nuc.md)

Devias ter o [Docker](https://www.docker.com/products/docker-desktop) instalado na tua máquina local para executar a aplicação do visualizador de vídeo.

## <a name="add-an-object-detection-camera"></a>Adicione uma câmara de deteção de objetos

Na sua aplicação IoT Central, navegue para o dispositivo **LVA Gateway 001** que criou anteriormente. Em seguida, selecione o separador **Comandos.**

Utilize os valores na tabela seguinte como os valores de parâmetro para o comando **Add Camera Request.** Os valores mostrados na tabela assumem que está a usar a câmara simulada no Azure VM, ajuste os valores adequadamente se estiver a utilizar uma câmara real:

| Campo| Descrição| Valor da amostra|
|---------|---------|---------|
| ID da câmera      | ID do dispositivo para provisionamento | câmara-003 |
| Nome da câmera    | Nome amigável           | Câmera de deteção de objetos |
| RTSP Url       | Endereço do fluxo   | RTSP://10.0.0.4:554/media/camera-300s.mkv|
| Nome de utilizador RTSP  |                         | utilizador    |
| Senha RTSP  |                         | palavra-passe    |
| Tipo de Deteção | Lista pendente                | Deteção de Objetos       |

Selecione **Executar** para adicionar o dispositivo da câmara:

:::image type="content" source="media/tutorial-video-analytics-manage/add-camera.png" alt-text="Adicionar câmera":::

> [!NOTE]
> O modelo do detetor **de objetos de borda LVA** já existe na aplicação.

## <a name="add-a-motion-detection-camera-optional"></a>Adicione uma câmara de deteção de movimento (opcional)

Se tiver duas câmaras ligadas ao seu dispositivo de gateway IoT Edge, repita os passos anteriores para adicionar uma câmara de deteção de movimento à aplicação. Utilize diferentes valores para os parâmetros **de ID da câmara,** **nome da câmara**e url **RTSP.**

## <a name="view-the-downstream-devices"></a>Ver os dispositivos a jusante

Selecione o separador **Dispositivos a jusante** para o dispositivo **LVA Gateway 001** para ver os dispositivos da câmara que acabou de adicionar:

:::image type="content" source="media/tutorial-video-analytics-manage/inspect-downstream.png" alt-text="Adicionar câmera":::

Os dispositivos da câmara também aparecem na lista na página **dispositivos** da aplicação.

## <a name="configure-and-manage-the-camera"></a>Configure e gerencie a câmara

Navegue para o dispositivo **camera-003** e selecione o **separador Gerir.**

Utilize os valores predefinidos ou modifique se precisar de personalizar as propriedades do dispositivo:

**Definições de câmera**

| Propriedade | Descrição | Valor sugerido |
|-|-|-|
| Anfitrião de reprodução de vídeo | Anfitrião para o espectador Azure Media Player | http://localhost:8094 |

**Operações e Diagnósticos LVA**

| Propriedade | Descrição | Valor sugerido |
|-|-|-|
| Início automático | Inicie a deteção do objeto quando o Gateway LVA recomeçar | Marcado |
| Debug Telemetria | Vestígios de eventos | Opcional |
|Inferência Tempo limite (seg)| A quantidade de tempo usado para determinar que as inferências pararam | 20 |

**Deteção de Objetos AI**

| Propriedade | Descrição | Valor sugerido |
|-|-|-|
| Tempo de captura de inferência máxima (seg) | Tempo máximo de captura | 15 |
| Aulas de Deteção | Cordas, delimitadas por vírgulas, com as etiquetas de deteção. Para mais informações, consulte a [lista de tags suportadas](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/yolov3-onnx/tags.txt) | caminhão, autocarro |
| Limiar de confiança | Percentagem de qualificação para determinar se a deteção do objeto é válida | 70 |
| Taxa de amostra de quadro de inferência (fps) | [Descrição aqui] | 2 |

Selecione **Guardar**.

Após alguns segundos, vê a mensagem de confirmação **aceite** para cada definição:

:::image type="content" source="media/tutorial-video-analytics-manage/object-detection.png" alt-text="Adicionar câmera":::

## <a name="start-lva-processing"></a>Iniciar o processamento de LVA

Navegue para o dispositivo **camera-003** e selecione o **separador Comandos.**

Executar o comando iniciar o **processamento LVA.**

Quando o comando terminar, consulte o histórico de comando para se certificar de que não há erros:

:::image type="content" source="media/tutorial-video-analytics-manage/start-processing.png" alt-text="Adicionar câmera":::

## <a name="monitor-the-cameras"></a>Monitorize as câmaras

Navegue para o dispositivo **camera-003** e selecione o **separador Dashboard:**

:::image type="content" source="media/tutorial-video-analytics-manage/camera-dashboard.png" alt-text="Adicionar câmera":::

O azulejo **do Conde de Deteção** mostra a contagem média de deteção de cada um dos objetos de deteção selecionados durante um intervalo de deteção de um segundo.

O gráfico **de tartes de Classes de Deteção** mostra a percentagem de deteções para cada tipo de classe.

O **Inference Event Video** é uma lista de ligações aos ativos da Azure Media Services que contêm as deteções. O link utiliza o leitor anfitrião descrito na secção seguinte.

## <a name="start-the-streaming-endpoint"></a>Iniciar o ponto final da transmissão em fluxo

Inicie o ponto final dos Serviços de Comunicação para permitir que a aplicação Media Player do seu cliente transmita o vídeo gravado:

* No portal Azure, navegue para o grupo de recursos **Lva-rg.**
* Abra o **recurso Streaming Endpoint.**
* Na página de detalhes do **ponto final do streaming,** selecione **Start**. Vê-se um aviso de que a faturação começa quando o ponto final começa.

## <a name="view-stored-video"></a>Ver vídeo armazenado

Os dias de ver câmaras e reagir a imagens suspeitas acabaram. Com a marcação automática de eventos e ligações diretas ao vídeo armazenado com a deteção inferida, os operadores de segurança podem encontrar eventos de interesse numa lista e, em seguida, seguir o link para ver o vídeo.

Pode utilizar o [leitor de vídeo AMP](https://github.com/Azure/live-video-analytics/tree/master/utilities/amp-viewer) para visualizar o vídeo armazenado na sua conta Azure Media Services.

A aplicação IoT Central armazena o vídeo nos Serviços Azure Media de onde pode transmiti-lo. Precisa de um leitor de vídeo para reproduzir o vídeo armazenado no Azure Media Services.

Certifica-te que o **Docker** está a trabalhar na tua máquina local.

Abra uma indicação de comando e utilize o seguinte comando para executar o leitor de vídeo num recipiente Docker na sua máquina local. Substitua os espaços reservados no comando pelos valores que fez anteriormente no ficheiro *scratchpad.txt.* Fez uma nota do `amsAadClientId` , , , , , e quando criou o principal serviço para a sua conta `amsAadSecret` de `amsAadTenantId` `amsSubscriptionId` `amsAccountName` Serviços de Media:

```bash
docker run -it --rm -e amsAadClientId="<FROM_AZURE_PORTAL>" -e amsAadSecret="<FROM_AZURE_PORTAL>" -e amsAadTenantId="<FROM_AZURE_PORTAL>" -e amsArmAadAudience="https://management.core.windows.net" -e amsArmEndpoint="https://management.azure.com" -e amsAadEndpoint="https://login.microsoftonline.com" -e amsSubscriptionId="<FROM_AZURE_PORTAL>" -e amsResourceGroup="<FROM_AZURE_PORTAL>" -e amsAccountName="<FROM_AZURE_PORTAL>" -p 8094:8094 mcr.microsoft.com/lva-utilities/amp-viewer:1.0-amd64
```

|Parâmetro estivador | Acesso AMS API (JSON)|
|-|-|
|amsAadClientId| AadClientId|
|amsAadSecret| AadSecret |
|amsAadTenantId| AadTenantId |
|amsSubscriptionId| SubscriptionId|
|amsResourceGroup| ResourceGroup |
|amsAccountName| AccountName|

Navegue para o dispositivo **camera-003** e selecione o **separador Dashboard.** Em seguida, clique numa das hiperligações de deteção de objetos capturados no azulejo **inference Event Video.** O vídeo aparece numa página exibida pelo leitor de vídeo local:

:::image type="content" source="media/tutorial-video-analytics-manage/video-snippet.png" alt-text="Adicionar câmera":::

## <a name="change-the-simulated-devices-in-application-dashboards"></a>Alterar os dispositivos simulados nos painéis de aplicação

Os painéis de aplicação são originalmente povoados com telemetria e propriedades geradas a partir dos dispositivos simulados IoT Central. Para configurar os azulejos para telemetria a partir de câmaras reais ou do simulador Live555, siga estes passos:

1. Navegue para o painel de aplicações **(Amostra) Real Camera Monitor.**
1. Selecione **Editar**.
1. Selecione o azulejo **Note** e elimine-o.
1. Mude o título do painel para *"Real Camera Monitor".*
1. No azulejo do Conde de **Inferência,** selecione o ícone de configuração.
1. Na secção Gráfico de **Configuração,** selecione uma ou mais câmaras reais no grupo **de dispositivos de detetor de objetos de borda LVA.**
1. Selecione o `AI Inference Interface/Inference Count` campo de telemetria.
1. Selecione **Atualizar**.

1. Repita os passos para os seguintes azulejos:
    1. O gráfico de tartes **de deteção** usa o `AI Inference Interface/Inference/entity/tag/value` tipo de telemetria.
    1. **A inferência** usa `AI Inference Interface/Inference/entity/tag/value` o último valor conhecido.
    1. **A confiança %** usa `AI Inference Interface/Inference/entity/tag/confidence` o último valor conhecido.
    1. **O instantâneo** usa `AI Inference Interface/Inference Image` mostrado como uma imagem.
    1. **Inference Event Video** usa `AI Inference Interface/Inference Event Video` mostrado como um link.
1. Selecione **Guardar**.

O **painel de instrumentos do Monitor de Câmara Real** mostra agora valores do seu verdadeiro dispositivo de câmara:

:::image type="content" source="media/tutorial-video-analytics-manage/update-real-cameras.png" alt-text="Adicionar câmera":::

## <a name="pause-processing"></a>Tratamento de pausa

Pode parar o processamento de análise de vídeo ao vivo na aplicação:

1. Na sua aplicação, navegue na página **Comandos** para obter câmara de deteção de objetos. Em seguida, executar o comando **stop LVA Processing.**

1. Pare o ponto final de streaming da sua conta de serviços de comunicação social:
    * No portal Azure, navegue para o grupo de recursos **Lva-rg.**
    * Clique no recurso **Streaming Endpoint.**
    * Na página de detalhes do **ponto final do streaming,** selecione **Stop**.

## <a name="tidy-up"></a>Arrumar tudo

Se terminou a aplicação, pode remover todos os recursos que criou da seguinte forma:

1. Na aplicação IoT Central, navegue para a **página de aplicação** da sua candidatura na secção **Administração.** Em seguida, selecione **Eliminar**.
1. No portal Azure, elimine o grupo de recursos **Lva-rg.**
1. Na sua máquina local, pare o recipiente **do espectador** estivador.

## <a name="next-steps"></a>Passos seguintes

Agora aprendeu a adicionar câmaras à aplicação IoT Central e configurá-las para deteção de objetos e movimentos.

Para aprender a personalizar o código fonte para os módulos IoT Edge:

> [!div class="nextstepaction"]
> [Modifique e construa os módulos de gateway de vídeo analytics ao vivo](./tutorial-video-analytics-build-module.md)
