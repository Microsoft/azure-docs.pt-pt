---
title: Alterar StorSimple Virtual Array dispositivo admin password | Microsoft Docs
description: Descreve como utilizar o portal Azure ou o StorSimple Virtual Array web UI para alterar a palavra-passe do administrador do dispositivo.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8bc8846d546faec194617ccb753cdbd105e16bf9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85513618"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Alterar a palavra-passe do administrador do dispositivo StorSimple Virtual Array através do Gestor de Dispositivos StorSimple

## <a name="overview"></a>Descrição Geral

Quando utilizar a interface Windows PowerShell para aceder ao Array Virtual StorSimple, é-lhe exigido que introduza uma palavra-passe do administrador do dispositivo. Quando o dispositivo StorSimple é inicialmente provisionado e iniciado, a palavra-passe padrão é *Password1*. Para a segurança dos seus dados, a palavra-passe predefinida expira na primeira vez que iniciar sôs o seu sôm e é obrigado a alterar esta palavra-passe.

Também pode utilizar a UI web local ou o portal Azure para alterar a palavra-passe do administrador do dispositivo a qualquer momento após a implementação do dispositivo no seu ambiente de produção. Cada um destes procedimentos é descrito neste artigo.

 ![lâmina de dispositivos](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Use o portal Azure para alterar a palavra-passe

Execute os seguintes passos para alterar a palavra-passe do administrador do dispositivo através do portal Azure.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Para alterar a palavra-passe do administrador do dispositivo através do portal Azure

1. Na página de aterragem de serviço, selecione o seu serviço, clique duas vezes no nome do serviço e, em seguida, na secção **Gestão,** clique em **Dispositivos**. Isto abre a lâmina **devices** que lista todos os seus dispositivos StorSimple Virtual Array.

2. Na lâmina **dispositivos,** clique duas vezes no dispositivo que requer uma alteração da palavra-passe.

3. Na lâmina **Definições** do seu dispositivo, clique em **Segurança**.

4. Na lâmina **definições de segurança,** faça o seguinte:
   
   1. Desloque-se para a secção **palavra-passe do administrador do dispositivo.** Forneça uma senha de administrador que contenha de 8 a 15 caracteres.
   2. Confirme a palavra-passe.
   3. Clique em **Guardar**, na parte superior do painel.

A palavra-passe do administrador do dispositivo está agora atualizada. Pode utilizar esta palavra-passe modificada para aceder ao dispositivo localmente.

![Lâmina de definições de segurança](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Use o uI web local para alterar a palavra-passe

Execute os seguintes passos para alterar a palavra-passe do administrador do dispositivo através da UI web local.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Para alterar a palavra-passe do administrador do dispositivo através da UI web local

1. Na UI web local, clique em **Alterar**  >  **palavra-passe de** manutenção para o seu dispositivo.
   
    ![alterar palavra-passe1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Introduza a **palavra-passe Atual**.
3. Forneça uma **nova senha.** A palavra-passe deve ter pelo menos 8 caracteres de comprimento. Deve conter 3 de 4 dos seguintes caracteres: maiúsculas, minúsculas, numéricas e caracteres especiais.
   
    Note que a sua palavra-passe não pode ser a mesma das últimas 24 palavras-passe.
4. Reintroduza a palavra-passe para a confirmar.
   
    ![alterar palavra-passe2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. Na parte inferior da página, clique **em Aplicar.** A nova senha é agora aplicada. Se a alteração da palavra-passe não for bem sucedida, vê o seguinte erro:
   
    ![erro de senha](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    Depois de a palavra-passe ser atualizada com sucesso, é notificado. Em seguida, pode utilizar esta palavra-passe modificada para aceder ao dispositivo localmente.


## <a name="next-steps"></a>Passos seguintes
Saiba como [administrar o seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

