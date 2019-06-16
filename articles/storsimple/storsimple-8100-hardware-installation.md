---
title: Instalar o dispositivo do Microsoft Azure StorSimple 8100 | Documentos da Microsoft
description: Descreve como descompactar, montar em bastidor e instalar o seu dispositivo StorSimple 8100 antes de implementar e configurar o software.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: b367b6e7126a442dc68646ff52a29c955f50b798
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60631231"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Descompactar,-montar, em bastidor e instalar os cabos do dispositivo StorSimple 8100
## <a name="overview"></a>Descrição geral
O Microsoft Azure StorSimple 8100 é um bastidor único, o dispositivo montado em bastidor. Este tutorial explica como descompactar, montar em bastidor e hardware do dispositivo de cabo o StorSimple 8100 antes de configurar e implementar o dispositivo StorSimple.

## <a name="unpack-your-storsimple-8100-device"></a>Descompactar o seu dispositivo StorSimple 8100
Os passos seguintes fornecem instruções claras e detalhadas sobre como descompactar o seu dispositivo de armazenamento StorSimple 8100. Este dispositivo é enviado numa única caixa.

### <a name="prepare-to-unpack-your-device"></a>Preparar para descompactar o seu dispositivo
Antes de descompactar o seu dispositivo, reveja as informações seguintes.

![Ícone de aviso](./media/storsimple-safety/IC740879.png)![ícone de peso pesada](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **aviso!**

1. Certifique-se de que tem duas pessoas disponíveis para gerir o peso do bastidor, se estiver lidando com-lo manualmente. Um bastidor totalmente configurado pode avaliar até 32 kg (70 lbs.).
2. Coloque a caixa numa superfície plana e uniforme.

Em seguida, conclua os seguintes passos para descompactar o seu dispositivo.

#### <a name="to-unpack-your-device"></a>Para desempacotar o dispositivo
1. Inspecione a caixa e a espuma de empacotamento para verificar se existem partes esmagadas, cortes, danos causados por água ou quaisquer outros danos óbvios. Se a caixa ou embalagem estiver gravemente danificada, não abra a caixa. Volte [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para o ajudar a avaliar se o dispositivo está em boa ordem funcional.
2. Desembale a caixa. A imagem seguinte mostra a vista descompactada do dispositivo StorSimple.
   
     ![Descompactar o dispositivo de armazenamento](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **Vista descompactada do seu dispositivo de armazenamento**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Caixa de remessa |
   |   2 |Espuma na parte inferior |
   |   3 |Dispositivo |
   |   4 |Espuma superior |
   |   5 |Caixa de acessórios |
3. Depois de desembalar a caixa, certifique-se de que tem:
   
   * 1 dispositivo único bastidor
   * 2 de energia de cabos
   * 1 crossover cabo Ethernet
   * 2 cabos de consola de série
   * 1 conversor de USB de série para o acesso de série
   * 1 screwdriver de T10 de prova de adulteração
   * 4 QSFP-para-SFP + adaptadores para utilização com interfaces de rede de 10 GbE
   * 1-montar em Bastidor kit (2 rails de lado com o hardware de montagem)
   * Obter documentação de introdução
     
     Se não recebeu qualquer um dos itens listados acima, [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md).

O passo seguinte é montar o dispositivo em bastidor.

## <a name="rack-mount-your-storsimple-8100-device"></a>Montar em Bastidor seu dispositivo StorSimple 8100
Siga os passos seguintes para instalar o dispositivo de armazenamento StorSimple 8100 num bastidor 19 polegadas padrão com o front-end e postagens rear. O dispositivo StorSimple 8100 tem um único compartimento primário.

A instalação é composta por vários passos, cada um dos quais é abordada nos seguintes procedimentos.

> [!IMPORTANT]
> Dispositivos do StorSimple tem de ser montado rack para a operação apropriada.
> 
> 

### <a name="prepare-the-site"></a>Preparar o site
O dispositivo tem de ser instalado num bastidor 19 polegadas padrão que tenha o front-end e postagens rear. Utilize o procedimento seguinte para se preparar para a instalação de rack.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Para preparar o site para a instalação de bastidor
1. Certifique-se de que o dispositivo fica apoiado de forma segura numa superfície de trabalho plana, estável e uniforme (ou semelhante).
2. Certifique-se de que o site onde pretende configurar tem alternada padrão de uma origem de independente ou uma unidade de distribuição de energia (PDU) de rack com uma alimentação ininterrupta (UPS).
3. Certifique-se de que a ranhura 2U um está disponível no rack no qual pretende montar o dispositivo.

![Ícone de aviso](./media/storsimple-safety/IC740879.png)![ícone de peso pesada](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **aviso!**

Certifique-se de que tem duas pessoas disponíveis para gerir o peso, se estiver lidando com a configuração do dispositivo manualmente. Um bastidor totalmente configurado pode avaliar até 32 kg (70 lbs.).

### <a name="rack-prerequisites"></a>Pré-requisitos de bastidor
O bastidor 8100 destina-se a instalação num bastidor 19 polegadas padrão cab com:

* Profundidade mínima de 27.84 polegadas a partir de rack post para publicação.
* Peso máximo de 32 kg para o dispositivo
* Máximo ter pressão contrária proveniente do Pascal 5 (medidor de água de 0,5 mm).

### <a name="rack-mounting-rail-kit"></a>Kit do rail de montagem de bastidor
Um conjunto de montar rails é fornecido para utilização com o CAB de bastidor de 19 polegadas. Os rails foram testados para lidar com o peso de bastidor máximo. Estes rails também irão permitir a instalação de múltiplas inclusões sem nenhuma perda de espaço no rack.

#### <a name="to-install-the-device-on-the-rails"></a>Para instalar o dispositivo nos rails
1. Efetue este passo apenas se rails internas não estão instalados no seu dispositivo. Normalmente, as internas rails são instalados na fábrica. Se não estiverem instalados rails, em seguida, instale os slides do rail do esquerda e direita para os lados do chassi bastidor. Eles anexar utilizando seis screws métrica em cada lado. Para ajudar com a orientação, os slides do rail são marcados **LH-Front** e **RH – Front**, e o end que está a afixação para trás do bastidor tem um end tapered.<br/>
   
    ![Anexar o slides do rail chassis de bastidor](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Anexar slides do rail interna para os lados do bastidor**
   
    Etiqueta | Descrição
    ----- | -----------
    1     | M 3 x 4 screws de cabeça de botão
    2     | Slides de chassis

2. Anexe o lado esquerdo externo e os assemblies de rail certo externa para os membros de vertical cab rack. Os colchetes são marcados **LH**, **RH**, e **nesse lado até** para orientá-lo a orientação correta.
3. Localize os pinos na parte frontal e posterior da montagem das calhas. Expanda o rail ajustar entre as postagens de rack e inseri os pins em frente e no rear rack post membro vertical buracos. Certifique-se de que o assembly do rail é o nível.
4. Utilize dois dos screws métrica fornecidos para proteger o assembly do rail para rack membros verticais. Utilize um screw em frente e um no traseiro.
5. Repita estes passos para o assembly de grade.<br/>
   
     ![Anexar o slides do rail rack Cab](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Anexar assemblies do rail externa para o bastidor**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Afixação screw |
   |   2 |Bastidor de front-quadrado buraco post screw |
   |   3 |Pins de localização de front-rail à esquerda |
   |   4 |Afixação screw |
   |   5 |Pins de localização rear rail à esquerda |

### <a name="mounting-the-device-in-the-rack"></a>Montar o dispositivo no rack
Utilizar os rails rack que apenas foram instalados, execute os seguintes passos para montar o dispositivo no rack.

#### <a name="to-mount-the-device"></a>Para montar o dispositivo
1. Com um assistente, o bastidor de comparação de precisão e alinhá-lo com os rails rack.
2. Cuidadosamente, insira o dispositivo para os rails e, em seguida, enviar por push o dispositivo completamente para o bastidor Cab.<br/>
   
    ![Inserir o dispositivo no rack](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montar o dispositivo no rack**
3. Remova os limites esquerdo e direito front-flange ao extrair os limites gratuitos. Os limites de flange simplesmente ajustar para os flanges.
4. Proteja o bastidor no rack, instalando um screw de cabeça Phillips fornecido por meio de cada flange, esquerda e direita.
5. Instale os limites de flange ao premi-los para a posição pretendida e ajuste-os no local.<br/>
   
     ![Instalação de caps de flange](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalar os limites de flange**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Screw fastening de bastidor |

A próxima etapa é instalar os cabos do dispositivo de alimentação, rede e acesso de série.

## <a name="cable-your-storsimple-8100-device"></a>Instalar os cabos do dispositivo StorSimple 8100
Os procedimentos seguintes explicam como instalar os cabos do dispositivo StorSimple 8100 de alimentação, rede e ligações seriais.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a cablagem do dispositivo, é necessário:

* Montar o seu dispositivo de armazenamento, completamente descompactado e rack.
* 2 cabos de energia com o seu dispositivo
* Acesso a 2 unidades de distribuição de energia (recomendado).
* Cabos de rede
* Fornecido seriais cabos
* Conversor USB Serial com o driver adequado instalado no seu PC (se necessário)
* Fornecido 4 QSFP-para-SFP + adaptadores para utilização com interfaces de rede de 10 GbE
* [Hardware suportado para as interfaces de rede 10 GbE no dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Cablagem de energia
O dispositivo inclui com redundância de energia e refrigeração módulos (PCMs). Ambos os PCMs tem de estar instalados e ligados a diferentes origens de dados para garantir a elevada disponibilidade.

Execute os seguintes passos para instalar os cabos do dispositivo para poder.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Cabos de rede
O dispositivo é uma configuração de ativo-em espera: em qualquer momento, um módulo de controlador está ativo e todas as operações de disco e rede enquanto o outro módulo do controlador de processamento está no modo de espera. Se um controlador de falhar, o controlador em modo de espera é ativado imediatamente e continua a todas as operações de rede e disco.

Para suportar esta ativação pós-falha de controlador redundantes, terá de instalar sua rede de dispositivos, conforme descrito nos passos seguintes.

#### <a name="to-cable-for-network-connection"></a>Para instalar os cabos de ligação de rede
1. O dispositivo tem seis interfaces de rede em cada controlador: quatro 1 Gbps, e Gbps Ethernet de 10 de duas portas. Identifica as várias portas de dados no backplane do seu dispositivo.
   
    ![Backplane do dispositivo 8100](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Atrás do dispositivo que mostra as portas de dados**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   0,1,4,5 |Interfaces de rede 1 GbE |
   |   2,3 |Interfaces de rede de 10 GbE |
   |   6 |Portas seriais |
2. Consulte o diagrama seguinte para cabos de rede. (A configuração de rede mínima é mostrada por linhas azuis sólidas. Configuração adicional necessária para elevada disponibilidade e desempenho é mostrada por linhas pontilhadas.)

    ![Instalar os cabos do dispositivo 2U para a rede](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Cablagem para o seu dispositivo de rede**

   |Etiqueta | Descrição |
   |----- | ----------- |
   | A    | Rede local com acesso à Internet |
   | B    | Controlador 0 |
   | C    | PCM 0 |
   | D    | Controlador 1 |
   | E    | PCM 1 |
   | F, G | Anfitriões |
   | 0-5  | Interfaces de rede |



Quando os cabos do dispositivo, requer a configuração mínima:

* Pelo menos duas interfaces de rede ligado em cada controlador com um para acesso à nuvem e outro para iSCSI. Os dados 0 porta é automaticamente ativada e configurada através da consola de série do dispositivo. Para além dos dados 0, outra porta de dados também tem de ser configurado através do portal clássico do Azure. Neste caso, ligue os dados 0 porta como a principal LAN (rede com acesso à Internet). As outras portas de dados podem ser ligadas ao segmento de LAN (VLAN) em SAN/iSCSI da rede, de acordo com a função pretendida.
* Interfaces idênticos em cada controlador ligado à mesma rede para garantir a disponibilidade, se ocorrer uma ativação pós-falha de controlador. Por exemplo, se optar por ligar dados 0 e 3 de dados para um dos controladores, terá de ligar os dados correspondentes 0 e 3 de dados no outro controlador.

Tenha em consideração a alta disponibilidade e desempenho:

* Sempre que possível, configure um par de interface de rede para acesso à nuvem (de 1 GbE) e outro par de iSCSI (10 GbE recomendado) em cada controlador.
* Sempre que possível, ligar a interfaces de rede de cada controlador para duas opções diferentes para garantir a disponibilidade em relação a uma falha de comutador. A figura ilustra as duas 10 GbE interfaces de rede, dados 2 e 3 de dados, de cada controlador ligada a comutadores diferentes dois.

Para obter mais informações, consulte a **interfaces de rede** sob a [requisitos de alta disponibilidade para o dispositivo StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Se estiver a utilizar SFP + transcetores com suas interfaces de rede de 10 GbE, utilize o QSFP fornecido-SFP + adaptadores. Para obter mais informações, aceda a [hardware suportado para as interfaces de rede 10 GbE no dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Cablagem de porta série
Execute os seguintes passos para instalar os cabos sua porta serial.

#### <a name="to-cable-for-serial-connection"></a>Para instalar os cabos para conexão serial
1. O dispositivo tem uma porta serial em cada controlador de que é identificado por um ícone de chave inglesa. Consulte a ilustração na [cabos de rede](#network-cabling) secção para localizar as portas seriais no backplane do seu dispositivo.
2. Identifica o controlador ativo no backplane seu dispositivo. Um piscando LED azul indica que o controlador está ativo.
3. Utilize os cabos seriais fornecidos (se necessário, o conversor de USB de série para o seu computador portátil) e ligue a sua consola ou o computador (com a emulação do terminal para o dispositivo) para a porta serial do controlador ativo.
4. Instale os controladores de USB de série (foi fornecidos com o dispositivo) no seu computador.
5. Configure a conexão serial da seguinte forma: 115.200 bauds, bits de dados de 8, os bits de paragem de 1, não paridade e fluxo de controlam definido como None.
6. Certifique-se de que a ligação está a funcionar, premindo Enter no console. Um menu da consola de série deve aparecer.

> [!NOTE]
> **Gestão de Lights-out**: Quando o dispositivo é instalado num datacenter remoto ou numa sala de computadores com acesso limitado, certifique-se de que as ligações seriais para os dois controladores sempre estão ligadas a um comutador de consola de série ou equipamento semelhante. Isto permite controlo remoto de fora de banda e as operações de suporte se existem interrupções de rede ou falhas inesperadas.
> 
> 

O dispositivo está agora instalou os cabos de alimentação, acesso à rede e conectividade serial. A próxima etapa é configurar o software e implementar o dispositivo.

## <a name="next-steps"></a>Passos Seguintes
Saiba como [implementar e configurar o dispositivo do StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md).

