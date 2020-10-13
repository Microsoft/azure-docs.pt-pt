---
title: Gerir registos de controlo de acessos em StorSimple / Microsoft Docs
description: Descreve como utilizar registos de controlo de acesso (ACRs) para determinar quais os anfitriões que podem ligar-se a um volume no dispositivo StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: 15c35fb314af27b1ced129a12f752d0a2794e0f8
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949909"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Utilize o serviço StorSimple Manager para gerir registos de controlo de acessos

## <a name="overview"></a>Overview (Descrição geral)
Os registos de controlo de acesso (ACRs) permitem-lhe especificar quais os anfitriões que podem ligar-se a um volume no dispositivo StorSimple. Os ACRs são definidos para um volume específico e contêm os nomes qualificados iSCSI (IQNs) dos anfitriões. Quando um hospedeiro tenta ligar-se a um volume, o dispositivo verifica o ACR associado a esse volume para o nome IQN e se existe uma correspondência, então a ligação é estabelecida. Os registos de controlo de acesso na secção **de Configuração** da sua lâmina de serviço StorSimple Device Manager exibem todos os registos de controlo de acesso com as QINs correspondentes dos anfitriões.

Este tutorial explica as seguintes tarefas comuns relacionadas com ACR:

* Adicione um registo de controlo de acesso
* Editar um registo de controlo de acesso
* Apagar um registo de controlo de acesso

> [!IMPORTANT]
> * Ao atribuir um ACR a um volume, tenha cuidado para que o volume não seja acedido simultaneamente por mais de um hospedeiro não agrupado, porque isso poderia corromper o volume.
> * Ao eliminar um ACR de um volume, certifique-se de que o anfitrião correspondente não está a aceder ao volume porque a eliminação pode resultar numa interrupção da leitura.

## <a name="get-the-iqn"></a>Obtenha o IQN

Execute os seguintes passos para obter o IQN de um anfitrião Windows que está a executar o Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Adicione um registo de controlo de acesso
Utilize a secção **de configuração** na lâmina de serviço StorSimple Device Manager para adicionar ACRs. Normalmente, irá associar um ACR a um volume.

Execute os seguintes passos para adicionar um ACR.

#### <a name="to-add-an-acr"></a>Para adicionar um ACR

1. Aceda ao seu serviço StorSimple Device Manager, clique duas vezes no nome do serviço e, em seguida, na secção **Configuração,** clique nos **registos de controlo de acesso**.
2. Na lâmina de **registos de controlo de acesso,** clique **+ Adicionar ACR**.

    ![Clique em adicionar ACR](./media/storsimple-8000-manage-acrs/createacr1.png)

3. Na lâmina **Add ACR,** faça os seguintes passos:

    1. Forneça um nome para o seu ACR.
    
    2. Forneça o nome IQN do seu anfitrião do Windows Server sob **o nome do iniciador iSCSI (IQN)**.

    3. Clique **em Adicionar** para criar o ACR.

        ![Clique em adicionar ACR 2](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  O ACR recém-adicionado será exibido na listagem tabular de ACRs.

    ![Clique em adicionar ACR 3](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Editar um registo de controlo de acesso
Utilize a secção **de configuração** na lâmina de serviço StorSimple Device Manager para editar ACRs.

> [!NOTE]
> Recomenda-se que modifique apenas os ACRs que não estão atualmente a ser utilizados. Para editar um ACR associado a um volume que está atualmente em uso, tem primeiro de tirar o volume offline.

Execute os seguintes passos para editar um ACR.

#### <a name="to-edit-an-access-control-record"></a>Para editar um registo de controlo de acesso
1.  Aceda ao seu serviço StorSimple Device Manager, clique duas vezes no nome do serviço e, em seguida, na secção **Configuração,** clique nos **registos de controlo de acesso**.

    ![Ir para registos de controlo de acesso](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Na listagem tabular dos registos de controlo de acesso, clique e selecione o ACR que pretende modificar.

    ![Editar registos de controlo de acessos](./media/storsimple-8000-manage-acrs/editacr1.png)

3. Na lâmina de **registo de controlo de acesso editar,** forneça um IQN diferente correspondente a outro hospedeiro.

    ![Editar registos de controlo de acesso 2](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Clique em **Guardar**. Quando lhe for pedida a confirmação, clique em **Sim**. 

    ![Editar registos de controlo de acesso 3](./media/storsimple-8000-manage-acrs/editacr3.png)

5. É notificado quando o ACR for atualizado. A listagem tabular também atualiza para refletir a mudança.

   
## <a name="delete-an-access-control-record"></a>Apagar um registo de controlo de acesso
Utilize a secção **de configuração** na lâmina de serviço StorSimple Device Manager para eliminar ACRs.

> [!NOTE]
> Só pode eliminar os ACRs que não estão atualmente a ser utilizados. Para eliminar um ACR associado a um volume atualmente em uso, tem primeiro de retirar o volume offline.

Execute os seguintes passos para eliminar um registo de controlo de acesso.

#### <a name="to-delete-an-access-control-record"></a>Para apagar um registo de controlo de acesso
1.  Aceda ao seu serviço StorSimple Device Manager, clique duas vezes no nome do serviço e, em seguida, na secção **Configuração,** clique nos **registos de controlo de acesso**.

    ![Aceda aos registos de controlo de acesso 1b](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Na listagem tabular dos registos de controlo de acesso, clique e selecione o ACR que pretende eliminar.

    ![Aceda aos registos de controlo de acesso 2](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Clique com o botão direito para invocar o menu de contexto e selecione **Delete**.

    ![Aceda aos registos de controlo de acesso 3](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Quando solicitado para confirmação, reveja as informações e, em seguida, clique em **Eliminar**.

    ![Aceda aos registos de controlo de acesso 4](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. É notificado quando a eliminação terminar. A listagem tabular é atualizada para refletir a eliminação.

    ![Aceda aos registos de controlo de acesso 5](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [a gestão de volumes StorSimple](storsimple-8000-manage-volumes-u2.md).
* Saiba mais sobre [a utilização do serviço StorSimple Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

