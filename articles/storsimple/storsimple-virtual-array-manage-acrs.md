---
title: Gerir registos de controlo de acesso para | de Matriz Virtual StorSimple Microsoft Docs
description: Descreve como gerir os registos de controlo de acesso (ACRs) para determinar quais os anfitriões que podem ligar-se a um volume no StorSimple Virtual Array.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bad0d7adfd77dff53b1582e63a91f2cd87a9233d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85507624"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Utilize o Gestor de Dispositivos StorSimple para gerir os registos de controlo de acesso para o StorSimple Virtual Array

## <a name="overview"></a>Descrição Geral

Os registos de controlo de acesso (ACRs) permitem-lhe especificar quais os anfitriões que podem ligar-se a um volume no StorSimple Virtual Array (também conhecido como dispositivo virtual StorSimple no local). Os ACRs são definidos para um volume específico e contêm os nomes qualificados iSCSI (IQNs) dos anfitriões. Quando um hospedeiro tenta ligar-se a um volume, o dispositivo verifica o ACR associado a esse volume para o nome IQN, e se houver uma correspondência, então a ligação é estabelecida. O **controlo de acesso regista** a lâmina dentro da secção de **Configuração** do seu serviço Desío, mostra todos os registos de controlo de acesso com as QINs correspondentes dos anfitriões.

![Gerir registos de controlo de acessos](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

Este tutorial explica as seguintes tarefas comuns relacionadas com ACR:

* Obtenha o IQN
* Adicione um registo de controlo de acesso
* Editar um registo de controlo de acesso
* Apagar um registo de controlo de acesso

> [!IMPORTANT]
> 
> * Ao atribuir um ACR a um volume, tenha cuidado para que o volume não seja acedido simultaneamente por mais de um hospedeiro não agrupado, porque isso poderia corromper o volume.
> * Ao eliminar um ACR de um volume, certifique-se de que o anfitrião correspondente não está a aceder ao volume porque a eliminação pode resultar numa interrupção da leitura.


## <a name="get-the-iqn"></a>Obtenha o IQN

Execute os seguintes passos para obter o IQN de um anfitrião Windows que está a executar o Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Adicione um ACR

Utiliza a lâmina **de registos de controlo** de acesso dentro da secção de **configuração** do seu serviço StorSimple Device Manager para adicionar ACRs. Normalmente, associa-se um ACR a um volume.

Para obter informações sobre a associação de um ACR a um volume, vá [adicionar um volume](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Ao atribuir um ACR a um volume, tenha cuidado para que o volume não seja acedido simultaneamente por mais de um hospedeiro não agrupado, porque isso poderia corromper o volume.


Execute os seguintes passos para adicionar um ACR.

#### <a name="to-add-an-acr"></a>Para adicionar um ACR

1. Na página de aterragem de serviço, selecione o seu serviço, clique duas vezes no nome do serviço e, em seguida, na secção **Configuração,** clique nos **registos de controlo de acesso**.
2. Na lâmina de **registos de controlo de acesso,** clique em **Adicionar**.
3. Na lâmina **Add ACR,** faça o seguinte:
   
    1. Forneça um **Nome** para o ACR.
    
    2. Em **nome do iniciador iSCSI,** forneça o nome IQN do seu anfitrião Windows. Para obter o IQN do seu anfitrião Windows Server, faça o seguinte:
   
    3. Inicie o iniciador do Microsoft iSCSI no anfitrião do Windows. Na janela Propriedades do Iniciador iSCSI, no separador **Configuração**, selecione e copie a cadeia do campo **Nome do Iniciador**.
    Cole esta corda no campo **IQN** na lâmina **Add ACR.**
   
    6. Clique **em Adicionar** para adicionar o ACR.  
   
        ![Adicionar registos de controlo de acesso](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. A listagem tabular é atualizada para refletir esta adição.

## <a name="edit-an-acr"></a>Editar um ACR

Utiliza a lâmina de **registos de controlo de acesso** dentro da secção de **Configuração** do seu serviço Desematado do Gestor de Dispositivos no portal Azure para editar ACRs.

> [!NOTE]
> Não deve modificar um ACR que está atualmente a ser utilizado. Para editar um ACR associado a um volume que está atualmente em uso, deve primeiro retirar o volume offline.


Execute os seguintes passos para editar um ACR.

#### <a name="to-edit-an-acr"></a>Para editar um ACR

1. Na página de aterragem de serviço, selecione o seu serviço, clique duas vezes no nome do serviço e, em seguida, na secção **Configuração,** **registos de controlo de acesso**.
2. Na lâmina de **registos de controlo do Acesso,** a partir da listagem tabular dos registos de controlo de acesso, clique duas vezes no ACR que pretende modificar.
3. Na lâmina de **registos de controlo de acesso editar,** faça o seguinte:
   
    1. Forneça o IQN para o ACR.
   
    2. Clique em **Guardar** na parte superior da lâmina para guardar o ACR modificado. Vê a seguinte mensagem de confirmação:
   
        ![Editar registos de controlo de acessos](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Apagar um registo de controlo de acesso

Utilize a página **de Configuração** no portal Azure para eliminar ACRs.

> [!NOTE]
> 
> * Não deve eliminar um ACR que está atualmente a ser utilizado. Para eliminar um ACR associado a um volume atualmente em uso, deve primeiro retirar o volume offline.
> * Ao eliminar um ACR de um volume, certifique-se de que o anfitrião correspondente não está a aceder ao volume porque a eliminação pode resultar numa interrupção da leitura.


Execute os seguintes passos para eliminar um registo de controlo de acesso.

#### <a name="to-delete-an-access-control-record"></a>Para apagar um registo de controlo de acesso

1. Na página de aterragem de serviço, selecione o seu serviço, clique duas vezes no nome do serviço e, em seguida, na secção **Configuração,** **registos de controlo de acesso**.

2. Na lâmina de **registos de controlo de acesso,** a partir da listagem tabular dos registos de controlo de acesso, clique duas vezes no ACR que pretende eliminar.

3. Na lâmina de registos de controlo de acesso editar, clique em **Eliminar**.
   
    ![Eliminar ACRS](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Quando solicitado para confirmação, clique em **Eliminar** para continuar com a eliminação. A listagem tabular é atualizada para refletir a eliminação.
   
   ![Mensagem de aviso](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a adição de volumes e configurar ACRs](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

