---
title: Tutorial para filtrar, analisar os dados para uma implementação avançada com a computação do Edge de caixa de dados do Azure | Documentos da Microsoft
description: Saiba como configurar a função de computação no limite da caixa de dados e utilizá-lo a transformar os dados para o fluxo de implementação avançada antes de enviar para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b446a3ebf92f6240d3bc02a148fbb8296efec926
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65950711"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-for-advanced-deployment-flow"></a>Tutorial: Transformar dados com o Azure Edge de caixa de dados para o fluxo de implementação avançada

Este tutorial descreve como configurar uma função de computação para um fluxo de implementação avançada no seu dispositivo do Edge de caixa de dados do Azure. Depois de configurar a função de computação, dados de caixa de borda pode transformar os dados antes de os enviar para o Azure.

Computação pode ser configurada para o fluxo de implementação simples ou avançadas no seu dispositivo.

|                  | Implementação simples                                | Implementação avançada                   |
|------------------|--------------------------------------------------|---------------------------------------|
| Se destina a     | Administradores de TI                                | Programadores                            |
| Type             | Utilize o serviço de borda de caixa de dados para implementar módulos      | Utilize o serviço IoT Hub para implementar módulos |
| Módulos implementados | Single                                           | Em cadeia ou vários módulos           |


Este procedimento pode demorar cerca de 20 a 30 minutos a concluir.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar computação
> * Adicionar partilhas
> * Adicionar um acionador
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência dos dados

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar uma função de computação no seu dispositivo do Edge de caixa de dados, certifique-se de que:

- Ativou o seu dispositivo Edge de caixa de dados conforme descrito em [ligue-se de que configurar e ative o Edge de caixa de dados do Azure](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Configurar computação

Para configurar a computação na extremidade da caixa de dados, irá criar um recurso do IoT Hub.

1. No portal do Azure do seu recurso de borda de caixa de dados, aceda a **descrição geral**. No painel da direita, sobre o **computação** mosaico, selecione **começar**.

    ![Introdução à computação](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. Sobre o **computação de configurar o Edge** mosaico, selecione **configurar computação**.

    ![Introdução à computação](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. Sobre o **computação de configurar o Edge** painel, introduza o seguinte:

   
    |Campo  |Value  |
    |---------|---------|
    |IoT Hub     | Escolha entre **novos** ou **existente**. <br> Por predefinição, um escalão Standard (S1) é utilizado para criar um recurso de IoT. Para utilizar um recurso de IoT do escalão gratuito, criar uma e, em seguida, selecione o recurso existente. <br> Em cada caso, o recurso do IoT Hub utiliza a mesma subscrição e grupo de recursos que é utilizado pelo recurso do Edge de caixa de dados.     |
    |Name     |Introduza um nome para o seu recurso do IoT Hub.         |

    ![Introdução à computação](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Selecione **Criar**. A criação de recursos do IoT Hub demora alguns minutos. Depois do recurso do IoT Hub é criado, o **computação de configurar o Edge** mosaico atualizações para mostrar a configuração de computação. Para confirmar que a função de computação de borda foi configurada, selecione **configuração do modo de exibição** sobre o **configurar computação** mosaico.
    
    ![Introdução à computação](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-4.png)

    Quando a função de computação de borda é configurada o dispositivo de limite, ele cria dois dispositivos: um dispositivo de IoT e um dispositivo IoT Edge. Ambos os dispositivos podem ser visualizados no recurso da IoT Hub. Um tempo de execução do IoT Edge também está em execução neste dispositivo IoT Edge.

    Neste momento, apenas a plataforma de Linux está disponível para seu dispositivo IoT Edge.


## <a name="add-shares"></a>Adicionar partilhas

Para a implementação avançada neste tutorial, terá duas partilhas: uma partilha de borda e outra partilha de local de borda.

1. Adicione uma partilha do Edge no dispositivo, efetuando os seguintes passos:

    1. No seu recurso de borda de caixa de dados, aceda a **computação Edge > começar**.
    2. Sobre o **adicionar partilhas** mosaico, selecione **Add**.
    3. Sobre o **Adicionar partilha** painel, forneça o nome da partilha e selecione o tipo de partilha.
    4. Para montar a partilha de borda, selecione a caixa de verificação **utilize a partilha com a computação de borda**.
    5. Selecione o **conta de armazenamento**, **serviço de armazenamento**, um utilizador existente e, em seguida, selecione **criar**.

        ![Adicionar uma partilha de borda](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    <!--If you created a local NFS share, use the following remote sync (rsync) command option to copy files onto the share:

    `rsync <source file path> < destination file path>`

    For more information about the rsync command, go to [Rsync documentation](https://www.computerhope.com/unix/rsync.htm).-->

    Depois de criar a partilha de borda, receberá uma notificação de criação com êxito. A lista de partilha é atualizada para refletir a nova partilha.

2. Adicionar uma partilha de local do Edge no dispositivo Edge ao repetir todas as etapas no passo anterior e selecionar a caixa de verificação **configurar como partilha local do Edge**. Os dados na partilha de local permanecem no dispositivo.

    ![Adicionar uma partilha de local do Edge](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. Na **partilhas** painel, verá a lista atualizada de partilhas.

    ![Lista atualizada de partilhas](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Para ver as propriedades da partilha local criada recentemente, selecione a partilha da lista. Na **módulos de computação de ponto de montagem Local para o Edge** caixa, copie o valor correspondente a esta partilha.

    Irá utilizar este ponto de montagem local quando implantar o módulo.

    ![A caixa "módulos de computação de ponto de montagem Local para o Edge"](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. Para ver as propriedades da partilha do Edge que criou, selecione a partilha da lista. Na **módulos de computação de ponto de montagem Local para o Edge** caixa, copie o valor correspondente a esta partilha.

    Irá utilizar este ponto de montagem local quando implantar o módulo.

    ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Adicionar um acionador

1. Aceda a **computação Edge > Acionadores**. Selecione **+ adicionar acionador**.

    ![Adicionar acionador](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. Na **adicionar acionador** painel, os seguintes valores de entrada.

    |Campo  |Value  |
    |---------|---------|
    |Nome do acionador     | Um nome exclusivo para o acionador.         |
    |Tipo de acionador     | Selecione **ficheiro** acionador. Um ficheiro é acionado sempre que ocorre um evento de ficheiro, como um ficheiro é escrito para a partilha de entrada. Um acionador agendado por outro lado, dispara com base numa agenda definida por si. Neste exemplo, precisamos de um acionador de ficheiro.    |
    |Partilha de entrada     | Selecione uma partilha de entrada. A partilha de local de borda é a partilha de entrada neste caso. O módulo utilizado aqui move ficheiros da partilha local do Edge, para uma partilha de borda em que são carregados para a cloud.        |

    ![Adicionar acionador](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. Será notificado depois do acionador é criado. A lista de acionadores é atualizada para apresentar o acionador recém-criado. Selecione o acionador que acabou de criar.

    ![Adicionar acionador](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Copie e guarde a rota de exemplo. Irá modificar esta rota de exemplo e utilizá-lo mais tarde no IoT Hub.

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Adicionar acionador](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Adicionar um módulo

Não há nenhum módulos personalizados neste dispositivo do Edge. Poderia adicionar um personalizado ou um módulo criado previamente. Para saber como criar um módulo personalizado, aceda à [desenvolver um C# módulo para o seu dispositivo Edge de caixa de dados](data-box-edge-create-iot-edge-module.md).

Nesta secção, adicionar um módulo personalizado para o dispositivo do IoT Edge que criou no [desenvolver um C# módulo para a extremidade da caixa de dados](data-box-edge-create-iot-edge-module.md). Este módulo personalizado utiliza ficheiros de uma partilha de local do Edge no dispositivo de limite e movê-los para uma partilha de borda (cloud) no dispositivo. A partilha de cloud, em seguida, envia os ficheiros para a conta de armazenamento do Azure que está associada a partilha de cloud.

1. Aceda a **computação Edge > começar**. Sobre o **adicionar módulos** mosaico, selecione o tipo de cenário como **avançadas**. Selecione **vá para o Hub IoT**.

    ![Selecione a implementação avançada](./media/data-box-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. No seu recurso do IoT Hub, aceda a **dispositivo IoT Edge** e, em seguida, selecione o seu dispositivo IoT Edge.

    ![Ir para o dispositivo IoT Edge no IoT Hub](./media/data-box-edge-deploy-configure-compute-advanced/add-module-2.png)

3. No **detalhes do dispositivo**, selecione **definir módulos**.

    ![A ligação do conjunto de módulos](./media/data-box-edge-deploy-configure-compute-advanced/add-module-3.png)

4. Sob **adicionar módulos**, efetue o seguinte procedimento:

    1. Introduza o nome, endereço, nome de utilizador e palavra-passe para as definições de registo de contentor para o módulo personalizado.
    O nome, endereço e as credenciais indicadas são utilizadas para obter os módulos com um URL correspondente. Para implementar este módulo, em **Deployment modules** (Módulos de implementação), selecione **IoT Edge module** (Módulo do IoT Edge). Este módulo do IoT Edge é um contentor do docker que pode implementar para o dispositivo do IoT Edge associada ao seu dispositivo Edge de caixa de dados.

        ![A página Definir módulos](./media/data-box-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Especifique as definições do módulo personalizado do IoT Edge. Os seguintes valores de entrada.
     
        |Campo  |Valor  |
        |---------|---------|
        |Name     | Um nome exclusivo para o módulo. Este módulo é um contentor do docker que pode implementar para o dispositivo do IoT Edge associado a extremidade da caixa de dados.        |
        |URI da Imagem     | O URI da imagem para a imagem de contentor correspondente para o módulo.        |
        |Credenciais exigidas     | Se a opção estiver marcada, o nome de utilizador e palavra-passe são utilizados para obter os módulos com um URL correspondente.        |
    
        Na **opções de criar contentor** , introduza os pontos de montagem local para os módulos de extremidade que copiou nos passos anteriores para a partilha de borda e a partilha local do Edge.

        > [!IMPORTANT]
        > Os caminhos usados aqui estão montados no seu contentor, pelo que devem corresponder ao que a funcionalidade no seu contentor de espera. Se estiver a seguir [criar um módulo personalizado](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code), o código especificado nesse módulo espera que os caminhos copiados. Não modifique estes caminhos.
    
        Na **opções de criar contentor** caixa, pode colar o exemplo a seguir:
    
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

        Forneça quaisquer variáveis de ambiente utilizados para seu módulo. Variáveis de ambiente fornecem informações opcionais que ajudam a definir o ambiente no qual o módulo for executado.

        ![A caixa de opções de criar contentor](./media/data-box-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. Se necessário, configure as definições avançadas de runtime Edge e, em seguida, clique em **seguinte**.

        ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5.  Sob **especificar rotas**, definir rotas entre módulos.  
    
    ![As rotas de especificar](./media/data-box-edge-deploy-configure-compute-advanced/add-module-7.png)

    Pode substituir *rota* com a seguinte cadeia de rota que copiou anteriormente. Neste exemplo, introduza o nome da partilha local que emite dados para a partilha de cloud. Substitua o `modulename` com o nome do módulo. Selecione **Seguinte**.
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![A secção de especificar rotas](./media/data-box-edge-deploy-configure-compute-advanced/add-module-8.png)

6.  Sob **rever implantação**, reveja todas as definições e, em seguida, selecione **submeter** para submeter o módulo para a implementação.

    ![A página Definir módulos](./media/data-box-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Esta ação inicia a implementação do módulo. Após a implementação estiver concluída, o **estado de Runtime** do módulo é **em execução**.

    ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Verificar dados transformar, transferir

A etapa final é garantir que o módulo está ligado e em execução conforme esperado. O estado de tempo de execução do módulo deve estar em execução para o seu dispositivo IoT Edge no recurso da IoT Hub.

Siga os passos seguintes para verificar a transformação de dados e a transferência para o Azure.
 
1.  No Explorador de ficheiros, ligar para o Edge local e partilhas de extremidade que criou anteriormente.

    ![Verificar a transformação dos dados](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1.  Adicione os dados à partilha local.

    ![Verificar a transformação dos dados](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    Os dados são movidos para a partilha na cloud.

    ![Verificar a transformação dos dados](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    Os dados, em seguida, são emitidos da partilha na cloud para a conta de armazenamento. Para ver os dados, aceda à sua conta de armazenamento e, em seguida, selecione **Explorador de armazenamento**. Pode ver os dados carregados na sua conta de armazenamento.

    ![Verificar a transformação dos dados](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
Concluiu o processo de validação.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configurar computação
> * Adicionar partilhas
> * Adicionar um acionador
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência dos dados

Para saber como administrar o seu dispositivo Edge de caixa de dados, veja:

> [!div class="nextstepaction"]
> [Use local web UI to administer a Data Box Edge](data-box-edge-manage-access-power-connectivity-mode.md) (Utilizar a IU da Web local para administrar o Data Box Edge)
