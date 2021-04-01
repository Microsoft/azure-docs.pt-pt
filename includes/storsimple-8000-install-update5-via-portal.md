---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9832a961562930e61e94b28ed78976fd9f8c6f78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93375886"
---
#### <a name="to-install-an-update-from-the-azure-portal"></a>Para instalar uma atualização a partir do portal do Azure

1. Na página do serviço StorSimple, selecione o seu dispositivo.

    ![Selecione dispositivo](./media/storsimple-8000-install-update5-via-portal/update1.png)

2. Navegue para **as definições do dispositivo** As  >  **atualizações do dispositivo**.

    ![Clique em atualizações do dispositivo](./media/storsimple-8000-install-update5-via-portal/update2.png)

2. Uma notificação aparece se novas atualizações estiverem disponíveis. Em alternativa, na lâmina de atualização do **dispositivo,** clique em **'Actualizar' 'Scan's**. É criada uma tarefa para procurar as atualizações disponíveis. É notificado quando a tarefa for concluída com êxito.

    ![Clique em atualizações do dispositivo 2](./media/storsimple-8000-install-update5-via-portal/update3.png)

3. Recomendamos que reveja as notas de versão antes de aplicar uma atualização ao seu dispositivo. Para aplicar atualizações, clique **em Instalar atualizações**. Na lâmina **de atualizações regulares Confirm,** reveja os pré-requisitos para completar antes de aplicar atualizações. Selecione a caixa de verificação para indicar que está pronto para atualizar o dispositivo e, em seguida, clique em **Instalar**.

    ![Clique em atualizações do dispositivo 3](./media/storsimple-8000-install-update5-via-portal/update4.png)

6. Um conjunto de verificações de pré-requisitos é iniciado. Estas verificações incluem:
   
   * **Verificações do estado de funcionamento do controlador** para verificar se os dois controladores do dispositivo estão em bom estado de funcionamento e online.
   * **Verificações do estado de funcionamento do componente de hardware** para verificar se todos os componentes de hardware do seu dispositivo StorSimple estão em bom estado de funcionamento.
   * **Verificações de DATA 0** para verificar se DATA 0 está ativado no seu dispositivo. Se esta interface não estiver ativada, terá de ativá-la e, em seguida, tentar novamente.

     A atualização é descarregada e instalada apenas se todas as verificações forem concluídas com sucesso. Será notificado quando as verificações estiverem em curso. Se os pré-verificações falharem, serão fornecidos os motivos do fracasso. Resolva estas questões e, em seguida, re-tentar a operação. Poderá ter de contactar o Suporte da Microsoft se não conseguir resolver estes problemas.

7. Após a conclusão dos pré-testes com sucesso, é criado um trabalho de atualização. Será notificado quando a tarefa de atualização for criada com êxito.
   
    ![Criação de tarefa de atualização](./media/storsimple-8000-install-update5-via-portal/update6.png)
   
    A atualização é então aplicada ao seu dispositivo.

9. A atualização demora algumas horas a ser concluída. Selecione a tarefa de atualização e clique em **Detalhes** para ver os detalhes da tarefa em qualquer altura.

    ![Atualizar criação de emprego 2](./media/storsimple-8000-install-update5-via-portal/update8.png)

     Também pode monitorizar o progresso do trabalho de atualização a partir das **definições** do Dispositivo > Jobs . Na lâmina **Jobs,** pode ver o progresso da atualização.

     ![Atualizar criação de emprego 3](./media/storsimple-8000-install-update5-via-portal/update7.png)

10. Depois de concluído o trabalho, navegue para as definições do **dispositivo > atualizações do dispositivo**. A versão do software deve agora ser atualizada.

