---
title: Palavra-passe de administrador do dispositivo de StorSimple Virtual Array de alteração | Documentos da Microsoft
description: Descreve como utilizar o portal do Azure ou a IU da web do StorSimple Virtual Array para alterar a palavra-passe de administrador do dispositivo.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5308badf439254062a8aefca1840eb21bc234ace
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580413"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Alterar a senha de administrador de dispositivo do StorSimple Virtual Array através do Gestor de dispositivos do StorSimple

## <a name="overview"></a>Descrição geral

Quando utiliza a interface do Windows PowerShell para aceder a matriz Virtual StorSimple, tem de introduzir uma palavra-passe de administrador do dispositivo. Quando o dispositivo StorSimple em primeiro lugar é aprovisionado e iniciado, a palavra-passe predefinido é *Password1*. Para garantir a segurança dos seus dados, a palavra-passe predefinida expira a primeira vez que iniciar sessão e tem de alterar esta palavra-passe.

Também pode utilizar a IU da web local ou o portal do Azure para alterar a palavra-passe de administrador do dispositivo em qualquer altura depois do dispositivo é implementado no seu ambiente de produção. Cada um desses procedimentos é descrito neste artigo.

 ![Painel de dispositivos](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Utilizar o portal do Azure para alterar a palavra-passe

Execute os seguintes passos para alterar a palavra-passe de administrador do dispositivo através do portal do Azure.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Para alterar a palavra-passe de administrador do dispositivo através do portal do Azure

1. Na página de aterrissagem do serviço, selecione o seu serviço, faça duplo clique o nome do serviço e, em seguida, dentro da **gerenciamento** secção, clique em **dispositivos**. Esta ação abre o **dispositivos** painel que apresenta uma lista de todos os seus dispositivos do StorSimple Virtual Array.

2. Na **dispositivos** painel, faça duplo clique no dispositivo que requer uma alteração de palavra-passe.

3. Na **configurações** painel para o seu dispositivo, clique em **segurança**.

4. Na **definições de segurança** painel, faça o seguinte:
   
   1. Desloque para baixo para o **palavra-passe de administrador do dispositivo** secção. Forneça uma palavra-passe de administrador que contém a partir de 8 a 15 carateres.
   2. Confirme a palavra-passe.
   3. Clique em **Guardar**, na parte superior do painel.

A palavra-passe de administrador do dispositivo foi atualizada. Pode utilizar esta palavra-passe modificado para aceder ao dispositivo localmente.

![Painel de definições de segurança](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Utilize a IU web local para alterar a palavra-passe

Execute os seguintes passos para alterar a palavra-passe de administrador do dispositivo através da IU da web local.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Para alterar a palavra-passe de administrador do dispositivo através da IU da web local

1. Na IU da web local, clique em **manutenção** > **alteração de palavra-passe** para o seu dispositivo.
   
    ![alterar password1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Introduza o **palavra-passe atual**.
3. Fornecer um **nova palavra-passe**. A palavra-passe tem de ser, pelo menos, 8 carateres de comprimento. Tem de conter 3 de 4 dos seguintes procedimentos: carateres em maiúsculas, minúsculas, numéricos e especiais.
   
    Tenha em atenção que a palavra-passe não pode ser o mesmo que as últimas 24 palavras-passe.
4. Reintroduza a palavra-passe para a confirmar.
   
    ![alterar password2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. Na parte inferior da página, clique em **aplicar**. Agora é aplicada a nova palavra-passe. Se a alteração de palavra-passe não for bem sucedida, verá o seguinte erro:
   
    ![Erro de palavra-passe](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    Depois da palavra-passe foi atualizada com êxito, será notificado. Em seguida, pode utilizar esta palavra-passe modificado para aceder ao dispositivo localmente.


## <a name="next-steps"></a>Passos Seguintes
Saiba como [administrar a sua matriz Virtual StorSimple](storsimple-ova-web-ui-admin.md).

