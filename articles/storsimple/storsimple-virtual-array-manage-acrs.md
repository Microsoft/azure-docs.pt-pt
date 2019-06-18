---
title: Gerir registos de controlo de acesso do StorSimple Virtual Array | Documentos da Microsoft
description: Descreve como gerir registos de controlo de acesso (ACRs) para determinar os anfitriões que podem ligar-se a um volume numa matriz Virtual StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1dfc1b0e0576402624bfe62de0e206d9bd7cd1b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64724430"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Utilize o StorSimple Device Manager para gerir os registos de controlo de acesso do StorSimple Virtual Array

## <a name="overview"></a>Descrição geral

Registos de controlo de acesso (ACRs) permitem-lhe especificar os anfitriões que podem ligar-se a um volume numa matriz Virtual StorSimple (também conhecido como o local dispositivo virtual StorSimple). ACRs estão definidos para um volume específico e contenham os nomes de qualificado iSCSI (IQNs) dos anfitriões. Quando um anfitrião tenta estabelecer ligação a um volume, o dispositivo verifica o ACR associado a esse volume para o nome do IQN e, se existir uma correspondência, a ligação ser estabelecida. O **registos de controlo de acesso** painel dentro do **configuração** secção do seu serviço de Gestor de dispositivos mostra todos os registos de controlo de acesso com os IQNs correspondentes dos anfitriões.

![Gerir registos de controlo de acesso](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

Este tutorial explica as seguintes tarefas de relacionados com o ACR comuns:

* Obter o IQN
* Adicionar um registo de controlo de acesso
* Editar um registo de controlo de acesso
* Eliminar um registo de controlo de acesso

> [!IMPORTANT]
> 
> * Ao atribuir um ACR para um volume, tenha cuidado que o volume não for simultaneamente acedido por mais de um anfitrião não agrupado porque isso poderia danificar o volume.
> * Ao eliminar um ACR de um volume, certifique-se de que o anfitrião correspondente não está acessando o volume porque a eliminação pode resultar numa interrupção de leitura / escrita.


## <a name="get-the-iqn"></a>Obter o IQN

Execute os passos seguintes para obter o IQN de um anfitrião do Windows que está a executar o Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Adicionar um ACR

Utilizar **registos de controlo de acesso** painel no **configuração** secção do seu serviço StorSimple Device Manager para adicionar ACRs. Normalmente, um ACR associar um volume.

Para informações sobre a associação de um ACR com um volume, aceda a [adicionar um volume](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Ao atribuir um ACR para um volume, tenha cuidado que o volume não for simultaneamente acedido por mais de um anfitrião não agrupado porque isso poderia danificar o volume.


Execute os seguintes passos para adicionar um ACR.

#### <a name="to-add-an-acr"></a>Para adicionar um ACR

1. Na página de aterrissagem do serviço, selecione o seu serviço, faça duplo clique o nome do serviço e, em seguida, dentro da **Configuration** secção, clique em **registos de controlo de acesso**.
2. Na **registos de controlo de acesso** painel, clique em **Add**.
3. Na **adicionar ACR** painel, faça o seguinte:
   
    1. Forneça um **Nome** para o ACR.
    
    2. Sob **nome do iniciador iSCSI**, forneça o nome do IQN de anfitrião do Windows. Para obter o IQN de anfitrião do Windows Server, efetue o seguinte:
   
    3. Inicie o iniciador do Microsoft iSCSI no anfitrião do Windows. Na janela de propriedades do iniciador iSCSI, sobre o **configuração** separador, selecione e copie a cadeia do **nome do iniciador** campo.
    Cole a seguinte cadeia no **IQN** campo na **adicionar ACR** painel.
   
    6. Clique em **adicionar** para adicionar o ACR.  
   
        ![Adicionar registos de controlo de acesso](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. Da lista tabular é atualizada para refletir esta adição.

## <a name="edit-an-acr"></a>Editar um ACR

Utilizar o **registos de controlo de acesso** painel no **configuração** secção do seu serviço de Gestor de dispositivos no portal do Azure para editar ACRs.

> [!NOTE]
> Não deve modificar um ACR que está atualmente em utilização. Para editar um ACR associado a um volume que está atualmente em utilização, deve primeiro coloque o volume offline.


Execute os seguintes passos para editar um ACR.

#### <a name="to-edit-an-acr"></a>Para editar um ACR

1. Na página de aterrissagem do serviço, selecione o seu serviço, faça duplo clique o nome do serviço e, em seguida, dentro da **Configuration** secção, **registos de controlo de acesso**.
2. Na **registos de controlo de acesso** painel, na lista tabular dos registos de controlo de acesso, clique duas vezes o ACR que deseja modificar.
3. Na **registos de controlo de acesso de edição** painel, faça o seguinte:
   
    1. Fornece o IQN para o ACR.
   
    2. Clique em **guardar** na parte superior do painel para guardar o ACR modificado. Verá a seguinte mensagem de confirmação:
   
        ![Editar registos de controlo de acesso](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Eliminar um registo de controlo de acesso

Utilizar o **configuração** página no portal do Azure para eliminar ACRs.

> [!NOTE]
> 
> * Não deve excluir um ACR que está atualmente em utilização. Para eliminar um ACR associado a um volume que está atualmente em utilização, deve primeiro coloque o volume offline.
> * Ao eliminar um ACR de um volume, certifique-se de que o anfitrião correspondente não está acessando o volume porque a eliminação pode resultar numa interrupção de leitura / escrita.


Execute os passos seguintes para eliminar um registo de controlo de acesso.

#### <a name="to-delete-an-access-control-record"></a>Para eliminar um registo de controlo de acesso

1. Na página de aterrissagem do serviço, selecione o seu serviço, faça duplo clique o nome do serviço e, em seguida, dentro da **Configuration** secção, **registos de controlo de acesso**.

2. Na **registos de controlo de acesso** painel, na lista tabular dos registos de controlo de acesso, clique duas vezes o ACR que pretende eliminar.

3. No painel de registos de controlo do acesso de edição, clique em **eliminar**.
   
    ![Eliminar ACRS](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Quando lhe for pedida confirmação, clique em **eliminar** para continuar com a eliminação. Da lista tabular é atualizada para refletir a eliminação.
   
   ![Mensagem de aviso](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [adicionar volumes e configurar ACRs](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

