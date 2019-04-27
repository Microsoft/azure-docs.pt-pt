---
title: Gerir registos de controlo de acesso no StorSimple | Documentos da Microsoft
description: Descreve como utilizar os registos de controlo de acesso (ACRs) para determinar os anfitriões que podem ligar-se para um volume no dispositivo StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: ade7da25d2307a382c17e7a3cbb26b601c34ef78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60321625"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Utilizar o serviço StorSimple Manager para gerir os registos de controlo de acesso

## <a name="overview"></a>Descrição geral
Registos de controlo de acesso (ACRs) permitem-lhe especificar os anfitriões que podem ligar-se para um volume no dispositivo StorSimple. ACRs estão definidos para um volume específico e contenham os nomes de qualificado iSCSI (IQNs) dos anfitriões. Quando um anfitrião tenta estabelecer ligação a um volume, o dispositivo verifica o ACR associados a esse volume para o nome do IQN e se existe uma correspondência, a ligação é estabelecida. O controlo de acesso se regista na **configuração** seção do painel de serviço do StorSimple Device Manager apresentar todos os registos de controlo de acesso com os IQNs correspondentes dos anfitriões.

Este tutorial explica as seguintes tarefas de relacionados com o ACR comuns:

* Adicionar um registo de controlo de acesso
* Editar um registo de controlo de acesso
* Eliminar um registo de controlo de acesso

> [!IMPORTANT]
> * Ao atribuir um ACR para um volume, tenha cuidado que o volume não for simultaneamente acedido por mais de um anfitrião não agrupado porque isso poderia danificar o volume.
> * Ao eliminar um ACR de um volume, certifique-se de que o anfitrião correspondente não está acessando o volume porque a eliminação pode resultar numa interrupção de leitura / escrita.

## <a name="get-the-iqn"></a>Obter o IQN

Execute os passos seguintes para obter o IQN de um anfitrião do Windows que está a executar o Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Adicionar um registo de controlo de acesso
Utilizar o **configuração** secção no painel do serviço StorSimple Device Manager para adicionar ACRs. Normalmente, irá associar um ACR com um volume.

Execute os seguintes passos para adicionar um ACR.

#### <a name="to-add-an-acr"></a>Para adicionar um ACR

1. Aceda ao seu serviço StorSimple Device Manager, faça duplo clique o nome do serviço e, em seguida, dentro da **Configuration** secção, clique em **registos de controlo de acesso**.
2. Na **registos de controlo de acesso** painel, clique em **+ adicionar ACR**.

    ![Clique em Adicionar ACR](./media/storsimple-8000-manage-acrs/createacr1.png)

3. Na **adicionar ACR** painel, siga os passos abaixo:

    1. Forneça um nome para o ACR.
    
    2. Forneça o nome do IQN de anfitrião do Windows Server sob **nome de iniciador (IQN) de iSCSI**.

    3. Clique em **adicionar** para criar o ACR.

        ![Clique em Adicionar ACR](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  O ACR recentemente adicionado será apresentada na lista tabular de ACRs.

    ![Clique em Adicionar ACR](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Editar um registo de controlo de acesso
Utilizar o **configuração** secção no painel do serviço StorSimple Device Manager para editar ACRs.

> [!NOTE]
> Recomenda-se que modificar apenas esses ACRs que não estão atualmente em utilização. Para editar um ACR associado a um volume que está atualmente em utilização, tem primeiro de executar o volume offline.

Execute os seguintes passos para editar um ACR.

#### <a name="to-edit-an-access-control-record"></a>Para editar um registo de controlo de acesso
1.  Aceda ao seu serviço StorSimple Device Manager, faça duplo clique o nome do serviço e, em seguida, dentro da **Configuration** secção, clique em **registos de controlo de acesso**.

    ![Ir para registos de controlo de acesso](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Na lista tabular dos registos de controlo de acesso, clique e selecione o ACR que deseja modificar.

    ![Editar registos de controlo de acesso](./media/storsimple-8000-manage-acrs/editacr1.png)

3. Na **registo de controlo de acesso de edição** painel, fornecer um IQN diferente correspondente para outro anfitrião.

    ![Editar registos de controlo de acesso](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Clique em **Guardar**. Quando lhe for pedida a confirmação, clique em **Sim**. 

    ![Editar registos de controlo de acesso](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Será notificado quando o ACR é atualizado. A listagem em tabela também é atualizado para refletir a alteração.

   
## <a name="delete-an-access-control-record"></a>Eliminar um registo de controlo de acesso
Utilizar o **configuração** secção no painel do serviço StorSimple Device Manager para eliminar ACRs.

> [!NOTE]
> Pode eliminar apenas esses ACRs que não estão atualmente em utilização. Para eliminar um ACR associado a um volume que está atualmente em utilização, tem primeiro de executar o volume offline.

Execute os passos seguintes para eliminar um registo de controlo de acesso.

#### <a name="to-delete-an-access-control-record"></a>Para eliminar um registo de controlo de acesso
1.  Aceda ao seu serviço StorSimple Device Manager, faça duplo clique o nome do serviço e, em seguida, dentro da **Configuration** secção, clique em **registos de controlo de acesso**.

    ![Ir para registos de controlo de acesso](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Na lista tabular dos registos de controlo de acesso, clique e selecione o ACR que pretende eliminar.

    ![Ir para registos de controlo de acesso](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Botão direito do mouse para invocar o menu de contexto e selecione **eliminar**.

    ![Ir para registos de controlo de acesso](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Quando lhe for pedida confirmação, reveja as informações e, em seguida, clique em **eliminar**.

    ![Ir para registos de controlo de acesso](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. Será notificado quando a eliminação estiver concluída. Da lista tabular é atualizada para refletir a eliminação.

    ![Ir para registos de controlo de acesso](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [gerir volumes do StorSimple](storsimple-8000-manage-volumes-u2.md).
* Saiba mais sobre [utilizar o serviço StorSimple Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

