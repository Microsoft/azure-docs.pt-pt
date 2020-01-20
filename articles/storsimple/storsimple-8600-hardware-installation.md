---
title: Instalar Microsoft Azure StorSimple dispositivo 8600
description: Descreve como desempacotar, montar em rack e cabear o dispositivo StorSimple 8600 antes de implantar e configurar o software.
author: alkohli
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 8d118e71b29b54b034788c37916091ad8f8b996f
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277119"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Desembalar, montar em rack e cabear seu dispositivo StorSimple 8600

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Visão geral
Seu Microsoft Azure StorSimple 8600 é um dispositivo de compartimento duplo e consiste em um compartimento primário e um EBOD. Este tutorial explica como desempacotar, montar em rack e conectar o hardware do dispositivo StorSimple 8600 antes de configurar o software StorSimple.

## <a name="unpack-your-storsimple-8600-device"></a>Desempacotar seu dispositivo StorSimple 8600
As etapas a seguir fornecem instruções claras e detalhadas sobre como desempacotar seu dispositivo de armazenamento StorSimple 8600. Esse dispositivo é fornecido em duas caixas, uma para o compartimento principal e outra para o compartimento EBOD. Essas duas caixas são colocadas em uma única caixa.

### <a name="prepare-to-unpack-your-device"></a>Preparar para desempacotar seu dispositivo
Antes de desempacotar o dispositivo, examine as informações a seguir.

![ícone de aviso](./media/storsimple-safety/IC740879.png)![ícone de peso pesado](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **aviso!**

1. Verifique se você tem duas pessoas disponíveis para gerenciar o peso do dispositivo se você o estiver manipulando manualmente. Um compartimento totalmente configurado pode pesar até 32 kg (70 lb.).
2. Coloque a caixa numa superfície plana e uniforme.

Em seguida, conclua as etapas a seguir para desempacotar seu dispositivo.

#### <a name="to-unpack-your-device"></a>Para desempacotar seu dispositivo
1. Inspecione a caixa e a espuma de empacotamento para verificar se existem partes esmagadas, cortes, danos causados por água ou quaisquer outros danos óbvios. Se a caixa ou embalagem estiver gravemente danificada, não abra a caixa. [Entre em contato com suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) para ajudá-lo a avaliar se o dispositivo está em boas condições de funcionamento.
2. Abra a caixa externa e, em seguida, retire as duas caixas correspondentes aos compartimentos primário e EBOD. Agora você pode desempacotar os compartimentos primário e EBOD. A figura a seguir mostra a exibição desempacotada de um dos compartimentos.
   
    ![Desempacotar seu dispositivo de armazenamento](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **Exibição desempacotada do seu dispositivo de armazenamento**
   
   | Label | Descrição |
   | --- | --- |
   |   1 |Caixa de embalagem |
   |   2 |Cabos SAS (na bandeja de acessórios e cabos) |
   |   3 |Espuma inferior |
   |   4 |Dispositivo |
   |   5 |Espuma superior |
   |   6 |Caixa de acessório |
3. Depois de desempacotar as duas caixas, verifique se você tem:
   
   * 1 compartimento principal (o compartimento primário e o compartimento EBOD estão em duas caixas separadas)
   * 1 compartimento EBOD
   * 4 cabos de alimentação, 2 em cada caixa
   * 2 cabos SAS (para conectar o compartimento principal ao compartimento EBOD)
   * 1 cabo Ethernet cruzado
   * 2 cabos de console serial
   * 1 conversor serial USB para acesso serial
   * 4 adaptadores QSFP-to-SFP + para uso com interfaces de rede de 10 GbE
   * 2 kits de montagem em rack (4 trilhos laterais com hardware de montagem, 2 cada para o compartimento primário e o compartimento EBOD), 1 em cada caixa
   * Documentação de introdução
     
     Se você não recebeu nenhum dos itens listados acima, [entre em contato com suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).  

O passo seguinte é montar o dispositivo em bastidor.

## <a name="rack-mount-your-storsimple-8600-device"></a>Montar em rack seu dispositivo StorSimple 8600
Siga as próximas etapas para instalar o dispositivo de armazenamento StorSimple 8600 em um rack padrão de 19 polegadas com postagens dianteiras e traseiras. Este dispositivo é fornecido com dois compartimentos: um compartimento principal e um compartimento EBOD. Ambos precisam ser montados em rack.

A instalação consiste em várias etapas, cada uma das quais é discutida nos procedimentos a seguir.

> [!IMPORTANT]
> Os dispositivos StorSimple devem ser montados em rack para uma operação adequada.
> 
> 

### <a name="site-preparation"></a>Preparação do site
Os compartimentos devem ser instalados em um rack padrão de 19 polegadas que tem as postagens frontal e traseira. Use o procedimento a seguir para preparar a instalação do rack.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Para preparar o site para instalação em rack
1. Verifique se os compartimentos primário e EBOD estão sendo protegidos com segurança em uma superfície de trabalho plana, estável e nivelada (ou semelhante).
2. Verifique se o site onde você pretende configurar tem energia CA padrão de uma fonte independente ou uma unidade de distribuição de energia (PDU) de rack com uma fonte de alimentação ininterrupta (UPS).
3. Verifique se um slot de 4U (2 X 2U) está disponível no rack no qual você pretende montar os compartimentos.

![ícone de aviso](./media/storsimple-safety/IC740879.png)![ícone de peso pesado](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **aviso!**

 Verifique se você tem duas pessoas disponíveis para gerenciar o peso se estiver manipulando a configuração do dispositivo manualmente. Um compartimento totalmente configurado pode pesar até 32 kg (70 lb.).

### <a name="rack-prerequisites"></a>Pré-requisitos do rack
Os compartimentos foram projetados para instalação em um gabinete de rack padrão de 19 polegadas com:

* Profundidade mínima de 27,84 polegadas da postagem do rack para a postagem
* Peso máximo de 32 kg para o dispositivo
* Pressão de retorno máxima de 5 Pascal (medidor de água de 0,5 mm)

### <a name="rack-mounting-rail-kit"></a>Kit de trilhos de montagem de rack
Um conjunto de trilhos de montagem será fornecido para uso com o gabinete de rack de 19 polegadas. Os trilhos foram testados para lidar com o peso máximo do compartimento. Esses trilhos também permitirão a instalação de vários compartimentos sem perda de espaço no rack. Instale o compartimento EBOD primeiro.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Para instalar o compartimento EBOD nos trilhos
1. Execute esta etapa somente se os trilhos internos não estiverem instalados no seu dispositivo. Normalmente, os trilhos internos são instalados na fábrica. Se os trilhos não estiverem instalados, instale os slides do trilho esquerdo e do trilho direito nos lados do chassi do compartimento. Eles são anexados usando seis parafusos de métricas em cada lado. Para ajudar com a orientação, os slides do trilho são marcados como **LH – front** e **RH – front**, e o final fixado em direção à parte posterior do compartimento tem um fim cônico.
   
    ![Anexando slides do trilho ao chassi do compartimento](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Anexando slides do trilho aos lados do compartimento**
   
   | Label | Descrição |
   | --- | --- |
   |  1 |Botão M 3x4-parafusos de cabeça |
   |  2 |Slides do chassi |
2. Anexe os assemblies esquerdo do trilho e do trilho à direita aos membros verticais do gabinete do rack. Os colchetes estão marcados como **LH**, **RH**e **este lado** para orientá-lo na orientação correta.
3. Localize os pinos na parte frontal e posterior da montagem das calhas. Estenda o trilho para ajustá-lo entre as postagens do rack e insira os pinos nos orifícios do membro vertical da postagem do rack frontal e traseiro. Certifique-se de que o assembly do trilho seja nível.
4. Proteja o assembly do trilho para os membros verticais do rack usando dois dos parafusos de métrica fornecidos. Use um parafuso na frente e outro na parte traseira.
5. Repita essas etapas para o outro assembly de trilho.
   
     ![Anexando slides do trilho ao gabinete do rack](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Anexando assemblies do trilho ao rack**
   
   | Label | Descrição |
   | --- | --- |
   |   1 |Parafuso fixação MSS |
   |   2 |Parafuso do rack frontal do buraco quadrado |
   |   3 |Pins do local do trilho frontal esquerdo |
   |   4 |Parafuso fixação MSS |
   |   5 |Pins do local do trilho traseiro esquerdo |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Montando o compartimento EBOD no rack
Usando os trilhos do rack que acabaram de ser instalados, execute as etapas a seguir para montar o compartimento EBOD no rack.

#### <a name="to-mount-the-ebod-enclosure"></a>Para montar o compartimento EBOD
1. Com um assistente, levante o compartimento e alinhe-o com os trilhos do rack.
2. Insira cuidadosamente o compartimento nos trilhos e, em seguida, envie-o por completo para o gabinete do rack.
   
    ![Inserindo dispositivo no rack](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montando o compartimento no rack**
3. Remova as tampas do flange frontal esquerda e direita puxando as tampas gratuitas. Os limites do flange simplesmente se encaixam nos flanges.
4. Proteja o compartimento no rack instalando um parafuso de cabeça Phillips fornecido por cada flange, à esquerda e à direita.
5. Instale as tampas do flange pressionando-as na posição e encaixando-as no lugar.
   
     ![Instalando as tampas do flange](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalando as tampas do flange**
   
   | Label | Descrição |
   | --- | --- |
   |   1 |Parafuso de fixação do compartimento |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Montando o compartimento principal no rack
Depois de concluir a montagem do compartimento EBOD, será necessário montar o compartimento primário seguindo as mesmas etapas.

> [!NOTE]
> * É possível ter alguns Slots vazios no rack entre o compartimento principal e o compartimento EBOD.
> * Use o cabo SAS de 2 m fornecido para conectar o compartimento principal ao compartimento EBOD.
> * Não há restrições sobre o posicionamento relativo da unidade principal na unidade EBOD. Portanto, o compartimento principal pode ser colocado no slot superior e no compartimento EBOD abaixo — ou vice-versa.
> 
> 

A próxima etapa é conectar o dispositivo a energia, rede e acesso serial.

## <a name="cable-your-storsimple-8600-device"></a>Cabear o dispositivo StorSimple 8600
Os procedimentos a seguir explicam como conectar o dispositivo StorSimple 8600 para conexões de energia, rede e série.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a conectar o dispositivo, será necessário:

* Seu compartimento principal e o compartimento EBOD, completamente desempacotados
* 4 cabos de alimentação (2 cada para o compartimento primário e EBOD) que vieram com seu dispositivo
* 2 cabos SAS fornecidos com o dispositivo para conectar o compartimento EBOD ao compartimento principal
* Acesso a 2 PDUs (unidades de distribuição de energia) (recomendado)
* Cabos de rede
* Cabos de série fornecidos
* Conversor serial USB com o driver apropriado instalado no seu PC (se necessário)
* Fornecidos 4 adaptadores QSFP-to-SFP + para uso com interfaces de rede de 10 GbE
* [Hardware com suporte para as interfaces de rede de 10 GbE em seu dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS e cabeamento de energia
Seu dispositivo tem um compartimento principal e um compartimento EBOD. Isso exige que as unidades sejam cabeadas para conectividade e energia SAS (Serial Attached SCSI).

Ao configurar este dispositivo pela primeira vez, execute as etapas para cabeamento SAS primeiro e, em seguida, conclua as etapas para cabeamento de energia.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Cabeamento de rede
Seu dispositivo está em uma configuração ativa-em espera: a qualquer momento, um módulo do controlador está ativo e processando todas as operações de disco e rede enquanto o outro módulo do controlador está em espera. Se ocorrer uma falha do controlador, o controlador em espera ativará imediatamente e continuará todas as operações de disco e de rede.

Para dar suporte a esse failover de controlador redundante, você precisa conectar a rede do dispositivo, conforme mostrado nas etapas a seguir.

#### <a name="to-cable-for-network-connection"></a>Para cabo para conexão de rede
1. Seu dispositivo tem seis interfaces de rede em cada controlador: quatro portas Ethernet de 1 Gbps e 2 10 Gbps. Consulte a ilustração a seguir para identificar as portas de dados no plano posterior do seu dispositivo.
   
     ![Plano posterior do dispositivo 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Parte traseira do dispositivo mostrando as portas de dados**
   
   | Label | Descrição |
   | --- | --- |
   |   0,1,4,5 |interfaces de rede de 1 GbE |
   |   2,3 |interfaces de rede de 10 GbE |
   |   6 |Portas seriais |
2. Consulte o diagrama a seguir para o cabeamento de rede. (A configuração mínima de rede é mostrada por linhas azuis sólidas. Para alta disponibilidade e desempenho, a configuração adicional necessária é mostrada por linhas pontilhadas.)

![Cabear o dispositivo 4U para rede](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Cabeamento de rede para seu dispositivo**

| Label | Descrição |
| --- | --- |
| A |LAN com acesso à Internet |
| B |Controlador 0 |
| C |PCM 0 |
| D |Controlador 1 |
| E |PCM 1 |
| F |Controlador EBOD 0 |
| G |Controlador EBOD 1 |
| H, I |Hosts (por exemplo, servidores de arquivos) |
| 0-5 |Interfaces de rede |
| 6 |Compartimento principal |
| 7 |Compartimento EBOD |

Ao cabear o dispositivo, a configuração mínima requer:

* Pelo menos duas interfaces de rede conectadas em cada controlador com uma para acesso à nuvem e outra para iSCSI. A porta DATA 0 é automaticamente habilitada e configurada por meio do console serial do dispositivo. Além do DATA 0, outra porta de dados também precisa ser configurada por meio do portal clássico do Azure. Nesse caso, conecte a porta DATA 0 à LAN primária (rede com acesso à Internet). As outras portas de dados podem ser conectadas ao segmento de rede de área de LAN (VLAN) SAN/iSCSI, dependendo da função pretendida.
* Interfaces idênticas em cada controlador conectado à mesma rede para garantir a disponibilidade se ocorrer um failover do controlador. Por exemplo, se você optar por conectar DATA 0 e DATA 3 para um dos controladores, será necessário conectar os dados correspondentes 0 e 3 no outro controlador.

Tenha em mente para alta disponibilidade e desempenho:

* Quando possível, configure um par de interface de rede para acesso à nuvem (1 GbE) e outro par para iSCSI (10 GbE recomendado) em cada controlador.
* Quando possível, conecte as interfaces de rede de cada controlador a dois comutadores diferentes para garantir a disponibilidade contra uma falha de comutador. A figura ilustra as interfaces de rede 2 10 GbE, DATA 2 e DATA 3, de cada controlador conectado a dois comutadores diferentes. Para obter mais informações, consulte as **interfaces de rede** sob os [requisitos de alta disponibilidade para seu dispositivo StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Se estiver usando transceptores SFP + com suas interfaces de rede de 10 GbE, use os adaptadores QSFP-SFP + fornecidos. Para obter mais informações, acesse [hardware com suporte para as interfaces de rede de 10 GbE em seu dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Cabeamento de porta serial
Execute as etapas a seguir para cabear a porta serial.

#### <a name="to-cable-for-serial-connection"></a>Para cabo para conexão serial
1. Seu dispositivo tem uma porta serial em cada controlador que é identificado por um ícone de chave inglesa. Para localizar as portas seriais, consulte a ilustração que mostra as portas de dados na parte posterior do seu dispositivo.
2. Identifique o controlador ativo no backplane do seu dispositivo. Um LED azul piscando indica que o controlador está ativo.
3. Use o cabo serial fornecido (se necessário, o conversor serial USB para seu laptop) e conecte seu console ou computador (com emulação de terminal ao dispositivo) à porta serial do controlador ativo.
4. Instale os drivers Serial USB (fornecidos com o dispositivo) em seu computador.
5. Configure a conexão serial da seguinte maneira:
   
   * 115.200 baud
   * 8 bits de dados
   * 1 bit de parada
   * Sem paridade
   * Controle de fluxo definido como **nenhum**
6. Verifique se a conexão está funcionando pressionando Enter no console. Um menu do console serial deve aparecer.

> [!NOTE]
> **Gerenciamento de luzes:** Quando o dispositivo for instalado em um datacenter remoto ou em uma sala de computadores com acesso limitado, verifique se as conexões de série para ambos os controladores estão sempre conectadas a um comutador de console serial ou equipamento semelhante. Isso permite operações de suporte e controle remoto fora de banda em caso de interrupção da rede ou falhas inesperadas.
> 
> 

Você concluiu o cabeamento do seu dispositivo para energia, acesso à rede e conexão serial. A próxima etapa é configurar o software em seu dispositivo.

## <a name="next-steps"></a>Passos seguintes
Agora você está pronto para [implantar e configurar seu dispositivo StorSimple local](storsimple-8000-deployment-walkthrough-u2.md).

