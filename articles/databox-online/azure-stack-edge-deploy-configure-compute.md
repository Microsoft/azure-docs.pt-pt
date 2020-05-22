---
title: Tutorial para filtrar, analisar dados com computação em Azure Stack Edge [ Microsoft Docs
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
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774109"
---
# <a name="tutorial-transform-data-with-azure-stack-edge"></a>Tutorial: Transforme dados com Borda de Stack Azure

Este tutorial descreve como configurar um papel de computação no seu dispositivo Azure Stack Edge. Depois de configurar a função de cálculo, o Azure Stack Edge pode transformar dados antes de enviá-los para o Azure.

Este procedimento pode demorar cerca de 10 a 15 minutos para ser concluído.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Calcular configurar
> * Adicionar ações
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência dos dados

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar uma função de computação no seu dispositivo Azure Stack Edge, certifique-se de que:

- Ativou o seu dispositivo Azure Stack Edge conforme descrito no [Connect, configurado e ativado o Azure Stack Edge](azure-stack-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Calcular configurar

Para configurar a computação no seu Azure Stack Edge, irá criar um recurso IoT Hub.

1. No portal Azure do seu recurso Azure Stack Edge, vá ao Overview. No painel direito, no azulejo **Compute,** selecione **Iniciar**.

    ![Começar com a computação](./media/azure-stack-edge-deploy-configure-compute/configure-compute-1.png)

2. No azulejo do **cálculo Configure Edge,** selecione **Configure compute**.
3. Na lâmina de **cálculo Configure Edge,** insere o seguinte:

   
    |Campo  |Valor  |
    |---------|---------|
    |IoT Hub     | Escolha entre **Novo** ou **Existente.** <br> Por padrão, um nível Standard (S1) é usado para criar um recurso IoT. Para utilizar um recurso IoT de nível livre, crie um e, em seguida, selecione o recurso existente. <br> Em cada caso, o recurso IoT Hub utiliza o mesmo grupo de subscrição e recursos que é utilizado pelo recurso Azure Stack Edge.     |
    |Name     |Insira um nome para o seu recurso IoT Hub.         |

    ![Começar com a computação](./media/azure-stack-edge-deploy-configure-compute/configure-compute-2.png)

4. Selecione **Criar**. A criação de recursos do IoT Hub leva alguns minutos. Após a criação do recurso IoT Hub, as atualizações de azulejos de **cálculo Configure** para mostrar a configuração do cálculo. Para confirmar que a função de computação Edge foi configurada, selecione **View Compute** no azulejo **de computação Configure.**
    
    ![Começar com a computação](./media/azure-stack-edge-deploy-configure-compute/configure-compute-3.png)

    > [!NOTE]
    > Se o diálogo **Configure Compute** for fechado antes de o IoT Hub estar associado ao dispositivo Azure Stack Edge, o IoT Hub é criado mas não é mostrado na configuração da computação. 
    
    Quando a função de computação Edge é configurada no dispositivo Edge, cria dois dispositivos: um dispositivo IoT e um dispositivo IoT Edge. Ambos os dispositivos podem ser visualizados no recurso IoT Hub. Um tempo de funcionamento ioT Edge também está em execução neste dispositivo IoT Edge. Neste ponto, apenas a plataforma Linux está disponível para o seu dispositivo IoT Edge.


## <a name="add-shares"></a>Adicionar ações

Para a simples implantação neste tutorial, você precisará de duas ações: uma partilha edge e outra parte local edge.

1. Adicione uma parte Edge no dispositivo fazendo os seguintes passos:

    1. No seu recurso Azure Stack Edge, vá à **computação Edge > Começar**.
    2. No azulejo **adicionar partilha(s),** selecione **Adicionar**.
    3. Na lâmina **de partilha Add,** forneça o nome da partilha e selecione o tipo de partilha.
    4. Para montar a parte Edge, selecione a caixa de verificação para **utilizar a parte com a computação Edge**.
    5. Selecione a **conta de Armazenamento,** **serviço de armazenamento,** um utilizador existente e, em seguida, selecione **Criar**.

        ![Adicione uma parte edge](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-1.png) 

    Se criou uma parte nfs local, utilize a seguinte opção de comando de sincronização remota (rsync) para copiar ficheiros na parte:

    `rsync <source file path> < destination file path>`

    Para mais informações sobre o comando rsync, vá à [documentação rsync](https://www.computerhope.com/unix/rsync.htm).

    A partilha Edge é criada e receberá uma notificação de criação bem sucedida. A lista de ações pode ser atualizada, mas deve esperar que a criação de ações esteja concluída.

2. Adicione uma parte local edge no dispositivo Edge repetindo todos os passos na etapa anterior e selecionando a caixa de verificação para **Configure como partilha local edge**. Os dados da partilha local permanecem no dispositivo.

    ![Adicione uma parte local edge](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. **Selecione Adicionar ações(s)** para ver a lista atualizada de ações.

    ![Lista atualizada de partilhas](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Adicione um módulo

Pode adicionar um módulo personalizado ou pré-construído. Não existem módulos personalizados neste dispositivo Edge. Para aprender a criar um módulo personalizado, vá desenvolver [um módulo C# para o seu dispositivo Azure Stack Edge](azure-stack-edge-create-iot-edge-module.md).

Nesta secção, adicione um módulo personalizado ao dispositivo IoT Edge que criou no [Desenvolver um módulo C# para o seu Azure Stack Edge](azure-stack-edge-create-iot-edge-module.md). Este módulo personalizado retira ficheiros de uma parte local edge no dispositivo Edge e move-os para uma partilha edge (cloud) no dispositivo. A partilha de nuvem empurra os ficheiros para a conta de armazenamento Azure que está associada à partilha de nuvens.

1. Vá ao **cálculo edge > Começar.** No azulejo **adicionar módulos,** selecione o tipo de cenário como **simples**. Selecione **Adicionar**.
2. Na **Configuração e adicione** a lâmina do módulo, insera os seguintes valores:

    
    |Campo  |Valor  |
    |---------|---------|
    |Nome     | Um nome único para o módulo. Este módulo é um recipiente de estivador que pode implantar para o dispositivo IoT Edge que está associado ao seu Azure Stack Edge.        |
    |Imagem URI     | A imagem URI para a imagem correspondente do recipiente para o módulo.        |
    |Credenciais necessárias     | Se verificado, o nome de utilizador e a palavra-passe são utilizados para recuperar módulos com um URL correspondente.        |
    |Parte de entrada     | Selecione uma parte de entrada. A parte local edge é a parte de entrada neste caso. O módulo aqui utilizado move ficheiros da partilha local edge para uma partilha edge onde são enviados para a nuvem.        |
    |Parte de saída     | Selecione uma parte de saída. A parte Edge é a parte de saída neste caso.        |
    |Tipo de acionador     | Selecione a partir **de Arquivo** ou **Agendar**. Um gatilho de ficheiro dispara sempre que ocorre um evento de ficheiro, como um ficheiro é escrito para a parte de entrada. Um gatilho programado dispara com base num horário definido por si.         |
    |Nome do gatilho     | Um nome único para o seu gatilho.         |
    |Variáveis de ambiente| Informações opcionais que ajudarão a definir o ambiente em que o seu módulo irá funcionar.   |

    ![Adicionar e configurar módulo](./media/azure-stack-edge-deploy-configure-compute/add-module-1.png)

3. Selecione **Adicionar**. O módulo é adicionado. As atualizações de azulejos **do módulo Adicionar** para indicar que o módulo está implantado. 

    ![Módulo implantado](./media/azure-stack-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Verificar a transformação e a transferência dos dados

O passo final é garantir que o módulo está conectado e em funcionamento como esperado. O estado de execução do módulo deve estar a funcionar para o seu dispositivo IoT Edge no recurso IoT Hub.

Para verificar se o módulo está em funcionamento, faça o seguinte:

1. Selecione o azulejo do **módulo Adicionar.** Isto leva-o à lâmina dos **Módulos.** Na lista de módulos, identifique o módulo que implementou. O estado de funcionamento do módulo que adicionou deve estar *em execução*.

    ![Verificar a transformação dos dados](./media/azure-stack-edge-deploy-configure-compute/verify-data-1.png)
 
1. No File Explorer, ligue-se às partilhas locais edge e Edge que criou anteriormente.

    ![Verificar a transformação dos dados](./media/azure-stack-edge-deploy-configure-compute/verify-data-2.png) 
 
1. Adicione os dados à partilha local.

    ![Verificar a transformação dos dados](./media/azure-stack-edge-deploy-configure-compute/verify-data-3.png) 
 
    Os dados são movidos para a partilha na cloud.

    ![Verificar a transformação dos dados](./media/azure-stack-edge-deploy-configure-compute/verify-data-4.png)  

    Os dados são então empurrados da parte da nuvem para a conta de armazenamento. Para ver os dados, vá ao Explorador de Armazenamento.

    ![Verificar a transformação dos dados](./media/azure-stack-edge-deploy-configure-compute/verify-data-5.png) 
 
Completou o processo de validação.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Calcular configurar
> * Adicionar ações
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência dos dados

Para aprender a administrar o seu dispositivo Azure Stack Edge, consulte:

> [!div class="nextstepaction"]
> [Use a Web UI local para administrar uma Borda de Pilha Azure](azure-stack-edge-manage-access-power-connectivity-mode.md)
