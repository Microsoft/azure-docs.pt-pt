---
title: Tutorial para filtrar, analisar dados para implementação avançada com computação em Azure Data Box Edge  Microsoft Docs
description: Aprenda a configurar a função de cálculo no Data Box Edge e use-o para transformar dados para um fluxo avançado de implementação antes de enviar para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b446a3ebf92f6240d3bc02a148fbb8296efec926
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384730"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-for-advanced-deployment-flow"></a>Tutorial: Transforme dados com Borda de Caixa de Dados Azure para fluxo avançado de implementação

Este tutorial descreve como configurar uma função de cálculo para um fluxo avançado de implementação no seu dispositivo Azure Data Box Edge. Depois de configurar a função de cálculo, data Box Edge pode transformar dados antes de enviá-los para o Azure.

A computação pode ser configurada para um fluxo de implementação simples ou avançado no seu dispositivo.

|                  | Implantação simples                                | Implantação avançada                   |
|------------------|--------------------------------------------------|---------------------------------------|
| Destinado a     | Administradores de TI                                | Programadores                            |
| Tipo             | Utilize o serviço Data Box Edge para implementar módulos      | Utilize o serviço IoT Hub para implementar módulos |
| Módulos implantados | Único                                           | Módulos acorrentados ou múltiplos           |


Este procedimento pode demorar cerca de 20 a 30 minutos para ser concluído.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Calcular configurar
> * Adicionar ações
> * Adicionar um acionador
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência dos dados

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar uma função de cálculo no seu dispositivo Data Box Edge, certifique-se de que:

- Ativou o seu dispositivo Data Box Edge conforme descrito no [Connect, configurado e ativado](data-box-edge-deploy-connect-setup-activate.md)o Edge da Caixa de Dados Azure .


## <a name="configure-compute"></a>Calcular configurar

Para configurar a computação no seu Data Box Edge, irá criar um recurso IoT Hub.

1. No portal Azure do seu recurso Data Box Edge, vá ao **Overview .** No painel direito, no azulejo **Compute,** selecione **Iniciar**.

    ![Começar com a computação](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. No azulejo do **cálculo Configure Edge,** selecione **Configure compute**.

    ![Começar com a computação](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. Na lâmina de **cálculo Configure Edge,** insere o seguinte:

   
    |Campo  |Valor  |
    |---------|---------|
    |IoT Hub     | Escolha entre **Novo** ou **Existente.** <br> Por padrão, um nível Standard (S1) é usado para criar um recurso IoT. Para utilizar um recurso IoT de nível livre, crie um e, em seguida, selecione o recurso existente. <br> Em cada caso, o recurso IoT Hub utiliza o mesmo grupo de subscrição e recursos que é utilizado pelo recurso Data Box Edge.     |
    |Nome     |Insira um nome para o seu recurso IoT Hub.         |

    ![Começar com a computação](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Selecione **Criar**. A criação de recursos do IoT Hub leva alguns minutos. Após a criação do recurso IoT Hub, as atualizações de azulejos de **computação Configure Edge** para mostrar a configuração da computação. Para confirmar que a função de computação Edge foi configurada, selecione **Ver config** no azulejo **computacional Configure.**
    
    ![Começar com a computação](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-4.png)

    Quando a função de computação Edge é configurada no dispositivo Edge, cria dois dispositivos: um dispositivo IoT e um dispositivo IoT Edge. Ambos os dispositivos podem ser visualizados no recurso IoT Hub. Um tempo de funcionamento ioT Edge também está em execução neste dispositivo IoT Edge.

    Neste ponto, apenas a plataforma Linux está disponível para o seu dispositivo IoT Edge.


## <a name="add-shares"></a>Adicionar ações

Para a implementação avançada neste tutorial, você precisará de duas ações: uma partilha edge e outra parte local edge.

1. Adicione uma parte Edge no dispositivo fazendo os seguintes passos:

    1. No seu recurso Data Box Edge, vá ao **Edge compute > Get started**.
    2. No azulejo **adicionar partilha(s),** selecione **Adicionar**.
    3. Na lâmina **de partilha Add,** forneça o nome da partilha e selecione o tipo de partilha.
    4. Para montar a parte Edge, selecione a caixa de verificação para **utilizar a parte com a computação Edge**.
    5. Selecione a **conta de Armazenamento,** **serviço de armazenamento,** um utilizador existente e, em seguida, selecione **Criar**.

        ![Adicione uma parte edge](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    <!--If you created a local NFS share, use the following remote sync (rsync) command option to copy files onto the share:

    `rsync <source file path> < destination file path>`

    For more information about the rsync command, go to [Rsync documentation](https://www.computerhope.com/unix/rsync.htm).-->

    Após a criação da partilha Edge, receberá uma notificação de criação bem sucedida. A lista de ações é atualizada para refletir a nova ação.

2. Adicione uma parte local edge no dispositivo Edge repetindo todos os passos na etapa anterior e selecionando a caixa de verificação para **Configure como partilha local edge**. Os dados da partilha local permanecem no dispositivo.

    ![Adicione uma parte local edge](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. Na lâmina **das Ações,** vê a lista atualizada de ações.

    ![Lista atualizada de partilhas](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Para ver as propriedades da recém-criada partilha local, selecione a parte da lista. Na caixa de módulos de **módulos de computação Edge,** copie o valor correspondente a esta partilha.

    Utilizará este ponto de montagem local quando implementar o módulo.

    ![A caixa "Local Mount Point for Edge compute modules"](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. Para ver as propriedades da partilha Edge que criou, selecione a parte da lista. Na caixa de módulos de **módulos de computação Edge,** copie o valor correspondente a esta partilha.

    Utilizará este ponto de montagem local quando implementar o módulo.

    ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Adicionar um acionador

1. Vá ao **Edge compute > Triggers**. Selecione **+ Adicionar gatilho**.

    ![Adicionar gatilho](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. Na lâmina **do gatilho Adicionar,** insera os seguintes valores.

    |Campo  |Valor  |
    |---------|---------|
    |Nome do gatilho     | Um nome único para o seu gatilho.         |
    |Tipo de gatilho     | Selecione o gatilho do **ficheiro.** Um gatilho de ficheiro dispara sempre que ocorre um evento de ficheiro, como um ficheiro é escrito para a parte de entrada. Um gatilho programado, por outro lado, acende-se com base num horário definido por si. Para este exemplo, precisamos de um gatilho de ficheiro.    |
    |Parte de entrada     | Selecione uma parte de entrada. A parte local edge é a parte de entrada neste caso. O módulo aqui utilizado move ficheiros da partilha local edge para uma partilha edge onde são enviados para a nuvem.        |

    ![Adicionar gatilho](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. É notificado após a criação do gatilho. A lista de gatilhos é atualizada para exibir o gatilho recém-criado. Selecione o gatilho que acabou de criar.

    ![Adicionar gatilho](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Copie e guarde a rota da amostra. Irá modificar esta rota da amostra e usá-la-á mais tarde no IoT Hub.

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Adicionar gatilho](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Adicione um módulo

Não existem módulos personalizados neste dispositivo Edge. Pode adicionar um módulo personalizado ou pré-construído. Para aprender a criar um módulo personalizado, vá desenvolver [um C# módulo para o seu dispositivo Data Box Edge](data-box-edge-create-iot-edge-module.md).

Nesta secção, adicione um módulo personalizado ao dispositivo IoT Edge que criou em [Desenvolver um C# módulo para o seu Data Box Edge](data-box-edge-create-iot-edge-module.md). Este módulo personalizado retira ficheiros de uma parte local edge no dispositivo Edge e move-os para uma partilha edge (cloud) no dispositivo. A partilha de nuvem empurra os ficheiros para a conta de armazenamento Azure que está associada à partilha de nuvens.

1. Vá ao **Edge compute > Get started**. No azulejo **adicionar módulos,** selecione o tipo de cenário como **avançado**. Selecione **Ir para IoT Hub**.

    ![Selecione implementação avançada](./media/data-box-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. No seu recurso IoT Hub, vá ao **dispositivo IoT Edge** e, em seguida, selecione o seu dispositivo IoT Edge.

    ![Vá ao dispositivo IoT Edge no IoT Hub](./media/data-box-edge-deploy-configure-compute-advanced/add-module-2.png)

3. Em **detalhes do Dispositivo,** selecione **Módulos de Conjunto**.

    ![A ligação de Módulos definidos](./media/data-box-edge-deploy-configure-compute-advanced/add-module-3.png)

4. Em **Módulos Add,** faça o seguinte:

    1. Introduza o nome, endereço, nome do utilizador e palavra-passe para as definições de registo do recipiente para o módulo personalizado.
    O nome, endereço e credenciais listadas são usados para recuperar módulos com um URL correspondente. Para implementar este módulo, em **Deployment modules** (Módulos de implementação), selecione **IoT Edge module** (Módulo do IoT Edge). Este módulo IoT Edge é um recipiente de estivador que pode implantar para o dispositivo IoT Edge que está associado ao seu dispositivo Data Box Edge.

        ![A página de Módulos de Conjunto](./media/data-box-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Especifique as definições do módulo personalizado do IoT Edge. Insera os seguintes valores.
     
        |Campo  |Valor  |
        |---------|---------|
        |Nome     | Um nome único para o módulo. Este módulo é um recipiente de estivador que pode implantar para o dispositivo IoT Edge associado ao seu Data Box Edge.        |
        |Imagem URI     | A imagem URI para a imagem correspondente do recipiente para o módulo.        |
        |Credenciais necessárias     | Se verificado, o nome de utilizador e a palavra-passe são utilizados para recuperar módulos com um URL correspondente.        |
    
        Na caixa De Criação de **Recipientes,** introduza os pontos de montagem locais para os módulos Edge que copiou nos passos anteriores para a partilha edge e partilha local Edge.

        > [!IMPORTANT]
        > Os caminhos aqui utilizados são montados no seu recipiente, pelo que devem corresponder à funcionalidade que o seu recipiente espera. Se estiver a seguir criar [um módulo personalizado,](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code)o código especificado nesse módulo espera os caminhos copiados. Não modifique estes caminhos.
    
        Na caixa De Criação de **Recipientes,** pode colar a seguinte amostra:
    
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

        ![O recipiente criar opções](./media/data-box-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. Se necessário, configure as definições avançadas de tempo de execução do Edge e, em seguida, clique em **Seguinte**.

        ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5.  Em **Rotas de Especificação,** desloque as rotas entre módulos.  
    
    ![As Rotas de Especificação](./media/data-box-edge-deploy-configure-compute-advanced/add-module-7.png)

    Pode substituir a *rota* pela seguinte corda de rota que copiou anteriormente. Neste exemplo, introduza o nome da parte local que irá empurrar os dados para a partilha da nuvem. Substitua o `modulename` pelo nome do módulo. Selecione **Seguinte**.
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![A secção Rotas Especificar](./media/data-box-edge-deploy-configure-compute-advanced/add-module-8.png)

6.  Sob **a implementação do Review,** reveja todas as definições e, em seguida, selecione **Enviar** para submeter o módulo para implementação.

    ![A página de Módulos de Conjunto](./media/data-box-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Esta ação inicia a implantação do módulo. Após a implementação estar concluída, o estado do módulo de tempo de **funcionamento** está **em execução**.

    ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Verificar a transformação de dados, a transferência

O passo final é garantir que o módulo está conectado e em funcionamento como esperado. O estado de execução do módulo deve estar a funcionar para o seu dispositivo IoT Edge no recurso IoT Hub.

Tome os seguintes passos para verificar a transformação de dados e a transferência para o Azure.
 
1.  No File Explorer, ligue-se às partilhas locais edge e Edge que criou anteriormente.

    ![Verificar a transformação dos dados](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1.  Adicione os dados à partilha local.

    ![Verificar a transformação dos dados](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    Os dados são movidos para a partilha na cloud.

    ![Verificar a transformação dos dados](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    Os dados são então empurrados da parte da nuvem para a conta de armazenamento. Para ver os dados, vá à sua conta de armazenamento e, em seguida, selecione **Storage Explorer**. Pode ver os dados enviados na sua conta de armazenamento.

    ![Verificar a transformação dos dados](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
Completou o processo de validação.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Calcular configurar
> * Adicionar ações
> * Adicionar um acionador
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência dos dados

Para aprender a administrar o seu dispositivo Data Box Edge, consulte:

> [!div class="nextstepaction"]
> [Use local web UI to administer a Data Box Edge](data-box-edge-manage-access-power-connectivity-mode.md) (Utilizar a IU da Web local para administrar o Data Box Edge)
