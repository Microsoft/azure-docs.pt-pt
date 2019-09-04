---
title: Tutorial para filtrar, analisar dados com computação em Azure Data Box Edge | Microsoft Docs
description: Saiba como configurar a função de computação no Data Box Edge e utilizá-la para transformar dados antes de os enviar para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b641ae62ba6e0cdacaeb46b1ffee2f02c7544763
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277211"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Tutorial: Transformar dados com Azure Data Box Edge

Este tutorial descreve como configurar uma função de computação em seu dispositivo Azure Data Box Edge. Depois de configurar a função de computação, Data Box Edge pode transformar dados antes de enviá-los para o Azure.

Esse procedimento pode levar cerca de 10 a 15 minutos para ser concluído.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar computação
> * Adicionar compartilhamentos
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência dos dados

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar uma função de computação em seu dispositivo Data Box Edge, verifique se:

- Você ativou o dispositivo Data Box Edge conforme descrito em [conectar, configurar e ativar o Azure data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Configurar computação

Para configurar a computação em seu Data Box Edge, você criará um recurso do Hub IoT.

1. Na portal do Azure do recurso de Data Box Edge, acesse visão geral. No painel direito, no bloco **computação** , selecione introdução.

    ![Introdução à computação](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. No bloco **Configurar computação de borda** , selecione **Configurar computação**.
3. Na folha **Configurar computação de borda** , insira o seguinte:

   
    |Campo  |Value  |
    |---------|---------|
    |IoT Hub     | Escolha de **novo** ou **existente**. <br> Por padrão, uma camada Standard (S1) é usada para criar um recurso de IoT. Para usar um recurso IoT de camada gratuita, crie um e, em seguida, selecione o recurso existente. <br> Em cada caso, o recurso de Hub IoT usa a mesma assinatura e grupo de recursos que é usada pelo recurso de Data Box Edge.     |
    |Name     |Insira um nome para o recurso do Hub IoT.         |

    ![Introdução à computação](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Selecione **Criar**. A criação de recursos do Hub IoT leva alguns minutos. Depois que o recurso do Hub IoT é criado, o configurar bloco de **computação** é atualizado para mostrar a configuração de computação. Para confirmar que a função de computação de borda foi configurada, selecione **Exibir computação** no bloco **Configurar computação** .
    
    ![Introdução à computação](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    > [!NOTE]
    > Se a caixa de diálogo **Configurar computação** for fechada antes de o Hub IOT ser associado ao dispositivo data Box Edge, o Hub IOT será criado, mas não será mostrado na configuração de computação. 
    
    Quando a função de computação de borda é configurada no dispositivo de borda, ela cria dois dispositivos: um dispositivo IoT e um dispositivo IoT Edge. Ambos os dispositivos podem ser exibidos no recurso do Hub IoT. Um tempo de execução IoT Edge também está em execução neste dispositivo IoT Edge. Neste ponto, apenas a plataforma Linux está disponível para seu dispositivo IoT Edge.


## <a name="add-shares"></a>Adicionar compartilhamentos

Para a implantação simples neste tutorial, você precisará de dois compartilhamentos: um compartilhamento de borda e outro compartilhamento local de borda.

1. Adicione um compartilhamento de borda no dispositivo executando as seguintes etapas:

    1. Em seu recurso de Data Box Edge, vá para a **computação de borda >** começar.
    2. No bloco **Adicionar compartilhamento (s)** , selecione **Adicionar**.
    3. Na folha **Adicionar compartilhamento** , forneça o nome do compartilhamento e selecione o tipo de compartilhamento.
    4. Para montar o compartilhamento de borda, marque a caixa de seleção para **usar o compartilhamento com a computação de borda**.
    5. Selecione a **conta de armazenamento**, o **serviço de armazenamento**, um usuário existente e, em seguida, selecione **criar**.

        ![Adicionar um compartilhamento de borda](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Se você criou um compartilhamento NFS local, use a seguinte opção de comando de sincronização remota (rsync) para copiar arquivos no compartilhamento:

    `rsync <source file path> < destination file path>`

    Para obter mais informações sobre o comando rsync, acesse a [documentação do rsync](https://www.computerhope.com/unix/rsync.htm).

    O compartilhamento de borda é criado e você receberá uma notificação de criação com êxito. A lista de compartilhamento pode ser atualizada, mas você deve aguardar a conclusão da criação do compartilhamento.

2. Adicione um compartilhamento local de borda no dispositivo de borda repetindo todas as etapas na etapa anterior e marcando a caixa de seleção para **configurar como compartilhamento local de borda**. Os dados no compartilhamento local permanecem no dispositivo.

    ![Adicionar um compartilhamento local de borda](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Selecione **Adicionar compartilhamento (s)** para ver a lista atualizada de compartilhamentos.

    ![Lista atualizada de partilhas](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Adicionar um módulo

Você pode adicionar um módulo personalizado ou pré-criado. Não há módulos personalizados neste dispositivo de borda. Para saber como criar um módulo personalizado, vá para [desenvolver um C# módulo para seu dispositivo data Box Edge](data-box-edge-create-iot-edge-module.md).

Nesta seção, você adiciona um módulo personalizado ao dispositivo IoT Edge que você criou no desenvolvimento de [um C# módulo para seu data Box Edge](data-box-edge-create-iot-edge-module.md). Esse módulo personalizado usa arquivos de um compartilhamento local de borda no dispositivo de borda e os move para um compartilhamento de borda (nuvem) no dispositivo. Em seguida, o compartilhamento de nuvem envia os arquivos para a conta de armazenamento do Azure que está associada ao compartilhamento de nuvem.

1. Vá para a **computação de borda > Introdução**. No bloco **Adicionar módulos** , selecione o tipo de cenário como **simples**. Selecione **Adicionar**.
2. Na folha **configurar e Adicionar módulo** , insira os seguintes valores:

    
    |Campo  |Valor  |
    |---------|---------|
    |Name     | Um nome exclusivo para o módulo. Esse módulo é um contêiner do Docker que você pode implantar no dispositivo IoT Edge associado ao seu Data Box Edge.        |
    |URI da Imagem     | O URI da imagem para a imagem de contêiner correspondente para o módulo.        |
    |Credenciais exigidas     | Se marcada, o nome de usuário e a senha serão usados para recuperar módulos com uma URL correspondente.        |
    |Compartilhamento de entrada     | Selecione um compartilhamento de entrada. Nesse caso, o compartilhamento local de borda é o compartilhamento de entrada. O módulo usado aqui move os arquivos do compartilhamento local de borda para um compartilhamento de borda onde eles são carregados na nuvem.        |
    |Compartilhamento de saída     | Selecione um compartilhamento de saída. Nesse caso, o compartilhamento de borda é o compartilhamento de saída.        |
    |Tipo de acionador     | Selecione do **arquivo** ou **agendamento**. Um gatilho de arquivo é acionado sempre que um evento de arquivo ocorre, como um arquivo, é gravado no compartilhamento de entrada. Um gatilho agendado é acionado com base em um agendamento definido por você.         |
    |Nome do acionador     | Um nome exclusivo para o gatilho.         |
    |Variáveis de ambiente| Informações opcionais que ajudarão a definir o ambiente no qual o módulo será executado.   |

    ![Adicionar e configurar o módulo](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Selecione **Adicionar**. O módulo é adicionado. O bloco **Adicionar módulo** é atualizado para indicar que o módulo foi implantado. 

    ![Módulo implantado](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Verificar a transformação e a transferência dos dados

A etapa final é garantir que o módulo esteja conectado e em execução conforme o esperado. O status de tempo de execução do módulo deve estar em execução para seu dispositivo de IoT Edge no recurso do Hub IoT.

Para verificar se o módulo está em execução, faça o seguinte:

1. Selecione o bloco **Adicionar módulo** . Isso leva você para a folha **módulos** . Na lista de módulos, identifique o módulo implantado. O status de tempo de execução do módulo que você adicionou deve estar *em execução*.

    ![Verificar a transformação dos dados](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  No explorador de arquivos, conecte-se aos compartilhamentos de borda local e de borda criados anteriormente.

    ![Verificar a transformação dos dados](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  Adicione os dados à partilha local.

    ![Verificar a transformação dos dados](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    Os dados são movidos para a partilha na cloud.

    ![Verificar a transformação dos dados](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    Os dados são enviados por push do compartilhamento de nuvem para a conta de armazenamento. Para exibir os dados, vá para a Gerenciador de Armazenamento.

    ![Verificar a transformação dos dados](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
Você concluiu o processo de validação.


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configurar computação
> * Adicionar compartilhamentos
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência dos dados

Para saber como administrar seu dispositivo Data Box Edge, consulte:

> [!div class="nextstepaction"]
> [Use local web UI to administer a Data Box Edge](data-box-edge-manage-access-power-connectivity-mode.md) (Utilizar a IU da Web local para administrar o Data Box Edge)
