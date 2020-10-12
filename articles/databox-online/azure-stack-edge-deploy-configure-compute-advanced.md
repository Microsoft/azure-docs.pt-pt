---
title: Tutorial para filtrar, analisar dados para implementação avançada com computação no Azure Stack Edge Pro Microsoft Docs
description: Aprenda a configurar o papel de computação no Azure Stack Edge Pro e use-o para transformar dados para fluxo de implementação avançado antes de enviar para a Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: f62eec29aebdcc98569134e0c3b75457467bc014
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903681"
---
# <a name="tutorial-transform-data-with-azure-stack-edge-pro-for-advanced-deployment-flow"></a>Tutorial: Transforme dados com Azure Stack Edge Pro para fluxo avançado de implementação

Este tutorial descreve como configurar um papel de computação para um fluxo avançado de implementação no seu dispositivo Azure Stack Edge Pro. Depois de configurar o papel de computação, o Azure Stack Edge Pro pode transformar dados antes de enviá-los para o Azure.

O cálculo pode ser configurado para um fluxo de implementação simples ou avançado no seu dispositivo.

| Critérios | Implantação simples                                | Implantação avançada                   |
|------------------|--------------------------------------------------|---------------------------------------|
| Destina-se a     | Administradores de TI                                | Programadores                            |
| Tipo             | Utilize o serviço Azure Stack Edge para implementar módulos      | Utilize o serviço IoT Hub para implantar módulos |
| Módulos implantados | Único                                           | Módulos acorrentados ou múltiplos           |


Este procedimento pode demorar cerca de 20 a 30 minutos para ser concluído.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure computação
> * Adicionar ações
> * Adicionar um acionador
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência dos dados

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar um papel de computação no seu dispositivo Azure Stack Edge Pro, certifique-se de que:

- Ativou o seu dispositivo Azure Stack Edge Pro como descrito no [Connect, configurar e ativar o Azure Stack Edge Pro](azure-stack-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Configure computação

Para configurar o cálculo no seu Azure Stack Edge Pro, irá criar um recurso IoT Hub.

1. No portal Azure do seu recurso Azure Stack Edge, vá ao **Overview**. No painel direito, no azulejo **compute,** **selecione Começar**.

    ![Começar com o computo](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. No azulejo **computacional Configure Edge,** selecione **Configure compute**.

    ![Começar com o computo](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. Na lâmina de **computação Configure Edge,** insira o seguinte:

   
    |Campo  |Valor  |
    |---------|---------|
    |IoT Hub     | Escolha entre **Novo** ou **Já.** <br> Por predefinição, é utilizado um escalão Standard (S1) para criar um recurso IoT. Para utilizar um recurso IoT de escalão gratuito, crie um e, em seguida, selecione o recurso existente. <br> Em cada caso, o recurso IoT Hub utiliza o mesmo grupo de subscrição e recursos que é utilizado pelo recurso Azure Stack Edge.     |
    |Nome     |Insira um nome para o seu recurso IoT Hub.         |

    ![Começar com o computo](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Selecione **Criar**. A criação de recursos IoT Hub demora alguns minutos. Após a criação do recurso IoT Hub, as atualizações de azulejos **do Configure Edge computamam** para mostrar a configuração do cálculo. Para confirmar que a função de computação Edge foi configurada, selecione **Ver configurar** no azulejo **computacional Configure.**
    
    ![Começar com o computo](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-4.png)

    Quando a função de computação Edge é configurada no dispositivo Edge, cria dois dispositivos: um dispositivo IoT e um dispositivo IoT Edge. Ambos os dispositivos podem ser vistos no recurso IoT Hub. Um IoT Edge Runtime também está a ser executado neste dispositivo IoT Edge.

    Neste momento, apenas a plataforma Linux está disponível para o seu dispositivo IoT Edge.


## <a name="add-shares"></a>Adicionar ações

Para a implementação avançada neste tutorial, você precisará de duas ações: uma partilha edge e outra partilha local edge.

1. Adicione uma partilha Edge no dispositivo fazendo os seguintes passos:

    1. No seu recurso Azure Stack Edge, vá ao **Edge compute > Começar**.
    2. No azulejo **(s) add share,s,** selecione **Adicionar**.
    3. Na lâmina **de partilha Add,** forneça o nome da partilha e selecione o tipo de partilha.
    4. Para montar a partilha Edge, selecione a caixa de verificação para **utilizar a partilha com o cálculo Edge**.
    5. Selecione a **conta de Armazenamento**, Serviço de **Armazenamento**, um utilizador existente e, em seguida, selecione **Criar**.

        ![Adicione uma parte edge](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    <!--If you created a local NFS share, use the following remote sync (rsync) command option to copy files onto the share:

    `rsync <source file path> < destination file path>`

    For more information about the rsync command, go to [Rsync documentation](https://www.computerhope.com/unix/rsync.htm).-->

    Após a criação da partilha Edge, receberás uma notificação de criação bem sucedida. A lista de ações é atualizada para refletir a nova participação.

2. Adicione uma partilha local Edge no dispositivo Edge repetindo todos os passos no passo anterior e selecionando a caixa de verificação para **configurar como partilha local edge**. Os dados da partilha local permanecem no dispositivo.

    ![Adicione uma parte local edge](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. Na lâmina **shares,** vê a lista atualizada de ações.

    ![Lista atualizada de partilhas](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Para ver as propriedades da parte local recém-criada, selecione a partilha da lista. Na **caixa de módulos de computação Edge, copie** o valor correspondente a esta partilha.

    Utilizará este ponto de montagem local quando colocar o módulo.

    ![A caixa "Local mount point for Edge compute modules"](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. Para ver as propriedades da partilha Edge que criou, selecione a partilha da lista. Na **caixa de módulos de computação Edge, copie** o valor correspondente a esta partilha.

    Utilizará este ponto de montagem local quando colocar o módulo.

    ![Adicionar módulo personalizado](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Adicionar um acionador

1. Vá ao **Edge compute > Triggers**. **Selecione + Adicionar o gatilho**.

    ![Adicionar acionador](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. Na lâmina do **gatilho Adicionar,** insira os seguintes valores.

    |Campo  |Valor  |
    |---------|---------|
    |Nome do gatilho     | Um nome único para o seu gatilho.         |
    |Tipo de acionador     | Selecione o gatilho **do ficheiro.** Um acionador de ficheiro é acionado sempre que ocorre um evento de ficheiro, como quando um ficheiro é gravado na partilha de entrada. Um gatilho programado, por outro lado, dispara com base num horário definido por si. Para este exemplo, precisamos de um gatilho.    |
    |Partilha de entrada     | Selecione uma partilha de entrada. A participação local edge é a parte de entrada neste caso. O módulo utilizado aqui move ficheiros da partilha local edge para uma partilha edge onde são enviados para a nuvem.        |

    ![Adicionar acionador](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. É notificado depois de o gatilho ser criado. A lista de gatilhos é atualizada para exibir o gatilho recém-criado. Selecione o gatilho que acabou de criar.

    ![Adicionar acionador](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Copie e guarde a rota da amostra. Irá modificar esta rota de amostra e usá-la mais tarde no IoT Hub.

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Adicionar acionador](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Adicionar um módulo

Não existem módulos personalizados neste dispositivo Edge. Pode adicionar um módulo personalizado ou pré-construído. Para aprender a criar um módulo personalizado, vá ao [Develop a C# module para o seu dispositivo Azure Stack Edge Pro](azure-stack-edge-create-iot-edge-module.md).

Nesta secção, adicione um módulo personalizado ao dispositivo IoT Edge que criou no [Develop a C# module para o seu Azure Stack Edge Pro](azure-stack-edge-create-iot-edge-module.md). Este módulo personalizado retira ficheiros de uma partilha local edge no dispositivo Edge e move-os para uma partilha edge (cloud) no dispositivo. A partilha em nuvem empurra então os ficheiros para a conta de armazenamento Azure que está associada à partilha da nuvem.

1. Vá ao **Edge compute > Começar**. No azulejo dos **módulos Add,** selecione o tipo de cenário como **avançado**. Selecione **Ir ao IoT Hub**.

    ![Selecione a implementação avançada](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. No seu recurso IoT Hub, vá ao **dispositivo IoT Edge** e, em seguida, selecione o seu dispositivo IoT Edge.

    ![Ir para o dispositivo IoT Edge no IoT Hub](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-2.png)

3. Nos **detalhes do dispositivo**, selecione set **Modules**.

    ![A ligação de módulos de conjunto](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-3.png)

4. Em **Módulos adicionais,** faça o seguinte:

    1. Introduza o nome, endereço, nome de utilizador e palavra-passe para as definições de registo do contentor para o módulo personalizado.
    O nome, endereço e credenciais listadas são usados para recuperar módulos com um URL correspondente. Para implementar este módulo, em **Deployment modules** (Módulos de implementação), selecione **IoT Edge module** (Módulo do IoT Edge). Este módulo IoT Edge é um recipiente de estivador que pode implantar no dispositivo IoT Edge que está associado ao seu dispositivo Azure Stack Edge Pro.

        ![A página de módulos de conjunto](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Especifique as definições do módulo personalizado do IoT Edge. Insira os seguintes valores.
     
        |Campo  |Valor  |
        |---------|---------|
        |Nome     | Um nome único para o módulo. Este módulo é um recipiente de estivador que pode implantar no dispositivo IoT Edge associado ao seu Azure Stack Edge Pro.        |
        |URI de imagem     | A imagem URI para a imagem do recipiente correspondente para o módulo.        |
        |Credenciais necessárias     | Se verificado, o nome de utilizador e a palavra-passe são utilizados para recuperar módulos com um URL correspondente.        |
    
        Na caixa **De Opções de Criação** de Recipientes, introduza os pontos de montagem locais para os módulos Edge que copiou nos passos anteriores para a partilha edge e edge local.

        > [!IMPORTANT]
        > Os caminhos utilizados aqui são montados no seu recipiente, pelo que devem corresponder à funcionalidade que o seu recipiente espera. Se estiver a seguir [Criar um módulo personalizado,](azure-stack-edge-create-iot-edge-module.md#update-the-module-with-custom-code)o código especificado nesse módulo espera os caminhos copiados. Não modifique estes caminhos.
    
        Na caixa **De Opções De Criação de Recipientes,** pode colar a seguinte amostra:
    
        ```
        {
          "HostConfig": 
          {
           "Binds": 
            [
             "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
             "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
           }
        }
        ```

        Forneça quaisquer variáveis ambientais utilizadas para o seu módulo. As variáveis ambientais fornecem informações opcionais que ajudam a definir o ambiente em que o seu módulo funciona.

        ![A caixa de opções de criação de contentores](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. Se necessário, configurar as definições avançadas de tempo de execução do Edge e, em seguida, clicar em **Seguinte**.

        ![Adicionar módulo personalizado](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5. Em **Especificar Rotas,** definir rotas entre módulos.  
   
   ![As Rotas Especificadas](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-7.png)

    Pode substituir *a rota* pela seguinte linha de rota que copiou anteriormente. Neste exemplo, insira o nome da partilha local que irá empurrar os dados para a partilha na nuvem. Substitua-o `modulename` pelo nome do módulo. Selecione **Seguinte**.
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![A secção De Especificar Rotas](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-8.png)

6. Na **implementação de Revisão,** reveja todas as definições e, em seguida, selecione **Submeter** para submeter o módulo para implementação.

   ![A página de módulos de conjunto](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Esta ação inicia a implantação do módulo. Após a colocação estar concluída, o estado de **funcionamento** do módulo está **em funcionamento**.

    ![Adicionar módulo personalizado](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Verificar a transformação de dados, a transferência

O passo final é garantir que o módulo está conectado e funcionando como esperado. O estado de tempo de funcionamento do módulo deve estar a funcionar para o seu dispositivo IoT Edge no recurso IoT Hub.

Tome as seguintes medidas para verificar a transformação de dados e transferência para Azure.
 
1. No File Explorer, ligue-se às ações edge local e Edge que criou anteriormente.

   ![Verificar a transformação dos dados](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1. Adicione os dados à partilha local.

   ![Verificar a transformação dos dados](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    Os dados são movidos para a partilha na cloud.

    ![Verificar a transformação dos dados](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    Os dados são então empurrados da parte da nuvem para a conta de armazenamento. Para ver os dados, aceda à sua conta de armazenamento e, em seguida, selecione **Storage Explorer**. Pode ver os dados enviados na sua conta de armazenamento.

    ![Verificar a transformação dos dados](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
Concluiu o processo de validação.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configure computação
> * Adicionar ações
> * Adicionar um acionador
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência dos dados

Para aprender a administrar o seu dispositivo Azure Stack Edge Pro, consulte:

> [!div class="nextstepaction"]
> [Use uI web local para administrar um Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
