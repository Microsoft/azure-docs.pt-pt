---
title: Configure mpIO para o seu dispositivo StorSimple / Microsoft Docs
description: Descreve como configurar o Multipath I/O (MPIO) para o seu dispositivo StorSimple ligado a um anfitrião que executa o Windows Server 2012 R2.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "60363397"
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Configure Multipath I/O para o seu dispositivo StorSimple

Este tutorial descreve os passos que deve seguir para instalar e utilizar a funcionalidade Multipath I/O (MPIO) num anfitrião que executa o Windows Server 2012 R2 e está ligado a um dispositivo físico StorSimple. A orientação neste artigo aplica-se apenas aos dispositivos físicos da série StorSimple 8000. O MPIO não é atualmente suportado num StorSimple Cloud Appliance.

A Microsoft construiu suporte para a funcionalidade Multipath I/O (MPIO) no Windows Server para ajudar a construir configurações de rede iSCSI altamente disponíveis e tolerantes a falhas. O MPIO utiliza componentes de percurso físico redundantes — adaptadores, cabos e interruptores — para criar caminhos lógicos entre o servidor e o dispositivo de armazenamento. Se houver uma falha de componente, fazendo com que um caminho lógico falhe, a lógica multipata utiliza um caminho alternativo para o I/S para que as aplicações ainda possam aceder aos seus dados. Além disso, dependendo da sua configuração, o MPIO também pode melhorar o desempenho reequilibrando a carga em todos estes caminhos. Para mais informações, consulte a visão geral do [MPIO](https://technet.microsoft.com/library/cc725907.aspx "Visão geral do MPIO e funcionalidades").

Para a elevada disponibilidade da sua solução StorSimple, o MPIO deve ser configurado no seu dispositivo StorSimple. Quando o MPIO é instalado nos seus servidores anfitriões que executam o Windows Server 2012 R2, os servidores podem então tolerar uma falha de ligação, rede ou interface.

## <a name="mpio-configuration-summary"></a>Resumo de configuração MPIO

MpIO é uma funcionalidade opcional no Windows Server e não é instalada por padrão. Deve ser instalada como uma funcionalidade através do Server Manager.

Siga estes passos para configurar mpio no seu dispositivo StorSimple:

* Passo 1: Instale MPIO no anfitrião do Servidor do Windows
* Passo 2: Configure MPIO para volumes StorSimple
* Passo 3: Monte StorSimple volumes no hospedeiro
* Passo 4: Configure MPIO para alta disponibilidade e equilíbrio de carga

Cada um dos passos anteriores é discutido nas seguintes secções.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Passo 1: Instale MPIO no anfitrião do Servidor do Windows

Para instalar esta funcionalidade no seu anfitrião do Windows Server, complete o seguinte procedimento.

#### <a name="to-install-mpio-on-the-host"></a>Para instalar MPIO no hospedeiro

1. Abra o Gestor de Servidores no seu anfitrião do Servidor windows. Por padrão, o Server Manager começa quando um membro do grupo Deadministradores inicia um computador que está a executar o Windows Server 2012 R2 ou o Windows Server 2012. Se o Gestor do Servidor ainda não estiver aberto, clique em **Iniciar > Server Manager**.
   
   ![Gestor de Servidor](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Clique no **Gestor do Servidor > dashboard > Adicionar funções e funcionalidades**. Isto inicia o assistente **de adicionar papéis e funcionalidades.**
   
   ![Adicionar papéis e funcionalidades wizard 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. No assistente **de adicionar funções e funcionalidades,** execute os seguintes passos:
   
   1. Na página **Antes de começar**, clique em **Seguinte**.
   2. Na página do tipo de **instalação Select,** aceite a definição predefinida da instalação **baseada em Funções ou baseada em funcionalidades.** Clique em **Seguinte**.
   
       ![Adicionar papéis e funcionalidades wizard 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. Na página do servidor de **destino Select,** escolha **Selecione um servidor a partir da piscina do servidor**. O seu servidor de anfitriões deve ser descoberto automaticamente. Clique em **Seguinte**.
   4. Na página **Selecionar papeis de servidor**, clique em **Seguinte**.
   5. Na página **Select,** selecione **Multipath I/O**e clique **em Next**.
   
       ![Adicionar papéis e funcionalidades wizard 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. Na página de seleções de **instalações Confirmar,** confirme a seleção e, em seguida, selecione **Reiniciar automaticamente o servidor de destino, se necessário,** como mostrado abaixo. Clique em **Instalar**.
   
       ![Adicionar papéis e funcionalidades wizard 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. É notificado quando a instalação estiver concluída. Clique em **Fechar** para fechar o assistente.
   
       ![Adicionar papéis e funcionalidades Wizard 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Passo 2: Configure MPIO para volumes StorSimple

O MPIO deve ser configurado para identificar volumes StorSimple. Para configurar mpiO para reconhecer volumes StorSimple, execute os seguintes passos.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Para configurar MPIO para volumes StorSimple

1. Abra a **configuração MPIO**. Clique no **gestor do servidor > ferramentas de > do dashboard > MPIO**.
2. Na caixa de diálogo **MPIO Properties,** selecione o separador **Discover Multi-Paths.**
3. Selecione **Adicionar suporte para dispositivos iSCSI,** e, em seguida, clique em **Adicionar**.  
   ![Propriedades MPIO Descobrem caminhos múltiplos](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Reinicie o servidor quando solicitado.
5. Na caixa de diálogo **MPIO Properties,** clique no separador **dispositivos MPIO.** Clique em **Adicionar**.
    </br>![Dispositivos MPIO Properties MPIO](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. Na caixa de diálogo **add MPIO Suporte,** em id de hardware do **dispositivo,** introduza o número de série do seu dispositivo. Para obter o número de série do dispositivo, aceda ao serviço StorSimple Device Manager. Navegue para **dispositivos > Dashboard**. O número de série do dispositivo é apresentado no painel **de quick glance** direito do painel do dispositivo.
    </br>
    ![Adicionar suporte de MPIO](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Reinicie o servidor quando solicitado.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Passo 3: Monte StorSimple volumes no hospedeiro

Depois de o MPIO estar configurado no Windows Server, o volume(s) criado no dispositivo StorSimple pode ser montado e pode, em seguida, tirar partido do MPIO para redundância. Para montar um volume, execute os seguintes passos.

#### <a name="to-mount-volumes-on-the-host"></a>Para montar volumes no hospedeiro

1. Abra a janela **iSCSI Initiator Properties** no anfitrião do Servidor do Windows. Clique no **Gestor do Servidor > dashboard > Ferramentas > iSCSI Iniciador**.
2. Na caixa de diálogo **iSCSI Initiator Properties,** clique no separador Discovery e clique em **Discover Target Portal**.
3. Na caixa de diálogo **Discover Target Portal,** execute os seguintes passos:
   
   1. Introduza o endereço IP da porta DATA do seu dispositivo StorSimple (por exemplo, introduza DADOS 0).
   2. Clique **em OK** para voltar à caixa de diálogo **iSCSI Initiator Properties.**
     
      > [!IMPORTANT]
      > **Se estiver a utilizar uma rede privada para ligações iSCSI, introduza o endereço IP da porta DATA que está ligada à rede privada.**
    
4. Repita os passos 2-3 para uma segunda interface de rede (por exemplo, DATA 1) no seu dispositivo. Tenha em mente que estas interfaces devem ser ativadas para o iSCSI. Para mais informações, consulte [Modificar as interfaces de rede](storsimple-8000-modify-device-config.md#modify-network-interfaces).
5. Selecione o separador **Targets** na caixa de diálogo **iSCSI Initiator Properties.** Deve ver o alvo do dispositivo StorSimple IQN em alvos **descobertos**.

   ![ISCSI Initiator Properties Targets Tab](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Clique em **Ligar** para estabelecer uma sessão iSCSI com o seu dispositivo StorSimple. Aparece uma caixa de diálogo **Connect to Target.**
7. Na caixa de diálogo **Connect to Target,** selecione a caixa de verificação de **vários percursos.** Clique em **Avançadas**.
8. Na caixa de diálogo **Definições Avançadas,** execute os seguintes passos:
   
   1. Na lista de drop-down do **Adaptador Local,** selecione **Microsoft iSCSI Initiator**.
   2. Na lista de entrega seletiva ip do **Iniciário,** selecione o endereço IP do anfitrião.
   3. Na lista de desaques do **Portal-Alvo** IP, selecione o IP da interface do dispositivo.
   4. Clique **em OK** para voltar à caixa de diálogo **iSCSI Initiator Properties.**
9. Clique em **Propriedades**. Na caixa de diálogo **Properties,** clique em **Adicionar Sessão**.
10. Na caixa de diálogo **Connect to Target,** selecione a caixa de verificação de **vários percursos.** Clique em **Avançadas**.
11. Na caixa de diálogo **Definições Avançadas:**

    1. Na lista de drop-down do **adaptador local,** selecione Microsoft iSCSI Initiator.
    2. Na lista de entrega seletiva ip do **Iniciador,** selecione o endereço IP correspondente ao anfitrião. Neste caso, está a ligar duas interfaces de rede no dispositivo a uma única interface de rede no hospedeiro. Portanto, esta interface é a mesma que previa para a primeira sessão.
    3. Na lista de desativação ip do **Portal alvo,** selecione o endereço IP para a segunda interface de dados ativada no dispositivo.
    4. Clique **em OK** para voltar à caixa de diálogo iSCSI Initiator Properties. Adicionou uma segunda sessão ao alvo.
12. Abra a **Gestão de Computadores** navegando para o Gestor de **Servidores > Dashboard > Gestão de Computadores.** No painel esquerdo, clique em **Armazenamento > Gestão de Discos**. O volume criado no dispositivo StorSimple que é visível para este hospedeiro aparece sob **a Gestão** do Disco como novo disco(s).
13. Inicialize o disco e crie um novo volume. Durante o processo de formato, selecione um tamanho de bloco de 64 KB.
    
    ![Gestão de Disco](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. Sob gestão de **discos,** clique no **disco** e selecione **Propriedades**.
15. Na caixa de diálogo StorSimple Model #### **Multi-Path Disk Device Properties,** clique no separador **MPIO.**
    
    ![StorSimple 8100 Multi-Path Disk DeviceProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. Na secção **Nome DSM,** clique em **Detalhes** e verifique se os parâmetros estão definidos para os parâmetros predefinidos. Os parâmetros predefinidos são:
    
    * Período de verificação do caminho = 30
    * Contagem de retry = 3
    * Período de remoção do DOP = 20
    * Intervalo de repetição = 1
    * Verificação do caminho ativado = sem controlo.

> [!NOTE]
> **Não modifique os parâmetros predefinidos.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Passo 4: Configure MPIO para alta disponibilidade e equilíbrio de carga

Para uma alta disponibilidade multi-caminho e equilíbrio de carga, várias sessões devem ser adicionadas manualmente para declarar os diferentes caminhos disponíveis. Por exemplo, se o anfitrião tiver duas interfaces ligadas à rede iSCSI e o dispositivo tiver duas interfaces ligadas à rede iSCSI, então precisa de quatro sessões configuradas com permutações de trajetória adequadas (serão necessárias apenas duas sessões se cada interface DATA e interface de anfitrião estiver numa subnet IP diferente e não for resaída).

**Recomendamos que tenha pelo menos 8 sessões paralelas ativas entre o dispositivo e o anfitrião da aplicação.** Isto pode ser conseguido permitindo 4 interfaces de rede no seu sistema Windows Server. Utilize interfaces de rede física ou interfaces virtuais através de tecnologias de virtualização de rede no nível de hardware ou sistema operativo no seu anfitrião Do Windows Server. Com as duas interfaces de rede no dispositivo, esta configuração resultaria em 8 sessões ativas. Esta configuração ajuda a otimizar o dispositivo e a entrada em nuvem.

> [!IMPORTANT]
> **Recomendamos que não misture interfaces de rede de 1 GbE e 10 GbE. Se utilizar duas interfaces de rede, ambas as interfaces devem ser de um tipo idêntico.**

O procedimento seguinte descreve como adicionar sessões quando um dispositivo StorSimple com duas interfaces de rede está ligado a um hospedeiro com duas interfaces de rede. Isto dá-lhe apenas 4 sessões. Utilize este mesmo procedimento com um dispositivo StorSimple com duas interfaces de rede ligadas a um hospedeiro com quatro interfaces de rede. Terá de configurar 8 em vez das 4 sessões aqui descritas.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Para configurar mpiO para alta disponibilidade e equilíbrio de carga

1. Execute uma descoberta do alvo: na caixa de diálogo **iSCSI Initiator Properties,** no separador **Discovery,** clique no **Discover Portal**.
2. Na caixa de diálogo **Connect to Target,** introduza o endereço IP de uma das interfaces de rede do dispositivo.
3. Clique **em OK** para voltar à caixa de diálogo **iSCSI Initiator Properties.**
4. Na caixa de diálogo **iSCSI Initiator Properties,** selecione o separador **Targets,** realce o alvo descoberto e, em seguida, clique em **Connect**. Aparece a caixa de diálogo **Connect to Target.**
5. Na caixa de diálogo **Connect to Target:**
   
   1. Deixe a definição de alvo selecionada por defeito para **adicionar esta ligação** à lista dos alvos favoritos. Isto faz com que o dispositivo tente reiniciar automaticamente a ligação sempre que este computador reinicia.
   2. Selecione a caixa de verificação de **vários percursos.**
   3. Clique em **Avançadas**.
6. Na caixa de diálogo **Definições Avançadas:**
   
   1. Na lista de drop-down do **Adaptador Local,** selecione **Microsoft iSCSI Initiator**.
   2. Na lista de entrega seletiva ip do **Iniciador,** selecione o endereço IP correspondente à primeira interface do anfitrião (interface iSCSI).
   3. Na lista de desativação ip do **Portal alvo,** selecione o endereço IP para a primeira interface de dados ativada no dispositivo.
   4. Clique **em OK** para voltar à caixa de diálogo iSCSI Initiator Properties.
7. Clique em **Propriedades,** e na caixa de diálogo **Properties,** clique em **Adicionar Sessão**.
8. Na caixa de diálogo **Connect to Target,** selecione a caixa de verificação de **vários percursos e,** em seguida, clique em **Advanced**.
9. Na caixa de diálogo **Definições Avançadas:**
   
   1. Na lista local de drop-down do **adaptador,** selecione **Microsoft iSCSI Initiator**.
   2. Na lista de entrega seletiva ip do **Iniciário,** selecione o endereço IP correspondente à segunda interface iSCSI no hospedeiro.
   3. Na lista de desativação ip do **Portal alvo,** selecione o endereço IP para a segunda interface de dados ativada no dispositivo.
   4. Clique **em OK** para voltar à caixa de diálogo **iSCSI Initiator Properties.** Adicionou agora uma segunda sessão ao alvo.
10. Repita os passos 8-10 para adicionar sessões adicionais (caminhos) ao alvo. Com duas interfaces no hospedeiro e duas no dispositivo, pode adicionar um total de quatro sessões.
11. Depois de adicionar as sessões desejadas (caminhos), na caixa de diálogo **iSCSI Initiator Properties,** selecione o alvo e clique em **Propriedades**. No separador Sessões da caixa de diálogo **Properties,** note os quatro identificadores de sessão que correspondem às possíveis permutações de caminho. Para cancelar uma sessão, selecione a caixa de verificação junto a um identificador de sessão e, em seguida, clique em **Desligar**.
12. Para visualizar os dispositivos apresentados dentro das sessões, selecione o separador **Dispositivos.** Para configurar a política de MPIO para um dispositivo selecionado, clique em **MPIO**. Aparece a caixa de diálogo De detalhes do **dispositivo.** No **separador MPIO,** pode selecionar as definições de Política de Equilíbrio de **Carga** adequadas. Também pode ver o tipo de caminho **Ativo** ou **De Standby.**

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a utilização do serviço StorSimple Device Manager para modificar a configuração do seu dispositivo StorSimple](storsimple-8000-modify-device-config.md).

