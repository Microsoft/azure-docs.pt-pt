---
title: Interface do usuário do Microsoft Azure StorSimple Gerenciador de Dados | Microsoft Docs
description: Descreve como usar a interface do usuário do serviço Gerenciador de Dados do StorSimple
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 3a87b4c07d2e961d9876fb44c6584f37ad9246df
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796651"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Gerenciar o serviço de Gerenciador de Dados do StorSimple no portal do Azure

Este artigo explica como você pode usar a interface do usuário do Gerenciador de Dados do StorSimple para transformar os dados que residem nos dispositivos da série StorSimple 8000. Os dados transformados podem ser consumidos por outros serviços do Azure, como os serviços de mídia do Azure, o Azure HDInsight, o Azure Machine Learning e o Azure Pesquisa Cognitiva.


## <a name="use-storsimple-data-transformation"></a>Usar a transformação de dados do StorSimple

O Gerenciador de Dados do StorSimple é o recurso no qual a transformação de dados é instanciada. O serviço de transformação de dados permite transformar dados do formato do StorSimple para o formato nativo em BLOBs ou arquivos do Azure. Para transformar os dados de formato nativo do StorSimple, você precisa especificar os detalhes sobre o dispositivo da série StorSimple 8000 e os dados de interesse que deseja transformar.

### <a name="create-a-storsimple-data-manager-service"></a>Criar um serviço de Gerenciador de Dados do StorSimple

Execute as etapas a seguir para criar um serviço de Gerenciador de Dados do StorSimple.

1. Utilize as credenciais da sua conta Microsoft para iniciar sessão no [portal do Azure](https://portal.azure.com/).

2. Clique em **+ criar um recurso** e pesquise Gerenciador de dados do StorSimple.

    ![Criar um serviço Gerenciador de Dados do StorSimple 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Clique em Gerenciador de Dados do StorSimple e, em seguida, clique em **criar**.
    
    ![Criar um serviço Gerenciador de Dados do StorSimple 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. Para o novo serviço, especifique o seguinte:

   1. Forneça um **nome de serviço** exclusivo para seu Gerenciador de dados do StorSimple. Este é um nome amigável que pode ser utilizado para identificar o serviço. O nome pode ter entre 3 e 24 carateres que podem ser letras, números e hífenes. O nome tem de começar e terminar com uma letra ou um número.

   2. Escolha uma **assinatura** na lista suspensa. A subscrição está ligada à sua conta de faturação. Esse campo será populado automaticamente (e não selecionável) se você tiver apenas uma assinatura.

   3. Selecione um grupo de recursos existente ou crie um novo grupo. Para obter mais informações, veja [Azure resource groups](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/) (Grupos de recursos do Azure).

   4. Especifique o **local** para o serviço que hospeda suas contas de armazenamento e seu serviço de Gerenciador de dados do StorSimple. O serviço StorSimple Device Manager, o serviço Gerenciador de Dados e a conta de armazenamento associada devem estar em regiões com suporte.
    
   5. Para obter um link para esse serviço em seu painel, selecione **fixar no painel**.
    
   6. Clique em **Criar**.

      ![Criar um serviço de Gerenciador de Dados do StorSimple 3](./media/storsimple-data-manager-ui/create-service-4.png)

A criação do serviço demora alguns minutos. Você verá uma notificação depois que o serviço for criado com êxito e o novo serviço for exibido.

### <a name="create-a-data-transformation-job-definition"></a>Criar uma definição de trabalho de transformação de dados

Em um serviço de Gerenciador de Dados do StorSimple, você precisa criar uma definição de trabalho de transformação de dados. Uma definição de trabalho especifica detalhes dos dados do StorSimple que você está interessado em migrar para uma conta de armazenamento no formato nativo. Depois de criar uma definição de trabalho, você pode executar esse trabalho novamente com configurações de tempo de execução diferentes.

Execute as etapas a seguir para criar uma definição de trabalho.

1. Navegue até o serviço que você criou. Vá para **gerenciamento > definições de trabalho**.

2. Clique em **+ definição de trabalho**.

    ![Clique em + definição de trabalho](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Forneça um nome para a definição de trabalho. O nome pode ter entre 3 e 63 caracteres. O nome pode conter letras maiúsculas e minúsculas, números e hifens.

4. Especifique um local onde o trabalho é executado. Esse local pode ser diferente do local onde o serviço é implantado.

5. Clique em **origem** para especificar o repositório de dados de origem.

    ![Configurar repositório de dados de origem](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Como esse é um novo serviço de Gerenciador de Dados, não há repositórios de dados configurados. Em **Configurar fonte de dados**, especifique os detalhes do seu dispositivo StorSimple da série 8000 e os dados de interesse.

   Para adicionar o Device Manager StorSimple como um repositório de dados, clique em **Adicionar novo** na lista suspensa repositório de dados e clique em **Adicionar repositório de dados**.

    ![Adicionar novo repositório de dados](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. Escolha **StorSimple 8000 Series Manager** como o tipo de repositório de dados.
    
   2. Insira um nome amigável para o repositório de dados de origem.
    
   3. Na lista suspensa, escolha uma assinatura associada ao serviço de Device Manager do StorSimple.
    
   4. Forneça o nome do Device Manager StorSimple para o **recurso**.

   5. Insira a chave de **criptografia de dados de serviço** para o serviço StorSimple Device Manager. 

      ![Configurar o repositório de dados de origem 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      Clique em **OK** quando terminar. Isso salva seu repositório de dados. Reutilize este Device Manager do StorSimple em outras definições de trabalho sem inserir esses parâmetros novamente. Levará alguns segundos depois que você clicar em **OK** para que o repositório de dados de origem recém-criado apareça na lista suspensa.

7. Na lista suspensa para **repositório de dados**, selecione o repositório de dados que você criou. 

   1. Insira o nome do dispositivo StorSimple 8000 Series que contém os dados de interesse.

   2. Especifique o nome do volume que reside no dispositivo StorSimple que tem seus dados de interesse.

   3. Na subseção **filtro** , insira o diretório raiz que contém os dados de interesse no formato _\MyRootDirectory\Data_ . Não há suporte para letras de unidade como _\c: \data_ . Você também pode adicionar qualquer filtro de arquivo aqui.

   4. O serviço de transformação de dados funciona nos dados que são enviados para o Azure por meio de instantâneos. Ao executar esse trabalho, você pode optar por fazer um backup sempre que esse trabalho for executado (para trabalhar nos dados mais recentes) ou usar o último backup existente na nuvem (se você estiver trabalhando em alguns dados arquivados).

   5. Clique em **OK**.

      ![Configurar o repositório de dados de origem 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Em seguida, o repositório de dados de destino precisa ser configurado. Escolha contas de armazenamento para colocar arquivos em BLOBs nessa conta. Na lista suspensa, selecione **Adicionar novo** e, em seguida, **definir configurações**.

9. Selecione o tipo de repositório de destino que você deseja adicionar e os outros parâmetros associados ao repositório.

    Se você selecionar um destino de tipo de conta de armazenamento, poderá especificar um nome amigável, assinatura (escolha o mesmo do serviço ou outro) e uma conta de armazenamento.
        ![configurar o repositório de dados de destino 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    Uma fila de armazenamento é criada quando o trabalho é executado. Esta fila é preenchida com mensagens sobre os blobs transformados, à medida que ficam prontos. O nome desta fila é igual ao nome da definição da tarefa.
    
10. Depois de adicionar o repositório de dados, aguarde alguns minutos.
    
    1. Selecione o repositório que você criou como o destino na lista suspensa no **nome da conta de destino**.

    2. Escolha o tipo de armazenamento como BLOBs ou arquivos. Especifique o nome do contêiner de armazenamento no qual os dados transformados residem. Clique em **OK**.

        ![Configurar a conta de armazenamento do repositório de dados de destino](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. Você também pode marcar a opção para apresentar uma estimativa de duração do trabalho antes de executar o trabalho. Clique em **OK** para criar a definição de trabalho. A definição do trabalho agora está concluída. Você pode usar essa definição de trabalho várias vezes por meio da interface do usuário com configurações de tempo de execução diferentes.

    ![Concluir definição de trabalho](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    A definição de trabalho recém-criada é adicionada à lista de definições de trabalho para esse serviço.

### <a name="run-the-job-definition"></a>Executar a definição de trabalho

Sempre que você precisar mover dados do StorSimple para a conta de armazenamento que você especificou na definição de trabalho, será necessário executá-los. Em tempo de execução, alguns parâmetros podem ser especificados de forma diferente. As etapas são as seguintes:

1. Selecione seu serviço de Gerenciador de Dados do StorSimple e vá para **gerenciamento > definições de trabalho**. Selecione e clique na definição de trabalho que você deseja executar.
     
     ![Iniciar execução de trabalho 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Clique em **executar agora**.
     
     ![Iniciar execução de trabalho 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Clique em **configurações de execução** para modificar as configurações que você pode querer alterar para esta execução de trabalho. Clique em **OK** e em **executar** para iniciar o trabalho.

    ![Iniciar execução de trabalho 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Para monitorar esse trabalho, vá para **trabalhos** em seu Gerenciador de dados do StorSimple. Além do monitoramento na folha **trabalhos** , você também pode escutar na fila de armazenamento em que uma mensagem é adicionada toda vez que um arquivo é movido do StorSimple para a conta de armazenamento.

    ![Iniciar execução de trabalho 4](./media/storsimple-data-manager-ui/start-job-run4.png)


## <a name="next-steps"></a>Passos seguintes

[Use o SDK do .net para iniciar Gerenciador de dados do StorSimple trabalhos](storsimple-data-manager-dotnet-jobs.md).