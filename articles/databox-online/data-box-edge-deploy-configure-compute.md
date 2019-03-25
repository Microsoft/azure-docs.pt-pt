---
title: Transformar dados com o Azure Data Box Edge | Microsoft Docs
description: Saiba como configurar a função de computação no Data Box Edge e utilizá-la para transformar dados antes de os enviar para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 31911c124aeafecb8ee37d14e58d3a0bdc0d4955
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400733"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Tutorial: Transformar dados com o Edge de caixa de dados do Azure

Este tutorial descreve como configurar uma função de computação no seu dispositivo do Edge de caixa de dados do Azure. Depois de configurar a função de computação, dados de caixa de borda pode transformar os dados antes de os enviar para o Azure.

Este procedimento pode demorar cerca de 10 a 15 minutos a concluir.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar computação
> * Adicionar partilhas
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência dos dados

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar uma função de computação no seu dispositivo do Edge de caixa de dados, certifique-se de que:

- Ativou o seu dispositivo Edge de caixa de dados conforme descrito em [ligue-se de que configurar e ative o Edge de caixa de dados do Azure](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Configurar computação

Para configurar a computação na extremidade da caixa de dados, irá criar um recurso do IoT Hub.

1. No portal do Azure do seu recurso de borda de caixa de dados, aceda à descrição geral. No painel da direita, sobre o **computação** mosaico, selecione **começar**.

    ![Introdução à computação](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. Sobre o **computação de configurar o Edge** mosaico, selecione **configurar computação**.
3. Sobre o **computação de configurar o Edge** painel, introduza o seguinte:

   
    |Campo  |Value  |
    |---------|---------|
    |IoT Hub     | Escolha entre **novos** ou **existente**. <br> Por predefinição, um escalão Standard (S1) é utilizado para criar um recurso de IoT. Para utilizar um recurso de IoT do escalão gratuito, criar uma e, em seguida, selecione o recurso existente. <br> Em cada caso, o recurso do IoT Hub utiliza a mesma subscrição e grupo de recursos que é utilizado pelo recurso do Edge de caixa de dados.     |
    |Name     |Introduza um nome para o seu recurso do IoT Hub.         |

    ![Introdução à computação](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Selecione **Criar**. A criação de recursos do IoT Hub demora alguns minutos. Depois do recurso do IoT Hub é criado, o **configurar computação** mosaico atualizações para mostrar a configuração de computação. Para confirmar que a função de computação de borda foi configurada, selecione **vista de computação** sobre o **configurar computação** mosaico.
    
    ![Introdução à computação](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    Quando a função de computação de borda é configurada o dispositivo de limite, ele cria dois dispositivos: um dispositivo de IoT e um dispositivo IoT Edge. Ambos os dispositivos podem ser visualizados no recurso da IoT Hub. Um tempo de execução do IoT Edge também está em execução neste dispositivo IoT Edge. Neste momento, apenas a plataforma de Linux está disponível para seu dispositivo IoT Edge.


## <a name="add-shares"></a>Adicionar partilhas

Para a implementação simple neste tutorial, terá duas partilhas: uma partilha de borda e outra partilha de local de borda.

1. Adicione uma partilha do Edge no dispositivo, efetuando os seguintes passos:

    1. No seu recurso de borda de caixa de dados, aceda a **computação Edge > começar**.
    2. Sobre o **adicionar partilhas** mosaico, selecione **Add**.
    3. Sobre o **Adicionar partilha** painel, forneça o nome da partilha e selecione o tipo de partilha.
    4. Para montar a partilha de borda, selecione a caixa de verificação **utilize a partilha com a computação de borda**.
    5. Selecione o **conta de armazenamento**, **serviço de armazenamento**, um utilizador existente e, em seguida, selecione **criar**.

        ![Adicionar uma partilha de borda](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Se tiver criado uma partilha NFS local, utilize a seguinte opção de comando de sincronização remoto (rsync) para copiar ficheiros para a partilha:

    `rsync <source file path> < destination file path>`

    Para obter mais informações sobre o comando rsync, aceda a [Rsync documentação](https://www.computerhope.com/unix/rsync.htm).

    Criar a partilha de borda, e receberá uma notificação de criação com êxito. A lista de partilha pode ser atualizada, mas tem de aguardar a criação de partilha para serem concluídas.

2. Adicionar uma partilha de local do Edge no dispositivo Edge ao repetir todas as etapas no passo anterior e selecionar a caixa de verificação **configurar como partilha local do Edge**. Os dados na partilha de local permanecem no dispositivo.

    ![Adicionar uma partilha de local do Edge](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Selecione **adicionar partilhas** para ver a lista atualizada de partilhas.

    ![Lista atualizada de partilhas](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Adicionar um módulo

Poderia adicionar um personalizado ou um módulo criado previamente. Não há nenhum módulos personalizados neste dispositivo do Edge. Para saber como criar um módulo personalizado, aceda à [desenvolver um C# módulo para o seu dispositivo Edge de caixa de dados](data-box-edge-create-iot-edge-module.md).

Nesta secção, adicionar um módulo personalizado para o dispositivo do IoT Edge que criou no [desenvolver um C# módulo para a extremidade da caixa de dados](data-box-edge-create-iot-edge-module.md). Este módulo personalizado utiliza ficheiros de uma partilha de local do Edge no dispositivo de limite e movê-los para uma partilha de borda (cloud) no dispositivo. A partilha de cloud, em seguida, envia os ficheiros para a conta de armazenamento do Azure que está associada a partilha de cloud.

1. Aceda a **computação Edge > começar**. Sobre o **adicionar módulos** mosaico, selecione o tipo de cenário como **simples**. Selecione **Adicionar**.
2. Na **configurar e adicionar módulo** painel, introduza os seguintes valores:

    
    |Campo  |Valor  |
    |---------|---------|
    |Name     | Um nome exclusivo para o módulo. Este módulo é um contentor do docker que pode implementar para o dispositivo do IoT Edge que está associada a extremidade da caixa de dados.        |
    |URI da Imagem     | O URI da imagem para a imagem de contentor correspondente para o módulo.        |
    |Credenciais exigidas     | Se a opção estiver marcada, o nome de utilizador e palavra-passe são utilizados para obter os módulos com um URL correspondente.        |
    |Partilha de entrada     | Selecione uma partilha de entrada. A partilha de local de borda é a partilha de entrada neste caso. O módulo utilizado aqui move ficheiros da partilha local do Edge, para uma partilha de borda em que são carregados para a cloud.        |
    |Partilha de saída     | Selecione uma partilha de saída. A partilha de borda é a partilha de saída neste caso.        |
    |Tipo de acionador     | Selecione entre **arquivo** ou **agenda**. Um ficheiro é acionado sempre que ocorre um evento de ficheiro, como um ficheiro é escrito para a partilha de entrada. Um acionador agendado dispara com base numa agenda definida por si.         |
    |Nome do acionador     | Um nome exclusivo para o acionador.         |
    |Variáveis de ambiente| Informações opcionais que ajudam a definir o ambiente no qual o módulo será executado.   |

    ![Adicionar e configurar o módulo](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Selecione **Adicionar**. O módulo é adicionado. O **Adicionar módulo** mosaico é atualizada para indicar que o módulo está implementado. 

    ![Módulo implementado](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Verificar a transformação e a transferência dos dados

A etapa final é garantir que o módulo está ligado e em execução conforme esperado. O estado de tempo de execução do módulo deve estar em execução para o seu dispositivo IoT Edge no recurso da IoT Hub.

Para verificar que o módulo está em execução, faça o seguinte:

1. Selecione o **Adicionar módulo** mosaico. Isto leva-o para o **módulos** painel. Na lista de módulos, identifique o módulo que implementou. O estado de tempo de execução do módulo adicionou deve ser *em execução*.

    ![Verificar a transformação dos dados](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  No Explorador de ficheiros, ligar para o Edge local e partilhas de extremidade que criou anteriormente.

    ![Verificar a transformação dos dados](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  Adicione os dados à partilha local.

    ![Verificar a transformação dos dados](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    Os dados são movidos para a partilha na cloud.

    ![Verificar a transformação dos dados](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    Os dados, em seguida, são emitidos da partilha na cloud para a conta de armazenamento. Para ver os dados, vá para o Explorador de armazenamento.

    ![Verificar a transformação dos dados](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
Concluiu o processo de validação.


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configurar computação
> * Adicionar partilhas
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência dos dados

Para saber como administrar o seu dispositivo Edge de caixa de dados, veja:

> [!div class="nextstepaction"]
> [Use local web UI to administer a Data Box Edge](data-box-edge-manage-access-power-connectivity-mode.md) (Utilizar a IU da Web local para administrar o Data Box Edge)
