---
title: Instale o dispositivo Microsoft Azure StorSimple 8600
description: Descreve como desempacotar, montar e cabo do seu dispositivo StorSimple 8600 antes de implantar e configurar o software.
author: alkohli
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 8d118e71b29b54b034788c37916091ad8f8b996f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91871818"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Desembale, suporte de cremalheira e cabo o seu dispositivo StorSimple 8600

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição Geral
O microsoft Azure StorSimple 8600 é um dispositivo de compostagem dupla e é composto por um recinto primário e um recinto EBOD. Este tutorial explica como desempacotar, montar e cabo o hardware do dispositivo StorSimple 8600 antes de configurar o software StorSimple.

## <a name="unpack-your-storsimple-8600-device"></a>Desembale o seu dispositivo StorSimple 8600
Os seguintes passos fornecem instruções claras e detalhadas sobre como desempacotar o seu dispositivo de armazenamento StorSimple 8600. Este dispositivo é enviado em duas caixas, uma para o recinto primário e outra para o recinto EBOD. Estas duas caixas são então colocadas numa única caixa.

### <a name="prepare-to-unpack-your-device"></a>Prepare-se para desempacotar o seu dispositivo
Antes de desembalar o seu dispositivo, reveja as seguintes informações.

![Ícone de peso pesado de aviso ](./media/storsimple-safety/IC740879.png)![ ](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **AVISO!**

1. Certifique-se de que dispõe de duas pessoas para gerir o peso do dispositivo se estiver a manuseá-lo manualmente. Um recinto totalmente configurado pode pesar até 32 kg.).
2. Coloque a caixa numa superfície plana e uniforme.

Em seguida, complete os seguintes passos para desempacotar o seu dispositivo.

#### <a name="to-unpack-your-device"></a>Para desempacotar o seu dispositivo
1. Inspecione a caixa e a espuma de empacotamento para verificar se existem partes esmagadas, cortes, danos causados por água ou quaisquer outros danos óbvios. Se a caixa ou embalagem estiver gravemente danificada, não abra a caixa. Por favor [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para o ajudar a avaliar se o dispositivo está em bom estado de funcionamento.
2. Abra a caixa exterior e, em seguida, tire as duas caixas correspondentes aos recintos primários e EBOD. Agora pode desempacotar os recintos primários e EBOD. A seguinte figura mostra a vista desembalada de um dos recintos.
   
    ![Desembale o seu dispositivo de armazenamento](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **Vista desembalada do seu dispositivo de armazenamento**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Caixa de embalagem |
   |   2 |Cabos SAS (em acessórios e bandeja de cabos) |
   |   3 |Espuma de fundo |
   |   4 |Dispositivo |
   |   5 |Espuma superior |
   |   6 |Caixa de acessório |
3. Depois de desempacotar as duas caixas, certifique-se de que tem:
   
   * 1 recinto primário (o recinto primário e o recinto EBOD estão em duas caixas separadas)
   * 1 Recinto EBOD
   * 4 cabos de alimentação, 2 em cada caixa
   * 2 cabos SAS (para ligar o recinto primário ao recinto EBOD)
   * 1 cabo Ethernet crossover
   * 2 cabos de consola em série
   * 1 conversor serial-USB para acesso em série
   * 4 adaptadores QSFP-to-SFP+ para utilização com interfaces de rede de 10 GbE
   * 2 kits de montagem de cremalheira (4 trilhos laterais com hardware de montagem, 2 cada para o recinto primário e recinto EBOD), 1 em cada caixa
   * Começar a documentação
     
     Se não recebeu nenhum dos itens acima indicados, contacte o [Microsoft Support](storsimple-8000-contact-microsoft-support.md).  

O passo seguinte é montar o dispositivo em bastidor.

## <a name="rack-mount-your-storsimple-8600-device"></a>Rack-mount o seu dispositivo StorSimple 8600
Siga os próximos passos para instalar o seu dispositivo de armazenamento StorSimple 8600 num rack standard de 19 polegadas com postes dianteiros e traseiros. Este dispositivo vem com duas caixas: um recinto primário e um recinto EBOD. Ambos precisam de ser montados.

A instalação consiste em múltiplas etapas, cada uma das quais é discutida nos seguintes procedimentos.

> [!IMPORTANT]
> Os dispositivos StorSimple devem ser montados na cremalheira para um bom funcionamento.
> 
> 

### <a name="site-preparation"></a>Preparação do local
As caixas devem ser instaladas num rack standard de 19 polegadas que tenha postes dianteiros e traseiros. Utilize o seguinte procedimento para preparar a instalação do rack.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Para preparar o local para a instalação do rack
1. Certifique-se de que as coberturas primárias e EBOD estão apoiadas com segurança numa superfície de trabalho plana, estável e nivelada (ou similar).
2. Verifique se o local onde pretende instalar tem energia CA padrão de uma fonte independente ou uma Unidade de Distribuição de Energia (PDU) com uma alimentação de alimentação ininterrupta (UPS).
3. Certifique-se de que está disponível uma ranhura 4U (2 X 2U) na cremalheira na qual pretende montar as caixas.

![Ícone de peso pesado de aviso ](./media/storsimple-safety/IC740879.png)![ ](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **AVISO!**

 Certifique-se de que dispõe de duas pessoas para gerir o peso se estiver a manusear a configuração do dispositivo manualmente. Um recinto totalmente configurado pode pesar até 32 kg.).

### <a name="rack-prerequisites"></a>Pré-requisitos de cremalheira
Os recintos são projetados para instalação num armário de rack standard de 19 polegadas com:

* Profundidade mínima de 27,84 polegadas do posto de cremalheira ao poste
* Peso máximo de 32 kg para o dispositivo
* Pressão máxima nas costas de 5 Pascal (medidor de água de 0,5 mm)

### <a name="rack-mounting-rail-kit"></a>Kit de trilho de montagem de cremalheira
Um conjunto de trilhos de montagem será fornecido para uso com o armário de rack de 19 polegadas. Os carris foram testados para suportar o peso máximo do recinto. Estes trilhos também permitirão a instalação de vários recintos sem perda de espaço dentro da cremalheira. Instale primeiro o recinto EBOD.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Para instalar o recinto EBOD sobre os trilhos
1. Execute este passo apenas se os trilhos internos não forem instalados no seu dispositivo. Normalmente, os trilhos internos são instalados na fábrica. Se os carris não forem instalados, instale os escorregas do trilho esquerdo e do trilho direito para os lados do chassis do recinto. Prendem-se com seis parafusos métricos de cada lado. Para ajudar na orientação, os slides ferroviários estão marcados **LH – Frente** e **RH – Frente**, e a extremidade que é afixada na parte traseira do recinto tem uma extremidade cónica.
   
    ![Fixação de escorregas de trilhos ao chassis do recinto](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Fixação de escorregas de trilhos para os lados do recinto**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |  1 |Parafusos m 3x4 cabeça de botão |
   |  2 |Slides de chassis |
2. Fixe os conjuntos ferroviários esquerdos e direito aos membros verticais do armário da cremalheira. Os suportes estão marcados **LH,** **RH,** e **este lado para cima** para guiá-lo através de uma orientação correta.
3. Localize os pinos na parte frontal e posterior da montagem das calhas. Estenda o trilho para caber entre os postes da cremalheira e insira os pinos nos orifícios verticais do suporte frontal e traseiro. Certifique-se de que a montagem ferroviária está nivelado.
4. Fixe o conjunto ferroviário aos membros verticais da cremalheira utilizando dois dos parafusos métricos fornecidos. Use um parafuso na frente e outro na parte de trás.
5. Repita estes passos para a outra montagem ferroviária.
   
     ![Fixação de slides de trilho ao armário de rack](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Fixação de conjuntos ferroviários à cremalheira**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Parafuso de aperto |
   |   2 |Parafuso do poste da frente do buraco quadrado |
   |   3 |Pinos de localização do trilho dianteiro esquerdo |
   |   4 |Parafuso de aperto |
   |   5 |Pinos de localização do trilho traseiro esquerdo |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Montagem do recinto EBOD na cremalheira
Utilizando os trilhos de cremalheira que acabam de ser instalados, execute os seguintes passos para montar o recinto EBOD na cremalheira.

#### <a name="to-mount-the-ebod-enclosure"></a>Para montar o recinto EBOD
1. Com um assistente, levante o recinto e alinhe-o com os trilhos da cremalheira.
2. Insira cuidadosamente o invólucro nos trilhos e empurre-o completamente para dentro do armário da prateleira.
   
    ![Dispositivo de inserção no rack](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montagem do recinto na cremalheira**
3. Retire as tampas dianteiras esquerda e direita puxando as tampas para fora. As tampas de flange simplesmente encaixam nos flanges.
4. Fixe o recinto na cremalheira instalando um parafuso de cabeça phillips através de cada flange, esquerda e direita.
5. Instale as tampas de flange pressionando-as na posição e colocando-as no lugar.
   
     ![Instalação de tampas de flange](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalação das tampas de flange**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Parafuso de fixação do recinto |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Montagem do recinto primário na cremalheira
Depois de ter terminado a montagem do recinto EBOD, terá de montar o recinto primário seguindo os mesmos passos.

> [!NOTE]
> * É possível ter algumas ranhuras vazias na cremalheira entre o recinto primário e o recinto EBOD.
> * Utilize o cabo SAS de 2m fornecido para ligar o invólucro primário ao compartimento EBOD.
> * Não existem constrangimentos na colocação relativa da unidade principal para a unidade EBOD. Portanto, o recinto primário pode ser colocado na ranhura superior e no recinto EBOD abaixo — ou vice-versa.
> 
> 

O próximo passo é teleférico do seu dispositivo para acesso a energia, rede e série.

## <a name="cable-your-storsimple-8600-device"></a>Cable your StorSimple 8600 device (Ligar os cabos do dispositivo StorSimple 8600)
Os seguintes procedimentos explicam como cabo o seu dispositivo StorSimple 8600 para ligações de alimentação, rede e série.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a teleférico, necessitará:

* O seu recinto primário e o recinto EBOD, completamente desembalados
* 4 cabos de alimentação (2 cada para o recinto primário e ebod) que vieram com o seu dispositivo
* 2 cabos SAS fornecidos com o dispositivo para ligar o recinto EBOD ao recinto primário
* Acesso a 2 Unidades de Distribuição de Energia (PDUs) (recomendado)
* Cabos de rede
* Cabos em série fornecidos
* Conversor serial-USB com o controlador apropriado instalado no seu PC (se necessário)
* Fornecido 4 adaptadores QSFP-to-SFP+ para utilização com interfaces de rede de 10 GbE
* [Hardware suportado para as interfaces de rede de 10 GbE no seu dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>Cabos SAS e Power
O seu dispositivo tem um recinto primário e um recinto EBOD. Isto requer que as unidades sejam ligadas em conjunto para a conectividade e a potência do SCSI (SAS) em série.

Ao configurar este dispositivo pela primeira vez, execute primeiro os passos para a cablagem SAS e, em seguida, complete os passos para a cablagem elétrica.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Cablagem de rede
O seu dispositivo encontra-se numa configuração de espera ativa: a qualquer momento, um módulo controlador está ativo e processa todas as operações de disco e rede enquanto o outro módulo controlador está em espera. Se ocorrer uma falha no controlador, o controlador de espera ativa imediatamente e continua todas as operações de disco e rede.

Para suportar este controlador redundante falha, é necessário por cabo a rede do seu dispositivo, como mostrado nos passos seguintes.

#### <a name="to-cable-for-network-connection"></a>Para cabo para ligação de rede
1. O seu dispositivo tem seis interfaces de rede em cada controlador: quatro 1 Gbps e duas portas Ethernet de 10 Gbps. Consulte a seguinte ilustração para identificar as portas de dados no backplane do seu dispositivo.
   
     ![Backplane de dispositivo 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Parte de trás do seu dispositivo mostrando as portas de dados**
   
   | Etiqueta | Description |
   | --- | --- |
   |   0,1,4,5 |1 Interfaces de rede GbE |
   |   2,3 |10 interfaces de rede GbE |
   |   6 |Portas em série |
2. Consulte o diagrama seguinte para a cablagem da rede. (A configuração mínima da rede é mostrada por linhas azuis sólidas. Para uma elevada disponibilidade e desempenho, a configuração adicional necessária é mostrada por linhas pontilhadas.)

![Cabo o seu dispositivo 4U para rede](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Cabo de rede para o seu dispositivo**

| Etiqueta | Description |
| --- | --- |
| A |LAN com acesso à Internet |
| B |Controlador 0 |
| C |PCM 0 |
| D |Controlador 1 |
| E |PCM 1 |
| F |Controlador EBOD 0 |
| G |Controlador EBOD 1 |
| H,I |Anfitriões (por exemplo, servidores de ficheiros) |
| 0-5 |Interfaces de rede |
| 6 |Recinto primário |
| 7 |Recinto EBOD |

Ao cabo do dispositivo, a configuração mínima requer:

* Pelo menos duas interfaces de rede ligadas em cada controlador com uma para acesso à nuvem e uma para iSCSI. A porta DATA 0 é automaticamente ativada e configurada através da consola em série do dispositivo. Além do DATA 0, outra porta de dados também precisa de ser configurada através do portal clássico Azure. Neste caso, ligue a porta DATA 0 à LAN primária (rede com acesso à Internet). As outras portas de dados podem ser ligadas ao segmento SAN/iSCSI LAN (VLAN) da rede, dependendo da função pretendida.
* Interfaces idênticas em cada controlador ligado à mesma rede para garantir a disponibilidade se ocorrer uma falha no controlador. Por exemplo, se optar por ligar os DADOS 0 e OS DADOS 3 a um dos controladores, tem de ligar os dados 0 e os dados 3 correspondentes ao outro controlador.

Tenha em mente a elevada disponibilidade e desempenho:

* Quando possível, configuure um par de interface de rede para acesso à nuvem (1 GbE) e outro par para iSCSI (10 GbE recomendado) em cada controlador.
* Quando possível, ligue as interfaces de rede de cada controlador a dois interruptores diferentes para garantir a disponibilidade contra uma falha no comutação. A figura ilustra as duas interfaces de rede de 10 GbE, DATA 2 e DATA 3, de cada controlador ligado a dois interruptores diferentes. Para obter mais informações, consulte as **interfaces da Rede** de acordo com os [requisitos de alta disponibilidade para o seu dispositivo StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Se utilizar os transmissores SFP+ com as suas interfaces de rede de 10 GbE, utilize os adaptadores QSFP-SFP+ fornecidos. Para obter mais informações, aceda ao [hardware suportado para as interfaces de rede de 10 GbE no seu dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Cablagem de porta em série
Execute os seguintes passos para a cabo a sua porta de série.

#### <a name="to-cable-for-serial-connection"></a>Para cabo para ligação em série
1. O seu dispositivo tem uma porta em série em cada controlador que é identificada por um ícone da chave inglesa. Para localizar as portas em série, consulte a ilustração que mostra as portas de dados na parte de trás do seu dispositivo.
2. Identifique o controlador ativo no seu plano de apoio do dispositivo. Um LED azul intermitente indica que o controlador está ativo.
3. Utilize o cabo de série fornecido (se necessário, o conversor em série USB para o seu portátil) e ligue a sua consola ou computador (com emulação terminal ao dispositivo) à porta de série do controlador ativo.
4. Instale os controladores serial-USB (enviados com o dispositivo) no seu computador.
5. Configurar a ligação em série da seguinte forma:
   
   * 115.200 baud
   * 8 bits de dados
   * 1 bit de paragem
   * Sem paridade
   * Conjunto de controlo de fluxo para **Nenhum**
6. Verifique se a ligação está a funcionar pressionando Enter na consola. Deve aparecer um menu de consola em série.

> [!NOTE]
> **Gestão de iluminação:** Quando o dispositivo estiver instalado num centro de dados remoto ou numa sala de computador com acesso limitado, certifique-se de que as ligações em série a ambos os controladores estão sempre ligadas a um interruptor de consola em série ou a um equipamento semelhante. Isto permite operações de controlo remoto fora da banda e de suporte em caso de perturbação da rede ou falhas inesperadas.
> 
> 

Completou a cablagem do seu dispositivo para obter energia, acesso à rede e ligação em série. O próximo passo é configurar o software no seu dispositivo.

## <a name="next-steps"></a>Passos seguintes
Está agora pronto para [implantar e configurar o seu dispositivo StorSimple no local.](storsimple-8000-deployment-walkthrough-u2.md)

