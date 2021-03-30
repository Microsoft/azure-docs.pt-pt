---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 89f7be31514f0b78c3bfb3efd6e6aca14658d5cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93375787"
---
#### <a name="to-create-a-manual-backup"></a>Para criar uma cópia de segurança manual

1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lista tabular de dispositivos, selecione o seu dispositivo. Aceda a **Definições > Gerir > Políticas de cópia de segurança**.

2. O painel **Políticas de cópia de segurança** lista todas as políticas de cópia de segurança em formato tabular, incluindo a política do volume do qual pretende criar cópias de segurança. Selecione a política associada ao volume que pretende criar cópia de segurança e clique com o botão direito para invocar o menu de contexto. Na lista pendente, selecione **Criar cópia de segurança agora**.

    ![Criar uma cópia de segurança manual](./media/storsimple-8000-create-manual-backup/createmanualbu1.png)

3. No painel **Criar cópia de segurança agora**, efetue os seguintes passos:

    1. Escolha o **Tipo de instantâneo** adequado na lista pendente: instantâneo **Local** ou da **Cloud**. Selecione o instantâneo local para cópias de segurança rápidas ou restauros e instantâneos de cloud para resiliência de dados.

        ![Criar backup manual 2](./media/storsimple-8000-create-manual-backup/createmanualbu2.png)

    2. Clique em **OK** para iniciar uma tarefa para criar um instantâneo. Verá uma notificação na parte superior da página quando a tarefa tiver sido criada com êxito.

        ![Criar backup manual 3](./media/storsimple-8000-create-manual-backup/createmanualbu4.png)

    3. Para monitorizar a tarefa, clique na notificação. Esta ação irá encaminhá-lo para o painel **Tarefas** onde pode ver o progresso da tarefa.


5. Uma vez concluída a tarefa da cópia de segurança, aceda ao separador **Catálogo de cópias de segurança**.

6. Defina as seleções de filtro para o dispositivo, a política de cópia de segurança e o intervalo de tempo adequados. A cópia de segurança deverá aparecer na lista de conjuntos de cópias de segurança que é apresentada no catálogo.

