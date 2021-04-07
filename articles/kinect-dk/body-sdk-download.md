---
title: Download SDK de rastreio de corpo de kinect Azure Kinect
description: Entenda como descarregar cada versão do Azure Kinect Sensor SDK no Windows ou Linux.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/18/2021
ms.topic: conceptual
keywords: azul, kinect, sdk, atualização de download, mais recente, disponível, instalar, corpo, rastreio
ms.openlocfilehash: 60018df56433785f3cb723dd54cc96a8e5289b67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654497"
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
1.1.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=102901)
1.0.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100942) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100848) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)

## <a name="linux-installation-instructions"></a>Instruções de instalação do Linux

Atualmente, a única distribuição suportada é ubuntu 18.04 e 20.04. Para solicitar apoio a outras distribuições, consulte [esta página.](https://aka.ms/azurekinectfeedback)

Em primeiro lugar, terá de configurar o [Repositório de Pacotes da Microsoft,](https://packages.microsoft.com/)seguindo as instruções [aqui](/windows-server/administration/linux-package-repository-for-microsoft-software).

O `libk4abt<major>.<minor>-dev` pacote contém os cabeçalhos e os ficheiros CMake para construir contra `libk4abt` .
O `libk4abt<major>.<minor>` pacote contém os objetos partilhados necessários para executar executáveis que `libk4abt` dependem, bem como o visualizador exemplo.

Os tutoriais básicos requerem o `libk4abt<major>.<minor>-dev` pacote. Para instalá-lo, executar

`sudo apt install libk4abt<major>.<minor>-dev`

Se o comando for bem sucedido, o SDK está pronto a ser utilizado.

> [!NOTE]
> Ao instalar o SDK, lembre-se do caminho a que se instala. Por exemplo, "C:\Program Files\Azure Kinect Body Tracking SDK 1.0.0". Encontrará as amostras referenciadas em artigos neste caminho.
> As amostras de rastreio do corpo estão localizadas na pasta [de amostras de rastreio](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) do corpo no repositório Azure-Kinect-Samples. Você vai encontrar as amostras referenciadas em artigos aqui.

## <a name="change-log"></a>Change log

### <a name="v110"></a>v1.1.0
* [Recurso] Adicione suporte para DirectML (apenas Windows) e execução tensorRT do modelo de estimativa de pose. Consulte as FAQ em novos ambientes de execução.
* [Recurso] Adicione `model_path` à `k4abt_tracker_configuration_t` estrutura. Permite que os utilizadores especifiquem o nome de pathname para o modelo de estimativa de pose. Incumprimentos ao `dnn_model_2_0_op11.onnx` modelo de estimativa de pose padrão localizado no diretório atual.
* [Recurso] Incluir `dnn_model_2_0_lite_op11.onnx` o modelo de estimativa de pose lite. Este modelo negoceia ~2x aumento de desempenho para ~5% de precisão.
* [Recurso] As amostras verificadas compilam-se com o Visual Studio 2019 e atualizam amostras para utilizar esta versão.
* [Mudança de Rutura] Atualize para ONNX Runtime 1.6 com suporte para ambientes de execução CPU, CUDA 11.1, DirectML (apenas Windows) e TensorRT 7.2.1. Requer atualização do controlador NVIDIA para R455 ou melhor.
* [Mudança de Rutura] Sem instalação nuGet.
* [Bug Fix] Adicione suporte para NVIDIA RTX série GPUs [Link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481)
* [Bug Fix] Adicionar suporte para GPUs integrados AMD e Intel (apenas Windows) [Link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481)
* [Bug Fix] Atualização para [ligação](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1125) CUDA 11.1
* [Bug Fix] Atualização para sensor SDK 1.4.1 [Link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1248)

### <a name="v101"></a>v1.0.1
* [Bug Fix] Corrige o problema que o SDK falha se o carregamento onnxruntime.dll do caminho no Windows construir 19025 ou mais tarde: [Link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v1.0.0
* [Recurso] Adicione o invólucro C# ao instalador MSI.
* [Bug Fix] Corrigir problema que a rotação da cabeça não pode ser detetado corretamente: [Ligação](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Bug Fix] Corrige o problema de que o uso do CPU sobe até 100% na máquina Linux: [Link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* [Amostras] Adicione duas amostras à amostra de repo. A amostra 1 demonstra como transformar os resultados do rastreio do corpo do espaço de profundidade para o espaço de cores [Link;](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample) amostra 2 demonstra como detetar [ligação](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample) de avião do piso

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do Azure Kinect DK](about-azure-kinect-dk.md)

- [Configurar o Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Configurar o rastreio do corpo de Azure Kinect](body-sdk-setup.md)