---
title: Instale a Atualização 1.1 no StorSimple Virtual Array [ StorSimple Virtual Array ] Microsoft Docs
description: Descreve como aplicar atualizações usando o portal Azure e a Web UI local para StorSimple Virtual Array
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/18/2018
ms.author: alkohli
ms.openlocfilehash: 88b903d68e4398b4e30b0b7435279c29bee6cd6b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79254485"
---
# <a name="install-update-11-on-your-storsimple-virtual-array"></a>Instale a Atualização 1.1 na sua Matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve os passos necessários para instalar o Update 1.1 no seu StorSimple Virtual Array através da UI web local e através do portal Azure.

Aplica as atualizações de software ou os hotfixes para manter o storSimple Virtual Array atualizado. Antes de aplicar uma atualização, recomendamos que leve os volumes ou partilhas offline no anfitrião primeiro e depois o dispositivo. Isto minimiza qualquer possibilidade de danos em dados. Depois de os volumes ou ações estarem offline, deve também retirar uma cópia de segurança manual do dispositivo.

> [!IMPORTANT]
> - A atualização 1.1 corresponde a **10.0.10307.0** versão de software no seu dispositivo. Para obter informações sobre o que é novidade nesta atualização, vá a notas de [Lançamento para Atualização 1.1](storsimple-virtual-array-update-11-release-notes.md).
>
> - Tenha em mente que instalar uma atualização ou um hotfix reinicia o seu dispositivo. Dado que o StorSimple Virtual Array é um único dispositivo de nó, qualquer I/O em curso é interrompido e o seu dispositivo experimenta tempo de inatividade.
>
> - A atualização 1.1 só está disponível no portal Azure se a matriz virtual estiver a executar o Update 1. Para as matrizes virtuais que executam as versões Update 0.6, tem de instalar o Update 1.0 primeiro e depois aplicar o Update 1.1.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Se executar o Update 0.2 e mais tarde, recomendamos que instale atualizações através do portal Azure. O procedimento do portal requer que o utilizador faça o download, descarregue e instale as atualizações. Dependendo da versão do software, a sua matriz virtual está a funcionar, aplicar a atualização através do portal Azure é diferente.

 - Se a sua matriz virtual estiver a executar o Update 1, o portal Azure instala diretamente o Update 1.1 (10.0.10307.0) no seu dispositivo. Este procedimento leva cerca de 10 minutos para ser concluído.
 - Se a sua matriz virtual estiver a executar a Atualização 0.6, a atualização é feita em duas fases. O portal Azure instala primeiro o Update 1.0 (10.0.10296.0) no seu dispositivo. A matriz virtual reinicia e o portal instala o Update 1.1 (10.0.10307.0) no seu dispositivo. Este procedimento leva cerca de 15 minutos para ser concluído.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-11.md)]

Depois de concluída a instalação, vá ao serviço StorSimple Device Manager. Selecione **Dispositivos** e, em seguida, selecione e clique no dispositivo que acaba de atualizar. Vá a **Definições > Gerir > Atualizações**do dispositivo . A versão de software exibida deve ser **10.0.10307.0**.

![Versão de software após atualização](./media/storsimple-virtual-array-install-update-11/azupdate17m2.png)

## <a name="use-the-local-web-ui"></a>Use a UI web local

Existem dois passos ao utilizar a UI web local:

* Descarregue a atualização ou o hotfix
* Instale a atualização ou o hotfix

> [!IMPORTANT] 
> **Proceda aesta atualização apenas se estiver a executar a Atualização 1 (10.0.10296.0). Se estiver a executar a Atualização 0.6, instale primeiro a [Atualização 1](storsimple-virtual-array-install-update-1.md) no seu dispositivo e, em seguida, aplique o Update 1.1.**

### <a name="download-the-update-or-the-hotfix"></a>Descarregue a atualização ou o hotfix

Execute os seguintes passos para baixar o Update 1.1 do Catálogo de Atualizações da Microsoft.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para descarregar a atualização ou o hotfix

1. Inicie o Internet Explorer e navegue para [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com).

2. Se estiver a utilizar o Catálogo de Atualizações da Microsoft pela primeira vez neste computador, clique em **Instalar** quando for solicitado para instalar o add-on do Microsoft Update Catalog.

3. Na caixa de pesquisa do Catálogo de Atualizações da Microsoft, introduza o número da Base de Conhecimento (KB) do hotfix que pretende descarregar. Introduza **4337628** para atualizar 1.1 e, em seguida, clique em **Procurar**.
   
    A listagem de hotfix aparece, por exemplo, **storSimple Virtual Array Update 1.1**.
   
    ![Catálogo de pesquisa](./media/storsimple-virtual-array-install-update-11/download1.png)

4. Clique em **Transferir**.

5. Descarregue os dois ficheiros para uma pasta. Também pode copiar a pasta para uma partilha de rede que é acessível a partir do dispositivo.

6. Abra a pasta onde os ficheiros estão localizados.

    ![Ficheiros no pacote](./media/storsimple-virtual-array-install-update-11/update01folder.png)

    Vê dois ficheiros:
    -  Um ficheiro de pacote autónomo da Microsoft Update `WindowsTH-KB3011067-x64`. Este ficheiro é utilizado para atualizar o software do dispositivo.
    - Um ficheiro que contém atualizações cumulativas para `Windows8.1-KB4284815-x64`de junho . Para mais informações sobre o que está incluído neste rollup, vá ao rollup mensal de [segurança de junho.](https://support.microsoft.com/help/4284815/windows-81-update-kb4284815)

### <a name="install-the-update-or-the-hotfix"></a>Instale a atualização ou o hotfix

Antes da instalação de atualização ou de fixação, certifique-se de que:

 - Tem a atualização ou o hotfix descarregado localmente no seu anfitrião ou acessível através de uma partilha de rede.
 - A sua matriz virtual está a executar a Atualização 1 (10.0.10296.0). Se estiver a executar a Atualização 0.6, instale primeiro a [Atualização 1](storsimple-virtual-array-install-update-1.md) e, em seguida, instale o Update 1.1.

Este procedimento leva cerca de 4 minutos para ser concluído. Execute os seguintes passos para instalar a atualização ou o hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>Para instalar a atualização ou o hotfix

1. Na Web UI local, vá à **Manutenção** > **Atualização**de Software . Tome nota da versão de software que está a executar. **Proceda aesta atualização apenas se estiver a executar a Atualização 1 (10.0.10296.0). Se estiver a executar a Atualização 0.6, instale primeiro a [Atualização 1](storsimple-virtual-array-install-update-1.md) no seu dispositivo e, em seguida, aplique o Update 1.1.**
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-11/update1m.png)

2. No caminho do **ficheiro Atualizar,** introduza o nome do ficheiro para a atualização ou para o hotfix. Também pode navegar no ficheiro de instalação de atualização ou hotfix se for colocado numa partilha de rede. Clique em **Aplicar**.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-11/update2m.png)

3. É apresentado um aviso. Dado que a matriz virtual é um único dispositivo de nó, após a atualização ser aplicada, o dispositivo reinicia e há tempo de inatividade. Clique no ícone de verificação.
   
   ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-11/update3m.png)

4. A atualização começa. Depois de o dispositivo ser atualizado com sucesso, reinicia. A UI local não é acessível nesta duração.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-11/update5m.png)

5. Após o reinício, é levado para a página **do Sinal.** Para verificar se o software do dispositivo foi atualizado, na Web UI local, vá à **Manutenção** > **Atualização**de Software . A versão de software exibida deve ser **10.0.0.0.0.10307** para a Atualização 1.1.
   
   > [!NOTE]
   > Relatamos as versões de software de uma forma ligeiramente diferente na UI web local e no portal Azure. Por exemplo, a UI web local reporta **10.0.0.0.10307** e o portal Azure reporta **10.0.10307.0** para a mesma versão.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-11/update6m.png)

6. Repita os passos 2-4 para instalar a correção de segurança do Windows utilizando `Windows8.1-KB4284815-x64`de ficheiros . A matriz virtual reinicia após a instalação e precisa de assinar na UI web local.


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a administração do seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).
