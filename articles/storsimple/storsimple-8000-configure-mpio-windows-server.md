---
title: Configure mPIO para o seu dispositivo StorSimple / Microsoft Docs
description: Descreve como configurar o Multipath I/O (MPIO) para o seu dispositivo StorSimple ligado a um anfitrião que executa o Windows Server 2012 R2.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: alkohli
ms.openlocfilehash: 3d44fada1eddf2d3f80bec085d8a5bf751197eb1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968813"
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Configure o Multipato I/O para o seu dispositivo StorSimple

Este tutorial descreve os passos que deve seguir para instalar e utilizar a função Multipath I/O (MPIO) num anfitrião que executa o Windows Server 2012 R2 e ligado a um dispositivo físico StorSimple. A orientação deste artigo aplica-se apenas aos dispositivos físicos da série StorSimple 8000. Atualmente, o MPIO não é suportado num aparelho de nuvem StorSimple.

A Microsoft construiu suporte para a funcionalidade Multipath I/O (MPIO) no Windows Server para ajudar a construir configurações de rede iSCSI altamente disponíveis e tolerantes a falhas. O MPIO utiliza componentes de trajetória física redundantes — adaptadores, cabos e interruptores — para criar caminhos lógicos entre o servidor e o dispositivo de armazenamento. Se houver uma falha de componente, causando um caminho lógico para falhar, a lógica multipatar usa um caminho alternativo para a E/S para que as aplicações ainda possam aceder aos seus dados. Além disso, dependendo da sua configuração, o MPIO também pode melhorar o desempenho reequilibrando a carga em todos estes caminhos. Para mais informações, consulte [a visão geral do MPIO.](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725907(v=ws.11) "Visão geral do MPIO e características")

Para a elevada disponibilidade da sua solução StorSimple, o MPIO deve ser configurado no seu dispositivo StorSimple. Quando o MPIO é instalado nos servidores anfitriões que executam o Windows Server 2012 R2, os servidores podem então tolerar uma falha de ligação, rede ou interface.

## <a name="mpio-configuration-summary"></a>Resumo da configuração do MPIO

O MPIO é uma funcionalidade opcional no Windows Server e não é instalado por predefinição. Deve ser instalada como uma funcionalidade através do Server Manager.

Siga estes passos para configurar a MPIO no seu dispositivo StorSimple:

* Passo 1: Instalar o MPIO no anfitrião do Servidor do Windows
* Passo 2: Configurar o MPIO para volumes StorSimple
* Passo 3: Monte StorSimple volumes no hospedeiro
* Passo 4: Configurar o MPIO para uma elevada disponibilidade e equilíbrio de carga

Cada uma das etapas anteriores é discutida nas seguintes secções.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Passo 1: Instalar o MPIO no anfitrião do Servidor do Windows

Para instalar esta função no anfitrião do Windows Server, preencha o seguinte procedimento.

#### <a name="to-install-mpio-on-the-host"></a>Para instalar o MPIO no anfitrião

1. Abra o Gestor do Servidor no seu anfitrião do Servidor do Windows. Por predefinição, o Gestor do Servidor começa quando um membro do grupo de administradores inicia sessão num computador que está a executar o Windows Server 2012 R2 ou Windows Server 2012. Se o Gestor do Servidor ainda não estiver aberto, clique **em Iniciar > Gestor do Servidor**.
   
   ![Gestor de Servidor](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Clique **em "Server Manager" > dashboard > Adicionar funções e funcionalidades**. Isto inicia o assistente **de adicionar papéis e funcionalidades.**
   
   ![Adicionar papéis e funcionalidades Assistente 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. No assistente **Add Roles and Features,** execute os seguintes passos:
   
   1. Na página **Antes de começar**, clique em **Seguinte**.
   2. Na página **do tipo de instalação Select,** aceite a definição predefinitiva da instalação **baseada em funções ou baseada em recursos.** Clique em **Seguinte**.
   
       ![Adicionar papéis e funcionalidades Assistente 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. Na página **do servidor de destino Select,** escolha **Selecionar um servidor a partir da piscina do servidor**. O servidor anfitrião deve ser descoberto automaticamente. Clique em **Seguinte**.
   4. Na página **Selecionar papeis de servidor**, clique em **Seguinte**.
   5. Na página **'Selecione',** selecione **Multipath I/O** e clique em **Seguinte**.
   
       ![Adicionar papéis e funcionalidades Assistente 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. Na página **de seleções de instalação Confirmar,** confirme a seleção e, em seguida, selecione **Reinicie automaticamente o servidor de destino, se necessário,** como mostrado abaixo. Clique em **Install** (Instalar).
   
       ![Adicionar papéis e funcionalidades Assistente 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. É notificado quando a instalação estiver concluída. Clique em **Fechar** para fechar o assistente.
   
       ![Adicionar papéis e funcionalidades Assistente 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Passo 2: Configurar o MPIO para volumes StorSimple

O MPIO deve ser configurado para identificar volumes StorSimple. Para configurar a MPIO para reconhecer os volumes StorSimple, execute os seguintes passos.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Para configurar o MPIO para volumes StorSimple

1. Abra a **configuração MPIO**. Clique **em > Do Gestor do Servidor > Ferramentas > MPIO**.
2. Na caixa de diálogo **MPIO Properties,** selecione o **separador Discover Multi-Paths.**
3. **Selecione Adicionar suporte para dispositivos iSCSI** e, em seguida, clique em **Adicionar**.  
   ![Propriedades MPIO Descubram vários caminhos](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Reinicie o servidor quando solicitado.
5. Na caixa de diálogo **MPIO Properties,** clique no separador **dispositivos MPIO.** Clique em **Adicionar**.
    </br>![MPIO Propriedades Dispositivos MPIO](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. Na caixa de diálogo **de suporte Add MPIO,** no **ID do hardware do dispositivo,** introduza o número de série do dispositivo. Para obter o número de série do dispositivo, aceda ao seu serviço StorSimple Device Manager. Navegue para **dispositivos > dashboard**. O número de série do dispositivo é apresentado no painel **de controlo rápido** direito do painel de instrumentos.
    </br>
    ![Adicionar suporte MPIO](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Reinicie o servidor quando solicitado.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Passo 3: Monte StorSimple volumes no hospedeiro

Depois de o MPIO ser configurado no Windows Server, o volume(s) criado no dispositivo StorSimple pode ser montado e pode então aproveitar o MPIO para redundância. Para montar um volume, execute os seguintes passos.

#### <a name="to-mount-volumes-on-the-host"></a>Para montar volumes no hospedeiro

1. Abra a janela **iSCSI Initiator Properties** no anfitrião do Windows Server. Clique **em > Dashboard > Tools > iSCSI Initiator**.
2. Na caixa de diálogo iSCSI Initiator Properties, clique no separador Discovery e, em seguida, clique em **'Descobrir O Portal' ( 'Destino' ('SCSI'**(ISCSI), clique no separador Discovery e, em seguida, clique em "Discover Target Portal". **iSCSI Initiator Properties**
3. Na caixa de diálogo **Do Portal do Destino Discover,** execute os seguintes passos:
   
   1. Introduza o endereço IP da porta DATA do seu dispositivo StorSimple (por exemplo, introduza os DADOS 0).
   2. Clique **em OK** para voltar à caixa de diálogo **iSCSI Initiator Properties.**
     
      > [!IMPORTANT]
      > **Se estiver a utilizar uma rede privada para ligações iSCSI, insira o endereço IP da porta DATA que está ligada à rede privada.**
    
4. Repita os passos 2-3 para uma segunda interface de rede (por exemplo, DATA 1) no seu dispositivo. Tenha em mente que estas interfaces devem ser ativadas para o iSCSI. Para obter mais informações, consulte [Modificar as interfaces de rede.](storsimple-8000-modify-device-config.md#modify-network-interfaces)
5. Selecione o **separador Destinos** na caixa de diálogo **iSCSI Initiator Properties.** Você deve ver o alvo do dispositivo StorSimple IQN em **alvos descobertos**.

   ![Separador de destinos de propriedades de iniciador iSCSI](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Clique **em Connect** para estabelecer uma sessão iSCSI com o seu dispositivo StorSimple. Aparece uma caixa de diálogo **de ligação ao alvo.**
7. Na caixa de diálogo **'Ligar ao Alvo',** selecione a caixa de verificação **de vários caminhos** ativada. Clique em **Avançadas**.
8. Na caixa de diálogo **De Definições Avançadas,** execute os seguintes passos:
   
   1. Na lista de drop-down do **adaptador local,** selecione **Microsoft iSCSI Initiator**.
   2. Na lista de drop-down **do Iniciador IP,** selecione o endereço IP do anfitrião.
   3. Na lista de drop-down do **Portal alvo,** selecione o IP da interface do dispositivo.
   4. Clique **em OK** para voltar à caixa de diálogo **iSCSI Initiator Properties.**
9. Clique em **Propriedades**. Na caixa de diálogo **Propriedades,** clique em **Add Session**.
10. Na caixa de diálogo **'Ligar ao Alvo',** selecione a caixa de verificação **de vários caminhos** ativada. Clique em **Avançadas**.
11. Na caixa de diálogo **De Definições Avançadas:**

    1. Na lista de drop-down do **adaptador Local,** selecione o Iniciador Microsoft iSCSI.
    2. Na lista de drop-down **do Iniciador IP,** selecione o endereço IP correspondente ao anfitrião. Neste caso, encontra-se a ligar duas interfaces de rede no dispositivo a uma única interface de rede no anfitrião. Portanto, esta interface é a mesma que foi fornecida para a primeira sessão.
    3. Na lista de drop-down **do Portal alvo,** selecione o endereço IP para a segunda interface de dados ativada no dispositivo.
    4. Clique **em OK** para voltar à caixa de diálogo iSCSI Initiator Properties. Adicionou uma segunda sessão ao alvo.
12. Abrir **a Gestão de Computadores** navegando para **o Gestor do Servidor > Dashboard > Gestão de Computadores**. No painel esquerdo, clique em **Storage > Disk Management**. O volume criado no dispositivo StorSimple que é visível para este hospedeiro aparece sob **a Gestão** do Disco como novo disco(s).
13. Inicialize o disco e crie um novo volume. Durante o processo de formato, selecione um tamanho de bloco de 64 KB.
    
    ![Gestão de Disco](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. Em **Gestão de Discos,** clique com o botão direito no **Disco** e selecione **Propriedades.**
15. Na caixa de diálogo StorSimple Model #### **Multi-Path Disk Device Properties,** clique no separador **MPIO.**
    
    ![StorSimple 8100 Dispositivo de disco multi-pathProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. Na secção **Nome DSM,** clique em **Detalhes** e verifique se os parâmetros estão definidos para os parâmetros predefinidos. Os parâmetros padrão são:
    
    * Período de Verificação do Caminho = 30
    * Contagem de repetições = 3
    * Período de remoção do DOP = 20
    * Intervalo de retíria = 1
    * Verificação do caminho Ativado = Não verificado.

> [!NOTE]
> **Não modifique os parâmetros predefinidos.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Passo 4: Configurar o MPIO para uma elevada disponibilidade e equilíbrio de carga

Para uma elevada disponibilidade e equilíbrio de carga com base em vários caminhos, devem ser adicionadas várias sessões manualmente para declarar os diferentes caminhos disponíveis. Por exemplo, se o anfitrião tiver duas interfaces ligadas à rede iSCSI e o dispositivo tiver duas interfaces ligadas à rede iSCSI, então precisa de quatro sessões configuradas com permutações de caminho adequadas (apenas serão necessárias duas sessões se cada interface data e interface de anfitrião estiver numa sub-rede IP diferente e não for encaminhável).

**Recomendamos que tenha pelo menos 8 sessões paralelas ativas entre o dispositivo e o anfitrião da aplicação.** Isto pode ser conseguido ativando 4 interfaces de rede no seu sistema Windows Server. Utilize interfaces de rede físicas ou interfaces virtuais através de tecnologias de virtualização de rede no hardware ou nível do sistema operativo no seu anfitrião Windows Server. Com as duas interfaces de rede no dispositivo, esta configuração resultaria em 8 sessões ativas. Esta configuração ajuda a otimizar o dispositivo e a produção em nuvem.

> [!IMPORTANT]
> **Recomendamos que não misture interfaces de rede de 1 GbE e 10 GbE. Se utilizar duas interfaces de rede, ambas as interfaces devem ter um tipo idêntico.**

O procedimento a seguir descreve como adicionar sessões quando um dispositivo StorSimple com duas interfaces de rede está ligado a um hospedeiro com duas interfaces de rede. Isto dá-lhe apenas 4 sessões. Utilize este mesmo procedimento com um dispositivo StorSimple com duas interfaces de rede ligadas a um hospedeiro com quatro interfaces de rede. Terá de configurar 8 em vez das 4 sessões descritas aqui.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Para configurar o MPIO para uma elevada disponibilidade e equilíbrio de carga

1. Realize uma descoberta do alvo: na caixa de diálogo **iSCSI Initiator Properties,** no separador **Discovery,** clique em **Discover Portal**.
2. Na caixa de diálogo **'Ligar ao Alvo',** insira o endereço IP de uma das interfaces de rede do dispositivo.
3. Clique **em OK** para voltar à caixa de diálogo **iSCSI Initiator Properties.**
4. Na caixa de diálogo **iSCSI Initiator Properties,** selecione o **separador Alvos,** realce o alvo descoberto e, em seguida, clique em **Connect**. Aparece a caixa de diálogo **'Ligar ao Alvo'.**
5. Na caixa de diálogo **'Ligar ao Alvo':**
   
   1. Deixe a definição de destino selecionada por defeito para **Adicionar esta ligação** à lista de alvos favoritos. Isto faz com que o dispositivo tente reiniciar automaticamente a ligação sempre que este computador reinicia.
   2. Selecione a caixa de verificação **de vários caminhos** Ativa.
   3. Clique em **Avançadas**.
6. Na caixa de diálogo **De Definições Avançadas:**
   
   1. Na lista de drop-down do **adaptador local,** selecione **Microsoft iSCSI Initiator**.
   2. Na lista de drop-down **do Iniciador IP,** selecione o endereço IP correspondente à primeira interface no anfitrião (interface iSCSI).
   3. Na lista de drop-down **do Portal alvo,** selecione o endereço IP para a primeira interface de dados ativada no dispositivo.
   4. Clique **em OK** para voltar à caixa de diálogo iSCSI Initiator Properties.
7. Clique em **Propriedades**, e na caixa de diálogo **Propriedades,** clique em **Add Session**.
8. Na caixa de diálogo **'Ligar ao Alvo',** selecione a caixa de verificação **de vários caminhos** ativa e, em seguida, clique em **Advanced**.
9. Na caixa de diálogo **De Definições Avançadas:**
   
   1. Na lista de drop-down do **adaptador Local,** selecione **Microsoft iSCSI Initiator**.
   2. Na lista de drop-down **do Iniciador IP,** selecione o endereço IP correspondente à segunda interface iSCSI no anfitrião.
   3. Na lista de drop-down **do Portal alvo,** selecione o endereço IP para a segunda interface de dados ativada no dispositivo.
   4. Clique **em OK** para voltar à caixa de diálogo **iSCSI Initiator Properties.** Agora acrescentou uma segunda sessão ao alvo.
10. Repita os passos 8-10 para adicionar sessões adicionais (caminhos) ao alvo. Com duas interfaces no hospedeiro e duas no dispositivo, pode adicionar um total de quatro sessões.
11. Depois de adicionar as sessões desejadas (caminhos), na caixa de diálogo **iSCSI Initiator Properties,** selecione o alvo e clique em **Propriedades**. No separador Sessões da caixa de diálogo **Properties,** note os identificadores de quatro sessões que correspondem às possíveis permutações de caminhos. Para cancelar uma sessão, selecione a caixa de verificação ao lado de um identificador de sessão e, em seguida, clique em **Desligar**.
12. Para visualizar os dispositivos apresentados dentro das sessões, selecione o **separador Dispositivos.** Para configurar a política do MPIO para um dispositivo selecionado, clique em **MPIO**. Aparece a caixa de diálogo detalhes do **dispositivo.** No separador **MPIO,** pode selecionar as definições adequadas da Política de Equilíbrio de **Carga.** Também pode ver o tipo de percurso **Ativo** ou **Standby.**

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a utilização do serviço StorSimple Device Manager para modificar a configuração do dispositivo StorSimple](storsimple-8000-modify-device-config.md).