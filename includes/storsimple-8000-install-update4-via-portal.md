---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a3ccf76b2722c04a9353fcc7020ff1387bc454c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67184462"
---
#### <a name="to-install-an-update-from-the-azure-portal"></a>Para instalar uma atualização a partir do portal do Azure

1. Na página do serviço StorSimple, selecione o seu dispositivo.

    ![Selecione dispositivo](./media/storsimple-8000-install-update4-via-portal/update1.png)

2. Navegar para **as atualizações** > do**dispositivo**.

    ![Clique em atualizações do Dispositivo](./media/storsimple-8000-install-update4-via-portal/update2.png)

2. Uma notificação aparece se novas atualizações estiverem disponíveis. Em alternativa, na lâmina de atualizações do **Dispositivo,** clique em **Atualizações de Digitalização**. É criada uma tarefa para procurar as atualizações disponíveis. É notificado quando a tarefa for concluída com êxito.

    ![Clique em atualizações do Dispositivo](./media/storsimple-8000-install-update4-via-portal/update3.png)

3. Recomendamos que reveja as notas de versão antes de aplicar uma atualização ao seu dispositivo. Para aplicar atualizações, clique em **instalar atualizações**. Na lâmina **de atualizações regulares do Confirm,** reveja os pré-requisitos para completar antes de aplicar atualizações. Selecione a caixa de verificação para indicar que está pronto para atualizar o dispositivo e, em seguida, clique em **Instalar**.

    ![Clique em atualizações do Dispositivo](./media/storsimple-8000-install-update4-via-portal/update4.png)

6. Um conjunto de verificações de pré-requisitos é iniciado. Estas verificações incluem:
   
   * **Verificações do estado de funcionamento do controlador** para verificar se os dois controladores do dispositivo estão em bom estado de funcionamento e online.
   * **Verificações do estado de funcionamento do componente de hardware** para verificar se todos os componentes de hardware do seu dispositivo StorSimple estão em bom estado de funcionamento.
   * **Verificações de DATA 0** para verificar se DATA 0 está ativado no seu dispositivo. Se esta interface não estiver ativada, terá de ativá-la e, em seguida, tentar novamente.

     A atualização só é descarregada e instalada se todas as verificações estiverem concluídas com sucesso. Será notificado quando as verificações estiverem em curso. Se os pré-controlos falharem, receberão as razões da falha. Abordar estas questões e, em seguida, voltar a tentar a operação. Poderá ter de contactar o Suporte da Microsoft se não conseguir resolver estes problemas.

7. Após a conclusão dos pré-controlos, é criado um trabalho de atualização. Será notificado quando a tarefa de atualização for criada com êxito.
   
    ![Criação de tarefa de atualização](./media/storsimple-8000-install-update4-via-portal/update6.png)
   
    A atualização é então aplicada ao seu dispositivo.

9. A atualização demora algumas horas a ser concluída. Selecione a tarefa de atualização e clique em **Detalhes** para ver os detalhes da tarefa em qualquer altura.

    ![Criação de tarefa de atualização](./media/storsimple-8000-install-update4-via-portal/update8.png)

     Também pode monitorizar o andamento do trabalho de atualização a partir das **definições**do dispositivo > Jobs . Na lâmina **Jobs,** pode ver o progresso da atualização.

     ![Criação de tarefa de atualização](./media/storsimple-8000-install-update4-via-portal/update7.png)

10. Depois de concluída a função, navegue para as definições do **Dispositivo > atualizações**do Dispositivo . A versão do software deve agora ser atualizada.

   ![Criação de tarefa de atualização](./media/storsimple-8000-install-update4-via-portal/update9.png)

