---
title: Instale atualizações num Microsoft Azure StorSimple Virtual Array [ Microsoft Docs
description: Descreve como usar o StorSimple Virtual Array web UI para aplicar atualizações usando o portal e método hotfix
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 9997a97b-9382-43ed-b56e-61369335c987
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7bf064ff01693f7a65c756a99c435d7f1a39840e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61409420"
---
# <a name="install-updates-on-your-storsimple-virtual-array---azure-portal"></a>Instale atualizações no seu StorSimple Virtual Array - portal Azure

## <a name="overview"></a>Descrição geral

Este artigo descreve os passos necessários para instalar atualizações no seu StorSimple Virtual Array através da UI web local e através do portal Azure. Tem de aplicar atualizações de software ou hotfixes para manter o storSimple Virtual Array atualizado. 

Tenha em mente que instalar uma atualização ou um hotfix reinicia o seu dispositivo. Dado que o StorSimple Virtual Array é um único dispositivo de nó, qualquer I/O em curso é interrompido e o seu dispositivo experimenta tempo de inatividade. 

Antes de aplicar uma atualização, recomendamos que leve os volumes ou partilhas offline no anfitrião primeiro e depois o dispositivo. Isto minimiza qualquer possibilidade de danos em dados.

> [!IMPORTANT]
> Se estiver a executar versões de software Update 0.1 ou GA, tem de utilizar o método de correção de hotéis através da UI web local para instalar a atualização 0.3. Se estiver a executar o Update 0.2, recomendamos que instale as atualizações através do portal clássico Azure.
 

## <a name="use-the-local-web-ui"></a>Use a UI web local

Existem dois passos ao utilizar a UI web local:

* Descarregue a atualização ou o hotfix
* Instale a atualização ou o hotfix

### <a name="download-the-update-or-the-hotfix"></a>Descarregue a atualização ou o hotfix

Execute os seguintes passos para transferir a atualização de software a partir do Catálogo Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para descarregar a atualização ou o hotfix

1. Inicie o Internet [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)Explorer e navegue para .

2. Se esta for a primeira vez que utiliza o Catálogo Microsoft Update neste computador, clique em **Instalar** quando lhe for pedido para instalar o suplemento do Catálogo Microsoft Update.

3. Na caixa de pesquisa do Catálogo de Atualizações da Microsoft, introduza o número da Base de Conhecimento (KB) do hotfix que pretende descarregar. Introduza **o 3182061** para atualizar 0.3 e, em seguida, clique em **Procurar**.
   
    A listagem de hotfix aparece, por exemplo, **storSimple Virtual Array Update 0.3**.
   
    ![Catálogo de pesquisa](./media/storsimple-virtual-array-install-update/download1.png)

4. Clique em **Adicionar**. A atualização é adicionada ao cesto.

5. Clique em **Ver Cesto**.

6. Clique em **Baixar**. Especifique ou **Pesquise** uma localização local onde pretende que as transferências apareçam. As atualizações são transferidas para a localização especificada e colocadas numa sub-pasta com o mesmo nome que a atualização. A pasta também pode ser copiada para uma partilha de rede que é acessível a partir do dispositivo.

7. Abra a pasta copiada, deve ver um `WindowsTH-KB3011067-x64`ficheiro Pacote Autónomo da Microsoft Update . Este ficheiro é utilizado para instalar a atualização ou o hotfix.

### <a name="install-the-update-or-the-hotfix"></a>Instale a atualização ou o hotfix

Antes da atualização ou instalação hotfix, certifique-se de que tem a atualização ou o hotfix descarregado localmente no seu anfitrião ou acessível através de uma partilha de rede. 

Utilize este método para instalar atualizações num dispositivo que executa as versões de software GA ou Update 0.1. Este procedimento leva menos de 2 minutos para ser concluído. Execute os seguintes passos para instalar a atualização ou o hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>Para instalar a atualização ou o hotfix

1. Na Web UI local, vá a**Atualização**de Software **de Manutenção** > .
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update/update1m.png)

2. No caminho do **ficheiro Atualizar,** introduza o nome do ficheiro para a atualização ou para o hotfix. Também pode navegar no ficheiro de instalação de atualização ou hotfix se for colocado numa partilha de rede. Clique em **Aplicar**.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update/update2m.png)

3. É apresentado um aviso. Dado que se trata de um único dispositivo de nó, após a atualização ser aplicada, o dispositivo reinicia e há tempo de inatividade. Clique no ícone de verificação.
   
   ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update/update3m.png)

4. A atualização começa. Depois de o dispositivo ser atualizado com sucesso, reinicia. A UI local não é acessível nesta duração.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update/update5m.png)

5. Após o reinício, é levado para a página **do Sinal.** Para verificar se o software do dispositivo foi atualizado, na Web UI local, vá para **a Atualização** > de**Software**de Manutenção . A versão de software exibida deve ser **10.0.0.0.0.10288.0** para Atualização 0.3.
   
   > [!NOTE]
   > Relatamos as versões de software de uma forma ligeiramente diferente na UI web local e no portal Azure. Por exemplo, a UI web local reporta **10.0.0.0.10288** e o portal Azure reporta **10.0.10288.0** para a mesma versão.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Se executar o Update 0.2, recomendamos que instale atualizações através do portal Azure. O procedimento do portal requer que o utilizador faça o download, descarregue e instale as atualizações. Este procedimento leva cerca de 7 minutos para ser concluído. Execute os seguintes passos para instalar a atualização ou o hotfix.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal.md)]

Depois de concluída a instalação (conforme indicado pelo estado de trabalho em 100 %), vá ao serviço StorSimple Device Manager. Selecione **Dispositivos** e, em seguida, selecione e clique no dispositivo que pretende atualizar a partir da lista de dispositivos ligados a este serviço. Na lâmina **Definições,** vá à secção **'Gerir'** e selecione **atualizações**do Dispositivo . A versão de software exibida deve ser **10.0.10288.0**.


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a administração do seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

