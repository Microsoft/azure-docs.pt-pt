---
title: Instalar o dispositivo do Microsoft Azure StorSimple 8600 | Documentos da Microsoft
description: Descreve como descompactar, montar em bastidor e instalar o seu dispositivo StorSimple 8600 antes de implementar e configurar o software.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: be3f68a00647840801e7c205d7abb34b718bd61c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60630942"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Descompactar,-montar, em bastidor e instalar os cabos do dispositivo StorSimple 8600
## <a name="overview"></a>Descrição geral
O Microsoft Azure StorSimple 8600 é um dispositivo duplo de bastidor e é composta por um site primário e um bastidor EBOD. Este tutorial explica como descompactar, montar em bastidor e hardware do dispositivo de cabo o StorSimple 8600 antes de configuram o software do StorSimple.

## <a name="unpack-your-storsimple-8600-device"></a>Descompactar o seu dispositivo StorSimple 8600
Os passos seguintes fornecem instruções claras e detalhadas sobre como descompactar o seu dispositivo de armazenamento StorSimple 8600. Este dispositivo é enviado em duas caixas, um para o bastidor primário e outro para a inclusão EBOD. Essas duas caixas, em seguida, são colocadas numa única caixa.

### <a name="prepare-to-unpack-your-device"></a>Preparar para descompactar o seu dispositivo
Antes de descompactar o seu dispositivo, reveja as informações seguintes.

![Ícone de aviso](./media/storsimple-safety/IC740879.png)![ícone de peso pesada](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **aviso!**

1. Certifique-se de que tem duas pessoas disponíveis para gerir o peso do dispositivo, se estiver lidando com-lo manualmente. Um bastidor totalmente configurado pode avaliar até 32 kg (70 lbs.).
2. Coloque a caixa numa superfície plana e uniforme.

Em seguida, conclua os seguintes passos para descompactar o seu dispositivo.

#### <a name="to-unpack-your-device"></a>Para desempacotar o dispositivo
1. Inspecione a caixa e a espuma de empacotamento para verificar se existem partes esmagadas, cortes, danos causados por água ou quaisquer outros danos óbvios. Se a caixa ou embalagem estiver gravemente danificada, não abra a caixa. Volte [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para o ajudar a avaliar se o dispositivo está em boa ordem funcional.
2. Abrir a caixa externa e, em seguida, remova as duas caixas correspondentes a primária e as inclusões EBOD. Agora pode descompactar o principal e as inclusões EBOD. A figura a seguir mostra a vista descompactada de uma das caixas.
   
    ![Descompactar o dispositivo de armazenamento](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **Vista descompactada do seu dispositivo de armazenamento**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Caixa de remessa |
   |   2 |Cabos SAS (na Bandeja de acessórios e cabos) |
   |   3 |Espuma na parte inferior |
   |   4 |Dispositivo |
   |   5 |Espuma superior |
   |   6 |Caixa de acessórios |
3. Após desempacotar as duas caixas, certifique-se de que tem:
   
   * 1 Bastidor principal (o bastidor principal e a inclusão EBOD são nas duas caixas separadas)
   * 1 Bastidor de EBOD
   * cabos, 2 em cada caixa de energia de 4
   * 2 cabos SAS (para ligar o bastidor principal ao bastidor EBOD)
   * 1 crossover cabo Ethernet
   * 2 cabos de consola de série
   * 1 conversor de USB de série para o acesso de série
   * 4 QSFP-para-SFP + adaptadores para utilização com interfaces de rede de 10 GbE
   * 2 bastidor kits de montagem (o 4 rails de lado com a montagem de hardware, 2 para o bastidor principal e a inclusão EBOD), 1 em cada caixa
   * Obter documentação de introdução
     
     Se não recebeu qualquer um dos itens listados acima, [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md).  

O passo seguinte é montar o dispositivo em bastidor.

## <a name="rack-mount-your-storsimple-8600-device"></a>Montar em Bastidor seu dispositivo StorSimple 8600
Siga os passos seguintes para instalar o dispositivo de armazenamento StorSimple 8600 num bastidor 19 polegadas padrão com o front-end e postagens rear. Este dispositivo é fornecido com duas inclusões: um bastidor principal e um bastidor EBOD. Ambas as tem de ser rack-montado.

A instalação é composta por vários passos, cada um dos quais é abordada nos seguintes procedimentos.

> [!IMPORTANT]
> Dispositivos do StorSimple tem de ser montado rack para a operação apropriada.
> 
> 

### <a name="site-preparation"></a>Preparação do site
As inclusões tem de ser instaladas num bastidor 19 polegadas padrão que tenha o front-end e postagens rear. Utilize o procedimento seguinte para se preparar para a instalação de rack.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Para preparar o site para a instalação de bastidor
1. Certifique-se de que o servidor primário e os bastidores de EBOD são ficar com segurança numa superfície plana, estável e nível de trabalho (ou semelhante).
2. Certifique-se de que o site onde pretende configurar tem alternada padrão de uma origem de independente ou um bastidor de unidade de distribuição de energia (PDU) com uma alimentação ininterrupta (UPS).
3. Certifique-se de que (2 X 2U) a uma 4U ranhura está disponível no rack no qual pretende montar as inclusões.

![Ícone de aviso](./media/storsimple-safety/IC740879.png)![ícone de peso pesada](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **aviso!**

 Certifique-se de que tem duas pessoas disponíveis para gerir o peso, se estiver lidando com a configuração do dispositivo manualmente. Um bastidor totalmente configurado pode avaliar até 32 kg (70 lbs.).

### <a name="rack-prerequisites"></a>Pré-requisitos de bastidor
As inclusões destinam-se para a instalação no cab com um bastidor de 19 polegadas padrão:

* Profundidade mínima de 27.84 polegadas a partir de rack post para post
* Peso máximo de 32 kg para o dispositivo
* Pressão máximo de 5 Pascal (medidor de água de 0,5 mm)

### <a name="rack-mounting-rail-kit"></a>Kit do rail de montagem de bastidor
Um conjunto de montar rails será fornecido para utilização com o CAB de bastidor de 19 polegadas. Os rails foram testados para lidar com o peso de bastidor máximo. Estes rails também irão permitir a instalação de múltiplas inclusões sem perda de espaço no rack. Instale o bastidor EBOD primeiro.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Para instalar o bastidor EBOD nos rails
1. Efetue este passo apenas se rails internas não estão instalados no seu dispositivo. Normalmente, as internas rails são instalados na fábrica. Se não estiverem instalados rails, em seguida, instale os slides do rail do esquerda e direita para os lados do chassi bastidor. Eles anexar utilizando seis screws métrica em cada lado. Para ajudar com a orientação, os slides do rail são marcados **LH-Front** e **RH – Front**, e o end que está a afixação para trás do bastidor tem um end tapered.
   
    ![Anexar o slides do rail chassis de bastidor](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Anexar slides do rail para os lados do bastidor**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |  1 |M 3 x 4 screws de cabeça de botão |
   |  2 |Slides de chassis |
2. Anexe o lado esquerdo e os assemblies de right rail para os membros de vertical cab rack. Os colchetes são marcados **LH**, **RH**, e **nesse lado até** para orientar orientação correta.
3. Localize os pinos na parte frontal e posterior da montagem das calhas. Expanda o rail ajustar entre as postagens de rack e inseri os pins em frente e no rack traseiro post buracos de membro vertical. Certifique-se de que o assembly do rail é o nível.
4. Proteger o assembly do rail para rack membros verticais com dois das métrica screws fornecidos. Utilize um screw em frente e um no traseiro.
5. Repita estes passos para o assembly de grade.
   
     ![Anexar o slides do rail rack Cab](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Anexar o assemblies do rail rack**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Afixação screw |
   |   2 |Bastidor de front-quadrado buraco post screw |
   |   3 |LEFT pins de localização de front-rail |
   |   4 |Afixação screw |
   |   5 |Pins de localização do rail rear à esquerda |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Montar o bastidor EBOD no rack
Utilizar os rails rack que apenas foram instalados, execute os seguintes passos para montar o bastidor EBOD no rack.

#### <a name="to-mount-the-ebod-enclosure"></a>Para montar o bastidor EBOD
1. Com um assistente, o bastidor de comparação de precisão e alinhá-lo com os rails rack.
2. Inserir cuidadosamente o bastidor nos rails e, em seguida, enviá-los completamente em rack Cab.
   
    ![Inserir o dispositivo no rack](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montar o bastidor no rack**
3. Remova os limites esquerdo e direito front-flange ao extrair os limites gratuitos. Os limites de flange simplesmente ajustar para os flanges.
4. Proteja o bastidor no rack, instalando um screw de cabeça Phillips fornecido por meio de cada flange, esquerda e direita.
5. Instale os limites de flange ao premi-los para a posição pretendida e ajuste-os no lugar certo.
   
     ![Instalação de caps de flange](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalar os limites de flange**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Screw fastening de bastidor |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Montar o bastidor principal no rack
Depois de terminar o bastidor EBOD de montagem, terá de montar o bastidor principal, os mesmos passos a seguir.

> [!NOTE]
> * É possível ter alguns blocos vazios no rack entre o bastidor principal e a inclusão EBOD.
> * Utilize o cabo SAS fornecido 2m para ligar o bastidor principal para a inclusão EBOD.
> * Não há nenhuma restrição no posicionamento da unidade principal para a unidade EBOD relativo. Por conseguinte, o bastidor principal pode ser colocado na ranhura de principal e o bastidor EBOD abaixo — e vice-versa.
> 
> 

A próxima etapa é instalar os cabos do dispositivo de alimentação, rede e acesso de série.

## <a name="cable-your-storsimple-8600-device"></a>Instalar os cabos do dispositivo StorSimple 8600
Os procedimentos seguintes explicam como ligar o seu dispositivo StorSimple 8600 de alimentação, rede e ligações seriais.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a instalar os cabos do dispositivo, terá de:

* Completamente descompactado o bastidor principal e a inclusão EBOD
* 4 cabos de energia (2 cada de principal e a inclusão EBOD) com o seu dispositivo
* 2 cabos SAS fornecidos com o dispositivo para ligar o bastidor EBOD ao bastidor principal
* Acesso a unidades de distribuição do 2 energia (PDUs) (recomendado)
* Cabos de rede
* Fornecido seriais cabos
* Conversor de USB de série com o driver adequado instalado no seu PC (se necessário)
* Fornecido 4 QSFP-para-SFP + adaptadores para utilização com interfaces de rede de 10 GbE
* [Hardware suportado para as interfaces de rede 10 GbE no dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS e cabeamento de energia
O dispositivo tem um bastidor principal e um bastidor EBOD. Isto requer que as unidades ser instalou os cabos em conjunto para conectividade Serial Attached SCSI (SAS) e de energia.

Ao configurar este dispositivo pela primeira vez, execute os passos para cablagem de SAS primeiro e, em seguida, conclua os passos para cablagem de energia.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Cabos de rede
O dispositivo está numa configuração de ativo-em espera: em qualquer momento, um módulo de controlador está ativo e todas as operações de disco e rede enquanto o outro módulo do controlador de processamento está no modo de espera. Se ocorrer uma falha de controlador, o controlador de reserva ativa imediatamente e continua a todas as operações de disco e rede.

Para suportar esta ativação pós-falha de controlador redundantes, terá de instalar sua rede de dispositivos, conforme mostrado nas etapas a seguir.

#### <a name="to-cable-for-network-connection"></a>Para instalar os cabos de ligação de rede
1. O dispositivo tem seis interfaces de rede em cada controlador: quatro portas de Gbps Ethernet de 1 Gbps e dois 10. Consulte a ilustração a seguir para identificar as portas de dados no backplane do seu dispositivo.
   
     ![Backplane do dispositivo 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Volta do seu dispositivo a mostrar as portas de dados**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   0,1,4,5 |Interfaces de rede 1 GbE |
   |   2,3 |Interfaces de rede de 10 GbE |
   |   6 |Portas seriais |
2. Consulte o diagrama seguinte para cabos de rede. (A configuração de rede mínima é mostrada por linhas azuis sólidas. Para elevada disponibilidade e desempenho, necessária configuração adicional é mostrada por linhas pontilhadas.)

![Instalar os cabos do dispositivo 4U para a rede](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Cablagem para o seu dispositivo de rede**

| Etiqueta | Descrição |
| --- | --- |
| A |Rede local com acesso à Internet |
| B |Controlador 0 |
| C |PCM 0 |
| D |Controlador 1 |
| E |PCM 1 |
| F |Controlador 0 de EBOD |
| G |1 o controlador de EBOD |
| H,I |Anfitriões (por exemplo, servidores de ficheiros) |
| 0-5 |Interfaces de rede |
| 6 |Bastidor principal |
| 7 |Bastidor EBOD |

Quando os cabos do dispositivo, requer a configuração mínima:

* Pelo menos duas interfaces de rede ligado em cada controlador com um para acesso à nuvem e outro para iSCSI. Os dados 0 porta é automaticamente ativada e configurada através da consola de série do dispositivo. Para além dos dados 0, outra porta de dados também tem de ser configurado através do portal clássico do Azure. Neste caso, ligue os dados 0 porta como a principal LAN (rede com acesso à Internet). As outras portas de dados podem ser ligadas ao segmento de LAN (VLAN) em SAN/iSCSI da rede, de acordo com a função pretendida.
* Interfaces idênticos em cada controlador ligado à mesma rede para garantir a disponibilidade, se ocorrer uma ativação pós-falha de controlador. Por exemplo, se optar por ligar dados 0 e 3 de dados para um dos controladores, terá de ligar os dados correspondentes 0 e 3 de dados no outro controlador.

Tenha em consideração a alta disponibilidade e desempenho:

* Sempre que possível, configure um par de interface de rede para acesso à nuvem (de 1 GbE) e outro par de iSCSI (10 GbE recomendado) em cada controlador.
* Sempre que possível, ligar a interfaces de rede de cada controlador para duas opções diferentes para garantir a disponibilidade em relação a uma falha de comutador. A figura ilustra as duas 10 GbE interfaces de rede, dados 2 e 3 de dados, de cada controlador ligada a comutadores diferentes dois. Para obter mais informações, consulte a **interfaces de rede** sob a [requisitos de alta disponibilidade para o dispositivo StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Se utilizar SFP + transcetores com suas interfaces de rede de 10 GbE, utilize o QSFP fornecido-SFP + adaptadores. Para obter mais informações, aceda a [hardware suportado para as interfaces de rede 10 GbE no dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Cablagem de porta série
Execute os seguintes passos para instalar os cabos sua porta serial.

#### <a name="to-cable-for-serial-connection"></a>Para instalar os cabos para conexão serial
1. O dispositivo tem uma porta serial em cada controlador de que é identificado por um ícone de chave inglesa. Para localizar as portas seriais, consulte a ilustração que mostra os dados de portas na parte de trás do seu dispositivo.
2. Identifica o controlador ativo no backplane seu dispositivo. Um piscando LED azul indica que o controlador está ativo.
3. Utilize o cabo serial fornecido (se necessário, o conversor de USB de série para o seu computador portátil) e ligue a sua consola ou o computador (com a emulação do terminal para o dispositivo) para a porta serial do controlador ativo.
4. Instale os controladores de USB de série (foi fornecidos com o dispositivo) no seu computador.
5. Configure a conexão serial da seguinte forma:
   
   * 115\.200 bauds
   * bits de 8 dados
   * bits de 1 paragem
   * Não existem paridade
   * Fluxo de controlo definido como **None**
6. Certifique-se de que a ligação está a funcionar, premindo Enter no console. Um menu da consola de série deve aparecer.

> [!NOTE]
> **Gestão de Lights-out:** Quando o dispositivo é instalado num datacenter remoto ou numa sala de computadores com acesso limitado, certifique-se de que as ligações seriais para os dois controladores sempre estão ligadas a um comutador de consola de série ou equipamento semelhante. Isso permite que o controlo remoto de fora de banda e as operações de suporte em caso de interrupção de rede ou falhas inesperadas.
> 
> 

Concluiu a cablagem o seu dispositivo para a potência, acesso à rede e conexão serial. A próxima etapa é configurar o software no seu dispositivo.

## <a name="next-steps"></a>Passos Seguintes
Agora, está pronto para [implementar e configurar o dispositivo do StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md).

