---
title: Altere as suas palavras-passe StorSimple | Microsoft Docs
description: Descreve como utilizar o serviço StorSimple Device Manager para alterar as palavras-passe do Gestor de Instantâneos StorSimple e do administrador do dispositivo.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 038084ba9ae43e14bc2eb42bf258912be27d062c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023759"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Utilize o serviço StorSimple Device Manager para alterar as suas palavras-passe StorSimple

## <a name="overview"></a>Descrição Geral
A opção **de definição do dispositivo do** portal Azure contém todos os parâmetros do dispositivo que pode reconfigurar num dispositivo StorSimple que é gerido por um serviço StorSimple Device Manager. Este tutorial explica como pode utilizar a opção **De Segurança** nas **definições do Dispositivo** para alterar o administrador do dispositivo ou a palavra-passe do Gestor de Instantâneo storSimple.

## <a name="change-the-device-administrator-password"></a>Alterar palavra-passe do administrador do dispositivo
Quando utilizar a interface Do Windows PowerShell para aceder ao dispositivo StorSimple, é-lhe exigido que introduza uma palavra-passe do administrador do dispositivo. Quando o primeiro dispositivo StorSimple é registado com um serviço, a palavra-passe padrão para esta interface é *a Password1*. Para a segurança dos seus dados, é-lhe exigido que altere esta palavra-passe no final do processo de registo. Não pode sair do processo de registo sem alterar esta palavra-passe. Para obter mais informações, consulte [o Passo 3: Configurar e registar o dispositivo através do Windows PowerShell para StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

A palavra-passe que foi definida pela primeira vez através da interface Windows PowerShell durante o registo pode ser alterada mais tarde através do portal Azure. Execute os seguintes passos para alterar a palavra-passe do administrador do dispositivo.

#### <a name="to-change-the-device-administrator-password"></a>Para alterar a palavra-passe do administrador do dispositivo
1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**.

2. A partir da listagem tabular de dispositivos, selecione e clique no dispositivo cuja palavra-passe pretende alterar.

    ![Screenshot mostrando o serviço StorSimple Device Manager. Em Gestão, os Dispositivos são selecionados. Na lista de dispositivos, é selecionado um dispositivo.](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Na lâmina **Definições,** aceda às **definições do dispositivo > Segurança**.

    ![Screenshot mostrando a lâmina de Definições do serviço Gestor de Dispositivos. Nas Definições do Dispositivo, a Segurança é selecionada.](./media/storsimple-8000-change-passwords/changepwd2.png)

4. Na lâmina de **definições de segurança,** clique em **Palavra-passe** para alterar a palavra-passe do administrador do dispositivo.

    ![Screenshot mostrando a lâmina de definições de segurança. O botão Palavra-Passe é realçado.](./media/storsimple-8000-change-passwords/changepwd3.png)

5. Na lâmina **palavra-passe,** forneça uma palavra-passe do administrador que contenha de 8 a 15 caracteres. A palavra-passe deve ser uma combinação de 3 ou mais caracteres maiúsculas, minúsculas, numéricas e especiais.

6. Confirme a palavra-passe.

    ![Screenshot mostrando a lâmina da palavra-passe. Sob a palavra-passe do dispositivo, as novas caixas de palavra-passe e palavra-passe confirmam a saúde.](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Clique **em Guardar** e quando solicitado para confirmação, clique em **Sim**.

    ![Screenshot mostrando a lâmina da palavra-passe. O botão Guardar é realçado.](./media/storsimple-8000-change-passwords/changepwd6.png)

A palavra-passe do administrador do dispositivo deve agora ser atualizada. Pode utilizar esta palavra-passe modificada para aceder à interface Windows PowerShell.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Desa estalades a palavra-passe do Gestor de Instantâneos StorSimple
O software Snapshot Manager do StorSimple reside no anfitrião do Windows e permite aos administradores gerir cópias de segurança do dispositivo StorSimple sob a forma de instantâneos locais e na nuvem.

Ao configurar um dispositivo no StorSimple Snapshot Manager, será solicitado que forneça o endereço IP do dispositivo e a palavra-passe para autenticar o seu dispositivo de armazenamento.

Pode definir ou alterar a palavra-passe para StorSimple Snapshot Manager através do portal Azure. Execute os seguintes passos para definir ou alterar a palavra-passe StorSimple Snapshot Manager.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Para definir a palavra-passe do Gestor instantâneo StorSimple
1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**.

2. A partir da listagem tabular de dispositivos, selecione e clique no dispositivo cuja palavra-passe StorSimple Snapshot Manager pretende definir ou alterar.

     ![Screenshot mostrando o serviço StorSimple Device Manager. Em Gestão, os Dispositivos são selecionados. Na lista de dispositivos, é selecionado um dispositivo.](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Na lâmina **Definições,** aceda às **definições do dispositivo > Segurança**.

     ![Screenshot mostrando a lâmina de Definições do serviço Gestor de Dispositivos. Nas Definições do Dispositivo, a Segurança é selecionada.](./media/storsimple-8000-change-passwords/changepwd2.png)

4. Na lâmina de **definições de segurança,** clique em **Palavra-passe** para definir ou alterar a palavra-passe StorSimple Snapshot Manager.

     ![Screenshot mostrando a lâmina de definições de segurança. O botão Palavra-Passe é realçado.](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. Na lâmina **da palavra-passe,** introduza uma palavra-passe de 14 ou 15 caracteres. Certifique-se de que a palavra-passe contém uma combinação de 3 ou mais caracteres maiúsculas, minúsculas, numéricas e especiais.

6. Confirme a palavra-passe.

     ![Screenshot mostrando a lâmina da palavra-passe. Sob palavra-passe do Gestor instantâneo, as novas caixas de palavra-passe e de confirmação de palavras-passe são preenchidas.](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Clique **em Guardar** e quando solicitado para confirmação, clique em **Sim**.

     ![Screenshot mostrando a lâmina da palavra-passe. O botão Guardar é realçado.](./media/storsimple-8000-change-passwords/changepwd6.png)

A palavra-passe StorSimple Snapshot Manager deve agora ser atualizada.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [a segurança StorSimple](storsimple-8000-security.md).
* Saiba mais sobre [a modificação da configuração do seu dispositivo.](storsimple-8000-modify-device-config.md)
* Saiba mais sobre [a utilização do serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

