---
title: Instale o dispositivo Microsoft Azure StorSimple 8100
description: Descreve como desempacotar, montar e cabo do seu dispositivo StorSimple 8100 antes de implantar e configurar o software.
author: alkohli
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 113b72ddf7e5d508c8a0b577d4004d4fbd83e8e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84699129"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Desembale, suporte de cremalheira e cabo o seu dispositivo StorSimple 8100

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição Geral
O microsoft Azure StorSimple 8100 é um único recinto, um dispositivo montado na cremalheira. Este tutorial explica como desempacotar, montar e cabo o hardware do dispositivo StorSimple 8100 antes de configurar e implantar o dispositivo StorSimple.

## <a name="unpack-your-storsimple-8100-device"></a>Desembale o seu dispositivo StorSimple 8100
Os seguintes passos fornecem instruções claras e detalhadas sobre como desempacotar o seu dispositivo de armazenamento StorSimple 8100. Este dispositivo é enviado numa única caixa.

### <a name="prepare-to-unpack-your-device"></a>Prepare-se para desempacotar o seu dispositivo
Antes de desembalar o seu dispositivo, reveja as seguintes informações.

![Ícone de peso pesado de aviso ](./media/storsimple-safety/IC740879.png)![ ](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **AVISO!**

1. Certifique-se de que dispõe de duas pessoas para gerir o peso do recinto se estiver a manuseá-lo manualmente. Um recinto totalmente configurado pode pesar até 32 kg.).
2. Coloque a caixa numa superfície plana e uniforme.

Em seguida, complete os seguintes passos para desempacotar o seu dispositivo.

#### <a name="to-unpack-your-device"></a>Para desempacotar o seu dispositivo
1. Inspecione a caixa e a espuma de empacotamento para verificar se existem partes esmagadas, cortes, danos causados por água ou quaisquer outros danos óbvios. Se a caixa ou embalagem estiver gravemente danificada, não abra a caixa. Por favor [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para o ajudar a avaliar se o dispositivo está em bom estado de funcionamento.
2. Desembale a caixa. A imagem a seguir mostra a vista desembalada do seu dispositivo StorSimple.
   
     ![Desembale o seu dispositivo de armazenamento](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **Vista desembalada do seu dispositivo de armazenamento**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Caixa de embalagem |
   |   2 |Espuma de fundo |
   |   3 |Dispositivo |
   |   4 |Espuma superior |
   |   5 |Caixa de acessório |
3. Depois de desembalar a caixa, certifique-se de que tem:
   
   * 1 único dispositivo de recinto
   * 2 cabos de alimentação
   * 1 cabo Ethernet crossover
   * 2 cabos de consola em série
   * 1 conversor serial-USB para acesso em série
   * 1 chave de fendas T10 à prova de adulteração
   * 4 adaptadores QSFP-to-SFP+ para utilização com interfaces de rede de 10 GbE
   * 1 kit de montagem de cremalheira (2 trilhos laterais com hardware de montagem)
   * Começar a documentação
     
     Se não recebeu nenhum dos itens acima indicados, contacte o [Microsoft Support](storsimple-8000-contact-microsoft-support.md).

O passo seguinte é montar o dispositivo em bastidor.

## <a name="rack-mount-your-storsimple-8100-device"></a>Rack-mount o seu dispositivo StorSimple 8100
Siga os próximos passos para instalar o seu dispositivo de armazenamento StorSimple 8100 num rack standard de 19 polegadas com postes dianteiros e traseiros . O dispositivo StorSimple 8100 tem um único recinto primário.

A instalação consiste em múltiplas etapas, cada uma das quais é discutida nos seguintes procedimentos.

> [!IMPORTANT]
> Os dispositivos StorSimple devem ser montados na cremalheira para um bom funcionamento.
> 
> 

### <a name="prepare-the-site"></a>Preparar o site
O aparelho deve ser instalado num rack standard de 19 polegadas que tenha postes dianteiros e traseiros. Utilize o seguinte procedimento para preparar a instalação do rack.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Para preparar o local para a instalação do rack
1. Certifique-se de que o dispositivo fica apoiado de forma segura numa superfície de trabalho plana, estável e uniforme (ou semelhante).
2. Verifique se o local onde pretende instalar tem energia CA padrão de uma fonte independente ou uma unidade de distribuição de energia de cremalheira (PDU) com uma alimentação de alimentação ininterrupta (UPS).
3. Certifique-se de que existe uma ranhura 2U na grelha na qual pretende montar o dispositivo.

![Ícone de peso pesado de aviso ](./media/storsimple-safety/IC740879.png)![ ](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **AVISO!**

Certifique-se de que dispõe de duas pessoas para gerir o peso se estiver a manusear a configuração do dispositivo manualmente. Um recinto totalmente configurado pode pesar até 32 kg.).

### <a name="rack-prerequisites"></a>Pré-requisitos de cremalheira
O recinto 8100 foi concebido para ser instalado num armário de rack standard de 19 polegadas com:

* Profundidade mínima de 27,84 polegadas do posto de cremalheira ao poste.
* Peso máximo de 32 kg para o dispositivo
* Pressão máxima nas costas de 5 Pascal (medidor de água de 0,5 mm).

### <a name="rack-mounting-rail-kit"></a>Kit de trilho de montagem de cremalheira
Um conjunto de trilhos de montagem é fornecido para uso com o armário de rack de 19 polegadas. Os carris foram testados para suportar o peso máximo do recinto. Estes trilhos também permitirão a instalação de vários recintos sem qualquer perda de espaço dentro da cremalheira.

#### <a name="to-install-the-device-on-the-rails"></a>Para instalar o dispositivo nos trilhos
1. Execute este passo apenas se os trilhos internos não forem instalados no seu dispositivo. Normalmente, os trilhos internos são instalados na fábrica. Se os carris não forem instalados, instale os escorregas do trilho esquerdo e do trilho direito para os lados do chassis do recinto. Prendem-se com seis parafusos métricos de cada lado. Para ajudar na orientação, os slides ferroviários estão marcados **LH – Frente** e **RH – Frente**, e a extremidade que é afixada na parte traseira do recinto tem uma extremidade cónica.<br/>
   
    ![Fixação de escorregas de trilhos ao chassis do recinto](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Fixação de escorregas interiores nos lados do recinto**
   
    Etiqueta | Descrição
    ----- | -----------
    1     | Parafusos m 3x4 cabeça de botão
    2     | Slides de chassis

2. Fixe o trilho exterior esquerdo e os conjuntos de carris exteriores direito aos membros verticais do armário da cremalheira. Os suportes estão marcados **LH,** **RH,** e **este lado para cima** para guiá-lo através da orientação correta.
3. Localize os pinos na parte frontal e posterior da montagem das calhas. Estenda o trilho para caber entre os postes da cremalheira e insira os pinos nos orifícios verticais do suporte da cremalheira dianteira e traseira. Certifique-se de que a montagem ferroviária está nivelado.
4. Utilize dois dos parafusos métricos fornecidos para fixar o conjunto ferroviário aos membros verticais da cremalheira. Use um parafuso na frente e outro na parte de trás.
5. Repita estes passos para a outra montagem ferroviária.<br/>
   
     ![Fixação de slides de trilho ao armário de rack](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Fixação de conjuntos de carris exteriores à cremalheira**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Parafuso de aperto |
   |   2 |Parafuso do poste da frente do buraco quadrado |
   |   3 |Pinos de localização dianteira do trilho esquerdo |
   |   4 |Parafuso de aperto |
   |   5 |Pinos de localização traseira do trilho esquerdo |

### <a name="mounting-the-device-in-the-rack"></a>Montagem do dispositivo na cremalheira
Utilizando os trilhos de cremalheira que acabaram de ser instalados, execute os seguintes passos para montar o dispositivo na cremalheira.

#### <a name="to-mount-the-device"></a>Para montar o dispositivo
1. Com um assistente, levante o recinto e alinhe-o com os trilhos da cremalheira.
2. Insira cuidadosamente o dispositivo nos carris e, em seguida, empurre o aparelho completamente para dentro do armário da prateleira.<br/>
   
    ![Dispositivo de inserção no rack](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montagem do dispositivo na cremalheira**
3. Retire as tampas dianteiras esquerda e direita puxando as tampas para fora. As tampas de flange simplesmente encaixam nos flanges.
4. Fixe o recinto na cremalheira instalando um parafuso de cabeça phillips através de cada flange, esquerda e direita.
5. Instale as tampas de flange pressionando-as na posição e estalando-as no lugar.<br/>
   
     ![Instalação de tampas de flange](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalação das tampas de flange**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Parafuso de fixação do recinto |

O próximo passo é teleférico do seu dispositivo para acesso a energia, rede e série.

## <a name="cable-your-storsimple-8100-device"></a>Cable your StorSimple 8100 device (Ligar os cabos do dispositivo StorSimple 8100)
Os seguintes procedimentos explicam como cabo o seu dispositivo StorSimple 8100 para ligações de alimentação, rede e série.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a cablagem do dispositivo, é necessário:

* O seu dispositivo de armazenamento, completamente desembalado e montado na cremalheira.
* 2 cabos de alimentação que vieram com o seu dispositivo
* Acesso a 2 Unidades de Distribuição de Energia (recomendada).
* Cabos de rede
* Cabos em série fornecidos
* Conversor USB em série com o controlador apropriado instalado no seu PC (se necessário)
* Fornecido 4 adaptadores QSFP-to-SFP+ para utilização com interfaces de rede de 10 GbE
* [Hardware suportado para as interfaces de rede de 10 GbE no seu dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Cablagem de energia
O seu dispositivo inclui módulos de potência e arrefecimento redundantes (PCMs). Ambos os PCMs devem ser instalados e ligados a diferentes fontes de energia para garantir uma elevada disponibilidade.

Execute os seguintes passos para teleférico para obter energia.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Cablagem de rede
O seu dispositivo é uma configuração de espera ativa: a qualquer momento, um módulo controlador está ativo e processa todas as operações de disco e rede enquanto o outro módulo controlador está em espera. Se um controlador falhar, o controlador de espera é ativado imediatamente e continua todas as operações de disco e rede.

Para suportar este controlador redundante falha, é necessário por cabo a rede do seu dispositivo, conforme descrito nos seguintes passos.

#### <a name="to-cable-for-network-connection"></a>Para cabo para ligação de rede
1. O seu dispositivo tem seis interfaces de rede em cada controlador: quatro 1 Gbps e duas portas Ethernet de 10 Gbps. Identifique as várias portas de dados no backplane do seu dispositivo.
   
    ![Backplane do dispositivo 8100](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Parte de trás do dispositivo mostrando portas de dados**
   
   | Etiqueta | Description |
   | --- | --- |
   |   0,1,4,5 |1 Interfaces de rede GbE |
   |   2,3 |10 interfaces de rede GbE |
   |   6 |Portas em série |
2. Consulte o diagrama seguinte para a cablagem da rede. (A configuração mínima da rede é mostrada por linhas azuis sólidas. A configuração adicional necessária para a elevada disponibilidade e desempenho é mostrada por linhas pontilhadas.)

    ![Cabo o seu dispositivo 2U para rede](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Cabo de rede para o seu dispositivo**

   |Etiqueta | Description |
   |----- | ----------- |
   | A    | LAN com acesso à Internet |
   | B    | Controlador 0 |
   | C    | PCM 0 |
   | D    | Controlador 1 |
   | E    | PCM 1 |
   | F, G | Anfitriões |
   | 0-5  | Interfaces de rede |



Ao cabo do dispositivo, a configuração mínima requer:

* Pelo menos duas interfaces de rede ligadas em cada controlador com uma para acesso à nuvem e uma para iSCSI. A porta DATA 0 é automaticamente ativada e configurada através da consola em série do dispositivo. Além do DATA 0, outra porta de dados também precisa de ser configurada através do portal clássico Azure. Neste caso, ligue a porta DATA 0 à LAN primária (rede com acesso à Internet). As outras portas de dados podem ser ligadas ao segmento SAN/iSCSI LAN (VLAN) da rede, dependendo da função pretendida.
* Interfaces idênticas em cada controlador ligado à mesma rede para garantir a disponibilidade se ocorrer uma falha no controlador. Por exemplo, se optar por ligar os DADOS 0 e OS DADOS 3 a um dos controladores, tem de ligar os dados 0 e os dados 3 correspondentes ao outro controlador.

Tenha em mente a elevada disponibilidade e desempenho:

* Quando possível, configuure um par de interface de rede para acesso à nuvem (1 GbE) e outro par para iSCSI (10 GbE recomendado) em cada controlador.
* Quando possível, ligue as interfaces de rede de cada controlador a dois interruptores diferentes para garantir a disponibilidade contra uma falha no comutação. A figura ilustra as duas interfaces de rede de 10 GbE, DATA 2 e DATA 3, de cada controlador ligado a dois interruptores diferentes.

Para obter mais informações, consulte as **interfaces da Rede** de acordo com os [requisitos de alta disponibilidade para o seu dispositivo StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Se estiver a utilizar transceptores SFP+ com as suas interfaces de rede de 10 GbE, utilize os adaptadores QSFP-SFP+ fornecidos. Para obter mais informações, aceda ao [hardware suportado para as interfaces de rede de 10 GbE no seu dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Cablagem de porta em série
Execute os seguintes passos para a cabo a sua porta de série.

#### <a name="to-cable-for-serial-connection"></a>Para cabo para ligação em série
1. O seu dispositivo tem uma porta em série em cada controlador que é identificada por um ícone da chave inglesa. Consulte a ilustração na secção [de cablagem da Rede](#network-cabling) para localizar as portas em série no plano traseiro do seu dispositivo.
2. Identifique o controlador ativo no seu plano de apoio do dispositivo. Um LED azul intermitente indica que o controlador está ativo.
3. Utilize os cabos de série fornecidos (se necessário, o conversor em série USB para o seu portátil) e ligue a sua consola ou computador (com emulação terminal ao dispositivo) à porta de série do controlador ativo.
4. Instale os controladores serial-USB (enviados com o dispositivo) no seu computador.
5. Configurar a ligação em série da seguinte forma: 115.200 baud, 8 bits de dados, 1 bit de paragem, sem paridade e controlo de fluxo definido para Nenhum.
6. Verifique se a ligação está a funcionar pressionando Enter na consola. Deve aparecer um menu de consola em série.

> [!NOTE]
> **Gestão de luzes**: Quando o dispositivo estiver instalado num centro de dados remoto ou numa sala de computador com acesso limitado, certifique-se de que as ligações em série a ambos os controladores estão sempre ligadas a um interruptor de consola em série ou a um equipamento semelhante. Isto permite operações de controlo remoto e de suporte fora da banda se houver interrupções de rede ou falhas inesperadas.
> 
> 

O seu dispositivo está agora a ser alimentado por cabo para alimentação, acesso à rede e conectividade em série. O próximo passo é configurar o software e implementar o seu dispositivo.

## <a name="next-steps"></a>Passos seguintes
Saiba como [implantar e configurar o seu dispositivo StorSimple no local.](storsimple-8000-deployment-walkthrough-u2.md)

