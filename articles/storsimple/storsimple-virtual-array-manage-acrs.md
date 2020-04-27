---
title: Gerir os registos de controlo de acesso para o StorSimple Virtual Array [ StorSimple Virtual Array ] Microsoft Docs
description: Descreve como gerir os registos de controlo de acesso (ACRs) para determinar quais os anfitriões que podem ligar-se a um volume no StorSimple Virtual Array.
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "64724430"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Utilize o StorSimple Device Manager para gerir os registos de controlo de acesso para o StorSimple Virtual Array

## <a name="overview"></a>Descrição geral

Os registos de controlo de acesso (ACRs) permitem especificar quais os anfitriões que podem ligar-se a um volume no StorSimple Virtual Array (também conhecido como dispositivo virtual StorSimple on-premido). Os ACRs são definidos para um volume específico e contêm os nomes iSCSI Qualificados (IQNs) dos anfitriões. Quando um hospedeiro tenta ligar-se a um volume, o dispositivo verifica o ACR associado a esse volume para o nome IQN, e se houver uma correspondência, então a ligação é estabelecida. A lâmina de controlo de **acesso dentro** da secção **de configuração** do seu serviço Device Manager apresenta todos os registos de controlo de acesso com as IQNs correspondentes dos anfitriões.

![Gerir registos de controlo de acesso](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

Este tutorial explica as seguintes tarefas comuns relacionadas com a ACR:

* Obtenha o IQN
* Adicione um registo de controlo de acesso
* Editar um registo de controlo de acesso
* Eliminar um registo de controlo de acesso

> [!IMPORTANT]
> 
> * Ao atribuir um ACR a um volume, tome cuidado para que o volume não seja simultaneamente acedido por mais de um hospedeiro não agrupado, porque isso poderia corromper o volume.
> * Ao apagar um ACR de um volume, certifique-se de que o anfitrião correspondente não está a aceder ao volume porque a eliminação pode resultar numa perturbação de leitura- escrita.


## <a name="get-the-iqn"></a>Obtenha o IQN

Execute os seguintes passos para obter o IQN de um anfitrião do Windows que esteja a executar o Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Adicione um ACR

Utiliza a lâmina de **registos** de controlo de acesso dentro da secção de **configuração** do serviço StorSimple Device Manager para adicionar ACRs. Normalmente, associa-se um ACR a um volume.

Para obter informações sobre a associação de um ACR com um volume, vá [adicionar um volume](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Ao atribuir um ACR a um volume, tome cuidado para que o volume não seja simultaneamente acedido por mais de um hospedeiro não agrupado, porque isso poderia corromper o volume.


Execute os seguintes passos para adicionar um ACR.

#### <a name="to-add-an-acr"></a>Para adicionar um ACR

1. Na página de aterragem de serviço, selecione o seu serviço, clique duas vezes no nome do serviço e, em seguida, dentro da secção **Configuração,** clique nos **registos**de controlo de acesso .
2. Na lâmina de registos de **controlo de acesso,** clique em **Adicionar**.
3. Na lâmina **Add ACR,** faça o seguinte:
   
    1. Forneça um **Nome** para o ACR.
    
    2. Sob o **nome do iniciador iSCSI,** forneça o nome IQN do seu anfitrião Windows. Para obter o IQN do seu anfitrião do Windows Server, faça o seguinte:
   
    3. Inicie o iniciador do Microsoft iSCSI no anfitrião do Windows. Na janela Propriedades do Iniciador iSCSI, no separador **Configuração**, selecione e copie a cadeia do campo **Nome do Iniciador**.
    Colá-lo no campo **IQN** na lâmina **Add ACR.**
   
    6. Clique em **Adicionar** para adicionar o ACR.  
   
        ![Adicionar registos de controlo de acesso](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. A listagem tabular é atualizada para refletir esta adição.

## <a name="edit-an-acr"></a>Editar um ACR

Utiliza a lâmina de **registos** de controlo de acesso dentro da secção **de Configuração** do serviço 'Gestor de Dispositivos' no portal Azure para editar ACRs.

> [!NOTE]
> Não deve modificar um ACR que esteja atualmente a ser utilizado. Para editar um ACR associado a um volume que está atualmente em uso, deve primeiro desligar o volume.


Execute os seguintes passos para editar um ACR.

#### <a name="to-edit-an-acr"></a>Para editar um ACR

1. Na página de aterragem de serviço, selecione o seu serviço, clique duas vezes no nome do serviço e, em seguida, dentro da secção **Configuração,** **Registos**de controlo de acesso .
2. Na lâmina de registos de controlo de **acesso,** a partir da listagem tabular dos registos de controlo de acesso, clique duas vezes no ACR que pretende modificar.
3. Na lâmina de registos de controlo de **acesso Editar,** faça o seguinte:
   
    1. Forneça o IQN para o ACR.
   
    2. Clique em **Guardar** na parte superior da lâmina para guardar o ACR modificado. Veja a seguinte mensagem de confirmação:
   
        ![Editar registos de controlo de acesso](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Eliminar um registo de controlo de acesso

Utilize a página **de Configuração** no portal Azure para eliminar ACRs.

> [!NOTE]
> 
> * Não deve eliminar um ACR que esteja atualmente a ser utilizado. Para eliminar um ACR associado a um volume que está atualmente em uso, deve primeiro desligar o volume.
> * Ao apagar um ACR de um volume, certifique-se de que o anfitrião correspondente não está a aceder ao volume porque a eliminação pode resultar numa perturbação de leitura- escrita.


Execute as seguintes etapas para eliminar um registo de controlo de acesso.

#### <a name="to-delete-an-access-control-record"></a>Para eliminar um registo de controlo de acesso

1. Na página de aterragem de serviço, selecione o seu serviço, clique duas vezes no nome do serviço e, em seguida, dentro da secção **Configuração,** **Registos**de controlo de acesso .

2. Na lâmina de registos de controlo de **acesso,** a partir da listagem tabular dos registos de controlo de acesso, clique duas vezes no ACR que pretende eliminar.

3. Na lâmina de registos de controlo de acesso Editar, clique em **Eliminar**.
   
    ![Eliminar ACRS](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Quando solicitado para confirmação, clique em **Apagar** para continuar com a eliminação. A listagem tabular é atualizada para refletir a eliminação.
   
   ![Mensagem de aviso](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [adicionar volumes e configurar ACRs](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

