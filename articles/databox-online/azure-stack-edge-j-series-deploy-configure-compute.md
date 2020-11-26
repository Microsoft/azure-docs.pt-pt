---
title: Tutorial para filtrar, analisar dados com computação no Azure Stack Edge Pro com GPU Microsoft Docs
description: Aprenda a configurar o papel de computação no dispositivo GPU Azure Stack Edge Pro e use-o para transformar dados antes de enviar para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 75428b28095b0e425a1670caffcf960aa6ae58f6
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185509"
---
# <a name="tutorial-transform-data-with-azure-stack-edge-pro"></a>Tutorial: Transformar dados com Azure Stack Edge Pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Este tutorial descreve como configurar um papel computacional no seu dispositivo Azure Stack Edge Pro. Depois de configurar o papel de computação, o Azure Stack Edge Pro pode transformar dados antes de enviá-los para o Azure.

Este procedimento pode demorar cerca de 10 a 15 minutos para ser concluído.


Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Configure computação
> * Adicionar ações
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência dos dados

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar um papel de computação no seu dispositivo Azure Stack Edge Pro, certifique-se de que:

- Ativou o seu dispositivo Azure Stack Edge Pro como descrito no [Ativar o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).


## <a name="configure-compute"></a>Configure computação

Para configurar o cálculo no seu Azure Stack Edge Pro, irá criar um recurso IoT Hub.

1. No portal Azure do seu recurso Azure Stack Edge, vá ao **Overview**. No painel direito, no azulejo **compute,** **selecione Começar**.

    ![Começar com o computo](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-1.png)

2. No azulejo **computacional Configure Edge,** selecione **Configure compute**.

    ![Configure computação](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-2.png)

3. Na lâmina de **computação Configure Edge,** insira o seguinte:

   
    |Campo  |Valor  |
    |---------|---------|
    |IoT Hub     | Escolha entre **Novo** ou **Já.** <br> Por predefinição, é utilizado um escalão Standard (S1) para criar um recurso IoT. Para utilizar um recurso IoT de escalão gratuito, crie um e, em seguida, selecione o recurso existente. <br> Em cada caso, o recurso IoT Hub utiliza o mesmo grupo de subscrição e recursos que é utilizado pelo recurso Azure Stack Edge.     |
    |Name     |Insira um nome para o seu recurso IoT Hub.         |

    ![Começar com o compute 2](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

4. Selecione **Criar**. A criação de recursos IoT Hub demora vários minutos. Após a criação do recurso IoT Hub, as atualizações de azulejos **de computação Configure** para mostrar a configuração do cálculo. 

    ![Começar com o computo 3](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. Para confirmar que a função de computação Edge foi configurada, selecione **Ver Computação** no azulejo **do cálculo Configure.**
    
    ![Começar com o compute 4](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

    > [!NOTE]
    > Se o diálogo **Configure Compute** for fechado antes de o IoT Hub estar associado ao dispositivo Azure Stack Edge Pro, o Hub IoT é criado mas não é mostrado na configuração do cálculo. 
    
    Quando a função de computação Edge é configurada no dispositivo Edge, cria dois dispositivos: um dispositivo IoT e um dispositivo IoT Edge. Ambos os dispositivos podem ser vistos no recurso IoT Hub. Um IoT Edge Runtime também está a ser executado neste dispositivo IoT Edge. Neste momento, apenas a plataforma Linux está disponível para o seu dispositivo IoT Edge.


## <a name="add-shares"></a>Adicionar ações

Para a simples implementação neste tutorial, você precisará de duas ações: uma partilha edge e outra partilha local edge.

1. Adicione uma partilha Edge no dispositivo fazendo os seguintes passos:

    1. No seu recurso Azure Stack Edge, vá ao **Edge compute > Começar**.
    2. No azulejo **(s) add share,s,** selecione **Adicionar**.

        ![Adicionar azulejo de partilha](./media/azure-stack-edge-j-series-deploy-configure-compute/add-share-1.png) 

    3. Na lâmina **de partilha Add,** forneça o nome da partilha e selecione o tipo de partilha.
    4. Para montar a partilha Edge, selecione a caixa de verificação para **utilizar a partilha com o cálculo Edge**.
    5. Selecione a **conta de Armazenamento**, Serviço de **Armazenamento**, um utilizador existente e, em seguida, selecione **Criar**.

        ![Adicione uma parte edge](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-1.png) 

    Se criou uma partilha NFS local, utilize a seguinte opção de comando de sincronização remota (rsync) para copiar ficheiros na partilha:

    `rsync <source file path> < destination file path>`

    Para mais informações sobre o `rsync` comando, aceda à [documentação do Rsync.](https://www.computerhope.com/unix/rsync.htm)

    > [!NOTE]
    > Para montar a partilha NFS para calcular, a rede de cálculo deve ser configurada na mesma sub-rede que o endereço IP virtual da NFS. Para mais detalhes sobre como configurar a rede de computação, vá ao [Enable compute network no seu Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

    A partilha Edge é criada e receberá uma notificação de criação bem sucedida. A lista de ações pode ser atualizada, mas deve esperar que a criação de ações esteja concluída.

2. Adicione uma partilha local Edge no dispositivo Edge repetindo todos os passos no passo anterior e selecionando a caixa de verificação para **configurar como partilha local edge**. Os dados da partilha local permanecem no dispositivo.

    ![Adicione uma parte local edge](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-2.png)

  
3. **Selecione Adicionar ações para** ver a lista atualizada de ações.

    ![Lista atualizada de partilhas](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Adicionar um módulo

Pode adicionar um módulo personalizado ou pré-construído. Não existem módulos personalizados neste dispositivo Edge. Para aprender a criar um módulo personalizado, vá ao [Develop a C# module para o seu dispositivo Azure Stack Edge Pro](azure-stack-edge-j-series-create-iot-edge-module.md).

Nesta secção, adicione um módulo personalizado ao dispositivo IoT Edge que criou no [Develop a C# module para o seu Azure Stack Edge Pro](azure-stack-edge-j-series-create-iot-edge-module.md). Este módulo personalizado retira ficheiros de uma partilha local edge no dispositivo Edge e move-os para uma partilha edge (cloud) no dispositivo. A partilha em nuvem empurra então os ficheiros para a conta de armazenamento Azure que está associada à partilha da nuvem.

1. Vá ao **Edge compute > Começar**. No azulejo dos **módulos Add,** selecione o tipo de cenário como **simples**. Selecione **Adicionar**.
2. No **Configure e adicione** a lâmina do módulo, insira os seguintes valores:

    
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

    ![Adicionar e configurar módulo](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-1.png)

3. Selecione **Adicionar**. O módulo é adicionado. Vá à página **geral.** Os **módulos** de azulejos atualizações para indicar que o módulo está implantado. 

    ![Módulo implantado](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Verificar a transformação e a transferência dos dados

O passo final é garantir que o módulo está conectado e funcionando como esperado. O estado de tempo de funcionamento do módulo deve estar a funcionar para o seu dispositivo IoT Edge no recurso IoT Hub.

Para verificar se o módulo está em funcionamento, faça o seguinte:

1. Selecione o azulejo do **módulo Add.** Isto leva-te à lâmina dos **Módulos.** Na lista de módulos, identifique o módulo que implementou. O estado de funcionamento do módulo que adicionou deve estar *a funcionar*.

    ![Ver módulo implantado](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-3.png)
 
1. No File Explorer, ligue-se às ações edge local e Edge que criou anteriormente.

    ![Verificar a transformação dos dados](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-2.png) 
 
1. Adicione os dados à partilha local.

    ![Verificar a transformação dos dados](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-3.png) 
 
   Os dados são movidos para a partilha na cloud.

    ![Verificar a transformação dos dados](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-4.png)  

   Os dados são então empurrados da parte da nuvem para a conta de armazenamento. Para visualizar os dados, pode utilizar o Storage Explorer.

    <!--![Verify data transform](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-5.png)-->
 
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
> [Use uI web local para administrar um Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
