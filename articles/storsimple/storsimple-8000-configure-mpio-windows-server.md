---
title: Configurar o MPIO para o dispositivo StorSimple | Documentos da Microsoft
description: Descreve como configurar Multipath i / o (MPIO) para o dispositivo StorSimple ligado ao anfitrião com o Windows Server 2012 R2.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: alkohli
ms.openlocfilehash: eda134257edb851eea076459b44e02fc59028f46
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078147"
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Configurar Multipath i / o para o dispositivo StorSimple

Este tutorial descreve os passos que deve seguir para instalar e utilizar a funcionalidade de Multipath i / o (MPIO) num anfitrião com o Windows Server 2012 R2 e ligado a um dispositivo físico StorSimple. As orientações neste artigo aplica-se a série StorSimple 8000 apenas a dispositivos físicos. MPIO não é atualmente suportado numa StorSimple Cloud Appliance.

A Microsoft criou o suporte para a funcionalidade de Multipath i / o (MPIO) no Windows Server para ajudar a criar configurações de rede iSCSI de elevada disponibilidade, tolerante a falhas. MPIO utiliza componentes de caminho físico redundantes, placas, cabos e comutadores, para criar caminhos lógicos entre o servidor e o dispositivo de armazenamento. Se houver uma falha de componente, fazendo com que um caminho lógico falhe, lógica de multipathing utiliza um caminho alternativo de e/s para que os aplicativos ainda podem acessar seus dados. Além disso consoante a configuração, MPIO também pode melhorar desempenho ao reequilibrar a carga entre todos esses caminhos. Para obter mais informações, consulte [descrição geral MPIO](https://technet.microsoft.com/library/cc725907.aspx "MPIO descrição geral e funcionalidades").

Para elevada disponibilidade da solução StorSimple, MPIO deve ser configurado no dispositivo StorSimple. Quando o MPIO está instalado nos seus servidores de anfitrião com o Windows Server 2012 R2, os servidores, em seguida, podem tolerar uma ligação, a rede ou a falha de interface.

## <a name="mpio-configuration-summary"></a>Resumo da configuração do MPIO

MPIO é uma funcionalidade opcional no Windows Server e não está instalado por predefinição. Deve ser instalada como uma funcionalidade através do Server Manager.

Siga estes passos para configurar a MPIO no dispositivo StorSimple:

* Passo 1: Instalar a MPIO no anfitrião Windows Server
* Passo 2: Configurar o MPIO para volumes do StorSimple
* Passo 3: Volumes do StorSimple de montagem no anfitrião
* Passo 4: Configurar o MPIO para elevada disponibilidade e balanceamento de carga

Cada um dos passos anteriores é abordada nas seções a seguir.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Passo 1: Instalar a MPIO no anfitrião Windows Server

Para instalar esta funcionalidade no anfitrião do Windows Server, execute o seguinte procedimento.

#### <a name="to-install-mpio-on-the-host"></a>Instalar a MPIO no anfitrião

1. Abra o Gestor de servidor no seu anfitrião do Windows Server. Por predefinição, o Gestor de servidor é iniciado quando um membro do grupo Administradores inicia sessão num computador que está a executar o Windows Server 2012 R2 ou Windows Server 2012. Se o Gestor de servidor não esteja ainda aberto, clique em **Iniciar > Gestor de servidor**.
   
   ![Gestor de servidores](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Clique em **Gestor de servidor > Dashboard > Adicionar funções e funcionalidades**. Esta ação inicia o **para adicionar funções e funcionalidades** assistente.
   
   ![Assistente para adicionar funções e funcionalidades 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. Na **para adicionar funções e funcionalidades** assistente, execute os seguintes passos:
   
   1. Sobre o **antes de começar** página, clique em **próxima**.
   2. Sobre o **selecionar tipo de instalação** página, aceite a predefinição de **baseada em funções ou baseada em recursos** instalação. Clique em **Seguinte**.
   
       ![Assistente para adicionar funções e funcionalidades 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. Sobre o **selecionar servidor de destino** página, selecione **selecione um servidor no agrupamento de servidores**. O servidor de anfitrião deve ser detetado automaticamente. Clique em **Seguinte**.
   4. Sobre o **selecionar funções de servidor** página, clique em **próxima**.
   5. Sobre o **selecionar funcionalidades** , selecione **Multipath i /** e clique em **seguinte**.
   
       ![Assistente para adicionar funções e funcionalidades 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. Sobre o **confirmar seleções de instalação** página, confirme a seleção e, em seguida, selecione **reiniciar automaticamente o servidor de destino quando necessário**, conforme mostrado abaixo. Clique em **Instalar**.
   
       ![Assistente para adicionar funções e funcionalidades 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. Será notificado quando a instalação estiver concluída. Clique em **Fechar** para fechar o assistente.
   
       ![Assistente para adicionar funções e funcionalidades 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Passo 2: Configurar o MPIO para volumes do StorSimple

MPIO tem de ser configurado para identificar volumes do StorSimple. Para configurar o MPIO para reconhecer os volumes do StorSimple, execute os seguintes passos.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Configurar o MPIO para volumes do StorSimple

1. Abra o **configuração do MPIO**. Clique em **Gestor de servidor > Dashboard > Ferramentas > MPIO**.
2. Na **propriedades de MPIO** caixa de diálogo, selecione a **detetar vários caminhos** separador.
3. Selecione **adicionar suporte para dispositivos iSCSI**e, em seguida, clique em **Add**.  
   ![Propriedades MPIO detetar vários caminhos](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Reinicie o servidor quando lhe for pedido.
5. Na **propriedades de MPIO** caixa de diálogo, clique no **dispositivos de MPIO** separador. Clique em **Adicionar**.
    </br>![Dispositivos MPIO de propriedades do MPIO](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. Na **adicionar suporte de MPIO** caixa de diálogo em **ID de Hardware do dispositivo**, introduza o seu número de série do dispositivo. Para obter o número de série do dispositivo, aceda ao seu serviço de Gestor de dispositivos do StorSimple. Navegue para **dispositivos > Dashboard**. O número de série do dispositivo é apresentado no lado direito **Glance rápida** painel do dashboard do dispositivo.
    </br>
    ![Adicionar suporte a MPIO](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Reinicie o servidor quando lhe for pedido.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Passo 3: Volumes do StorSimple de montagem no anfitrião

Depois de MPIO está configurado no Windows Server, volumes criados no dispositivo StorSimple podem ser montados e, em seguida, podem tirar partido do MPIO para redundância. Para montar um volume, execute os seguintes passos.

#### <a name="to-mount-volumes-on-the-host"></a>Para montar os volumes no anfitrião

1. Abra o **propriedades do iniciador iSCSI** janela no anfitrião Windows Server. Clique em **Gestor de servidor > Dashboard > Ferramentas > iniciador iSCSI**.
2. Na **propriedades do iniciador iSCSI** caixa de diálogo, clique no separador de deteção e, em seguida, clique em **detetar Portal de destino**.
3. Na **detetar Portal de destino** diálogo caixa, execute os seguintes passos:
   
   1. Introduza o endereço IP da porta de dados do dispositivo StorSimple (por exemplo, introduza dados 0).
   2. Clique em **OK** para voltar para o **propriedades do iniciador iSCSI** caixa de diálogo.
     
      > [!IMPORTANT]
      > **Se estiver a utilizar uma rede privada para ligações de iSCSI, introduza o endereço IP da porta de dados que está ligado à rede privada.**
    
4. Repita os passos 2 e 3 para uma segunda interface de rede (por exemplo, 1 de dados) no seu dispositivo. Tenha em mente que essas interfaces devem ser ativadas para iSCSI. Para obter mais informações, consulte [modificar interfaces de rede](storsimple-8000-modify-device-config.md#modify-network-interfaces).
5. Selecione o **destinos** separador a **propriedades do iniciador iSCSI** caixa de diálogo. Deverá ver o dispositivo StorSimple IQN em de destino **destinos detetados**.

   ![Separador de destinos de propriedades do iniciador de iSCSI](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Clique em **Connect** para estabelecer uma sessão de iSCSI com o seu dispositivo StorSimple. R **ligue-se ao destino** é apresentada a caixa de diálogo.
7. Na **ligue-se ao destino** caixa de diálogo, selecione a **ativar múltiplos caminhos** caixa de verificação. Clique em **Advanced**.
8. Na **definições avançadas** diálogo caixa, execute os seguintes passos:
   
   1. Sobre o **adaptador Local** na lista pendente, selecione **Iniciador do Microsoft iSCSI**.
   2. Sobre o **IP iniciador** pendente, selecione o endereço IP do anfitrião.
   3. Na **Portal de destino** IP lista pendente, selecione o IP da interface do dispositivo.
   4. Clique em **OK** para voltar para o **propriedades do iniciador iSCSI** caixa de diálogo.
9. Clique em **Propriedades**. Na **propriedades** caixa de diálogo, clique em **sessão adicionar**.
10. Na **ligue-se ao destino** caixa de diálogo, selecione a **ativar múltiplos caminhos** caixa de verificação. Clique em **Advanced**.
11. Na **definições avançadas** caixa de diálogo:

    1. Sobre o **adaptador Local** na lista pendente, selecione Microsoft o Iniciador do iSCSI.
    2. Sobre o **IP iniciador** pendente, selecione o endereço IP correspondente no anfitrião. Neste caso, está a ligar duas interfaces de rede no dispositivo para uma única interface de rede no anfitrião. Portanto, essa interface é o mesmo que fornecido para a primeira sessão.
    3. Sobre o **IP do Portal de destino** na lista pendente, selecione o endereço IP para a segunda interface de dados ativada no dispositivo.
    4. Clique em **OK** para regressar à caixa de diálogo de propriedades do iniciador iSCSI. Adicionou uma segunda sessão para o destino.
12. Open **gestão de computadores** ao navegar até **Gestor de servidor > Dashboard > Gestão de computadores**. No painel esquerdo, clique em **armazenamento > Gestão de discos**. O volume criado no dispositivo StorSimple que estão visíveis para este anfitrião é apresentado em **gestão de discos** como novo disco (s).
13. Inicialize o disco e criar um novo volume. Durante o processo de formato, selecione um tamanho de bloco de 64 KB.
    
    ![Gestão de discos](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. Sob **gestão de discos**, clique com botão direito a **disco** e selecione **propriedades**.
15. No modelo de StorSimple # # # **propriedades do dispositivo de disco de múltiplos caminhos** caixa de diálogo, clique nas **MPIO** separador.
    
    ![StorSimple 8100 Multi-Path Disk DeviceProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. Na **nome do DSM** secção, clique em **detalhes** e certifique-se de que os parâmetros estão definidos para os parâmetros de predefinição. Os parâmetros de predefinição são:
    
    * Certifique-se de caminho período = 30
    * Contagem de repetições = 3
    * PDO remover período = 20
    * Intervalo de repetição = 1
    * Certifique-se de caminho ativado = desmarcada.

> [!NOTE]
> **Não modifique os parâmetros de predefinição.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Passo 4: Configurar o MPIO para elevada disponibilidade e balanceamento de carga

Para múltiplos caminhos com uma base de elevada disponibilidade e balanceamento de carga, várias sessões devem ser adicionados manualmente para declarar os caminhos diferentes disponíveis. Por exemplo, se o anfitrião tiver duas interfaces ligado à rede iSCSI e o dispositivo tiver duas interfaces ligados à rede iSCSI, então precisa de sessões de quatro configurados com permutas de cadeias de caminho apropriado (apenas duas sessões será necessárias se cada interface de dados e interface de anfitrião estiver numa sub-rede IP diferente e não for encaminhável).

**Recomendamos que tenha, pelo menos, 8 sessões paralelas ativas entre o dispositivo e seu host de aplicativo.** Isso pode ser conseguido ao ativar 4 interfaces de rede no seu sistema Windows Server. Utilize interfaces de rede física ou virtual interfaces por meio de tecnologias de Virtualização de rede no nível de hardware ou sistema operativo no seu anfitrião do Windows Server. Com as duas interfaces de rede no dispositivo, esta configuração resultaria em 8 sessões ativas. Esta configuração ajuda a otimizar o débito de dispositivo e na cloud.

> [!IMPORTANT]
> **Recomendamos que não misture de 1 GbE e interfaces de rede de 10 GbE. Se usar duas interfaces de rede, ambas as interfaces devem ter um tipo idêntico.**

O procedimento seguinte descreve como adicionar sessões quando um dispositivo do StorSimple com duas interfaces de rede está ligado a um anfitrião com duas interfaces de rede. Isto dá-lhe apenas 4 sessões. Utilize este mesmo procedimento com um dispositivo StorSimple com duas interfaces de rede ligados a um anfitrião com quatro interfaces de rede. Terá de configurar 8 em vez das sessões de 4 descritas aqui.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Para configurar o MPIO para elevada disponibilidade e balanceamento de carga

1. Executar uma deteção do destino: no **propriedades do iniciador iSCSI** caixa de diálogo a **deteção** separador, clique em **detetar Portal**.
2. Na **ligue-se ao destino** caixa de diálogo, introduza o endereço IP de uma das interfaces de rede do dispositivo.
3. Clique em **OK** para voltar para o **propriedades do iniciador iSCSI** caixa de diálogo.
4. Na **propriedades do iniciador iSCSI** caixa de diálogo, selecione a **destinos** separador, realce o destino detetado e, em seguida, clique em **Connect**. O **ligue-se ao destino** é apresentada a caixa de diálogo.
5. Na **ligue-se ao destino** caixa de diálogo:
   
   1. Deixe a predefinição selecionada a definição de destino para **adicionar esta ligação** à lista de destinos de Favoritos. Desta forma, o dispositivo tentar automaticamente reiniciar a conexão, sempre que este computador é reiniciado.
   2. Selecione o **ativar múltiplos caminhos** caixa de verificação.
   3. Clique em **Advanced**.
6. Na **definições avançadas** caixa de diálogo:
   
   1. Sobre o **adaptador Local** na lista pendente, selecione **Iniciador do Microsoft iSCSI**.
   2. Sobre o **IP iniciador** pendente, selecione o endereço IP correspondente no anfitrião (interface de iSCSI), a primeira interface.
   3. Sobre o **IP do Portal de destino** na lista pendente, selecione o endereço IP para a primeira interface de dados ativada no dispositivo.
   4. Clique em **OK** para regressar à caixa de diálogo de propriedades do iniciador iSCSI.
7. Clique em **propriedades**e, no **propriedades** caixa de diálogo, clique em **sessão adicionar**.
8. Na **ligue-se ao destino** caixa de diálogo, selecione a **ativar múltiplos caminhos** caixa de verificação e, em seguida, clique em **avançadas**.
9. Na **definições avançadas** caixa de diálogo:
   
   1. Sobre o **adaptador Local** na lista pendente, selecione **Iniciador do Microsoft iSCSI**.
   2. Sobre o **IP iniciador** pendente, selecione o endereço IP correspondente à segunda interface de iSCSI no anfitrião.
   3. Sobre o **IP do Portal de destino** na lista pendente, selecione o endereço IP para a segunda interface de dados ativada no dispositivo.
   4. Clique em **OK** para voltar para o **propriedades do iniciador iSCSI** caixa de diálogo. Acabou de adicionar uma segunda sessão para o destino.
10. Repita os passos 8 a 10 para adicionar sessões adicionais (caminhos) para o destino. Com duas interfaces no anfitrião e dois no dispositivo, pode adicionar um total de sessões de quatro.
11. Depois de adicionar as sessões desejadas (caminhos), no **propriedades do iniciador iSCSI** caixa de diálogo caixa, selecione o destino e clique em **propriedades**. No separador de sessões do **propriedades** caixa de diálogo, tenha em atenção a sessão de quatro identificadores que correspondem às permutas de cadeias de caminho possível. Para cancelar uma sessão, selecione a caixa de verificação junto a um identificador de sessão e, em seguida, clique em **desligar**.
12. Para ver os dispositivos apresentados no sessões, selecione o **dispositivos** separador. Para configurar a política MPIO para um dispositivo selecionado, clique em **MPIO**. O **detalhes do dispositivo** é apresentada a caixa de diálogo. Sobre o **MPIO** separador, pode selecionar a adequada **política de balanceamento de carga** definições. Também pode ver o **Active Directory** ou **modo de espera** tipo de caminho.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [com o serviço StorSimple Device Manager para modificar a configuração do dispositivo StorSimple](storsimple-8000-modify-device-config.md).

