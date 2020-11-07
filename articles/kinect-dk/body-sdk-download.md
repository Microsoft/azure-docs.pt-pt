---
title: Download SDK de rastreio de corpo de kinect Azure Kinect
description: Entenda como descarregar cada versão do Azure Kinect Sensor SDK no Windows ou Linux.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: azul, kinect, sdk, atualização de download, mais recente, disponível, instalar, corpo, rastreio
ms.openlocfilehash: 0ac0598d893617f341b9e1fd4d45c0c3e3f3c619
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359600"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>Baixar Azure Kinect Body Tracking SDK

Este documento fornece links para instalar cada versão do Azure Kinect Body Tracking SDK.

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Azure Kinect Body Tracking SDK conteúdos

- Cabeçalhos e bibliotecas para construir uma aplicação de rastreio de corpo usando o Azure Kinect DK.
- DLLs redistribuíveis necessários por aplicações de rastreio corporal utilizando o Azure Kinect DK.
- Aplicações de rastreio de corpo de amostra.

## <a name="windows-download-links"></a>Links de descarregamento do Windows

Versão       | Download
--------------|----------
1.0.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100942) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100848) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)
0.9.5 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100636) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.5)
0.9.4 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100415) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.4)
0.9.3 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100307) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.3)
0.9.2 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100128) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.2)
0.9.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100063) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.1)
0.9.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=58402) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.0)

## <a name="linux-installation-instructions"></a>Instruções de instalação do Linux

Atualmente, a única distribuição suportada é ubuntu 18.04. Para solicitar apoio a outras distribuições, consulte [esta página.](https://aka.ms/azurekinectfeedback)

Em primeiro lugar, terá de configurar o [Repositório de Pacotes da Microsoft,](https://packages.microsoft.com/)seguindo as instruções [aqui](/windows-server/administration/linux-package-repository-for-microsoft-software).

O `libk4abt<major>.<minor>-dev` pacote contém os cabeçalhos e os ficheiros CMake para construir contra `libk4abt` .
O `libk4abt<major>.<minor>` pacote contém os objetos partilhados necessários para executar executáveis que `libk4abt` dependem, bem como o visualizador exemplo.

Os tutoriais básicos requerem o `libk4abt<major>.<minor>-dev` pacote. Para instalá-lo, executar

`sudo apt install libk4abt1.0-dev`

Se o comando for bem sucedido, o SDK está pronto a ser utilizado.

> [!NOTE]
> Ao instalar o SDK, lembre-se do caminho a que se instala. Por exemplo, "C:\Program Files\Azure Kinect Body Tracking SDK 1.0.0". Encontrará as amostras referenciadas em artigos neste caminho.
> As amostras de rastreio do corpo estão localizadas na pasta [de amostras de rastreio](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) do corpo no repositório Azure-Kinect-Samples. Você vai encontrar as amostras referenciadas em artigos aqui.

## <a name="change-log"></a>Change log

### <a name="v101"></a>v1.0.1
* [Bug Fix] Corrige o problema que o SDK falha se o carregamento onnxruntime.dll do caminho no Windows construir 19025 ou mais tarde: [Link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v1.0.0
* [Recurso] Adicione o invólucro C# ao instalador MSI.
* [Bug Fix] Corrigir problema que a rotação da cabeça não pode ser detetado corretamente: [Ligação](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Bug Fix] Corrige o problema de que o uso do CPU sobe até 100% na máquina Linux: [Link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* [Amostras] Adicione duas amostras à amostra de repo. A amostra 1 demonstra como transformar os resultados do rastreio do corpo do espaço de profundidade para o espaço de cores [Link;](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample) amostra 2 demonstra como detetar [ligação](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample) de avião do piso

### <a name="v095"></a>v0.9.5
* [Recurso] Suporte C. O invólucro C# é embalado no pacote nuget.
* [Recurso] Suporte multi-tracker. É permitido criar vários localizadores. Agora o utilizador pode criar vários rastreadores para rastrear corpos de diferentes dispositivos Azure Kinect.
* [Recurso] Suporte de processamento multi-thread para o modo CPU. Ao correr no modo CPU, todos os núcleos serão utilizados para maximizar a velocidade.
* [Recurso] Adicione `gpu_device_id` à `k4abt_tracker_configuration_t` estrutura. Permitir que os utilizadores especifiquem o dispositivo GPU que não seja o padrão para executar o algoritmo de rastreio do corpo.
* [Alteração de Correção de Bug/Rutura] Fixe tipografia num nome comum. Mude o nome da articulação de `K4ABT_JOINT_SPINE_NAVAL` `K4ABT_JOINT_SPINE_NAVEL` .

### <a name="v094"></a>v0.9.4
* [Recurso] Adicione o suporte das articulações da mão. O SDK fornecerá informações para três juntas adicionais para cada mão: MÃO, HANDTIP, POLEGAR.
* [Recurso] Adicione o nível de confiança da previsão para cada articulação detetada.
* [Recurso] Adicione suporte ao modo CPU. Ao alterar o `cpu_only_mode` valor em , agora o `k4abt_tracker_configuration_t` SDK pode ser executado no modo CPU, o que não requer que o utilizador tenha uma poderosa placa gráfica.

### <a name="v093"></a>v0.9.3
* [Recurso] Publique um novo modelo DNN dnn_model_2_0.onnx, que melhora em grande parte a robustez do rastreio corporal.
* [Recurso] Desative o alisamento temporal por defeito. As articulações rastreadas serão mais responsivas.
* [Recurso] Melhore a precisão do mapa do índice do corpo.
* [Bug Fix] Corrija o bug de que a definição de orientação do sensor não é eficaz.
* [Bug Fix] Mude o tipo de body_index_map de K4A_IMAGE_FORMAT_CUSTOM para K4A_IMAGE_FORMAT_CUSTOM8.
* [Edição conhecida] Dois corpos próximos podem fundir-se ao segmento de instância única.

### <a name="v092"></a>v0.9.2
* [Mudança de Rutura] A atualização depende do mais recente Sensor Kinect SDK 1.2.0 do Azure Kinect Sensor.
* [API Change] `k4abt_tracker_create` função começará a tomar uma `k4abt_tracker_configuration_t` entrada. 
* [API Change] Altere `k4abt_frame_get_timestamp_usec` a API `k4abt_frame_get_device_timestamp_usec` para ser mais específica e consistente com o Sensor SDK 1.2.0.
* [Recurso] Permita aos utilizadores especificar a orientação de montagem do sensor ao criar o rastreador para obter resultados mais precisos de rastreio do corpo ao montar em diferentes ângulos.
* [Recurso] Forneça uma nova API `k4abt_tracker_set_temporal_smoothing` para alterar a quantidade de alisamento temporal que o utilizador quer aplicar.
* [Recurso] Adicione o invólucro C++ k4abt.hpp.
* [Recurso] Adicione o cabeçalho de definição de versão k4abtversion.h.
* [Bug Fix] Correção de erros que causaram uma utilização extremamente elevada do CPU.
* [Bug Fix] Corrija o bicho de colisão do madeireiro.

### <a name="v091"></a>v0.9.1
* [Bug Fix] Corrija a fuga de memória ao destruir o localizador
* [Bug Fix] Melhores mensagens de erro para dependências em falta
* [Bug Fix] Falhar sem colidir ao criar uma segunda instância de rastreador
* [Bug Fix] As variáveis ambientais do logger agora funcionam corretamente
* Apoio Técnico para Linux

### <a name="v090"></a>v0.9.0

* [Mudança de Rutura] Desvalorizou a dependência da SDK para CUDA 10.0 (da CUDA 10.1). O tempo de funcionação onnx oficialmente só suporta até CUDA 10.0.
* [Mudança de Rutura] Comutado para tempo de funcionamento ONNX em vez de tempo de execução de tensorflow. Reduz o tempo de lançamento do primeiro quadro e o uso da memória. Também reduz o tamanho binário SDK.
* [API Change] Renomeado `k4abt_tracker_queue_capture()` para `k4abt_tracker_enqueue_capture()`
* [API Change] Invadiu `k4abt_frame_get_body()` duas funções distintas: `k4abt_frame_get_body_skeleton()` e `k4abt_frame_get_body_id()` . Agora pode consultar a identificação do corpo sem copiar sempre toda a estrutura do esqueleto.
* [API Change] Função  `k4abt_frame_get_timestamp_usec()` adicional para simplificar os passos para os utilizadores consultarem o calendário da moldura do corpo.
* Melhorou ainda mais a precisão do algoritmo de rastreio do corpo e a fiabilidade do rastreio

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do Azure Kinect DK](about-azure-kinect-dk.md)

- [Configurar o Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Configurar o rastreio do corpo de Azure Kinect](body-sdk-setup.md)