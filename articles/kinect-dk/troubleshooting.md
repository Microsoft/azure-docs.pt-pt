---
title: Azure Kinect conhecidos problemas e resolução de problemas
description: Saiba mais sobre alguns dos problemas conhecidos e dicas de resolução de problemas ao utilizar o Sensor SDK com Azure Kinect DK.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/05/2021
ms.topic: conceptual
keywords: resolução de problemas, atualização, bug, kinect, feedback, recuperação, registo, dicas
ms.openlocfilehash: da5242a09934a756093a9e02b6d474e6c75fecda
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108745"
---
# <a name="azure-kinect-known-issues-and-troubleshooting"></a>Azure Kinect conhecidos problemas e resolução de problemas

Esta página contém problemas conhecidos e dicas de resolução de problemas ao utilizar o Sensor SDK com Azure Kinect DK. Consulte também [as páginas de suporte do produto](./index.yml) para problemas específicos do hardware do produto.

## <a name="known-issues"></a>Problemas conhecidos

- Problemas de compatibilidade com controladores de anfitriões USB ASMedia (por exemplo, chipset ASM1142)
  - Alguns casos que usam o controlador USB da Microsoft podem desbloquear
  - Muitos computadores também têm controladores de anfitrião alternativos e mudar a porta USB3 pode ajudar

Para mais problemas relacionados com sensor SDK, consulte [os problemas do GitHub](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/issues)

## <a name="collecting-logs"></a>Recolha de troncos

A exploração madeireira para K4A.dll é ativada através de variáveis ambientais. Por padrão, a sessão é enviada para o stdout e apenas são gerados erros e mensagens críticas. Estas definições podem ser alteradas de modo a que a sessão seja para um ficheiro. A verbosidade também pode ser ajustada conforme necessário. Abaixo está um exemplo, para o Windows, de permitir o registo de um ficheiro, chamado k4a.log, e irá capturar mensagens de aviso e de nível superior.

1. `set K4A_ENABLE_LOG_TO_A_FILE=k4a.log`
2. `set K4A_LOG_LEVEL=w`
3. Executar cenário a partir de viagens de comando (por exemplo, espectador de lançamento)
4. Navegue para k4a.log e partilhe o ficheiro.

Para obter mais informações, consulte abaixo o clip a partir do ficheiro do cabeçalho:

```console
/**
* environment variables
* K4A_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4A_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4A_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

O registo do K4ABT.dll de rastreio do corpo é semelhante, exceto que os utilizadores devem modificar um conjunto diferente de nomes variáveis ambientais:

```console
/**
* environment variables
* K4ABT_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4ABT_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4ABT_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

## <a name="device-doesnt-enumerate-in-device-manager"></a>Dispositivo não enumera no gestor de dispositivos

- Verifique o LED de estado por trás do dispositivo, se estiver a piscar o âmbar, tem problemas de conectividade USB e não obtém energia suficiente. O cabo de alimentação deve ser ligado ao adaptador de alimentação fornecido. Enquanto o cabo de alimentação tem um tipo USB A ligado, o dispositivo requer mais energia do que uma porta USB pc pode fornecer. Por isso, não se ligue a uma porta de PC ou a um hub USB.
- Verifique se tem cabo de alimentação ligado e utiliza a porta USB3 para obter dados.
- Tente alterar a porta USB3 para a ligação de dados (recomendação para utilizar a porta USB perto da placa-mãe, por exemplo, na parte de trás do PC).
- Verifique se o cabo, os cabos danificados ou de menor qualidade podem causar uma enumeração pouco fiável (o dispositivo mantém o "piscar" no gestor do dispositivo).
- Se tiver ligado ao portátil e estiver a funcionar com a bateria, pode estar a estrangular a energia da porta.
- Reinicie o PC anfitrião.
- Se o problema persistir, pode haver problema de compatibilidade.
- Se a falha ocorrer durante a atualização do firmware e o dispositivo não tiver recuperado por si só, execute [o reset da fábrica](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk).

## <a name="azure-kinect-viewer-fails-to-open"></a>Azure Kinect Viewer falha na abertura

- Verifique primeiro se o seu dispositivo enumera no Gestor de Dispositivos do Windows.

    ![Câmaras Azure Kinect no gestor de dispositivos Windows](./media/resources/viewer-fails.png)

- Verifique se tem outra aplicação utilizando o dispositivo (por exemplo, aplicação da câmara Windows). Apenas uma aplicação de cada vez pode aceder ao dispositivo.
- Verifique o registo k4aviewer.err para obter mensagens de erro.
- Abra a aplicação da câmara do Windows e verifique se funciona.
- Dispositivo de ciclo de alimentação, espera o LED de streaming para desligar antes de utilizar o dispositivo.
- Reinicie o PC anfitrião.
- Certifique-se de que está a utilizar os controladores gráficos mais recentes no seu PC.
- Se estiver a utilizar a sua própria construção de SDK, tente utilizar a versão oficialmente lançada se isso corrigir o problema.
- Se o problema persistir, [colete registos](troubleshooting.md#collecting-logs) e feedback de ficheiros.

## <a name="cannot-find-microphone"></a>Não consigo encontrar o microfone

- Verifique primeiro se a matriz do microfone está enumerada no Gestor de Dispositivos.
- Se um dispositivo for enumerado e funcionar de outra forma corretamente no Windows, o problema pode ser que, após a atualização do firmware, o Windows tenha atribuído diferentes IDs de contentores à Câmara de Profundidade.
- Pode tentar reiniciá-lo indo ao Device Manager, clicando à direita em "Azure Kinect Microphone Array", e selecionar "Desinstalar o dispositivo". Uma vez concluído, desprender e recolocar o sensor.

    ![Matriz de microfone Azure Kinect](./media/resources/mic-not-found.png)

- Depois disso reinicie o Azure Kinect Viewer e tente novamente.

## <a name="device-firmware-update-issues"></a>Problemas de atualização do Firmware do dispositivo

- Se o número de versão correto não for reportado após a atualização, poderá ter de ligar o dispositivo.
- Se a atualização do firmware for interrompida, pode entrar em mau estado e não enumerar. Retire e recoloque o dispositivo e aguarde 60 segundos para ver se consegue recuperar.
Se não, então realizar um [reset de fábrica](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)

## <a name="image-quality-issues"></a>Questões de qualidade de imagem

- Inicie [o visualizador Azure Kinect](azure-kinect-viewer.md) e verifique o posicionamento do dispositivo para interferência ou se o sensor estiver bloqueado ou se a lente estiver suja.
- Experimente diferentes modos de funcionamento para reduzir se o problema estiver a acontecer em modo específico.
- Para partilhar problemas de qualidade de imagem com a equipa pode:

1) Faça uma pausa no [espectador Azure Kinect](azure-kinect-viewer.md) e tire uma imagem ou
2) Veja a gravação utilizando [o gravador Azure Kinect,](azure-kinect-recorder.md)por exemplo, `k4arecorder.exe -l 5 -r 5 output.mkv`

## <a name="inconsistent-or-unexpected-device-timestamps"></a>Sementetas de tempo de dispositivo inconsistentes ou inesperadas

A chamada ```k4a_device_set_color_control``` pode induzir alterações de tempo temporariamente no dispositivo que podem levar algumas capturas para estabilizar. Evite ligar para a API no ciclo de captura de imagens para evitar a reposição do cálculo de tempo interno a cada nova imagem. Em vez disso, ligue para a API antes de iniciar a câmara ou quando precisar de alterar o valor dentro do ciclo de captura de imagem. Em particular, evite ```k4a_device_set_color_control(K4A_COLOR_CONTROL_AUTO_EXPOSURE_PRIORITY)``` chamar.

## <a name="usb3-host-controller-compatibility"></a>Compatibilidade do controlador de anfitrião USB3

Se o dispositivo não está a enumerar sob o gestor do dispositivo, pode ser porque está ligado a um controlador USB3 não suportado. 

Para o Azure Kinect DK no **Windows, Intel,** **Texas Instruments (TI)** e **Renesas** são os *únicos controladores hospedeiros que são suportados.* O Azure Kinect SDK nas plataformas windows baseia-se num ID de contentor unificado, e deve abranger dispositivos USB 2.0 e 3.0 para que o SDK possa encontrar os dispositivos de profundidade, cor e áudio que estão fisicamente localizados no mesmo dispositivo. No Linux, mais controladores hospedeiros podem ser suportados, uma vez que esta plataforma depende menos do ID do contentor e mais nos números de série do dispositivo. 

O tópico dos controladores de anfitriões USB torna-se ainda mais complicado quando um PC tem mais do que um controlador de anfitrião instalado. Quando os controladores de hospedeiro são misturados, um utilizador pode experimentar problemas em que algumas portas funcionam bem e outras não funcionam de todo. Dependendo da forma como as portas estão ligadas ao caso, você pode ver todas as portas da frente com problemas com o Azure Kinect

**Janelas:** Para saber que controlador anfitrião tem gestor de dispositivos aberto

1. Ver dispositivos de > por tipo 
2. Com a a azure Kinect conectada câmaras->Azure Kinect 4K Camera
3. Ver dispositivos de > por conexão

![Resolução de problemas da porta USB](./media/resources/usb-troubleshooting.png)

Para entender melhor qual a porta USB ligada no seu PC, repita estes passos para cada porta USB, uma vez que liga o Azure Kinect DK a diferentes portas USB no PC.

## <a name="depth-camera-auto-powers-down"></a>Potências automáticas da câmara de profundidade para baixo

O laser utilizado pela câmara de profundidade para calcular dados de profundidade de imagem, tem uma vida útil limitada. Para maximizar a vida útil dos lasers, a câmara de profundidade detetará quando os dados de profundidade não estão a ser consumidos. A câmara de profundidade diminui quando o dispositivo está a ser transmitido durante vários minutos, mas o PC anfitrião não está a ler os dados. Também impacta a sincronização de multi dispositivos onde os dispositivos subordinados se iniciam num estado em que a câmara de profundidade está a transmitir e os quadros de profundidade estão ativamente a ajudar à espera que o dispositivo principal comece a sincronizar capturas. Para evitar este problema em cenários de captura de Multi Device, certifique-se de que o dispositivo principal começa dentro de um minuto após o início do primeiro subordinado. 

## <a name="using-body-tracking-sdk-with-unreal"></a>Usando O SDK de rastreio do corpo com irreal

Para utilizar o Body Tracking SDK com Unreal, certifique-se de que adicionou `<SDK Installation Path>\tools` à variável ambiental `PATH` e copiado e para `dnn_model_2_0.onnx` `cudnn64_7.dll` `Program Files/Epic Games/UE_4.23/Engine/Binaries/Win64` .

## <a name="using-azure-kinect-on-headless-linux-system"></a>Usando Azure Kinect no sistema Linux sem cabeça

O motor de profundidade Azure Kinect em Linux utiliza o OpenGL. O OpenGL requer uma instância de janela que requer que um monitor seja ligado ao sistema. Uma solução alternativa para esta questão é:

1. Faça o login automático para a conta de utilizador que pretende utilizar. Consulte [este](https://vitux.com/how-to-enable-disable-automatic-login-in-ubuntu-18-04-lts/) artigo para obter instruções sobre como ativar o login automático.
2. Desligue o sistema, desligue o monitor e ligue o sistema. O login automático força a criação de uma sessão de x-servidor.
3. Conecte-se via ssh e desloque a variável DISPLAY env `export DISPLAY=:0`
4. Inicie a sua aplicação Azure Kinect.

O utilitário [xtrlock](http://manpages.ubuntu.com/manpages/xenial/man1/xtrlock.1x.html) pode ser utilizado para bloquear imediatamente o ecrã após o início automático. Adicione o seguinte comando à aplicação de arranque ou ao serviço sistema:

`bash -c “xtrlock -b”`

## <a name="missing-c-documentation"></a>Documentação C# desaparecida

A documentação sensor SDK C# está localizada [aqui.](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/namespace_microsoft_1_1_azure_1_1_kinect_1_1_sensor.html)

A documentação Body Tracking SDK C# está localizada [aqui.](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/namespace_microsoft_1_1_azure_1_1_kinect_1_1_body_tracking.html)

## <a name="specifying-onnx-runtime-execution-environment"></a>Especificar o ambiente de execução onnx

O Body Tracking SDK suporta ambientes de execução CPU, CUDA, DirectML (apenas Windows) e TensorRT para inferir o modelo de estimativa de pose. Os `K4ABT_TRACKER_PROCESSING_MODE_GPU` predefinidos da execução cuda na execução linux e directML no Windows. Foram adicionados três modos adicionais para selecionar ambientes específicos de execução: `K4ABT_TRACKER_PROCESSING_MODE_GPU_CUDA` `K4ABT_TRACKER_PROCESSING_MODE_GPU_DIRECTML` , e `K4ABT_TRACKER_PROCESSING_MODE_GPU_TENSORRT` .

> [!NOTE]  
> ONNX Runtime exibe avisos para opcodes que não são acelerados. Estes podem ser ignorados com segurança.

O tempo de execução ONNX inclui variáveis ambientais para controlar o caching do modelo TensorRT. Os valores recomendados são:
- ORT_TENSORRT_ENGINE_CACHE_ENABLE=1 
- ORT_TENSORRT_CACHE_PATH="pathname"

A pasta deve ser criada antes do início do rastreio do corpo.

> [!IMPORTANT]  
> O TensorRT pré-processa o modelo antes da inferência, resultando em tempos de arranque prolongados em comparação com outros ambientes de execução. O caching do motor limita isto à primeira execução no entanto é experimental e é específico para o modelo, versão ONNX Runtime, versão TensorRT e modelo GPU.

O ambiente de execução TensorRT suporta tanto o FP32 (padrão) como o FP16. FP16 negoceia ~2x aumento de desempenho para uma diminuição mínima de precisão. Para especificar o FP16:
- ORT_TENSORRT_FP16_ENABLE=1

## <a name="required-dlls-for-onnx-runtime-execution-environments"></a>DLLs necessários para ambientes de execução onnx

|Modo      | CUDA 11.1            | CUDNN 8.0.5          | TensorRT 7.2.1       |
|----------|----------------------|----------------------|----------------------|
| CPU      | cudart64_110         | cudnn64_8            | -                    |
|          | cufft64_10           |                      |                      |
|          | cublas64_11          |                      |                      |
|          | cublasLt64_11        |                      |                      |
| CUDA     | cudart64_110         | cudnn64_8            | -                    |
|          | cufft64_10           | cudnn_ops_infer64_8  |                      |
|          | cublas64_11          | cudnn_cnn_infer64_8  |                      |
|          | cublasLt64_11        |                      |                      |
| DirectML | cudart64_110         | cudnn64_8            | -                    |
|          | cufft64_10           |                      |                      |
|          | cublas64_11          |                      |                      |
|          | cublasLt64_11        |                      |                      |
| TensorRT | cudart64_110         | cudnn64_8            | nvinfer              |
|          | cufft64_10           | cudnn_ops_infer64_8  | nvinfer_plugin       |
|          | cublas64_11          | cudnn_cnn_infer64_8  | myelin64_1           |
|          | cublasLt64_11        |                      |                      |
|          | nvrtc64_111_0        |                      |                      |
|          | nvrtc-builtins64_111 |                      |                      |

## <a name="next-steps"></a>Passos seguintes

[Mais informações de apoio](support.md)