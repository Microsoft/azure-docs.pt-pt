---
title: Instalar atualização 1.0 em StorSimple Virtual Array / Microsoft Docs
description: Descreve como utilizar o UI web StorSimple Virtual Array para aplicar o Update 1.0 utilizando o portal Azure e o método de correção em calor.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 5bb6b61bb7d9392984bf5c308ce7ee039234461e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91295571"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Instale atualização 1.0 no seu StorSimple Virtual Array

## <a name="overview"></a>Descrição geral

Este artigo descreve os passos necessários para instalar o Update 1.0 no seu StorSimple Virtual Array através da UI web local e através do portal Azure.

Aplica as atualizações de software ou hotfixes para manter o seu StorSimple Virtual Array atualizado. Antes de aplicar uma atualização, recomendamos que leve primeiro os volumes ou partilhas offline no anfitrião e, em seguida, no dispositivo. Isto minimiza qualquer possibilidade de danos em dados. Depois de os volumes ou ações estarem offline, também deverá fazer uma cópia de segurança manual do dispositivo.

> [!IMPORTANT]
>
> - A atualização 1.0 corresponde à versão de software **10.0.10296.0** no seu dispositivo. Para obter informações sobre as novidades desta atualização, aceda às [notas de lançamento para atualização 1.0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Tenha em atenção que a instalação de uma atualização ou correção reinicia o dispositivo. Dado que o StorSimple Virtual Array é um único dispositivo de nó, qualquer I/S em andamento é interrompido e o seu dispositivo experimenta tempo de inatividade.
>
> - A atualização 1 só está disponível no portal Azure se a matriz virtual estiver a executar Update 0.6. Para as matrizes virtuais que executam as versões pré-Actualização 0.6, tem de instalar o Update 0.6 primeiro e depois instalar o Update 1.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Se executar a Atualização 0.2 e posteriormente, recomendamos que instale atualizações através do portal Azure. O procedimento do portal requer que o utilizador digitalize, descarregue e, em seguida, instale as atualizações. Dependendo da versão de software que o seu conjunto virtual está a executar, a aplicação da atualização através do portal Azure é diferente.

 - Se o seu conjunto virtual estiver a executar Atualização 0.6, o portal Azure instala diretamente a Atualização 1 (10.0.10296.0) no seu dispositivo. Este procedimento leva cerca de 7 minutos para ser concluído.
 - Se o seu conjunto virtual estiver a executar uma versão antes do Update 0.6, a atualização é feita em duas fases. O portal Azure instala pela primeira vez a Atualização 0.6 (10.0.10293.0) no seu dispositivo. A matriz virtual reinicia e o portal instala a Atualização 1 (10.0.10296.0) no seu dispositivo. Este procedimento leva cerca de 15 minutos para ser concluído.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

Depois de concluída a instalação, aceda ao serviço StorSimple Device Manager. Selecione **Dispositivos** e, em seguida, selecione e clique no dispositivo que acabou de atualizar. Vá a **Definições > Gerir > atualizações do dispositivo**. A versão de software visualizada deve ser **10.0.10296.0**.

![Versão de software após atualização](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>Use a uI web local

Existem dois passos ao utilizar a uI web local:

* Descarregue a atualização ou o hotfix
* Instale a atualização ou o hotfix

> [!IMPORTANT] 
> **Proceda a esta atualização apenas se estiver a executar Atualização 0.6 (10.0.10293.0). Se estiver a executar uma versão anterior, [instale o Update 0.6](storsimple-virtual-array-install-update-06.md) no seu dispositivo primeiro e, em seguida, aplique o Update 1.**

### <a name="download-the-update-or-the-hotfix"></a>Descarregue a atualização ou o hotfix

Se a sua matriz virtual estiver a executar o Update 0.6, execute os seguintes passos para descarregar o Update 1 do Catálogo de Atualizações da Microsoft.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para descarregar a atualização ou o hotfix

1. Inicie o Internet Explorer e navegue para [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Se estiver a utilizar o Catálogo de Atualizações da Microsoft pela primeira vez neste computador, clique em **Instalar** quando solicitado para instalar o addon do Catálogo de Atualizações do Microsoft.

3. Na caixa de pesquisa do Catálogo de Atualizações da Microsoft, insira o número da Base de Conhecimento (KB) do hotfix que pretende descarregar. Introduza **4047203** para atualizar 1.0 e, em seguida, clique em **Procurar**.
   
    A listagem hotfix aparece, por exemplo, **StorSimple Virtual Array Update 1.0**.
   
    ![Catálogo de pesquisa](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Clique **em Baixar**.

5. Descarregue os dois ficheiros para uma pasta. Também pode copiar a pasta para uma partilha de rede que seja acessível a partir do dispositivo.

6. Abra a pasta onde os ficheiros estão localizados.

    ![Ficheiros no pacote](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Vê dois ficheiros:
    -  Um ficheiro pacote autónomo da Microsoft Update `WindowsTH-KB3011067-x64` . Este ficheiro é utilizado para atualizar o software do dispositivo.
    - Um ficheiro que contém atualizações cumulativas para agosto `windows8.1-kb4034681-x64` . Para obter mais informações sobre o que está incluído neste rollup, vá até [agosto de segurança mensal rollup](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>Instale a atualização ou o hotfix

Antes da atualização ou instalação do hotfix, certifique-se de que:

 - Tem a atualização ou o hotfix descarregado localmente no seu anfitrião ou acessível através de uma partilha de rede.
 - A sua matriz virtual está a executar Atualização 0.6 (10.0.10293.0). Se estiver a executar uma versão antes do Update 0.6, instale primeiro [o Update 0.6](storsimple-virtual-array-install-update-06.md) e, em seguida, instale o Update 1.

Este procedimento leva cerca de 4 minutos para ser concluído. Execute os seguintes passos para instalar a atualização ou hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>Para instalar a atualização ou o hotfix

1. Na UI web local, **Maintenance**aceda à  >  **Atualização de Software de**Manutenção . Tome nota da versão de software que está a executar. **Proceda a esta atualização apenas se estiver a executar Atualização 0.6 (10.0.10293.0). Se estiver a executar uma versão anterior, [instale o Update 0.6](storsimple-virtual-array-install-update-06.md) no seu dispositivo primeiro e, em seguida, aplique o Update 1.**
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. Na **trajetória do ficheiro 'Actualizar',** insira o nome do ficheiro para a atualização ou o hotfix. Também pode navegar no ficheiro de instalação de atualização ou hotfix se for colocado numa partilha de rede. Clique em **Aplicar**.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. É apresentado um aviso. Dado que a matriz virtual é um único dispositivo de nó, após a aplicação da atualização, o dispositivo reinicia e há tempo de inatividade. Clique no ícone de verificação.
   
   ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. A atualização começa. Depois de o dispositivo ser atualizado com sucesso, reinicia-o. A UI local não é acessível nesta duração.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Após o reinício, é levado para a página **Signo.** Para verificar se o software do dispositivo se atualizou, na UI web local, vá à **Maintenance**  >  **Atualização de Software**de Manutenção . A versão de software visualizada deve ser **10.0.0.0.0.10296** para a atualização 1.0.
   
   > [!NOTE]
   > Reportamos as versões de software de uma forma ligeiramente diferente na UI web local e no portal Azure. Por exemplo, o web UI local reporta **10.0.0.0.0.10296** e o portal Azure reporta **10.0.10296.0** para a mesma versão.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Repita os passos 2-4 para instalar a correção de segurança do Windows utilizando o ficheiro `windows8.1-kb4012213-x64` . A matriz virtual reinicia após a instalação e precisa de iniciar súm na UI web local.

> [!NOTE]
> Se aplicou diretamente o Update 1 a um dispositivo que executa uma versão antes do Update 0.6, está a perder algumas atualizações. Entre em contato com o Microsoft Support para os próximos passos.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a administração do seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).
