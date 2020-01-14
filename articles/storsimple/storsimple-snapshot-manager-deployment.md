---
title: Implantar o StorSimple Snapshot Manager | Microsoft Docs
description: Saiba como baixar e instalar o StorSimple Snapshot Manager, um snap-in do MMC para gerenciar a proteção de dados e os recursos de backup do StorSimple.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 6d3e3d6cdf7a831bf09d9c4709c1a60d27683438
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933371"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Implantar o snap-in do StorSimple Snapshot Manager MMC

## <a name="overview"></a>Visão geral
O StorSimple Snapshot Manager é um snap-in do MMC (console de gerenciamento Microsoft) que simplifica a proteção de dados e o gerenciamento de backup em um ambiente de Microsoft Azure StorSimple. Com o StorSimple Snapshot Manager, você pode gerenciar Microsoft Azure StorSimple armazenamento local e na nuvem como se fosse um sistema de armazenamento totalmente integrado, simplificando, assim, os processos de backup e restauração e reduzindo os custos. 

Este tutorial descreve os requisitos de configuração, bem como os procedimentos para instalar, remover e atualizar Snapshot Manager do StorSimple.

> [!NOTE]
> * Você não pode usar o StorSimple Snapshot Manager para gerenciar Microsoft Azure StorSimple matrizes virtuais (também conhecidas como dispositivos virtuais locais do StorSimple).
> * Se você planeja instalar o StorSimple atualização 2 em seu dispositivo StorSimple, certifique-se de baixar a versão mais recente do StorSimple Snapshot Manager e instalá-lo **antes de instalar a atualização 2 do storsimple**. A versão mais recente do StorSimple Snapshot Manager é compatível com versões anteriores e funciona com todas as versões lançadas do Microsoft Azure StorSimple. Se você estiver usando a versão anterior do StorSimple Snapshot Manager, será necessário atualizá-la (não é necessário desinstalar a versão anterior antes de instalar a nova versão).


## <a name="storsimple-snapshot-manager-installation"></a>Instalação do StorSimple Snapshot Manager
O StorSimple Snapshot Manager pode ser instalado em computadores que executam o sistema operacional Windows Server 2008 R2 SP1, Windows Server 2012 ou Windows Server 2012 R2. Em servidores que executam o Windows 2008 R2, você também deve instalar o Windows Server 2008 SP1 e o Windows Management Framework 3,0.

Antes de instalar ou atualizar o snap-in StorSimple Snapshot Manager para o MMC (console de gerenciamento Microsoft), verifique se o dispositivo Microsoft Azure StorSimple e o servidor host estão configurados corretamente.

## <a name="configure-prerequisites"></a>Configurar pré-requisitos
As etapas a seguir fornecem uma visão geral de alto nível das tarefas de configuração que você deve concluir antes de instalar o Snapshot Manager do StorSimple. Para obter informações completas de configuração de Microsoft Azure StorSimple e configuração, incluindo requisitos de sistema e instruções passo a passo, consulte [implantar seu dispositivo StorSimple local](storsimple-8000-deployment-walkthrough-u2.md).

> [!IMPORTANT]
> Antes de começar, examine a [lista de verificação da configuração de implantação](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) e os [pré-requisitos de implantação](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) em [implantar seu dispositivo StorSimple local](storsimple-8000-deployment-walkthrough-u2.md).
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>Antes de instalar o StorSimple Snapshot Manager
1. Desembale, monte e conecte o dispositivo Microsoft Azure StorSimple conforme descrito em [instalar o dispositivo storsimple 8100](storsimple-8100-hardware-installation.md) ou [instalar o dispositivo storsimple 8600](storsimple-8600-hardware-installation.md).
2. Verifique se o computador host está executando um dos seguintes sistemas operacionais:
   
   * Windows Server 2008 R2 (em servidores que executam o Windows 2008 R2, você também deve instalar o Windows Server 2008 SP1 e o Windows Management Framework 3,0)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     Para um dispositivo virtual StorSimple, o host deve ser uma Microsoft Azure máquina virtual.
3. Certifique-se de atender a todos os requisitos de configuração do Microsoft Azure StorSimple. Para obter detalhes, acesse [pré-requisitos de implantação](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites).
4. Conecte o dispositivo ao host e execute a configuração inicial. Para obter detalhes, vá para [etapas de implantação](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps).
5. Crie volumes no dispositivo, atribua-os ao host e verifique se o host pode montá-los e usá-los. O StorSimple Snapshot Manager dá suporte aos seguintes tipos de volumes:
   
   * Volumes básicos
   * Volumes simples
   * Volumes dinâmicos
   * Volumes dinâmicos espelhados (RAID 1)
   * Volumes compartilhados de cluster
     
     Para obter informações sobre como criar volumes no dispositivo StorSimple ou dispositivo virtual StorSimple, vá para a [etapa 6: criar um volume](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume), em [implantar seu dispositivo storsimple local](storsimple-8000-deployment-walkthrough-u2.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Instalar um novo Snapshot Manager StorSimple
Antes de instalar o StorSimple Snapshot Manager, certifique-se de que os volumes criados no dispositivo StorSimple ou no dispositivo virtual StorSimple sejam montados, inicializados e formatados conforme descrito em [configurar pré-requisitos](#configure-prerequisites).

> [!IMPORTANT]
> * Para um dispositivo virtual StorSimple, o host deve ser uma Microsoft Azure máquina virtual. 
> * O host deve estar executando o Windows 2008 R2, o Windows Server 2012 ou o Windows Server 2012 R2. Se o servidor estiver executando o Windows Server 2008 R2, você também deverá instalar o Windows Server 2008 SP1 e o Windows Management Framework 3,0.
> * Você deve configurar uma conexão iSCSI do host para o dispositivo StorSimple antes de poder conectar o dispositivo ao StorSimple Snapshot Manager.

Siga estas etapas para concluir uma nova instalação do StorSimple Snapshot Manager. Se você estiver instalando uma atualização, vá para [atualizar ou reinstalar o StorSimple snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

* Etapa 1: instalar o StorSimple Snapshot Manager 
* Etapa 2: conectar o StorSimple Snapshot Manager a um dispositivo 
* Etapa 3: verificar a conexão com o dispositivo 

### <a name="step-1-install-storsimple-snapshot-manager"></a>Etapa 1: instalar o StorSimple Snapshot Manager
Use as etapas a seguir para instalar o StorSimple Snapshot Manager.

#### <a name="to-install-storsimple-snapshot-manager"></a>Para instalar o StorSimple Snapshot Manager
1. Baixe o software StorSimple Snapshot Manager (vá para o [storsimple snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) no centro de download da Microsoft) e salve-o localmente no host.
2. No explorador de arquivos, clique com o botão direito do mouse na pasta compactada e, em seguida, clique em **extrair tudo**.
3. Na janela **extrair pastas compactadas (zipadas)** , na caixa **selecionar um destino e extrair arquivos** , digite ou navegue até o caminho onde você deseja que o arquivo seja extraído.
   
    > [!IMPORTANT]
    > Você deve instalar o StorSimple Snapshot Manager na unidade C:.
    
4. Marque a caixa de seleção **Mostrar arquivos extraídos ao concluir** e, em seguida, clique em **extrair**.
   
    ![Caixa de diálogo extrair arquivos](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. Quando a extração for concluída, a pasta de destino será aberta. Clique duas vezes no ícone de instalação do aplicativo que aparece na pasta de destino.
6. Quando a mensagem **instalação bem-sucedida** for exibida, clique em **fechar**. Você deve ver o ícone StorSimple Snapshot Manager na área de trabalho.
   
    ![ícone da área de trabalho](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Etapa 2: conectar o StorSimple Snapshot Manager a um dispositivo
Use as etapas a seguir para conectar o StorSimple Snapshot Manager a um dispositivo StorSimple.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Para conectar o StorSimple Snapshot Manager a um dispositivo
1. Clique no ícone StorSimple Snapshot Manager na área de trabalho. A janela Snapshot Manager do StorSimple é exibida. A janela contém um painel de **escopo** , um painel de **resultados** e um painel de **ações** . 
   
    ![Interface do usuário do StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * O painel **escopo** (o painel esquerdo) contém uma lista de nós organizados em uma estrutura de árvore. Você pode expandir alguns nós para selecionar uma exibição ou dados específicos relacionados a esse nó. Clique no ícone de seta para expandir ou recolher um nó. Clique com o botão direito do mouse em um item no painel **escopo** para ver uma lista de ações disponíveis para esse item.
   * O painel de **resultados** (o painel central) contém informações detalhadas de status sobre o nó, a exibição ou os dados que você selecionou no painel **escopo** .
   * O painel **ações** lista as operações que você pode executar no nó, na exibição ou nos dados que você selecionou no painel **escopo** .
     
     Para obter uma descrição completa da interface do usuário do StorSimple Snapshot Manager, consulte a [interface do usuário do storsimple snapshot Manager](storsimple-use-snapshot-manager.md).
2. No painel **escopo** , clique com o botão direito do mouse no nó **dispositivos** e clique em **configurar um dispositivo**. A caixa de diálogo **configurar um dispositivo** é exibida.
   
    ![Configurar um dispositivo](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. Na caixa de listagem **dispositivo** , selecione o endereço IP do dispositivo Microsoft Azure StorSimple ou dispositivo virtual. Na caixa de texto **senha** , digite a senha do snapshot Manager do StorSimple que você criou para o dispositivo no portal do Azure. Clique em **OK**.
4. O StorSimple Snapshot Manager pesquisa pelo dispositivo que você identificou. Se o dispositivo estiver disponível, o StorSimple Snapshot Manager adicionará uma conexão. Você pode [verificar a conexão com o dispositivo](#to-verify-the-connection) para confirmar que a conexão foi adicionada com êxito.
   
    Se o dispositivo não estiver disponível por algum motivo, o StorSimple Snapshot Manager retornará uma mensagem de erro. Clique em **OK** para fechar a mensagem de erro e, em seguida, clique em **Cancelar** para fechar a caixa de diálogo **configurar um dispositivo** .
5. Quando ele se conecta a um dispositivo, o StorSimple Snapshot Manager importa cada grupo de volumes configurado para esse dispositivo, desde que o grupo de volumes tenha backups associados. Os grupos de volumes que não têm backups associados não são importados. Além disso, as políticas de backup criadas para um grupo de volumes não são importadas. Para ver os grupos importados, clique com o botão direito do mouse no nó mais alto dos **grupos de volumes** no painel **escopo** e clique em **alternar grupos importados**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Etapa 3: verificar a conexão com o dispositivo
Use as etapas a seguir para verificar se o StorSimple Snapshot Manager está conectado ao dispositivo StorSimple.

#### <a name="to-verify-the-connection"></a>Para verificar a conexão
1. No painel **escopo** , clique no nó **dispositivos** .
   
    ![Status do dispositivo Snapshot Manager StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Verifique o painel de **resultados** : 
   
   * Se um indicador verde aparecer no ícone do dispositivo e **disponível** aparecer na coluna **status** , o dispositivo será conectado. 
   * Se um indicador vermelho aparecer no ícone do dispositivo e não estiver disponível aparece na coluna **status** , o dispositivo não estará conectado. 
   * Se a **atualização** aparecer na coluna **status** , o StorSimple snapshot Manager está recuperando grupos de volume e backups associados para um dispositivo conectado.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Atualizar ou reinstalar o StorSimple Snapshot Manager
Você deve desinstalar o StorSimple Snapshot Manager completamente antes de atualizar ou reinstalar o software. 

Antes de reinstalar o StorSimple Snapshot Manager, faça backup do banco de dados Snapshot Manager StorSimple existente no computador host. Isso salva as políticas de backup e as informações de configuração para que você possa restaurar facilmente esses dados do backup.

Siga estas etapas se você estiver atualizando ou reinstalando o StorSimple Snapshot Manager:

* Etapa 1: desinstalar o StorSimple Snapshot Manager 
* Etapa 2: fazer backup do banco de dados Snapshot Manager do StorSimple 
* Etapa 3: reinstalar o StorSimple Snapshot Manager e restaurar o banco de dados 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Etapa 1: desinstalar o StorSimple Snapshot Manager
Use as etapas a seguir para desinstalar o StorSimple Snapshot Manager.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Para desinstalar o StorSimple Snapshot Manager
1. No computador host, abra o **painel de controle**, clique em **programas**e, em seguida, clique em **programas e recursos**.
2. No painel esquerdo, clique em **desinstalar ou alterar um programa**.
3. Clique com o botão direito do mouse em **StorSimple snapshot Manager**e clique em **desinstalar**.
4. Isso inicia o programa de instalação do StorSimple Snapshot Manager. Clique em **Modificar instalação**e, em seguida, clique em **desinstalar**.
   
   > [!NOTE]
   > Se houver algum processo do MMC em execução em segundo plano, como o StorSimple Snapshot Manager ou o gerenciamento de disco, a desinstalação falhará e você receberá uma mensagem para fechar todas as instâncias do MMC antes de tentar desinstalar o programa. Selecione **fechar aplicativos automaticamente e tentar reiniciá-los após a conclusão da instalação**e clique em **OK**.
   > 
   > 
5. Quando o processo de desinstalação for concluído, uma mensagem **configuração bem-sucedida** será exibida. Clique em **Fechar**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Etapa 2: fazer backup do banco de dados Snapshot Manager do StorSimple
Use as etapas a seguir para criar e salvar uma cópia do banco de dados StorSimple Snapshot Manager.

#### <a name="to-back-up-the-database"></a>Para fazer backup do banco de dados
1. Pare o serviço de gerenciamento do Microsoft StorSimple:
   
   1. Inicie o Gestor de Servidores.
   2. No painel Gerenciador do Servidor, no menu **ferramentas** , selecione **Serviços**.
   3. Na página **Serviços** , selecione **serviço de gerenciamento do Microsoft StorSimple**.
   4. No painel direito, em **serviço de gerenciamento do Microsoft StorSimple**, clique em **parar o serviço**.
      
        ![Parar o serviço StorSimple Device Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Navegue até C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData é uma pasta oculta.
  
3. Localize o arquivo XML do catálogo, copie o arquivo e armazene a cópia em um local seguro ou na nuvem.
   
    ![Arquivo de catálogo de backup do StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Reinicie o serviço de gerenciamento do Microsoft StorSimple: 
   
   1. No painel Gerenciador do Servidor, no menu **ferramentas** , selecione **Serviços**.
   2. Na página **Serviços** , selecione o **serviço de gerenciamento do Microsoft StorSimple**.
   3. No painel direito, em **serviço de gerenciamento do Microsoft StorSimple**, clique em **reiniciar o serviço**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Etapa 3: reinstalar o StorSimple Snapshot Manager e restaurar o banco de dados
Para reinstalar o StorSimple Snapshot Manager, siga as etapas em [instalar um novo storsimple snapshot Manager](#install-a-new-storsimple-snapshot-manager). Em seguida, use o procedimento a seguir para restaurar o banco de dados Snapshot Manager do StorSimple.

#### <a name="to-restore-the-database"></a>Para restaurar o banco de dados
1. Pare o serviço de gerenciamento do Microsoft StorSimple:
   
   1. Inicie o Gestor de Servidores.
   2. No painel Gerenciador do Servidor, no menu **ferramentas** , selecione **Serviços**.
   3. Na página **Serviços** , selecione **serviço de gerenciamento do Microsoft StorSimple**.
   4. No painel direito, em **serviço de gerenciamento do Microsoft StorSimple**, clique em **parar o serviço**.
2. Navegue até C:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > ProgramData é uma pasta oculta.
   > 
   > 
3. Exclua o arquivo XML do catálogo e substitua-o pela versão que você salvou anteriormente.
4. Reinicie o serviço de gerenciamento do Microsoft StorSimple: 
   
   1. No painel Gerenciador do Servidor, no menu **ferramentas** , selecione **Serviços**.
   2. Na página **Serviços** , selecione **serviço de gerenciamento do Microsoft StorSimple**.
   3. No painel direito, em **serviço de gerenciamento do Microsoft StorSimple**, clique em **reiniciar o serviço**.

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre o StorSimple Snapshot Manager, acesse [o que é o storsimple snapshot Manager?](storsimple-what-is-snapshot-manager.md).
* Para saber mais sobre a interface do usuário do StorSimple Snapshot Manager, acesse [storsimple snapshot Manager interface do usuário](storsimple-use-snapshot-manager.md).
* Para saber mais sobre como usar o StorSimple Snapshot Manager, acesse [usar o storsimple snapshot Manager para administrar sua solução do StorSimple](storsimple-snapshot-manager-admin.md).

