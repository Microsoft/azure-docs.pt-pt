---
title: Implementar O Gestor de Snapshot StorSimple [ StorSimple Snapshot Manager] Microsoft Docs
description: Saiba como descarregar e instalar o StorSimple Snapshot Manager, um snap-in MMC para gerir as funcionalidades de proteção de dados StorSimple e funcionalidades de backup.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75933371"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Implemente o Snap-in StorSimple Snapshot Manager MMC

## <a name="overview"></a>Descrição geral
O StorSimple Snapshot Manager é um snap-in da Consola de Gestão da Microsoft (MMC) que simplifica a proteção de dados e a gestão de backup num ambiente Microsoft Azure StorSimple. Com o StorSimple Snapshot Manager, pode gerir o Microsoft Azure StorSimple no local e armazenamento em nuvem como se fosse um sistema de armazenamento totalmente integrado, simplificando assim os processos de backup e restauro e reduzindo custos. 

Este tutorial descreve os requisitos de configuração, bem como procedimentos para instalar, remover e atualizar o StorSimple Snapshot Manager.

> [!NOTE]
> * Não é possível utilizar o StorSimple Snapshot Manager para gerir as Matrizes Virtuais Microsoft Azure StorSimple (também conhecidas como dispositivos virtuais StorSimple on-premises).
> * Se planeia instalar o StorSimple Update 2 no seu dispositivo StorSimple, certifique-se de que descarrega a versão mais recente do StorSimple Snapshot Manager e instale-o antes de instalar o **StorSimple Update 2**. A versão mais recente do StorSimple Snapshot Manager é compatível com o retrocesso e funciona com todas as versões lançadas do Microsoft Azure StorSimple. Se estiver a utilizar a versão anterior do StorSimple Snapshot Manager, terá de atualizá-lo (não precisa de desinstalar a versão anterior antes de instalar a nova versão).


## <a name="storsimple-snapshot-manager-installation"></a>Instalação StorSimple Snapshot Manager
O StorSimple Snapshot Manager pode ser instalado em computadores que estão a executar o Windows Server 2008 R2 SP1, Windows Server 2012 ou o sistema operativo Windows Server 2012 R2. Nos servidores que executam o Windows 2008 R2, deve também instalar o Windows Server 2008 SP1 e o Windows Management Framework 3.0.

Antes de instalar ou atualizar o snap-in do StorSimple Snapshot Manager para a Consola de Gestão da Microsoft (MMC), certifique-se de que o dispositivo Microsoft Azure StorSimple e o servidor anfitrião estão configurados corretamente.

## <a name="configure-prerequisites"></a>Configurar pré-requisitos
Os seguintes passos fornecem uma visão geral de alto nível das tarefas de configuração que deve completar antes de instalar o StorSimple Snapshot Manager. Para obter informações completas sobre a configuração e configuração do Microsoft Azure StorSimple, incluindo os requisitos do sistema e instruções passo a passo, consulte [implementar o seu dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md).

> [!IMPORTANT]
> Antes de começar, reveja a lista de verificação de configuração de [implementação](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) e os [pré-requisitos](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) de implementação no dispositivo [StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md).
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>Antes de instalar o StorSimple Snapshot Manager
1. Desembalar, montar e ligar o dispositivo Microsoft Azure StorSimple conforme descrito no [dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [instale o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Certifique-se de que o seu computador de acolhimento está a executar um dos seguintes sistemas operativos:
   
   * Windows Server 2008 R2 (nos servidores que executam o Windows 2008 R2, deve também instalar o Windows Server 2008 SP1 e o Windows Management Framework 3.0)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     Para um dispositivo virtual StorSimple, o anfitrião deve ser uma Máquina Virtual Microsoft Azure.
3. Certifique-se de que cumpre todos os requisitos de configuração Do Microsoft Azure StorSimple. Para mais detalhes, vá aos [pré-requisitos](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites)de Implantação .
4. Ligue o dispositivo ao hospedeiro e execute a configuração inicial. Para mais detalhes, vá aos [passos de Implantação.](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps)
5. Crie volumes no dispositivo, atribua-os ao hospedeiro e verifique se o hospedeiro pode montá-los e usá-los. O StorSimple Snapshot Manager suporta os seguintes tipos de volumes:
   
   * Volumes básicos
   * Volumes simples
   * Volumes dinâmicos
   * Volumes dinâmicos espelhados (RAID 1)
   * Volumes partilhados por cluster
     
     Para obter informações sobre a criação de volumes no dispositivo StorSimple ou no dispositivo virtual StorSimple, vá ao [Passo 6: Criar um volume](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume), no dispositivo [StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Instale um novo StorSimple Snapshot Manager
Antes de instalar o StorSimple Snapshot Manager, certifique-se de que os volumes que criou no dispositivo StorSimple ou no dispositivo virtual StorSimple são montados, inicializados e formatados conforme descrito nos [pré-requisitos](#configure-prerequisites)da Configuração .

> [!IMPORTANT]
> * Para um dispositivo virtual StorSimple, o anfitrião deve ser uma Máquina Virtual Microsoft Azure. 
> * O anfitrião deve estar a executar o Windows 2008 R2, o Windows Server 2012 ou o Windows Server 2012 R2. Se o seu servidor estiver a executar o Windows Server 2008 R2, também deve instalar o Windows Server 2008 SP1 e o Windows Management Framework 3.0.
> * Tem de configurar uma ligação iSCSI do anfitrião ao dispositivo StorSimple antes de poder ligar o dispositivo ao StorSimple Snapshot Manager.

Siga estes passos para completar uma nova instalação do StorSimple Snapshot Manager. Se estiver a instalar uma atualização, vá para [atualizar ou reinstale o StorSimple Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

* Passo 1: Instalar O Gestor de Instantâneos StorSimple Simple 
* Passo 2: Ligar o StorSimple Snapshot Manager a um dispositivo 
* Passo 3: Verificar a ligação ao dispositivo 

### <a name="step-1-install-storsimple-snapshot-manager"></a>Passo 1: Instalar O Gestor de Instantâneos StorSimple Simple
Utilize os seguintes passos para instalar o StorSimple Snapshot Manager.

#### <a name="to-install-storsimple-snapshot-manager"></a>Para instalar o StorSimple Snapshot Manager
1. Descarregue o software StorSimple Snapshot Manager (vá ao [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) no Microsoft Download Center) e guarde-o localmente no anfitrião.
2. No File Explorer, clique na pasta comprimido e clique em **extrair tudo**.
3. Na janela **de pastas Comprimidas (Zipped),** na caixa de **ficheiros de destino e extrato,** escreva ou navegue pelo caminho onde pretende arquivar para ser extraído.
   
    > [!IMPORTANT]
    > Tem de instalar o StorSimple Snapshot Manager no C: unidade.
    
4. Selecione os **ficheiros extraídos do Show quando estiver completo** a caixa de verificação e, em seguida, clique em **Extrair**.
   
    ![Extrair caixas de diálogo de ficheiros](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. Quando a extração estiver terminada, abre-se a pasta de destino. Clique duas vezes no ícone de configuração da aplicação que aparece na pasta de destino.
6. Quando a mensagem **de sucesso configurar** aparecer, clique em **Fechar**. Deve ver o ícone StorSimple Snapshot Manager no seu ambiente de trabalho.
   
    ![ícone de ambiente de trabalho](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Passo 2: Ligar o StorSimple Snapshot Manager a um dispositivo
Utilize os seguintes passos para ligar o StorSimple Snapshot Manager a um dispositivo StorSimple.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Para ligar o StorSimple Snapshot Manager a um dispositivo
1. Clique no ícone StorSimple Snapshot Manager no seu ambiente de trabalho. Aparece a janela StorSimple Snapshot Manager. A janela contém um painel **de âmbito,** um painel de **resultados** e um painel de **ações.** 
   
    ![Interface de utilizador do StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * O painel **scope** (o painel esquerdo) contém uma lista de nós organizados numa estrutura de árvores. Pode expandir alguns nódosos para selecionar uma vista ou dados específicos relacionados com esse nó. Clique no ícone da seta para expandir ou colapsar um nó. Clique num item no **painel** scope para ver uma lista de ações disponíveis para esse item.
   * O painel **de resultados** (o painel central) contém informações detalhadas sobre o nó, vista ou dados que selecionou no painel **scope.**
   * O painel **de ações** lista as operações que pode realizar no nó, visualização ou dados que selecionou no painel **Scope.**
     
     Para obter uma descrição completa da interface de utilizador do StorSimple Snapshot Manager, consulte a interface de [utilizador do StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).
2. No painel **Scope,** clique no nó dos **Dispositivos** e, em seguida, clique em **Configurar um dispositivo**. Aparece a caixa de diálogo **Configuração de um Dispositivo.**
   
    ![Configurar um dispositivo](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. Na caixa da lista **do Dispositivo,** selecione o endereço IP do dispositivo Microsoft Azure StorSimple ou dispositivo virtual. Na caixa de texto **Password,** digite a palavra-passe StorSimple Snapshot Manager que criou para o dispositivo no portal Azure. Clique em **OK**.
4. O StorSimple Snapshot Manager procura o dispositivo que identificou. Se o dispositivo estiver disponível, o StorSimple Snapshot Manager adiciona uma ligação. Pode [verificar a ligação ao dispositivo](#to-verify-the-connection) para confirmar que a ligação foi adicionada com sucesso.
   
    Se o dispositivo não estiver disponível por qualquer motivo, o StorSimple Snapshot Manager devolve uma mensagem de erro. Clique **em OK** para fechar a mensagem de erro e, em seguida, clique em **Cancelar** para fechar a caixa de diálogo **Configurar um Dispositivo.**
5. Quando se conecta a um dispositivo, o StorSimple Snapshot Manager importa cada grupo de volume configurado para esse dispositivo, desde que o grupo de volume tenha cópias de segurança associadas. Não são importados grupos de volume que não possuam cópias de segurança associadas. Além disso, as políticas de backup criadas para um grupo de volume não são importadas. Para ver os grupos importados, clique à direita no nó dos **grupos** de maior volume no painel **scope** e clique em **Toggle grupos importados**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Passo 3: Verificar a ligação ao dispositivo
Utilize os seguintes passos para verificar se o StorSimple Snapshot Manager está ligado ao dispositivo StorSimple.

#### <a name="to-verify-the-connection"></a>Para verificar a ligação
1. No painel **Scope,** clique no nó dos **Dispositivos.**
   
    ![Estado do dispositivo StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Verifique o painel **de resultados:** 
   
   * Se aparecer um indicador verde no ícone do dispositivo e **disponível** aparecer na coluna **'Estado',** o dispositivo está ligado. 
   * Se aparecer um indicador vermelho no ícone do dispositivo e o Indisponível aparecer na coluna **'Estado',** então o dispositivo não está ligado. 
   * Se o **Refreshing** aparecer na coluna **'Status',** então o StorSimple Snapshot Manager está a recuperar grupos de volume e cópias de segurança associadas para um dispositivo conectado.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Atualizar ou reinstalar O Gestor de Snapshot StorSimple
Deve desinstalar completamente o StorSimple Snapshot Manager antes de atualizar ou reinstalar o software. 

Antes de reinstalar o StorSimple Snapshot Manager, volte a colocar a base de dados storSimple Snapshot Manager existente no computador anfitrião. Isto guarda as políticas de backup e informações de configuração para que possa facilmente restaurar estes dados a partir de backup.

Siga estes passos se estiver a atualizar ou a reinstalar o StorSimple Snapshot Manager:

* Passo 1: Desinstalar O Gestor de Snapshot StorSimple 
* Passo 2: Apoiar a base de dados do StorSimple Snapshot Manager 
* Passo 3: Reinstalar o StorSimple Snapshot Manager e restaurar a base de dados 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Passo 1: Desinstalar O Gestor de Snapshot StorSimple
Utilize os seguintes passos para desinstalar o StorSimple Snapshot Manager.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Para desinstalar o StorSimple Snapshot Manager
1. No computador anfitrião, abra o Painel de **Controlo,** clique em **Programas**e clique em **Programas e Funcionalidades**.
2. No painel esquerdo, clique em **Desinstalar ou alterar um programa**.
3. Clique no **StorSimple Snapshot Manager**e, em seguida, clique em **Desinstalar**.
4. Isto inicia o programa storSimple Snapshot Manager Setup. Clique em **Modificar configuração**e, em seguida, clique em **desinstalar**.
   
   > [!NOTE]
   > Se houver algum processo MMC em segundo plano, como o StorSimple Snapshot Manager ou a Disk Management, a desinstalação falhará e receberá uma mensagem para encerrar todas as instâncias do MMC antes de tentar desinstalar o programa. Selecione **automaticamente fechar as aplicações e tentar reiniciá-las depois**de concluída a configuração , e, em seguida, clique em **OK**.
   > 
   > 
5. Quando o processo de desinstalação estiver terminado, aparece uma mensagem de sucesso de **configuração.** Clique em **Fechar**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Passo 2: Apoiar a base de dados do StorSimple Snapshot Manager
Utilize os seguintes passos para criar e guardar uma cópia da base de dados StorSimple Snapshot Manager.

#### <a name="to-back-up-the-database"></a>Para fazer o apoio à base de dados
1. Pare o Serviço de Gestão Microsoft StorSimple:
   
   1. Inicie o Gestor de Servidores.
   2. No Dashboard do Gestor de Servidores, no menu **Ferramentas,** selecione **Serviços**.
   3. Na página **Serviços,** selecione **Microsoft StorSimple Management Service**.
   4. No painel certo, no **microsoft StorSimple Management Service,** clique em **Parar o serviço**.
      
        ![Pare o serviço StorSimple Device Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Navegue em C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData é uma pasta oculta.
  
3. Encontre o ficheiro XML do catálogo, copie o ficheiro e guarde a cópia num local seguro ou na nuvem.
   
    ![Ficheiro de catálogo de backup StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Reiniciar o Serviço de Gestão Microsoft StorSimple: 
   
   1. No Dashboard do Gestor de Servidores, no menu **Ferramentas,** selecione **Serviços**.
   2. Na página **Serviços,** selecione o Serviço de **Gestão Microsoft StorSimple**.
   3. No painel certo, no **microsoft StorSimple Management Service,** clique **em reiniciar o serviço**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Passo 3: Reinstalar o StorSimple Snapshot Manager e restaurar a base de dados
Para reinstalar o StorSimple Snapshot Manager, siga os passos em [Instalar um novo StorSimple Snapshot Manager](#install-a-new-storsimple-snapshot-manager). Em seguida, utilize o seguinte procedimento para restaurar a base de dados StorSimple Snapshot Manager.

#### <a name="to-restore-the-database"></a>Para restaurar a base de dados
1. Pare o Serviço de Gestão Microsoft StorSimple:
   
   1. Inicie o Gestor de Servidores.
   2. No Dashboard do Gestor de Servidores, no menu **Ferramentas,** selecione **Serviços**.
   3. Na página **Serviços,** selecione **Microsoft StorSimple Management Service**.
   4. No painel certo, no **microsoft StorSimple Management Service,** clique em **Parar o serviço**.
2. Navegue em C:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > ProgramData é uma pasta oculta.
   > 
   > 
3. Elimine o ficheiro XML do catálogo e substitua-o pela versão que guardou anteriormente.
4. Reiniciar o Serviço de Gestão Microsoft StorSimple: 
   
   1. No Dashboard do Gestor de Servidores, no menu **Ferramentas,** selecione **Serviços**.
   2. Na página **Serviços,** selecione **Microsoft StorSimple Management Service**.
   3. No painel certo, no **microsoft StorSimple Management Service,** clique **em reiniciar o serviço**.

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre o StorSimple Snapshot Manager, vá ao [What is StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)
* Para saber mais sobre a interface de utilizador do StorSimple Snapshot Manager, vá à interface de [utilizador do StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).
* Para saber mais sobre a utilização do StorSimple Snapshot Manager, vá ao [StorSimple Snapshot Manager para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).

