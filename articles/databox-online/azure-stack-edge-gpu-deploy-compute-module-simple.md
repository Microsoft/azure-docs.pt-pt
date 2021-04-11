---
title: Utilize um módulo IoT Edge para implementar carga de trabalho computacional no Azure Stack Edge Pro com GPU | Microsoft Docs
description: Aprenda a executar uma carga de trabalho computacional utilizando um módulo IoT Edge pré-criado no seu dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: d10e27c80a9253de7482644debd19debce8f4e50
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055302"
---
# <a name="tutorial-run-a-compute-workload-with-iot-edge-module-on-azure-stack-edge-pro-gpu"></a>Tutorial: Executar uma carga de trabalho computacional com módulo IoT Edge no GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Este tutorial descreve como executar uma carga de trabalho computacional usando um módulo IoT Edge no seu dispositivo GPU Azure Stack Edge Pro. Depois de configurar o cálculo, o dispositivo transformará os dados antes de enviá-los para a Azure.

Este procedimento pode demorar cerca de 10 a 15 minutos para ser concluído.


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure computação
> * Adicionar ações
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência dos dados

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar um papel de computação no seu dispositivo GPU Azure Stack Edge Pro, certifique-se de que:

- Ativou o seu dispositivo Azure Stack Edge Pro como descrito no [Ativar o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).
- Tem um módulo IoT Edge que pode executar nos seus dados. Neste tutorial, usamos um `filemove2` módulo que transfere dados da edge local share no seu dispositivo para a partilha edge de onde os dados vão para a conta de Armazenamento Azure.


## <a name="configure-compute"></a>Configure computação

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]


## <a name="add-shares"></a>Adicionar ações

Para a simples implementação neste tutorial, você precisará de duas ações: uma partilha edge e outra partilha local edge.

1. Para adicionar uma partilha Edge no dispositivo, faça os seguintes passos:

    1. No seu recurso Azure Stack Edge, vá ao **gateway de armazenamento cloud > Shares**.
    2. A partir da barra de comando, selecione **+ Adicionar a partilha**.
    3. Na lâmina **de partilha Add,** forneça o nome da partilha e selecione o tipo de partilha.
    4. Para montar a partilha Edge, selecione a caixa de verificação para **utilizar a partilha com o cálculo Edge**.
    5. Selecione a **conta de Armazenamento**, Serviço de **Armazenamento**, um utilizador existente e, em seguida, selecione **Criar**.

        ![Adicione uma parte edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-1.png) 


    > [!NOTE]
    > Para montar a partilha NFS para calcular, a rede de cálculo deve ser configurada na mesma sub-rede que o endereço IP virtual da NFS. Para mais detalhes sobre como configurar a rede de computação, vá ao [Enable compute network no seu Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

    A partilha Edge é criada e receberá uma notificação de criação bem sucedida. A lista de ações pode ser atualizada, mas deve esperar que a criação de ações esteja concluída.

2. Para adicionar uma parte local edge no dispositivo, repita todos os passos no passo anterior e selecione a caixa de verificação para **configurar como partilha local edge**. Os dados da partilha local permanecem no dispositivo.

    ![Adicione uma parte local edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-2.png)

    Se criou uma partilha NFS local, utilize a seguinte opção de comando de sincronização remota (rsync) para copiar ficheiros na partilha:

    `rsync <source file path> < destination file path>`

    Para mais informações sobre o `rsync` comando, aceda à [ `Rsync` documentação.](https://www.computerhope.com/unix/rsync.htm)
 
3. Vá ao **gateway de armazenamento cloud > Shares** para ver a lista atualizada de ações.

    ![Lista atualizada de partilhas](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Adicionar um módulo

Pode adicionar um módulo personalizado ou pré-construído. O dispositivo não vem com módulos pré-construídos ou personalizados. Para aprender a criar um módulo personalizado, vá ao [Develop a C# module para o seu dispositivo Azure Stack Edge Pro](./azure-stack-edge-gpu-create-iot-edge-module.md).

Nesta secção, adicione um módulo personalizado ao dispositivo IoT Edge que criou no [Develop a C# module para o seu Azure Stack Edge Pro](./azure-stack-edge-gpu-create-iot-edge-module.md). Este módulo personalizado retira ficheiros de uma partilha local edge no dispositivo Edge e move-os para uma partilha edge (cloud) no dispositivo. A partilha em nuvem empurra então os ficheiros para a conta de armazenamento Azure que está associada à partilha da nuvem.

Para adicionar um módulo, faça os seguintes passos:

1. Vá a **IoT Edge > Módulos**. A partir da barra de comando, selecione **+ Adicionar módulo**. 

2. Na lâmina do **módulo Adicionar,** introduza os seguintes valores:

    
    |Campo  |Valor  |
    |---------|---------|
    |Nome     | Um nome único para o módulo. Este módulo é um recipiente de estivador que pode implantar no dispositivo IoT Edge que está associado ao seu Azure Stack Edge Pro.        |
    |URI de imagem     | A imagem URI para a imagem do recipiente correspondente para o módulo.        |
    |Credenciais necessárias     | Se verificado, o nome de utilizador e a palavra-passe são utilizados para recuperar módulos com um URL correspondente.        |
    |Partilha de entrada     | Selecione uma partilha de entrada. A participação local edge é a parte de entrada neste caso. O módulo utilizado aqui move ficheiros da partilha local edge para uma partilha edge onde são enviados para a nuvem.        |
    |Parte da produção     | Selecione uma parte de saída. A parte Edge é a parte de produção neste caso.        |
    |Tipo de acionador     | Selecione a partir **de Arquivo** ou **Agendamento**. Um acionador de ficheiro é acionado sempre que ocorre um evento de ficheiro, como quando um ficheiro é gravado na partilha de entrada. Um acionador agendado é acionado com base num agendamento definido por si.         |
    |Nome do gatilho     | Um nome único para o seu gatilho.         |
    |Variáveis de ambiente| Informação opcional que ajudará a definir o ambiente em que o seu módulo irá funcionar.   |

    ![Adicionar e configurar módulo](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-1.png)

3. Selecione **Adicionar**. O módulo é adicionado. O **IoT Edge >** atualizações da página dos Módulos para indicar que o módulo está implantado. O estado de funcionamento do módulo que adicionou deve estar *a funcionar*.

    ![Módulo implantado](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Verificar a transformação e a transferência dos dados

O passo final é garantir que o módulo está a executar e processar dados como esperado. O estado de tempo de funcionamento do módulo deve estar a funcionar para o seu dispositivo IoT Edge no recurso IoT Hub.

Para verificar se o módulo está a executar e a processar dados como esperado, faça o seguinte:


1. No File Explorer, ligue-se às ações edge local e Edge que criou anteriormente. Veja os degraus 

    ![Ligue-se às ações de nuvem edge local e Edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-1.png) 
 
1. Adicione os dados à partilha local.

    ![Ficheiro copiado para a edge local share](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-2.png) 
 
   Os dados são movidos para a partilha na cloud.

    ![O ficheiro mudou-se para a partilha da nuvem Edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-3.png)  

   Os dados são então empurrados da parte da nuvem para a conta de armazenamento. Para visualizar os dados, pode utilizar o Storage Explorer ou o Azure Storage no portal.

    ![Verificar dados na conta de armazenamento](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-4.png)
 
Concluiu o processo de validação.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configure computação
> * Adicionar ações
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência dos dados

Para aprender a administrar o seu dispositivo Azure Stack Edge Pro, consulte:

> [!div class="nextstepaction"]
> [Use uI web local para administrar um Azure Stack Edge Pro](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md)