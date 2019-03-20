---
title: Interface de Usuário do Microsoft Azure StorSimple Data Manager | Documentos da Microsoft
description: Descreve como utilizar o serviço do StorSimple Data Manager da interface do Usuário
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
ms.openlocfilehash: fa897b4b77f7f5869eab2ba2e7db9afbd84febfa
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121507"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Gerir o serviço StorSimple Data Manager no portal do Azure

Este artigo explica como pode utilizar a IU do Gestor de dados do StorSimple para transformar os dados que residem nos dispositivos de série StorSimple 8000. Os dados transformados, em seguida, podem ser consumidos por outros serviços do Azure, como serviços de multimédia do Azure, Azure HDInsight, Azure Machine Learning e Azure Search.


## <a name="use-storsimple-data-transformation"></a>Utilizar transformação de dados do StorSimple

O StorSimple Data Manager é o recurso no qual dados transformação é instanciada. O serviço de transformação de dados permite-lhe transformar dados o formato do StorSimple para o formato nativo em blobs ou ficheiros do Azure. Para transformar os dados de formato nativo do StorSimple, terá de especificar os detalhes sobre o seu dispositivo da série StorSimple 8000 e os dados de interesse que deseja transformar.

### <a name="create-a-storsimple-data-manager-service"></a>Criar um serviço de Gestor de dados do StorSimple

Execute os seguintes passos para criar um serviço do StorSimple Data Manager.

1. Utilize as credenciais da sua conta Microsoft para iniciar sessão no [portal do Azure](https://portal.azure.com/).

2. Clique em **+ criar um recurso** e pesquisa para o StorSimple Data Manager.

    ![Criar um serviço do StorSimple Data Manager 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Clique em StorSimple Data Manager e, em seguida, clique em **criar**.
    
    ![Criar um serviço do StorSimple Data Manager 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. Para o novo serviço, especifique o seguinte:

   1. Fornecer um exclusivo **nome do serviço** para o seu StorSimple Data Manager. Este é um nome amigável que pode ser utilizado para identificar o serviço. O nome pode ter entre 3 e 24 carateres que podem ser letras, números e hífenes. O nome tem de começar e terminar com uma letra ou um número.

   2. Escolher uma **subscrição** na lista pendente. A subscrição está ligada à sua conta de faturação. Este campo é automaticamente preenchida (e não selecionável) se tiver apenas uma subscrição.

   3. Selecione um grupo de recursos existente ou crie um novo grupo. Para obter mais informações, veja [Azure resource groups](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/) (Grupos de recursos do Azure).

   4. Especifique a **localização** para o seu serviço que aloja as suas contas de armazenamento e o seu serviço do StorSimple Data Manager. O serviço StorSimple Device Manager, o serviço Gestor de dados e a conta de armazenamento associado devem todos ser nas regiões suportadas.
    
   5. Para obter uma ligação para este serviço no seu dashboard, selecione **afixar ao dashboard**.
    
   6. Clique em **Criar**.

      ![Criar um serviço do StorSimple Data Manager 3](./media/storsimple-data-manager-ui/create-service-4.png)

A criação do serviço demora alguns minutos. Verá uma notificação depois do serviço é criado com êxito e o novo serviço é apresentado.

### <a name="create-a-data-transformation-job-definition"></a>Criar uma definição de tarefa de transformação de dados

Dentro de um serviço de Gestor de dados do StorSimple, terá de criar uma definição de tarefa de transformação de dados. Uma definição de tarefa Especifica os detalhes dos dados do StorSimple que tem interesse em Mover para uma conta de armazenamento no formato nativo. Depois de criar uma definição de tarefa, em seguida, pode executar novamente esta tarefa com definições de tempo de execução diferente.

Execute os seguintes passos para criar uma definição de tarefa.

1. Navegue para o serviço que criou. Aceda a **gestão > definições da tarefa**.

2. Clique em **+ definição de tarefa**.

    ![Clique em + definição de tarefa](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Forneça um nome para a sua definição de tarefa. O nome pode ter entre 3 e 63 carateres. O nome pode conter letras maiúsculas e minúsculas, números e hífenes.

4. Especifique uma localização onde o seu trabalho é executada. Esta localização pode ser diferente da localização onde o serviço é implementado.

5. Clique em **origem** para especificar o repositório de dados de origem.

    ![Configurar o repositório de dados de origem](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Como se trata de um novo serviço de Gestor de dados, nenhum repositório de dados está configurado. Na **configurar origem de dados**, especifique os detalhes do seu dispositivo da série StorSimple 8000 e os dados de interesse.

   Para adicionar o seu StorSimple Device Manager como um repositório de dados, clique em **adicionar novo** na lista pendente de repositório de dados e clique em **adicionar repositório de dados**.

    ![Adicionar novo repositório de dados](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. Escolher **série StorSimple 8000 Manager** como o tipo de repositório de dados.
    
   2. Introduza um nome amigável para o repositório de dados de origem.
    
   3. Na lista pendente, escolha uma assinatura associada ao seu serviço StorSimple Device Manager.
    
   4. Forneça o nome do StorSimple Device Manager para o **recursos**.

   5. Introduza o **encriptação do serviço de dados** chave para o serviço StorSimple Device Manager. 

      ![Configurar o repositório de dados de origem 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      Clique em **OK** quando tiver terminado. Esta ação guarda o repositório de dados. Reutilize este Gestor de dispositivos do StorSimple nas outras definições de tarefa sem introduzir estes parâmetros novamente. Demora alguns segundos depois de clicar em **OK** para o repositório de dados de origem criado recentemente sejam apresentados na lista pendente.

7. Na lista pendente para **repositório de dados**, selecione o repositório de dados que criou. 

   1. Introduza o nome do dispositivo da série StorSimple 8000 que contém os dados de interesse.

   2. Especifique o nome do volume que reside no dispositivo StorSimple que tem os dados de interesse.

   3. Na **filtro** subsecção, introduza o diretório de raiz que contém os dados de interesse na _\MyRootDirectory\Data_ formato. Letras de unidade, tais _\C:\Data_ não são suportadas. Também pode adicionar quaisquer filtros de ficheiro aqui.

   4. O serviço de transformação de dados funciona nos dados que são enviados para o Azure através de instantâneos. Quando executar esta tarefa, pode optar por fazer uma cópia de segurança, sempre que esta tarefa é executada (para trabalhar em dados mais recentes) ou utilizar a última cópia de segurança existente na cloud (se estiver a trabalhar em alguns dados arquivados).

   5. Clique em **OK**.

      ![Configurar o repositório de dados de origem 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Em seguida, o repositório de dados de destino tem de ser configurado. Escolha contas de armazenamento para colocar ficheiros em blobs nessa conta. Na lista pendente, selecione **adicionar novo** e, em seguida **configurar as definições de**.

9. Selecione o tipo de repositório de destino que pretende adicionar e os outros parâmetros associados com o repositório.

    Se selecionar um destino de tipo de conta de armazenamento, pode especificar um nome amigável, a subscrição (escolher o mesmo que o serviço ou outro) e uma conta de armazenamento.
        ![Configurar o repositório de dados de destino 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    Uma fila de armazenamento é criada quando a tarefa é executada. Esta fila é preenchida com mensagens sobre os blobs transformados, à medida que ficam prontos. O nome desta fila é igual ao nome da definição da tarefa.
    
10. Depois de adicionar o repositório de dados, aguarde alguns minutos.
    
    1. Selecione o repositório que criou como o destino na lista pendente na **nome da conta de destino**.

    2. Escolha o tipo de armazenamento como blobs ou ficheiros. Especifique o nome do contentor de armazenamento onde os dados transformados residem. Clique em **OK**.

        ![Configurar a conta de armazenamento de repositório de dados de destino](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. Também pode verificar a opção para apresentar uma estimativa da duração de tarefa antes de executar a tarefa. Clique em **OK** para criar a definição de tarefa. A definição de tarefa está concluída. Pode utilizar esta definição de tarefa várias vezes através da IU com definições de tempo de execução diferente.

    ![Definição de tarefa concluída](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    A definição de tarefa recentemente criada é adicionada à lista de definições de tarefa para este serviço.

### <a name="run-the-job-definition"></a>Executar a definição de tarefa

Sempre que precisar de mover dados do StorSimple para a conta de armazenamento que especificou na definição de tarefa, tem de executá-lo. No tempo de execução, alguns parâmetros podem ser especificados de forma diferente. Os passos são os seguintes:

1. Selecione o seu serviço de Gestor de dados do StorSimple e aceda a **gestão > definições da tarefa**. Selecione e clique a definição de tarefa que pretende executar.
     
     ![Iniciar a tarefa executar 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Clique em **executar agora**.
     
     ![Iniciar a tarefa seja executada 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Clique em **definições de execução** para modificar as definições que pode pretender alterar para esta tarefa de execução. Clique em **OK** e, em seguida, clique em **executar** para iniciar seu trabalho.

    ![Iniciar a tarefa seja executada 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Para monitorizar esta tarefa, aceda a **tarefas** no seu Gestor de dados do StorSimple. Para além de monitorização no **tarefas** painel, pode também escutar a fila de armazenamento onde uma mensagem for adicionada sempre que um ficheiro é movido do StorSimple para a conta de armazenamento.

    ![Iniciar a tarefa seja executada 4](./media/storsimple-data-manager-ui/start-job-run4.png)


## <a name="next-steps"></a>Passos Seguintes

[Utilizar o .NET SDK para iniciar tarefas do StorSimple Data Manager](storsimple-data-manager-dotnet-jobs.md).