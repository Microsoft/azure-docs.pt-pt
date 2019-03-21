---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d2df7388018c463ba58b57be46945915210b84b6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125142"
---
#### <a name="to-install-an-update-from-the-azure-portal"></a>Para instalar uma atualização a partir do portal do Azure

1. Na página do serviço StorSimple, selecione o seu dispositivo.

    ![Selecionar dispositivo](./media/storsimple-8000-install-update5-via-portal/update1.png)

2. Navegue para **definições do dispositivo** > **atualizações do dispositivo**.

    ![Clique em atualizações do dispositivo](./media/storsimple-8000-install-update5-via-portal/update2.png)

2. É apresentada uma notificação se estiverem disponíveis novas atualizações. Em alternativa, no **atualizações do dispositivo** painel, clique em **procurar atualizações**. É criada uma tarefa para procurar as atualizações disponíveis. É notificado quando a tarefa for concluída com êxito.

    ![Clique em atualizações do dispositivo](./media/storsimple-8000-install-update5-via-portal/update3.png)

3. Recomendamos que reveja as notas de versão antes de aplicar uma atualização ao seu dispositivo. Para aplicar atualizações, clique em **instalar atualizações**. Na **confirmar atualizações regulares** painel, reveja os pré-requisitos para concluir antes de aplicar atualizações. Selecione a caixa de verificação para indicar que está pronto para atualizar o dispositivo e, em seguida, clique em **instalar**.

    ![Clique em atualizações do dispositivo](./media/storsimple-8000-install-update5-via-portal/update4.png)

6. Um conjunto de verificações de pré-requisitos é iniciado. Estas verificações incluem:
   
   * **Verificações do estado de funcionamento do controlador** para verificar se os dois controladores do dispositivo estão em bom estado de funcionamento e online.
   * **Verificações do estado de funcionamento do componente de hardware** para verificar se todos os componentes de hardware do seu dispositivo StorSimple estão em bom estado de funcionamento.
   * **Verificações de DATA 0** para verificar se DATA 0 está ativado no seu dispositivo. Se esta interface não estiver ativada, terá de ativá-la e, em seguida, tentar novamente.

     A atualização é transferida e instalada apenas se todas as verificações forem concluídas com êxito. Será notificado quando as verificações estiverem em curso. Se o prechecks falharem, em seguida, será fornecido com os motivos da falha. Resolver esses problemas e, em seguida, repita a operação. Poderá ter de contactar o Suporte da Microsoft se não conseguir resolver estes problemas.

7. Depois dos prechecks forem concluídas com êxito, é criada uma tarefa de atualização. Será notificado quando a tarefa de atualização for criada com êxito.
   
    ![Criação de tarefa de atualização](./media/storsimple-8000-install-update5-via-portal/update6.png)
   
    A atualização é então aplicada ao seu dispositivo.

9. A atualização demora algumas horas a ser concluída. Selecione a tarefa de atualização e clique em **Detalhes** para ver os detalhes da tarefa em qualquer altura.

    ![Criação de tarefa de atualização](./media/storsimple-8000-install-update5-via-portal/update8.png)

     Também pode monitorizar o progresso da tarefa de atualização **definições do dispositivo > tarefas**. Sobre o **tarefas** painel, pode ver o progresso da atualização.

     ![Criação de tarefa de atualização](./media/storsimple-8000-install-update5-via-portal/update7.png)

10. Depois da tarefa estiver concluída, navegue para o **definições do dispositivo > atualizações do dispositivo**. Agora deve ser atualizada a versão do software.

