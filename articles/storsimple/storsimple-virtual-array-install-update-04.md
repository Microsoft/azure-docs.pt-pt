---
title: Instale atualizações em | de matriz virtual StorSimple Microsoft Docs
description: Descreve como utilizar o UI web StorSimple Virtual Array para aplicar o Update 0.4 utilizando o portal Azure e o método de correção em calor.
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
ms.date: 02/07/2017
ms.author: alkohli
ms.openlocfilehash: cc458f91a9b67ddd54e706575d29b8fc8618a2f7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94534301"
---
# <a name="install-update-04-on-your-storsimple-virtual-array"></a>Instale atualização 0.4 no seu StorSimple Virtual Array

## <a name="overview"></a>Descrição geral

Este artigo descreve os passos necessários para instalar o Update 0.4 no seu StorSimple Virtual Array através da UI web local e através do portal Azure. Tem de aplicar atualizações de software ou hotfixes para manter o seu StorSimple Virtual Array atualizado. 

Tenha em atenção que a instalação de uma atualização ou correção reinicia o dispositivo. Dado que o StorSimple Virtual Array é um único dispositivo de nó, qualquer I/S em andamento é interrompido e o seu dispositivo experimenta tempo de inatividade. 

Antes de aplicar uma atualização, recomendamos que leve primeiro os volumes ou partilhas offline no anfitrião e, em seguida, no dispositivo. Isto minimiza qualquer possibilidade de danos em dados.

> [!IMPORTANT]
> Se estiver a executar as versões de software Update 0.1 ou GA, tem de utilizar o método hotfix através da UI web local para instalar a atualização 0.3. Se estiver a executar a Atualização 0.2 ou posterior, recomendamos que instale as atualizações através do portal Azure.
 

## <a name="use-the-local-web-ui"></a>Use a uI web local

Existem dois passos ao utilizar a uI web local:

* Descarregue a atualização ou o hotfix
* Instale a atualização ou o hotfix

### <a name="download-the-update-or-the-hotfix"></a>Descarregue a atualização ou o hotfix

Execute os seguintes passos para transferir a atualização de software a partir do Catálogo Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para descarregar a atualização ou o hotfix

1. Inicie o Internet Explorer e navegue para [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Se esta for a primeira vez que utiliza o Catálogo Microsoft Update neste computador, clique em **Instalar** quando lhe for pedido para instalar o suplemento do Catálogo Microsoft Update.

3. Na caixa de pesquisa do Catálogo de Atualizações da Microsoft, insira o número da Base de Conhecimento (KB) do hotfix que pretende descarregar. Introduza **3216577** para Atualização 0.4 e, em seguida, clique em **Procurar**.
   
    A listagem hotfix aparece, por exemplo, **StorSimple Virtual Array Update 0.4**.
   
    ![Catálogo de pesquisa](./media/storsimple-virtual-array-install-update-04/download1.png)

4. Clique em **Adicionar**. A atualização é adicionada ao cesto.

5. Clique em **Ver Cesto**.

6. Clique em **Transferir**. Especifique ou **Pesquise** uma localização local onde pretende que as transferências apareçam. As atualizações são transferidas para a localização especificada e colocadas numa sub-pasta com o mesmo nome que a atualização. A pasta também pode ser copiada para uma partilha de rede que é acessível a partir do dispositivo.

7. Abra a pasta copiada, deverá ver um ficheiro pacote autónomo da Microsoft Update `WindowsTH-KB3011067-x64` . Este ficheiro é utilizado para instalar a atualização ou o hotfix.

### <a name="install-the-update-or-the-hotfix"></a>Instale a atualização ou o hotfix

Antes da atualização ou instalação do hotfix, certifique-se de que tem a atualização ou o hotfix descarregado localmente no seu anfitrião ou acessível através de uma partilha de rede. 

Utilize este método para instalar atualizações num dispositivo em execução de versões de software GA ou Update 0.1. Este procedimento leva menos de 2 minutos para ser concluído. Execute os seguintes passos para instalar a atualização ou hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>Para instalar a atualização ou o hotfix

1. Na UI web local, aceda à  >  **Atualização de Software de** Manutenção .
   
    ![A screenshot mostra a atualização de Software selecionada a partir do menu Manutenção.](./media/storsimple-virtual-array-install-update/update1m.png)

2. Na **trajetória do ficheiro 'Actualizar',** insira o nome do ficheiro para a atualização ou o hotfix. Também pode navegar no ficheiro de instalação de atualização ou hotfix se for colocado numa partilha de rede. Clique em **Aplicar**.
   
    ![A screenshot mostra a caixa de texto do caminho do ficheiro Update na página de atualização do Software.](./media/storsimple-virtual-array-install-update/update2m.png)

3. É apresentado um aviso. Dado que se trata de um único dispositivo de nó, após a aplicação da atualização, o dispositivo reinicia e há tempo de inatividade. Clique no ícone de verificação.
   
   ![A screenshot mostra um aviso de caixa de diálogo de tempo de inatividade.](./media/storsimple-virtual-array-install-update/update3m.png)

4. A atualização começa. Depois de o dispositivo ser atualizado com sucesso, reinicia-o. A UI local não é acessível nesta duração.
   
    ![A screenshot mostra uma mensagem de sucesso para a atualização.](./media/storsimple-virtual-array-install-update/update5m.png)

5. Após o reinício, é levado para a página **Signo.** Para verificar se o software do dispositivo se atualizou, na UI web local, vá à   >  **Atualização de Software** de Manutenção . A versão do software visualizada deve ser **10.0.0.0.0.10289.0** para a atualização 0.4.
   
   > [!NOTE]
   > Reportamos as versões de software de uma forma ligeiramente diferente na UI web local e no portal Azure. Por exemplo, o web UI local reporta **10.0.0.0.0.10289** e o portal Azure reporta **10.0.10289.0** para a mesma versão.
   
    ![A screenshot mostra a página de atualização do Software com a versão atual do software.](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Se executar a Atualização 0.2 e posteriormente, recomendamos que instale atualizações através do portal Azure. O procedimento do portal requer que o utilizador digitalize, descarregue e, em seguida, instale as atualizações. Este procedimento leva cerca de 7 minutos para ser concluído. Execute os seguintes passos para instalar a atualização ou hotfix.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Depois de concluída a instalação (conforme indicado pelo estado de trabalho a 100 %), aceda ao serviço StorSimple Device Manager. Selecione **Dispositivos** e, em seguida, selecione e clique no dispositivo que pretende atualizar a partir da lista de dispositivos ligados a este serviço. Na lâmina **Definições,** vá à secção **Gerir** e selecione **atualizações do dispositivo**. A versão de software visualizada deve ser **10.0.10289.0**.


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a administração do seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

