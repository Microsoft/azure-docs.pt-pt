---
title: Implementar o Snapshot Manager do StorSimple | Documentos da Microsoft
description: Saiba como transferir e instalar o Snapshot Manager do StorSimple, um snap-in do MMC para gerir recursos de proteção e cópia de segurança de dados do StorSimple.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: ee17e4b69d1e6c9de465e4241ee2237361e320b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61077743"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Implementar o snap-in do MMC do Snapshot Manager do StorSimple

## <a name="overview"></a>Descrição geral
O StorSimple Snapshot Manager é um snap-in da consola de gestão da Microsoft (MMC), que simplifica a proteção de dados e gestão de cópia de segurança num ambiente do Microsoft Azure StorSimple. Com o StorSimple Snapshot Manager, pode gerir o Microsoft Azure StorSimple no local e armazenamento na nuvem como se fosse um sistema de armazenamento totalmente integrado, portanto, simplificando os processos de backup e restauração e reduzindo os custos. 

Este tutorial descreve os requisitos de configuração, bem como dos procedimentos para instalar, remover e atualizar o Snapshot Manager do StorSimple.

> [!NOTE]
> * Não é possível utilizar o Snapshot Manager do StorSimple para gerir matrizes virtuais do Microsoft Azure StorSimple (também conhecido como StorSimple no local dispositivos virtuais).
> * Se planear instalar atualização 2 do StorSimple no dispositivo StorSimple, certifique-se de que transferir a versão mais recente do Snapshot Manager do StorSimple e instalá-lo **antes de instalar a atualização 2 do StorSimple**. A versão mais recente do Snapshot Manager do StorSimple é compatível com versões anteriores e funciona com todas as versões do Microsoft Azure StorSimple. Se estiver a utilizar a versão anterior do Snapshot Manager do StorSimple, terá de atualizá-lo (não é necessário desinstalar a versão anterior antes de instalar a nova versão).


## <a name="storsimple-snapshot-manager-installation"></a>Instalação do Snapshot Manager do StorSimple
Snapshot Manager do StorSimple pode ser instalado em computadores que executem o Windows Server 2008 R2 SP1, o Windows Server 2012 ou o sistema operativo Windows Server 2012 R2. Em servidores com o Windows 2008 R2, também tem de instalar o Windows Server 2008 SP1 e Windows Management Framework 3.0.

Antes de instalar ou atualizar o snap-in Snapshot Manager do StorSimple para a consola de gestão da Microsoft (MMC), certifique-se de que o servidor de anfitrião e de dispositivos do Microsoft Azure StorSimple estão configuradas corretamente.

## <a name="configure-prerequisites"></a>Configurar os pré-requisitos
Os passos seguintes fornecem uma visão geral das tarefas de configuração que tem de concluir antes de instalar o Snapshot Manager do StorSimple. Para o Microsoft Azure StorSimple toda a configuração e informações de configuração, incluindo os requisitos de sistema e instruções passo a passo, veja [implementar no seu dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md).

> [!IMPORTANT]
> Antes de começar, reveja os [lista de verificação de configuração de implementação](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) e e [pré-requisitos da implementação](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) na [implementar no seu dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md).
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>Antes de instalar o Snapshot Manager do StorSimple
1. Descompactar, montar e ligar o dispositivo do Microsoft Azure StorSimple, conforme descrito em [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Certifique-se de que o computador anfitrião está em execução um dos seguintes sistemas operativos:
   
   * Windows Server 2008 R2 (em servidores que executam o Windows 2008 R2, também tem de instalar Windows Server 2008 SP1 e Windows Management Framework 3.0)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     Para um dispositivo virtual StorSimple, o anfitrião tem de ser uma Máquina Virtual do Microsoft Azure.
3. Certifique-se de que cumpre todos os requisitos de configuração do Microsoft Azure StorSimple. Para obter mais informações, aceda a [pré-requisitos de implementação](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites).
4. Ligue o dispositivo para o anfitrião e execute a configuração inicial. Para obter mais informações, aceda a [passos de implementação](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps).
5. Crie volumes no dispositivo, atribuí-los para o anfitrião e certifique-se de que o anfitrião pode montar e utilizá-los. Snapshot Manager do StorSimple suporta os seguintes tipos de volumes:
   
   * Volumes básicos
   * Volumes simples
   * Volumes dinâmicos
   * Volumes dinâmicos espelhados (RAID 1)
   * Volumes partilhados de cluster
     
     Para informações sobre a criação de volumes no dispositivo do StorSimple ou dispositivo virtual StorSimple, aceda a [passo 6: Criar um volume](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume), na [implementar no seu dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Instalar um novo Snapshot Manager do StorSimple
Antes de instalar o Snapshot Manager do StorSimple, certifique-se de que os volumes que criou no dispositivo do StorSimple ou dispositivo virtual StorSimple são montados, inicializados e formatados conforme descrito em [configurar os pré-requisitos](#configure-prerequisites).

> [!IMPORTANT]
> * Para um dispositivo virtual StorSimple, o anfitrião tem de ser uma Máquina Virtual do Microsoft Azure. 
> * O anfitrião tem de estar em execução no Windows 2008 R2, Windows Server 2012 ou Windows Server 2012 R2. Se o servidor está a executar o Windows Server 2008 R2, também tem de instalar Windows Server 2008 SP1 e Windows Management Framework 3.0.
> * Tem de configurar uma ligação de iSCSI do anfitrião para o dispositivo StorSimple antes de poder ligar o dispositivo para o StorSimple Snapshot Manager.

Siga estes passos para concluir uma instalação nova do Snapshot Manager do StorSimple. Se estiver a instalar uma atualização, aceda a [atualizar ou reinstalar o Snapshot Manager do StorSimple](#upgrade-or-reinstall-storsimple-snapshot-manager).

* Passo 1: Instalar o Snapshot Manager do StorSimple 
* Passo 2: Ligar o Snapshot Manager do StorSimple para um dispositivo 
* Passo 3: Verificar a ligação para o dispositivo 

### <a name="step-1-install-storsimple-snapshot-manager"></a>Passo 1: Instalar o Snapshot Manager do StorSimple
Utilize os seguintes passos para instalar o Snapshot Manager do StorSimple.

#### <a name="to-install-storsimple-snapshot-manager"></a>Para instalar o Snapshot Manager do StorSimple
1. Baixar o software Snapshot Manager do StorSimple (aceda a [Snapshot Manager do StorSimple](https://www.microsoft.com/download/details.aspx?id=44220) no Microsoft Download Center) e guarde-o localmente no anfitrião.
2. No Explorador de ficheiros, faça duplo clique a pasta comprimida e, em seguida, clique em **extrair todos os**.
3. Na **pastas extrair compactadas (Zipped)** janela, na **selecione um destino e extrair arquivos** caixa, escreva ou navegue para o caminho onde pretende ao ficheiro a ser extraído.
   
    > [!IMPORTANT]
    > Tem de instalar o Snapshot Manager do StorSimple na unidade c:.
    
4. Selecione o **Show extraídos de arquivos após concluir o procedimento** caixa de verificação e, em seguida, clique em **extrair**.
   
    ![Extrair a caixa de diálogo de ficheiros](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. Quando a extração estiver concluída, abre-se a pasta de destino. Faça duplo clique no ícone de configuração de aplicação que aparece na pasta de destino.
6. Quando o **concluída com êxito do programa de configuração** é apresentada a mensagem, clique em **fechar**. Deverá ver o ícone do Snapshot Manager do StorSimple no seu ambiente de trabalho.
   
    ![ícone de área de trabalho](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Passo 2: Ligar o Snapshot Manager do StorSimple para um dispositivo
Utilize os seguintes passos para ligar o Snapshot Manager do StorSimple para um dispositivo StorSimple.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Para ligar o Snapshot Manager do StorSimple para um dispositivo
1. Clique no ícone do Snapshot Manager do StorSimple no seu ambiente de trabalho. É apresentada a janela do Snapshot Manager do StorSimple. A janela contém um **âmbito** painel, uma **resultados** painel e um **ações** painel. 
   
    ![Interface de utilizador do Snapshot Manager do StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * O **âmbito** painel (painel esquerdo) contém uma lista de nós organizados numa estrutura de árvore. Pode expandir alguns nós para selecionar uma vista ou dados específicos relacionados com esse nó. Clique no ícone de seta para expandir ou recolher um nó. Com o botão direito num item na **âmbito** painel para ver uma lista de ações disponíveis para esse item.
   * O **resultados** painel (painel central) contém informações de estado detalhadas sobre o nó, vista ou dados que selecionou no **âmbito** painel.
   * O **ações** painel lista as operações que pode efetuar no nó, vista ou dados que selecionou no **âmbito** painel.
     
     Para obter uma descrição completa da interface de utilizador do Snapshot Manager do StorSimple, consulte [interface de utilizador do Snapshot Manager do StorSimple](storsimple-use-snapshot-manager.md).
2. Na **âmbito** painel, clique com botão direito a **dispositivos** nó e, em seguida, clique **configurar um dispositivo**. O **Configure um dispositivo** é apresentada a caixa de diálogo.
   
    ![Configurar um dispositivo](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. Na **dispositivo** caixa de listagem, selecione o endereço IP do dispositivo do Microsoft Azure StorSimple ou dispositivo virtual. Na **palavra-passe** texto, escreva a palavra-passe do Snapshot Manager do StorSimple que criou para o dispositivo no portal do Azure. Clique em **OK**.
4. Pesquisa do Snapshot Manager do StorSimple para o dispositivo que identificou. Se o dispositivo estiver disponível, o Snapshot Manager do StorSimple adiciona uma ligação. Pode [verificar a ligação para o dispositivo](#to-verify-the-connection) para confirmar que a ligação foi adicionada com êxito.
   
    Se o dispositivo estiver indisponível por qualquer motivo, o Snapshot Manager do StorSimple devolve uma mensagem de erro. Clique em **OK** para fechar a mensagem de erro e, em seguida, clique em **Cancelar** para fechar o **configurar um dispositivo** caixa de diálogo.
5. Quando se liga a um dispositivo, o Snapshot Manager do StorSimple importa a cada grupo de volumes configurado para que o dispositivo, desde que o grupo de volume tiver associados a cópias de segurança. Grupos de volumes que não têm cópias de segurança associadas não são importados. Além disso, as políticas de cópia de segurança que foram criadas para um grupo de volume não são importadas. Para ver os grupos importados, com o botão direito a mais **grupos de volumes** nó a **âmbito** painel e clique em **alternar grupos importados**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Passo 3: Verificar a ligação para o dispositivo
Utilize os seguintes passos para verificar se o Snapshot Manager do StorSimple é ligado ao dispositivo StorSimple.

#### <a name="to-verify-the-connection"></a>Para verificar a ligação
1. Na **âmbito** painel, clique nas **dispositivos** nó.
   
    ![Estado do dispositivo StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Verifique os **resultados** painel: 
   
   * Se é apresentado um indicador de verde no ícone de dispositivo e **disponível** é apresentado na **estado** coluna, em seguida, o dispositivo está ligado. 
   * Se é apresentado um indicador de vermelho no ícone de dispositivo e indisponível é exibido no **estado** coluna, em seguida, o dispositivo não está ligado. 
   * Se **atualização** é apresentado na **estado** coluna, em seguida, o Snapshot Manager do StorSimple está a obter grupos de volumes e cópias de segurança associadas para um dispositivo ligado.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Atualizar ou reinstalar o Snapshot Manager do StorSimple
Deve desinstalar completamente o Snapshot Manager do StorSimple antes de atualizar ou reinstalar o software. 

Antes de reinstalar o Snapshot Manager do StorSimple, criar cópias de segurança da base de dados existente do Snapshot Manager do StorSimple no computador anfitrião. Isto guarda as informações de configuração e políticas de cópia de segurança, de modo a que pode facilmente restaurar os dados da cópia de segurança.

Se estiver a atualizar ou reinstalar o Snapshot Manager do StorSimple, siga estes passos:

* Passo 1: Desinstalar o Snapshot Manager do StorSimple 
* Passo 2: Criar cópias de segurança da base de dados do Snapshot Manager do StorSimple 
* Passo 3: Reinstalar o Snapshot Manager do StorSimple e restaurar a base de dados 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Passo 1: Desinstalar o Snapshot Manager do StorSimple
Utilize os seguintes passos para desinstalar o Snapshot Manager do StorSimple.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Para desinstalar o Snapshot Manager do StorSimple
1. No computador anfitrião, abra a **painel de controlo**, clique em **programas**e, em seguida, clique em **programas e funcionalidades**.
2. No painel esquerdo, clique em **desinstalar ou alterar um programa**.
3. Com o botão direito **Snapshot Manager do StorSimple**e, em seguida, clique em **desinstalação**.
4. Esta ação inicia o programa de configuração do Snapshot Manager do StorSimple. Clique em **modificar configuração**e, em seguida, clique em **desinstalação**.
   
   > [!NOTE]
   > Se existirem quaisquer processos MMC em execução em segundo plano, como o Snapshot Manager do StorSimple ou gestão de discos, a desinstalação irá falhar e receberá uma mensagem para fechar todas as instâncias do MMC, antes de tentar desinstalar o programa. Selecione **automaticamente fechar aplicações e tentar reiniciá-las após a conclusão da instalação**e, em seguida, clique em **OK**.
   > 
   > 
5. Quando o processo de desinstalação estiver concluído, um **concluída com êxito do programa de configuração** é apresentada a mensagem. Clique em **Fechar**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Passo 2: Criar cópias de segurança da base de dados do Snapshot Manager do StorSimple
Utilize os seguintes passos para criar e guardar uma cópia da base de dados do Snapshot Manager do StorSimple.

#### <a name="to-back-up-the-database"></a>Para criar cópias de segurança da base de dados
1. Pare o serviço de gestão do Microsoft StorSimple:
   
   1. Inicie o Gestor de servidor.
   2. No Dashboard do Gestor de servidores, sobre o **ferramentas** menu, selecione **serviços**.
   3. Sobre o **serviços** página, selecione **serviço de gestão do Microsoft StorSimple**.
   4. No painel direito, sob **serviço de gestão do Microsoft StorSimple**, clique em **parar o serviço**.
      
        ![Parar o serviço StorSimple Device Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Navegue para C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData é uma pasta oculta.
  
3. Encontrar o ficheiro XML de catálogo, copie o ficheiro e armazene a cópia num local seguro ou na cloud.
   
    ![Arquivo de catálogo de cópia de segurança do StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Reinicie o serviço de gestão do Microsoft StorSimple: 
   
   1. No Dashboard do Gestor de servidores, sobre o **ferramentas** menu, selecione **serviços**.
   2. Sobre o **serviços** página, selecione a **serviço de gestão do Microsoft StorSimple**.
   3. No painel direito, sob **serviço de gestão do Microsoft StorSimple**, clique em **reiniciar o serviço**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Passo 3: Reinstalar o Snapshot Manager do StorSimple e restaurar a base de dados
Para reinstalar o Snapshot Manager do StorSimple, siga os passos em [instalar um novo Snapshot Manager do StorSimple](#install-a-new-storsimple-snapshot-manager). Em seguida, utilize o procedimento seguinte para restaurar a base de dados do Snapshot Manager do StorSimple.

#### <a name="to-restore-the-database"></a>Para restaurar a base de dados
1. Pare o serviço de gestão do Microsoft StorSimple:
   
   1. Inicie o Gestor de servidor.
   2. No Dashboard do Gestor de servidores, sobre o **ferramentas** menu, selecione **serviços**.
   3. Sobre o **serviços** página, selecione **serviço de gestão do Microsoft StorSimple**.
   4. No painel direito, sob **serviço de gestão do Microsoft StorSimple**, clique em **parar o serviço**.
2. Navegue para C:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > ProgramData é uma pasta oculta.
   > 
   > 
3. Eliminar o ficheiro XML de catálogo e substitua-o com a versão que guardou anteriormente.
4. Reinicie o serviço de gestão do Microsoft StorSimple: 
   
   1. No Dashboard do Gestor de servidores, sobre o **ferramentas** menu, selecione **serviços**.
   2. Sobre o **serviços** página, selecione **serviço de gestão do Microsoft StorSimple**.
   3. No painel direito, sob **serviço de gestão do Microsoft StorSimple**, clique em **reiniciar o serviço**.

## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre o StorSimple Snapshot Manager, aceda a [o que é o Snapshot Manager do StorSimple?](storsimple-what-is-snapshot-manager.md).
* Para saber mais sobre a interface de utilizador do Snapshot Manager do StorSimple, aceda a [interface de utilizador do Snapshot Manager do StorSimple](storsimple-use-snapshot-manager.md).
* Para saber mais sobre como utilizar o Snapshot Manager do StorSimple, aceda à [utilização StorSimple Snapshot Manager para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).

