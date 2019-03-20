---
title: Instalar atualização 0.5 na matriz Virtual StorSimple | Documentos da Microsoft
description: Descreve como utilizar a IU da web de matriz Virtual StorSimple para aplicar atualizações utilizando o método de portal e a correção do Azure
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
ms.date: 05/10/2017
ms.author: alkohli
ms.openlocfilehash: e09ff4bcbc141b1a1f80bc278918a291639c1885
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58005402"
---
# <a name="install-update-05-on-your-storsimple-virtual-array"></a>Instalar atualização 0.5 na sua matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve os passos necessários para instalar atualização 0.5 na sua matriz Virtual StorSimple através da IU da web local e através do portal do Azure. Tem de aplicar as atualizações de software ou correções para manter a sua matriz Virtual StorSimple atualizados.

Antes de aplicar uma atualização, recomendamos que levar os volumes ou compartilhamentos offline no anfitrião primeiro e, em seguida, o dispositivo. Isto minimiza qualquer possibilidade de danos em dados. Depois dos volumes ou compartilhamentos estiverem offline, também deve reservar um manual cópia de segurança do dispositivo.

> [!IMPORTANT]
> - Atualização 0.5 corresponde à **10.0.10290.0** versão de software no seu dispositivo. Para obter informações sobre o que há de novo nesta atualização, aceda a [notas de versão para atualização 0.5](storsimple-virtual-array-update-05-release-notes.md).
>
> - Se estiver a executar atualização 0.2 ou posterior, recomendamos que instale as atualizações através do portal do Azure. Se estiver a executar a atualização 0.1 ou versões de software de disponibilidade geral, tem de utilizar o método de correção através da IU da web local para instalar atualização 0.5.
>
> - Tenha em atenção que a instalação de uma atualização ou correção reinicia o seu dispositivo. Uma vez que a matriz Virtual StorSimple é um dispositivo de nó único, qualquer e/s em curso é interrompida e o período de indisponibilidade ocorre com o seu dispositivo.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Se executar a atualização 0,2 e posterior, recomendamos que instale atualizações por meio do portal do Azure. O procedimento de portal requer que o utilizador procurar, transferir e, em seguida, instale as atualizações. Este procedimento demora cerca de 7 minutos a concluir. Execute os seguintes passos para instalar a atualização ou correção.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Depois de concluída a instalação, aceda ao seu serviço StorSimple Device Manager. Selecione **dispositivos** e, em seguida, selecione e clique no dispositivo que acabou de atualizar. Aceda a **definições > Gerir > atualizações do dispositivo**. A versão de software apresentados deve ser **10.0.10290.0**.

## <a name="use-the-local-web-ui"></a>Utilizar a IU da web local

Existem dois passos quando utilizar a IU da web local:

* Transferir a atualização ou a correção
* Instalar a atualização ou a correção

### <a name="download-the-update-or-the-hotfix"></a>Transferir a atualização ou a correção

Execute os seguintes passos para transferir a atualização de software a partir do Catálogo Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para transferir a atualização ou a correção

1. Inicie o Internet Explorer e navegue para [ https://catalog.update.microsoft.com ](https://catalog.update.microsoft.com).

2. Se esta for a primeira vez que utiliza o Catálogo Microsoft Update neste computador, clique em **Instalar** quando lhe for pedido para instalar o suplemento do Catálogo Microsoft Update.

3. Na caixa de pesquisa do catálogo Microsoft Update, introduza o número de Base de dados de conhecimento (BDC) de correção que pretende transferir. Introduza **4021576** para atualizar 0,5 e, em seguida, clique em **pesquisa**.
   
    A lista de correções é apresentada, por exemplo, **StorSimple Virtual matriz atualização 0.5**.
   
    ![Catálogo de pesquisa](./media/storsimple-virtual-array-install-update-05/download1.png)

4. Clique em **Transferir**. 

5. Deverá ver dois ficheiros para transferir, um *. msu* e uma *. cab* ficheiro. Transferir cada um desses arquivos para uma pasta. A pasta também pode ser copiada para uma partilha de rede que é acessível a partir do dispositivo.

6. Abra a pasta onde os ficheiros estão localizados.
    ![Ficheiros no pacote](./media/storsimple-virtual-array-install-update-05/update05folder.png)

    Verá:
    -  Um ficheiro de pacote do Microsoft Update autónomo `WindowsTH-KB3011067-x64`. Este ficheiro é utilizado para atualizar o software de dispositivo.
    - Um ficheiro de pacote de agente de monitorização de Geneva `GenevaMonitoringAgentPackageInstaller`. Este ficheiro é utilizado para atualizar o agente de monitorização e diagnóstico do serviço (MDS). Clique duas vezes o arquivo cab. É apresentado um. msi. Selecione o ficheiro, botão direito do mouse e, em seguida **extrair** o ficheiro. Irá utilizar o _. msi_ ficheiro a atualizar o agente.

        ![Extrair o ficheiro de atualização de agente do MDS](./media/storsimple-virtual-array-install-update-05/extract-geneva-monitoring-agent-installer.png)
        
    

### <a name="install-the-update-or-the-hotfix"></a>Instalar a atualização ou a correção

Antes da instalação de atualização ou correção, certifique-se de que tem a atualização ou a correção transferido localmente no seu anfitrião ou acessível através de uma partilha de rede.

Utilize este método para instalar atualizações num dispositivo com o GA ou atualizar 0,1 versões de software. Este procedimento demora menos de 2 minutos a concluir. Execute os seguintes passos para instalar a atualização ou correção.

#### <a name="to-install-the-update-or-the-hotfix"></a>Para instalar a atualização ou a correção

1. Na IU da web local, aceda a **manutenção** > **atualização de Software**.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. Na **caminho do ficheiro de atualização**, introduza o nome de ficheiro para a atualização ou a correção. Também pode navegar para o ficheiro de instalação de atualização ou correção se colocado num compartilhamento de rede. Clique em **Aplicar**.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. É apresentado um aviso. Isso é um dispositivo de nó único, após a atualização é aplicada, o dispositivo é reiniciado e não há período de indisponibilidade. Clique no ícone de verificação.
   
   ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. A atualização começa. Depois do dispositivo é atualizado com êxito, reinicia. A interface do Usuário local não está acessível desta duração.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Após o reinício estiver concluído, será direcionado para o **iniciar sessão** página. Para verificar que tiver atualizado o software de dispositivo, na web local da interface do Usuário, aceda ao **manutenção** > **atualização de Software**. A versão de software apresentados deve ser **10.0.0.0.0.10290.0** para atualização 0.5.
   
   > [!NOTE]
   > Iremos comunicar as versões de software numa forma um pouco diferentes na IU da web local e o portal do Azure. Por exemplo, a IU web local comunica **10.0.0.0.0.10290** e os relatórios do portais do Azure **10.0.10290.0** para a mesma versão.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-05/update6m.png)

6. O passo seguinte é atualizar o agente do MDS. Na **atualização de Software** página, vá para o **caminho do ficheiro de atualização** e navegue para o `GenevaMonitoringAgentPackageInstaller.msi` ficheiro. Repita os passos 2 a 4. Depois da matriz virtual é reiniciado, inicie sessão na IU da web local.

A atualização já está concluída.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [administrando sua matriz Virtual StorSimple](storsimple-ova-web-ui-admin.md).

