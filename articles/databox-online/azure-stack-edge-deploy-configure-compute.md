---
title: Tutorial para filtrar, analisar dados com computação no Azure Stack Edge Microsoft Docs
description: Aprenda a configurar o papel de computação no Azure Stack Edge e use-o para transformar dados antes de enviar para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: ab9c93dc029498c70a828f38f297c1e02dcb29ee
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2020
ms.locfileid: "83774109"
---
# <a name="tutorial-transform-data-with-azure-stack-edge"></a>Tutorial: Transformar dados com Azure Stack Edge

Este tutorial descreve como configurar um papel computacional no seu dispositivo Azure Stack Edge. Depois de configurar o papel de computação, o Azure Stack Edge pode transformar dados antes de enviá-los para o Azure.

Este procedimento pode demorar cerca de 10 a 15 minutos para ser concluído.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure computação
> * Adicionar ações
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência dos dados

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar um papel de computação no seu dispositivo Azure Stack Edge, certifique-se de que:

- Ativou o seu dispositivo Azure Stack Edge como descrito no [Connect, configurar e ativar o Azure Stack Edge](azure-stack-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Configure computação

Para configurar o cálculo no seu Azure Stack Edge, irá criar um recurso IoT Hub.

1. No portal Azure do seu recurso Azure Stack Edge, vá ao Overview. No painel direito, no azulejo **compute,** **selecione Começar**.

    ![Começar com o computo](./media/azure-stack-edge-deploy-configure-compute/configure-compute-1.png)

2. No azulejo **computacional Configure Edge,** selecione **Configure compute**.
3. Na lâmina de **computação Configure Edge,** insira o seguinte:

   
    |Campo  |Valor  |
    |---------|---------|
    |IoT Hub     | Escolha entre **Novo** ou **Já.** <br> Por padrão, um nível Standard (S1) é usado para criar um recurso IoT. Para utilizar um recurso IoT de nível livre, crie um e, em seguida, selecione o recurso existente. <br> Em cada caso, o recurso IoT Hub utiliza o mesmo grupo de subscrição e recursos que é utilizado pelo recurso Azure Stack Edge.     |
    |Nome     |Insira um nome para o seu recurso IoT Hub.         |

    ![Começar com o computo](./media/azure-stack-edge-deploy-configure-compute/configure-compute-2.png)

4. Selecione **Criar**. A criação de recursos IoT Hub demora alguns minutos. Após a criação do recurso IoT Hub, as atualizações de azulejos **de computação Configure** para mostrar a configuração do cálculo. Para confirmar que a função de computação Edge foi configurada, selecione **Ver Computação** no azulejo **do cálculo Configure.**
    
    ![Começar com o computo](./media/azure-stack-edge-deploy-configure-compute/configure-compute-3.png)

    > [!NOTE]
    > Se o diálogo **Configure Compute** for fechado antes de o IoT Hub estar associado ao dispositivo Azure Stack Edge, o IoT Hub é criado mas não é mostrado na configuração do cálculo. 
    
    Quando a função de computação Edge é configurada no dispositivo Edge, cria dois dispositivos: um dispositivo IoT e um dispositivo IoT Edge. Ambos os dispositivos podem ser vistos no recurso IoT Hub. Um IoT Edge Runtime também está a ser executado neste dispositivo IoT Edge. Neste momento, apenas a plataforma Linux está disponível para o seu dispositivo IoT Edge.


## <a name="add-shares"></a>Adicionar ações

Para a simples implementação neste tutorial, você precisará de duas ações: uma partilha edge e outra partilha local edge.

1. Adicione uma partilha Edge no dispositivo fazendo os seguintes passos:

    1. No seu recurso Azure Stack Edge, vá ao **Edge compute > Começar**.
    2. No azulejo **(s) add share,s,** selecione **Adicionar**.
    3. Na lâmina **de partilha Add,** forneça o nome da partilha e selecione o tipo de partilha.
    4. Para montar a partilha Edge, selecione a caixa de verificação para **utilizar a partilha com o cálculo Edge**.
    5. Selecione a **conta de Armazenamento**, Serviço de **Armazenamento**, um utilizador existente e, em seguida, selecione **Criar**.

        ![Adicione uma parte edge](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-1.png) 

    Se criou uma partilha NFS local, utilize a seguinte opção de comando de sincronização remota (rsync) para copiar ficheiros na partilha:

    `rsync <source file path> < destination file path>`

    Para mais informações sobre o comando rsync, aceda à [documentação Rsync](https://www.computerhope.com/unix/rsync.htm).

    A partilha Edge é criada e receberá uma notificação de criação bem sucedida. A lista de ações pode ser atualizada, mas deve esperar que a criação de ações esteja concluída.

2. Adicione uma partilha local Edge no dispositivo Edge repetindo todos os passos no passo anterior e selecionando a caixa de verificação para **configurar como partilha local edge**. Os dados da partilha local permanecem no dispositivo.

    ![Adicione uma parte local edge](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. **Selecione Adicionar ações para** ver a lista atualizada de ações.

    ![Lista atualizada de partilhas](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Adicionar um módulo

Pode adicionar um módulo personalizado ou pré-construído. Não existem módulos personalizados neste dispositivo Edge. Para aprender a criar um módulo personalizado, vá ao [Develop a C# module para o seu dispositivo Azure Stack Edge](azure-stack-edge-create-iot-edge-module.md).

Nesta secção, adicione um módulo personalizado ao dispositivo IoT Edge que criou no [Develop a C# module para o seu Azure Stack Edge](azure-stack-edge-create-iot-edge-module.md). Este módulo personalizado retira ficheiros de uma partilha local edge no dispositivo Edge e move-os para uma partilha edge (cloud) no dispositivo. A partilha em nuvem empurra então os ficheiros para a conta de armazenamento Azure que está associada à partilha da nuvem.

1. Vá ao **Edge compute > Começar**. No azulejo dos **módulos Add,** selecione o tipo de cenário como **simples**. Selecione **Adicionar**.
2. No **Configure e adicione** a lâmina do módulo, insira os seguintes valores:

    
    |Campo  |Valor  |
    |---------|---------|
    |Nome     | Um nome único para o módulo. Este módulo é um recipiente de estivador que pode implantar no dispositivo IoT Edge que está associado ao seu Azure Stack Edge.        |
    |URI de imagem     | A imagem URI para a imagem do recipiente correspondente para o módulo.        |
    |Credenciais necessárias     | Se verificado, o nome de utilizador e a palavra-passe são utilizados para recuperar módulos com um URL correspondente.        |
    |Partilha de entrada     | Selecione uma partilha de entrada. A participação local edge é a parte de entrada neste caso. O módulo utilizado aqui move ficheiros da partilha local edge para uma partilha edge onde são enviados para a nuvem.        |
    |Parte da produção     | Selecione uma parte de saída. A parte Edge é a parte de produção neste caso.        |
    |Tipo de acionador     | Selecione a partir **de Arquivo** ou **Agendamento**. Um ficheiro dispara sempre que ocorre um evento de ficheiro, como um ficheiro, para a parte de entrada. Um gatilho programado dispara com base num horário definido por si.         |
    |Nome do gatilho     | Um nome único para o seu gatilho.         |
    |Variáveis de ambiente| Informação opcional que ajudará a definir o ambiente em que o seu módulo irá funcionar.   |

    ![Adicionar e configurar módulo](./media/azure-stack-edge-deploy-configure-compute/add-module-1.png)

3. Selecione **Adicionar**. O módulo é adicionado. As atualizações do azulejo do **módulo Add** para indicar que o módulo está implantado. 

    ![Módulo implantado](./media/azure-stack-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Verificar a transformação e a transferência dos dados

O passo final é garantir que o módulo está conectado e funcionando como esperado. O estado de tempo de funcionamento do módulo deve estar a funcionar para o seu dispositivo IoT Edge no recurso IoT Hub.

Para verificar se o módulo está em funcionamento, faça o seguinte:

1. Selecione o azulejo do **módulo Add.** Isto leva-te à lâmina dos **Módulos.** Na lista de módulos, identifique o módulo que implementou. O estado de funcionamento do módulo que adicionou deve estar *a funcionar*.

    ![Verificar a transformação dos dados](./media/azure-stack-edge-deploy-configure-compute/verify-data-1.png)
 
1. No File Explorer, ligue-se às ações edge local e Edge que criou anteriormente.

    ![Verificar a transformação dos dados](./media/azure-stack-edge-deploy-configure-compute/verify-data-2.png) 
 
1. Adicione os dados à partilha local.

    ![Verificar a transformação dos dados](./media/azure-stack-edge-deploy-configure-compute/verify-data-3.png) 
 
    Os dados são movidos para a partilha na cloud.

    ![Verificar a transformação dos dados](./media/azure-stack-edge-deploy-configure-compute/verify-data-4.png)  

    Os dados são então empurrados da parte da nuvem para a conta de armazenamento. Para ver os dados, vá ao Explorador de Armazenamento.

    ![Verificar a transformação dos dados](./media/azure-stack-edge-deploy-configure-compute/verify-data-5.png) 
 
Concluiu o processo de validação.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configure computação
> * Adicionar ações
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência dos dados

Para aprender a administrar o seu dispositivo Azure Stack Edge, consulte:

> [!div class="nextstepaction"]
> [Use uI web local para administrar um Azure Stack Edge](azure-stack-edge-manage-access-power-connectivity-mode.md)
