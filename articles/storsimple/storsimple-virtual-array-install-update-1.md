---
title: Instalar atualização 1.0 em matriz Virtual StorSimple | Documentos da Microsoft
description: Descreve como utilizar a IU da web de matriz Virtual StorSimple para aplicar atualizações utilizando o método de portal e a correção do Azure
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: fa53213e577028628d48db91704578e23888f2a8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013865"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Instalar atualização 1.0 em sua matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve os passos necessários para instalar a atualização 1.0 em sua matriz Virtual StorSimple através da IU da web local e através do portal do Azure.

Aplicar as atualizações de software ou correções para manter a sua matriz Virtual StorSimple atualizados. Antes de aplicar uma atualização, recomendamos que levar os volumes ou compartilhamentos offline no anfitrião primeiro e, em seguida, o dispositivo. Isto minimiza qualquer possibilidade de danos em dados. Depois dos volumes ou compartilhamentos estiverem offline, também deve reservar um manual cópia de segurança do dispositivo.

> [!IMPORTANT]
> - A atualização 1.0 corresponde a **10.0.10296.0** versão de software no seu dispositivo. Para obter informações sobre o que há de novo nesta atualização, aceda a [notas de versão para a atualização 1.0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Tenha em atenção que a instalação de uma atualização ou correção reinicia o seu dispositivo. Uma vez que a matriz Virtual StorSimple é um dispositivo de nó único, qualquer e/s em curso é interrompida e o período de indisponibilidade ocorre com o seu dispositivo.
>
> - Atualização 1 está disponível no portal do Azure apenas se a matriz virtual está a executar a atualização 0.6. Para matrizes virtuais a executar versões de pré-atualização 0,6, tem de instalar a atualização 0.6 primeiro e, em seguida, instalar a atualização 1.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Se executar a atualização 0,2 e posterior, recomendamos que instale atualizações por meio do portal do Azure. O procedimento de portal requer que o utilizador procurar, transferir e, em seguida, instale as atualizações. Consoante a versão do software que sua matriz virtual está em execução, aplicar a atualização através do portal do Azure é diferente.

 - Se a sua matriz virtual está a executar a atualização 0.6, o portal do Azure instala diretamente atualização 1 (10.0.10296.0) no seu dispositivo. Este procedimento demora cerca de 7 minutos a concluir.
 - Se a sua matriz virtual está em execução uma versão anterior à atualização 0.6, a atualização é feita em duas fases. O portal do Azure instala pela primeira vez atualização 0.6 (10.0.10293.0) no seu dispositivo. A matriz virtual é reiniciada e o portal, em seguida, instala o Update 1 (10.0.10296.0) no seu dispositivo. Este procedimento demora cerca de 15 minutos a concluir.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

Depois de concluída a instalação, aceda ao seu serviço StorSimple Device Manager. Selecione **dispositivos** e, em seguida, selecione e clique no dispositivo que acabou de atualizar. Aceda a **definições > Gerir > atualizações do dispositivo**. A versão de software apresentados deve ser **10.0.10296.0**.

![Versão de software após a atualização](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>Utilizar a IU da web local

Existem dois passos quando utilizar a IU da web local:

* Transferir a atualização ou a correção
* Instalar a atualização ou a correção

> [!IMPORTANT] 
> **Continue com esta atualização apenas se estiver a executar atualização 0.6 (10.0.10293.0). Se estiver a executar uma versão anterior, [instalar a atualização 0.6](storsimple-virtual-array-install-update-06.md) no seu dispositivo primeiro e, em seguida, aplique o Update 1.**

### <a name="download-the-update-or-the-hotfix"></a>Transferir a atualização ou a correção

Se a sua matriz virtual está a executar a atualização 0.6, execute os seguintes passos para transferir a atualização 1 do catálogo Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para transferir a atualização ou a correção

1. Inicie o Internet Explorer e navegue para [ https://catalog.update.microsoft.com ](https://catalog.update.microsoft.com).

2. Se estiver a utilizar o catálogo Microsoft Update pela primeira vez neste computador, clique em **instalar** quando lhe for pedido para instalar o suplemento do catálogo Microsoft Update.

3. Na caixa de pesquisa do catálogo Microsoft Update, introduza o número de Base de dados de conhecimento (BDC) de correção que pretende transferir. Introduza **4047203** para atualização 1.0 e, em seguida, clique em **pesquisa**.
   
    A lista de correções é apresentada, por exemplo, **StorSimple Virtual Array atualização 1.0**.
   
    ![Catálogo de pesquisa](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Clique em **Transferir**.

5. Baixe os dois ficheiros para uma pasta. Também pode copiar a pasta para uma partilha de rede que é acessível a partir do dispositivo.

6. Abra a pasta onde os ficheiros estão localizados.

    ![Ficheiros no pacote](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Verá dois arquivos:
    -  Um ficheiro de pacote do Microsoft Update autónomo `WindowsTH-KB3011067-x64`. Este ficheiro é utilizado para atualizar o software de dispositivo.
    - Um ficheiro que contém as atualizações cumulativas para Agosto `windows8.1-kb4034681-x64`. Para obter mais informações sobre o que inclui este rollup, aceda a [agregação de segurança mensal de Agosto](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>Instalar a atualização ou a correção

Antes da instalação de atualização ou correção, certifique-se de que:

 - Tem a atualização ou correção transferido localmente no seu anfitrião ou acessível através de uma partilha de rede.
 - A sua matriz virtual está em execução de atualização 0.6 (10.0.10293.0). Se estiver a executar uma versão anterior à atualização 0.6, [instalar a atualização 0.6](storsimple-virtual-array-install-update-06.md) primeiro e, em seguida, instale a atualização 1.

Este procedimento demora cerca de 4 minutos a concluir. Execute os seguintes passos para instalar a atualização ou correção.

#### <a name="to-install-the-update-or-the-hotfix"></a>Para instalar a atualização ou a correção

1. Na IU da web local, aceda a **manutenção** > **atualização de Software**. Tome nota da versão do software que está a executar. **Continue com esta atualização apenas se estiver a executar atualização 0.6 (10.0.10293.0). Se estiver a executar uma versão anterior, [instalar a atualização 0.6](storsimple-virtual-array-install-update-06.md) no seu dispositivo primeiro e, em seguida, aplique o Update 1.**
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. Na **caminho do ficheiro de atualização**, introduza o nome de ficheiro para a atualização ou a correção. Também pode navegar para o ficheiro de instalação de atualização ou correção se colocado num compartilhamento de rede. Clique em **Aplicar**.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. É apresentado um aviso. Dada a matriz virtual é um dispositivo de nó único, após a atualização é aplicada, o dispositivo é reiniciado e não há período de indisponibilidade. Clique no ícone de verificação.
   
   ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. A atualização começa. Depois do dispositivo é atualizado com êxito, reinicia. A interface do Usuário local não está acessível desta duração.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Após o reinício estiver concluído, será direcionado para o **iniciar sessão** página. Para verificar que tiver atualizado o software de dispositivo, na web local da interface do Usuário, aceda ao **manutenção** > **atualização de Software**. A versão de software apresentados deve ser **10.0.0.0.0.10296** para atualização 1.0.
   
   > [!NOTE]
   > Iremos comunicar as versões de software numa forma um pouco diferentes na IU da web local e o portal do Azure. Por exemplo, a IU web local comunica **10.0.0.0.0.10296** e os relatórios do portais do Azure **10.0.10296.0** para a mesma versão.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Repita os passos 2 a 4 para instalar a correção de segurança do Windows usando o arquivo `windows8.1-kb4012213-x64`. A matriz virtual é reiniciado após a instalação e terá de iniciar sessão na IU da web local.

> [!NOTE]
> Se aplicadas diretamente a atualização 1 para um dispositivo que execute uma versão anterior à atualização 0.6, estão em falta algumas atualizações. Entre em contato com Support da Microsoft para passos seguintes.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [administrando sua matriz Virtual StorSimple](storsimple-ova-web-ui-admin.md).
