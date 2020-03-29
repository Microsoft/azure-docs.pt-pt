---
title: Alterar a senha de administração do dispositivo StorSimple Virtual Array / Microsoft Docs
description: Descreve como usar o portal Azure ou o StorSimple Virtual Array web UI para alterar a palavra-passe do administrador do dispositivo.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60580413"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Alterar a palavra-passe do administrador do dispositivo StorSimple Virtual Array através do StorSimple Device Manager

## <a name="overview"></a>Descrição geral

Quando utilizar a interface Windows PowerShell para aceder ao StorSimple Virtual Array, é necessário introduzir uma palavra-passe do administrador do dispositivo. Quando o dispositivo StorSimple é aprovisionado e iniciado, a palavra-passe por defeito é *Password1*. Para a segurança dos seus dados, a palavra-passe predefinida expira na primeira vez que faz o insessão e é obrigado a alterar esta palavra-passe.

Também pode utilizar a UI web local ou o portal Azure para alterar a palavra-passe do administrador do dispositivo a qualquer momento após a implementação do dispositivo no seu ambiente de produção. Cada um destes procedimentos é descrito neste artigo.

 ![lâmina dispositivos](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Use o portal Azure para alterar a palavra-passe

Execute os seguintes passos para alterar a palavra-passe do administrador do dispositivo através do portal Azure.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Para alterar a palavra-passe do administrador do dispositivo através do portal Azure

1. Na página de aterragem de serviço, selecione o seu serviço, clique duas vezes no nome do serviço e, em seguida, dentro da secção **Gestão,** clique em **Dispositivos**. Isto abre a lâmina **de Dispositivos** que lista todos os seus dispositivos StorSimple Virtual Array.

2. Na lâmina **dispositivos,** clique duas vezes no dispositivo que requer uma alteração de senha.

3. Na lâmina **Definições** para o seu dispositivo, clique em **Segurança**.

4. Na lâmina definições de **segurança,** faça o seguinte:
   
   1. Desloque-se até à secção password do administrador do **dispositivo.** Forneça uma palavra-passe de administrador que contenha de 8 a 15 caracteres.
   2. Confirme a palavra-passe.
   3. Clique em **Guardar**, na parte superior do painel.

A palavra-passe do administrador do dispositivo está agora atualizada. Pode utilizar esta senha modificada para aceder ao dispositivo localmente.

![Lâmina de definições de segurança](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Use a UI web local para alterar a senha

Execute os seguintes passos para alterar a palavra-passe do administrador do dispositivo através da UI web local.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Para alterar a palavra-passe do administrador do dispositivo através da UI web local

1. Na Web UI local, clique na alteração da**palavra-passe** **de manutenção** > para o seu dispositivo.
   
    ![alterar palavra-passe1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Introduza a **palavra-passe Atual**.
3. Forneça uma **nova senha.** A palavra-passe deve ter pelo menos 8 caracteres de comprimento. Deve conter 3 de 4 dos seguintes caracteres: maiúsculas, minúsculas, numéricas e caracteres especiais.
   
    Note que a sua palavra-passe não pode ser a mesma que as últimas 24 palavras-passe.
4. Reintroduza a palavra-passe para a confirmar.
   
    ![alterar palavra-passe2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. Na parte inferior da página, clique em **Aplicar**. A nova senha é agora aplicada. Se a alteração da palavra-passe não for bem sucedida, vê o seguinte erro:
   
    ![erro de senha](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    Depois de a palavra-passe ser atualizada com sucesso, é notificado. Em seguida, pode utilizar esta senha modificada para aceder ao dispositivo localmente.


## <a name="next-steps"></a>Passos seguintes
Aprenda a administrar o [seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

