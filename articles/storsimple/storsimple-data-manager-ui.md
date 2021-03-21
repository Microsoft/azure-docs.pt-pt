---
title: Microsoft Azure StorSimple Data Manager UI
description: Saiba como pode utilizar o UI StorSimple Data Manager para transformar os dados que residem nos dispositivos da série StorSimple 8000.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 802f732e8d62f5df861be525316b3c31ab4d0655
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94957933"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Gerir o serviço StorSimple Data Manager no portal Azure

Este artigo explica como pode utilizar o UI StorSimple Data Manager para transformar os dados que residem nos dispositivos da série StorSimple 8000. Os dados transformados podem então ser consumidos por outros serviços Azure Media, Azure Media Services, Azure HDInsight, Azure Machine Learning e Azure Cognitive Search.


## <a name="use-storsimple-data-transformation"></a>Utilizar a transformação de dados StorSimple

O StorSimple Data Manager é o recurso dentro do qual a transformação de dados é instantânea. O serviço de Transformação de Dados permite transformar dados do formato StorSimple para formato nativo em blobs ou Ficheiros Azure. Para transformar os dados do formato nativo StorSimple, precisa de especificar os detalhes sobre o seu dispositivo da série StorSimple 8000 e os dados de interesse que pretende transformar.

### <a name="create-a-storsimple-data-manager-service"></a>Criar um serviço StorSimple Data Manager

Execute os seguintes passos para criar um serviço StorSimple Data Manager.

1. Utilize as credenciais da sua conta Microsoft para iniciar sessão no [portal do Azure](https://portal.azure.com/).

2. Clique **+ Crie um recurso** e procure o Gestor de Dados StorSimple.

    ![Criar um serviço StorSimple Data Manager 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Clique em StorSimple Data Manager e, em seguida, clique em **Criar**.
    
    ![Criar um serviço StorSimple Data Manager 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. Para o novo serviço, especifique o seguinte:

   1. Forneça um nome de **Serviço** único para o seu Gestor de Dados StorSimple. Este é um nome amigável que pode ser utilizado para identificar o serviço. O nome pode ter entre 3 e 24 carateres que podem ser letras, números e hífenes. O nome tem de começar e terminar com uma letra ou um número.

   2. Escolha uma **Subscrição** da lista de abandono. A subscrição está ligada à sua conta de faturação. Este campo é automaticamente povoado (e não selecionável) se tiver apenas uma subscrição.

   3. Selecione um grupo de recursos existente ou crie um novo grupo. Para obter mais informações, veja [Azure resource groups](../azure-resource-manager/management/manage-resource-groups-portal.md) (Grupos de recursos do Azure).

   4. Especifique a **Localização** do seu serviço que aloja as suas contas de armazenamento e o seu serviço StorSimple Data Manager. O seu serviço StorSimple Device Manager, o serviço Data Manager e a conta de armazenamento associada devem estar nas regiões suportadas.
    
   5. Para obter um link para este serviço no seu painel de instrumentos, selecione **Pin para painel de instrumentos**.
    
   6. Clique em **Criar**.

      ![Criar um serviço StorSimple Data Manager 3](./media/storsimple-data-manager-ui/create-service-4.png)

A criação do serviço demora alguns minutos. Vê uma notificação após a criação do serviço com sucesso e o novo serviço é apresentado.

### <a name="create-a-data-transformation-job-definition"></a>Criar uma definição da tarefa de transformação de dados

Dentro de um serviço StorSimple Data Manager, você precisa criar uma definição de trabalho de transformação de dados. Uma definição de trabalho especifica detalhes dos dados StorSimple que está interessado em mudar para uma conta de armazenamento no formato nativo. Uma vez que crie uma definição de emprego, então pode executar este trabalho novamente com diferentes configurações de tempo de execução.

Execute os seguintes passos para criar uma definição de emprego.

1. Navegue para o serviço que criou. Vá para **a Gestão > Definições de Emprego.**

2. Clique **+ Definição de trabalho**.

    ![Clique em +Definição de Trabalho](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Forneça um nome para a definição de trabalho. O nome pode ter entre 3 e 63 caracteres. O nome pode conter letras maiúsculas e minúsculas, números e hífenes.

4. Especifique um local onde o seu trabalho é executado. Esta localização pode ser diferente do local onde o serviço é implantado.

5. Clique **em Fonte** para especificar o repositório de dados de origem.

    ![Configure a origem repo](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Uma vez que este é um novo serviço de Data Manager, nenhum repositório de dados está configurado. Na **origem de dados configure,** especifique os detalhes do seu dispositivo da série StorSimple 8000 e os dados de interesse.

   Para adicionar o seu Gestor de Dispositivos StorSimple como um repositório de dados, clique em **Adicionar novo** no repositório de dados e, em seguida, clique em **Adicionar Repositório de Dados**.

    ![Adicionar novo repo de dados](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. Escolha **o Gestor da série StorSimple 8000** como tipo de repositório de dados.
    
   2. Insira um nome amigável para o seu repositório de dados de origem.
    
   3. A partir da lista de dropdown, escolha uma subscrição associada ao seu serviço StorSimple Device Manager.
    
   4. Fornecer o nome do Gestor de Dispositivos StorSimple para o **recurso**.

   5. Introduza a chave **de encriptação de dados** de Serviço para o serviço StorSimple Device Manager. 

      ![Configure dados de origem repo 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      Clique **em OK** quando terminar. Isto poupa o seu repositório de dados. Reutilizar este Gestor de Dispositivos StorSimple noutras definições de trabalho sem voltar a introduzir estes parâmetros. Demora alguns segundos depois de clicar **em OK** para que o repositório de dados de origem recém-criado apareça no dropdown.

7. A partir da lista de dropdown para **repositório de dados,** selecione o repositório de dados que criou. 

   1. Insira o nome do dispositivo da série StorSimple 8000 que contém os dados de interesse.

   2. Especifique o nome do volume que reside no dispositivo StorSimple que tem os seus dados de interesse.

   3. Na subsecção **Filter,** introduza o diretório de raiz que contém os seus dados de interesse no formato _\MyRootDirectory\Data._ As letras de acionamento tais _\C:\Os dados_ não são suportados. Também pode adicionar filtros de ficheiros aqui.

   4. O serviço de transformação de dados funciona apenas com a última imagem dos dados que é empurrado para a Azure.

   5. Clique em **OK**.

      ![Configure dados de origem repo 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Em seguida, o repositório de dados-alvo precisa de ser configurado. Escolha as contas de armazenamento para colocar ficheiros em bolhas nessa conta. No dropdown, **selecione Adicione as** definições novas e, em seguida, **configurar**.

9. Selecione o tipo de repositório de destino que pretende adicionar e os outros parâmetros associados ao repositório.

    Se selecionar um alvo do tipo de conta de Armazenamento, pode especificar um nome amigável, subscrição (escolha o mesmo que o do serviço ou outro) e uma conta de armazenamento.
        ![Configure dados-alvo repo 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    Uma fila de armazenamento é criada quando o trabalho funciona. Esta fila é preenchida com mensagens sobre os blobs transformados, à medida que ficam prontos. O nome desta fila é igual ao nome da definição da tarefa.
    
10. Depois de adicionar o repositório de dados, espere alguns minutos.
    
    1. Selecione o repositório que criou como alvo da lista de abandono no nome da **conta Target**.

    2. Escolha o tipo de armazenamento como bolhas ou ficheiros. Especificar o nome do recipiente de armazenamento onde residem os dados transformados. Clique em **OK**.

        ![Configure a conta de armazenamento de dados-alvo](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. Também pode verificar a opção de apresentar uma estimativa da duração do trabalho antes de executar o trabalho. Clique **em OK** para criar a definição de trabalho. A sua definição de trabalho está completa. Pode utilizar esta definição de trabalho várias vezes através da UI com diferentes configurações de tempo de execução.

    ![Definição completa de trabalho](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    A definição de emprego recém-criada é adicionada à lista de definições de emprego para este serviço.

### <a name="run-the-job-definition"></a>Executar a definição de tarefa

Sempre que precisar de transferir dados do StorSimple para a conta de armazenamento que especificou na definição de trabalho, tem de os executar. No tempo de execução, alguns parâmetros podem ser especificados de forma diferente. Os passos são os seguintes:

1. Selecione o seu serviço StorSimple Data Manager e vá para **a Gestão > definições de Emprego**. Selecione e clique na definição de trabalho que deseja executar.
     
     ![Iniciar a corrida de trabalho 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Clique **em Executar Agora**.
     
     ![Iniciar a corrida de trabalho 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Clique **nas definições de Executar** para modificar quaisquer definições que possa querer alterar para esta execução de trabalho. Clique **em OK** e, em seguida, clique em **Executar** para lançar o seu trabalho.

    ![Iniciar a corrida de trabalho 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Para monitorizar este trabalho, vá ao **Jobs** no seu StorSimple Data Manager. Além da monitorização na lâmina **Jobs,** também pode ouvir na fila de armazenamento onde uma mensagem é adicionada sempre que um ficheiro é movido de StorSimple para a conta de armazenamento.

    ![Iniciar a corrida de trabalho 4](./media/storsimple-data-manager-ui/start-job-run4.png)

### <a name="view-logs-after-job-completion"></a>Ver registos após a conclusão do trabalho

Após a conclusão de um trabalho, pode ver o estado do trabalho. O estatuto de trabalho pode ser **bem sucedido,** **parcialmente bem sucedido** e **falhado**. Pode ver a lista de ficheiros que foram copiados com sucesso e ficheiros que não foram copiados. Estas listas estão disponíveis num contentor chamado **"storsimple-data-manager-joblogs"** na sua conta de armazenamento alvo. Dentro deste recipiente, pode procurar uma pasta com o mesmo nome que a definição de trabalho. Dentro disto, será criada uma pasta para cada tarefa que contenha as suas listas. O nome desta pasta será o GUID do trabalho, que pode obter na página de detalhes do trabalho. Em alternativa, na maioria dos casos verá um link para os registos de cópias dentro da própria página de empregos.
Existem 2 conjuntos de ficheiros csv que verá nesta pasta. Todos os ficheiros que começam com **ofilelist copiado...** conterão a lista de ficheiros copiados com sucesso. Todos os ficheiros que começam com **ficheiros falhados...** contêm ficheiros que não foram capazes de ser copiados, juntamente com uma mensagem de erro.


## <a name="next-steps"></a>Passos seguintes

[Utilize .NET SDK para lançar empregos StorSimple Data Manager](storsimple-data-manager-dotnet-jobs.md).