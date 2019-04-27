---
title: Alterar as palavras-passe do StorSimple | Documentos da Microsoft
description: Descreve como utilizar o serviço StorSimple Device Manager para alterar suas senhas de administrador do Snapshot Manager do StorSimple e o dispositivo.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60638041"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Utilize o serviço StorSimple Device Manager para alterar as palavras-passe do StorSimple

## <a name="overview"></a>Descrição geral
O portal do Azure **definições do dispositivo** opção contém todos os parâmetros de dispositivo que pode reconfigurar num dispositivo StorSimple que é gerido por um serviço StorSimple Device Manager. Este tutorial explica como pode utilizar o **Security** opção sob **definições do dispositivo** para alterar o seu administrador de dispositivo ou a palavra-passe do Snapshot Manager do StorSimple.

## <a name="change-the-device-administrator-password"></a>Alterar palavra-passe do administrador do dispositivo
Quando utiliza a interface do Windows PowerShell para aceder ao dispositivo StorSimple, tem de introduzir uma palavra-passe de administrador do dispositivo. Quando o primeiro dispositivo StorSimple está registado com um serviço, a palavra-passe predefinida para esta interface está *Password1*. Para garantir a segurança dos seus dados, tem de alterar esta palavra-passe no final do processo de registo. Não pode sair do processo de registo sem alterar esta palavra-passe. Para obter mais informações, consulte [passo 3: Configurar e registar o dispositivo através do Windows PowerShell para StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

A palavra-passe que foi definida pela primeira vez por meio da interface do Windows PowerShell durante o registo pode ser alterada mais tarde através do portal do Azure. Execute os seguintes passos para alterar a palavra-passe de administrador do dispositivo.

#### <a name="to-change-the-device-administrator-password"></a>Para alterar a palavra-passe de administrador do dispositivo
1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**.

2. Na lista tabular de dispositivos, selecione e clique no dispositivo cujo palavra-passe que pretende alterar.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Na **definições** painel, aceda à **definições do dispositivo > segurança**.

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. Na **definições de segurança** painel, clique em **palavra-passe** para alterar a palavra-passe de administrador do dispositivo.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. Na **palavra-passe** painel, forneça uma palavra-passe de administrador que contém a partir de 8 a 15 carateres. A palavra-passe tem de ser uma combinação de 3 ou mais carateres em maiúsculas, minúsculas, numéricos e especiais.

6. Confirme a palavra-passe.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Clique em **salvar** e quando lhe for pedida confirmação, clique em **Sim**.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

A palavra-passe de administrador do dispositivo deve ser atualizada. Pode utilizar esta palavra-passe modificado para acessar a interface do Windows PowerShell.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Definir a palavra-passe do Snapshot Manager do StorSimple
O software Snapshot Manager do StorSimple reside no anfitrião do Windows e permite aos administradores gerir cópias de segurança do dispositivo StorSimple sob a forma de instantâneos locais e na nuvem.

Quando configurar um dispositivo no Snapshot Manager do StorSimple, será solicitado para fornecer o endereço IP do dispositivo e a palavra-passe para autenticar o dispositivo de armazenamento.

Pode definir ou alterar a palavra-passe para o StorSimple Snapshot Manager através do portal do Azure. Execute os seguintes passos para definir ou alterar a palavra-passe do Snapshot Manager do StorSimple.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Para definir a palavra-passe do Snapshot Manager do StorSimple
1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**.

2. Na lista tabular de dispositivos, selecione e clique no dispositivo cujo Snapshot Manager do StorSimple palavra-passe, que pretende definir ou alterar.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Na **definições** painel, aceda à **definições do dispositivo > segurança**.

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. Na **definições de segurança** painel, clique em **palavra-passe** para definir ou alterar a palavra-passe do Snapshot Manager do StorSimple.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. Na **palavra-passe** painel, introduza uma palavra-passe é 14 e 15 carateres. Certifique-se de que a palavra-passe contém uma combinação de 3 ou mais carateres em maiúsculas, minúsculas, numéricos e especiais.

6. Confirme a palavra-passe.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Clique em **salvar** e quando lhe for pedida confirmação, clique em **Sim**.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Agora deve ser atualizada a palavra-passe do Snapshot Manager do StorSimple.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [StorSimple segurança](storsimple-8000-security.md).
* Saiba mais sobre [modificando a configuração do dispositivo](storsimple-8000-modify-device-config.md).
* Saiba mais sobre [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

