---
title: Altere as suas palavras-passe StorSimple / Microsoft Docs
description: Descreve como utilizar o serviço StorSimple Device Manager para alterar as suas palavras-passe do StorSimple Snapshot Manager e do administrador do dispositivo.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: ab874bbdcd47a4bfa9abfba721afa46d0f23a338
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268031"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Utilize o serviço StorSimple Device Manager para alterar as suas palavras-passe StorSimple

## <a name="overview"></a>Descrição geral
A opção de **definição** do dispositivo do portal Azure contém todos os parâmetros do dispositivo que pode reconfigurar num dispositivo StorSimple que é gerido por um serviço StorSimple Device Manager. Este tutorial explica como pode utilizar a opção **de Segurança** sob **as definições do Dispositivo** para alterar o administrador do seu dispositivo ou a palavra-passe do StorSimple Snapshot Manager.

## <a name="change-the-device-administrator-password"></a>Alterar palavra-passe do administrador do dispositivo
Quando utilizar a interface Do Windows PowerShell para aceder ao dispositivo StorSimple, é necessário introduzir uma palavra-passe do administrador do dispositivo. Quando o primeiro dispositivo StorSimple é registado com um serviço, a palavra-passe padrão para esta interface é *password1*. Para a segurança dos seus dados, é-lhe exigido que altere esta palavra-passe no final do processo de registo. Não pode sair do processo de registo sem alterar esta senha. Para mais informações, consulte [o Passo 3: Configure e registe o dispositivo através do Windows PowerShell para storSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

A palavra-passe que foi definida pela primeira vez através da interface Do Windows PowerShell durante o registo pode ser alterada posteriormente através do portal Azure. Execute os seguintes passos para alterar a palavra-passe do administrador do dispositivo.

#### <a name="to-change-the-device-administrator-password"></a>Para alterar a palavra-passe do administrador do dispositivo
1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**.

2. A partir da listagem tabular de dispositivos, selecione e clique no dispositivo cuja palavra-passe pretende alterar.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Na lâmina **Definições,** vá às **definições**do dispositivo > Security .

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. Na lâmina de definições de **Segurança,** clique em **Password** para alterar a palavra-passe do administrador do dispositivo.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. Na lâmina **password,** forneça uma palavra-passe do administrador que contenha de 8 a 15 caracteres. A palavra-passe deve ser uma combinação de 3 ou mais caracteres maiúsculos, minúsculos, numéricos e especiais.

6. Confirme a palavra-passe.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Clique em **Guardar** e quando solicitado para confirmação, clique **em Sim**.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

A palavra-passe do administrador do dispositivo deve agora ser atualizada. Pode utilizar esta palavra-passe modificada para aceder à interface Do Windows PowerShell.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Defino a senha do StorSimple Snapshot Manager
O software Snapshot Manager do StorSimple reside no anfitrião do Windows e permite aos administradores gerir cópias de segurança do dispositivo StorSimple sob a forma de instantâneos locais e na nuvem.

Ao configurar um dispositivo no StorSimple Snapshot Manager, será solicitado que forneça o endereço IP do dispositivo e a palavra-passe para autenticar o seu dispositivo de armazenamento.

Pode definir ou alterar a palavra-passe para StorSimple Snapshot Manager através do portal Azure. Execute os seguintes passos para definir ou alterar a palavra-passe do StorSimple Snapshot Manager.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Para definir a senha do StorSimple Snapshot Manager
1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**.

2. A partir da listagem tabular dos dispositivos, selecione e clique no dispositivo cuja palavra-passe storSimple Snapshot Manager pretende definir ou alterar.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Na lâmina **Definições,** vá às **definições**do dispositivo > Security .

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. Na lâmina de definições de **Segurança,** clique em **Password** para definir ou alterar a palavra-passe do StorSimple Snapshot Manager.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. Na lâmina **password,** introduza uma palavra-passe com 14 ou 15 caracteres. Certifique-se de que a palavra-passe contém uma combinação de 3 ou mais caracteres maiúsculos, minúsculos, numéricos e especiais.

6. Confirme a palavra-passe.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Clique em **Guardar** e quando solicitado para confirmação, clique **em Sim**.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

A palavra-passe do StorSimple Snapshot Manager deve agora ser atualizada.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a [segurança StorSimple](storsimple-8000-security.md).
* Saiba mais sobre [modificar a configuração do seu dispositivo](storsimple-8000-modify-device-config.md).
* Saiba mais sobre [a utilização do serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

