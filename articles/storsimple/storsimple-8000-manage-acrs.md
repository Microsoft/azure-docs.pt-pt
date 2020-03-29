---
title: Gerir registos de controlo de acesso no StorSimple [ Microsoft Docs
description: Descreve como utilizar registos de controlo de acesso (ACRs) para determinar quais os anfitriões que podem ligar-se a um volume no dispositivo StorSimple.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "64693243"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Utilize o serviço StorSimple Manager para gerir registos de controlo de acesso

## <a name="overview"></a>Descrição geral
Os registos de controlo de acesso (ACRs) permitem especificar quais os anfitriões que podem ligar-se a um volume no dispositivo StorSimple. Os ACRs são definidos para um volume específico e contêm os nomes iSCSI Qualificados (IQNs) dos anfitriões. Quando um hospedeiro tenta ligar-se a um volume, o dispositivo verifica o ACR associado a esse volume para o nome IQN e se houver uma correspondência, então a ligação é estabelecida. Os registos de controlo de acesso na secção **de configuração** da sua lâmina de serviço StorSimple Device Manager exibem todos os registos de controlo de acesso com os QI correspondentes dos anfitriões.

Este tutorial explica as seguintes tarefas comuns relacionadas com a ACR:

* Adicione um registo de controlo de acesso
* Editar um registo de controlo de acesso
* Eliminar um registo de controlo de acesso

> [!IMPORTANT]
> * Ao atribuir um ACR a um volume, tome cuidado para que o volume não seja simultaneamente acedido por mais de um hospedeiro não agrupado, porque isso poderia corromper o volume.
> * Ao apagar um ACR de um volume, certifique-se de que o anfitrião correspondente não está a aceder ao volume porque a eliminação pode resultar numa perturbação de leitura- escrita.

## <a name="get-the-iqn"></a>Obtenha o IQN

Execute os seguintes passos para obter o IQN de um anfitrião do Windows que esteja a executar o Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Adicione um registo de controlo de acesso
Utiliza a secção **de Configuração** na lâmina de serviço StorSimple Device Manager para adicionar ACRs. Normalmente, você irá associar um ACR com um volume.

Execute os seguintes passos para adicionar um ACR.

#### <a name="to-add-an-acr"></a>Para adicionar um ACR

1. Aceda ao serviço StorSimple Device Manager, clique duas vezes no nome do serviço e, em seguida, dentro da secção **Configuração,** clique nos **registos**de controlo de acesso .
2. Na lâmina de registos de **controlo de acesso,** clique **em + Adicionar ACR**.

    ![Clique em adicionar ACR](./media/storsimple-8000-manage-acrs/createacr1.png)

3. Na lâmina **Add ACR,** faça os seguintes passos:

    1. Forneça um nome para o seu ACR.
    
    2. Forneça o nome IQN do seu anfitrião do Servidor Windows sob o **nome iSCSI iniciante (IQN)**.

    3. Clique em **Adicionar** para criar o ACR.

        ![Clique em adicionar ACR](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  O Recém-adicionado ACR irá exibir na listagem tabular de ACRs.

    ![Clique em adicionar ACR](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Editar um registo de controlo de acesso
Utiliza a secção **de Configuração** na lâmina de serviço StorSimple Device Manager para editar ACRs.

> [!NOTE]
> Recomenda-se que modifique apenas os ACRs que não estão atualmente a ser utilizados. Para editar um ACR associado a um volume que está atualmente em uso, tem primeiro de desligar o volume.

Execute os seguintes passos para editar um ACR.

#### <a name="to-edit-an-access-control-record"></a>Para editar um registo de controlo de acesso
1.  Aceda ao serviço StorSimple Device Manager, clique duas vezes no nome do serviço e, em seguida, dentro da secção **Configuração,** clique nos **registos**de controlo de acesso .

    ![Vá a registos de controlo de acesso](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Na listagem tabular dos registos de controlo de acesso, clique e selecione o ACR que pretende modificar.

    ![Editar registos de controlo de acesso](./media/storsimple-8000-manage-acrs/editacr1.png)

3. Na lâmina de registo de controlo de **acesso Editar,** forneça um QIN diferente correspondente a outro hospedeiro.

    ![Editar registos de controlo de acesso](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Clique em **Guardar**. Quando lhe for pedida a confirmação, clique em **Sim**. 

    ![Editar registos de controlo de acesso](./media/storsimple-8000-manage-acrs/editacr3.png)

5. É notificado quando o ACR é atualizado. A listagem tabular também atualiza para refletir a mudança.

   
## <a name="delete-an-access-control-record"></a>Eliminar um registo de controlo de acesso
Utilize a secção **de Configuração** na lâmina de serviço StorSimple Device Manager para eliminar ACRs.

> [!NOTE]
> Só pode eliminar os ACRs que não estão atualmente a ser utilizados. Para eliminar um ACR associado a um volume que está atualmente em uso, tem primeiro de desligar o volume.

Execute as seguintes etapas para eliminar um registo de controlo de acesso.

#### <a name="to-delete-an-access-control-record"></a>Para eliminar um registo de controlo de acesso
1.  Aceda ao serviço StorSimple Device Manager, clique duas vezes no nome do serviço e, em seguida, dentro da secção **Configuração,** clique nos **registos**de controlo de acesso .

    ![Vá a registos de controlo de acesso](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Na listagem tabular dos registos de controlo de acesso, clique e selecione o ACR que pretende eliminar.

    ![Vá a registos de controlo de acesso](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Clique à direita para invocar o menu de contexto e **selecione Eliminar**.

    ![Vá a registos de controlo de acesso](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Quando solicitado para confirmação, reveja as informações e clique em **Eliminar**.

    ![Vá a registos de controlo de acesso](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. É notificado quando a supressão terminar. A listagem tabular é atualizada para refletir a eliminação.

    ![Vá a registos de controlo de acesso](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a gestão de [volumes StorSimple](storsimple-8000-manage-volumes-u2.md).
* Saiba mais sobre [a utilização do serviço StorSimple Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

