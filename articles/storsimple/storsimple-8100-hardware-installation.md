---
title: Instalar o dispositivo Microsoft Azure StorSimple 8100 | Microsoft Docs
description: Descreve como desempacotar, montar em rack e cabear o dispositivo StorSimple 8100 antes de implantar e configurar o software.
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
ms.openlocfilehash: 0b2301c1c9b846e2ea005f049c5aadb3d4634a81
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963589"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Desembalar, montar em rack e cabear seu dispositivo StorSimple 8100

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição geral
Seu Microsoft Azure StorSimple 8100 é um único compartimento, dispositivo montado em rack. Este tutorial explica como desempacotar, montar em rack e conectar o hardware do dispositivo StorSimple 8100 antes de configurar e implantar o dispositivo StorSimple.

## <a name="unpack-your-storsimple-8100-device"></a>Desempacotar seu dispositivo StorSimple 8100
As etapas a seguir fornecem instruções claras e detalhadas sobre como desempacotar seu dispositivo de armazenamento StorSimple 8100. Este dispositivo é enviado numa única caixa.

### <a name="prepare-to-unpack-your-device"></a>Preparar para desempacotar seu dispositivo
Antes de desempacotar o dispositivo, examine as informações a seguir.

![Ícone](./media/storsimple-safety/IC740879.png)![de aviso ícone](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) de peso pesado **aviso!**

1. Verifique se você tem duas pessoas disponíveis para gerenciar o peso do compartimento se você o estiver manipulando manualmente. Um compartimento totalmente configurado pode pesar até 32 kg (70 lb.).
2. Coloque a caixa numa superfície plana e uniforme.

Em seguida, conclua as etapas a seguir para desempacotar seu dispositivo.

#### <a name="to-unpack-your-device"></a>Para desempacotar seu dispositivo
1. Inspecione a caixa e a espuma de empacotamento para verificar se existem partes esmagadas, cortes, danos causados por água ou quaisquer outros danos óbvios. Se a caixa ou embalagem estiver gravemente danificada, não abra a caixa. [Entre em contato com suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) para ajudá-lo a avaliar se o dispositivo está em boas condições de funcionamento.
2. Desembale a caixa. A imagem a seguir mostra a exibição desempacotada do seu dispositivo StorSimple.
   
     ![Desempacotar seu dispositivo de armazenamento](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **Exibição desempacotada do seu dispositivo de armazenamento**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Caixa de embalagem |
   |   2 |Espuma inferior |
   |   3 |Dispositivo |
   |   4 |Espuma superior |
   |   5 |Caixa de acessório |
3. Depois de desembalar a caixa, certifique-se de que tem:
   
   * 1 dispositivo de compartimento único
   * 2 cabos de alimentação
   * 1 cabo Ethernet cruzado
   * 2 cabos de console serial
   * 1 conversor serial USB para acesso serial
   * 1 chave de fenda T10 à prova de adulteração
   * 4 adaptadores QSFP-to-SFP + para uso com interfaces de rede de 10 GbE
   * 1 kit de montagem em rack (2 trilhos laterais com hardware de montagem)
   * Documentação do Introdução
     
     Se você não recebeu nenhum dos itens listados acima, [entre em contato com suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).

O passo seguinte é montar o dispositivo em bastidor.

## <a name="rack-mount-your-storsimple-8100-device"></a>Montar em rack seu dispositivo StorSimple 8100
Siga as próximas etapas para instalar o dispositivo de armazenamento StorSimple 8100 em um rack padrão de 19 polegadas com postagens dianteiras e traseiras. O dispositivo StorSimple 8100 tem um único compartimento primário.

A instalação consiste em várias etapas, cada uma das quais é discutida nos procedimentos a seguir.

> [!IMPORTANT]
> Os dispositivos StorSimple devem ser montados em rack para uma operação adequada.
> 
> 

### <a name="prepare-the-site"></a>Preparar o site
O dispositivo deve ser instalado em um rack padrão de 19 polegadas que tem as postagens frontal e traseira. Use o procedimento a seguir para preparar a instalação do rack.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Para preparar o site para instalação em rack
1. Certifique-se de que o dispositivo fica apoiado de forma segura numa superfície de trabalho plana, estável e uniforme (ou semelhante).
2. Verifique se o site onde você pretende configurar tem energia CA padrão de uma fonte independente ou uma unidade de distribuição de energia (PDU) de rack com uma fonte de alimentação ininterrupta (UPS).
3. Verifique se um slot 2U está disponível no rack no qual você pretende montar o dispositivo.

![Ícone](./media/storsimple-safety/IC740879.png)![de aviso ícone](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) de peso pesado **aviso!**

Verifique se você tem duas pessoas disponíveis para gerenciar o peso se estiver manipulando a configuração do dispositivo manualmente. Um compartimento totalmente configurado pode pesar até 32 kg (70 lb.).

### <a name="rack-prerequisites"></a>Pré-requisitos do rack
O compartimento 8100 foi projetado para instalação em um gabinete de rack padrão de 19 polegadas com:

* Profundidade mínima de 27,84 polegadas da postagem do rack para a postagem.
* Peso máximo de 32 kg para o dispositivo
* Pressão de retorno máxima de 5 Pascal (medidor de água de 0,5 mm).

### <a name="rack-mounting-rail-kit"></a>Kit de trilhos de montagem de rack
Um conjunto de trilhos de montagem é fornecido para uso com o gabinete de rack de 19 polegadas. Os trilhos foram testados para lidar com o peso máximo do compartimento. Esses trilhos também permitirão a instalação de vários compartimentos sem qualquer perda de espaço no rack.

#### <a name="to-install-the-device-on-the-rails"></a>Para instalar o dispositivo nos trilhos
1. Execute esta etapa somente se os trilhos internos não estiverem instalados no seu dispositivo. Normalmente, os trilhos internos são instalados na fábrica. Se os trilhos não estiverem instalados, instale os slides do trilho esquerdo e do trilho direito nos lados do chassi do compartimento. Eles são anexados usando seis parafusos de métricas em cada lado. Para ajudar com a orientação, os slides do trilho são marcados como **LH – front** e **RH – front**, e o final fixado em direção à parte posterior do compartimento tem um fim cônico.<br/>
   
    ![Anexando slides do trilho ao chassi do compartimento](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Anexando slides do trilho interno aos lados do compartimento**
   
    Etiqueta | Descrição
    ----- | -----------
    1     | Botão M 3x4-parafusos de cabeça
    2     | Slides do chassi

2. Anexe os assemblies do trilho esquerdo externo e do trilho direito externo aos membros verticais do gabinete do rack. Os colchetes estão marcados como **LH**, **RH**e **este lado** para orientá-lo na orientação correta.
3. Localize os pinos na parte frontal e posterior da montagem das calhas. Estenda o trilho para ajustá-lo entre as postagens do rack e insira os pinos nos orifícios do membro vertical da postagem do rack frontal e traseiro. Certifique-se de que o assembly do trilho seja nível.
4. Use dois dos parafusos de métrica fornecidos para proteger o assembly do trilho para os membros verticais do rack. Use um parafuso na frente e outro na parte traseira.
5. Repita essas etapas para o outro assembly de trilho.<br/>
   
     ![Anexando slides do trilho ao gabinete do rack](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Anexando assemblies do trilho externo ao rack**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Parafuso fixação MSS |
   |   2 |Parafuso do rack frontal do buraco quadrado |
   |   3 |Pins do local frontal do trilho esquerdo |
   |   4 |Parafuso fixação MSS |
   |   5 |Pins do local traseiro do trilho esquerdo |

### <a name="mounting-the-device-in-the-rack"></a>Montando o dispositivo no rack
Usando os trilhos do rack que acabaram de ser instalados, execute as etapas a seguir para montar o dispositivo no rack.

#### <a name="to-mount-the-device"></a>Para montar o dispositivo
1. Com um assistente, levante o compartimento e alinhe-o com os trilhos do rack.
2. Insira cuidadosamente o dispositivo nos trilhos e, em seguida, envie o dispositivo completamente para o gabinete do rack.<br/>
   
    ![Inserindo dispositivo no rack](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montando o dispositivo no rack**
3. Remova as tampas do flange frontal esquerda e direita puxando as tampas gratuitas. Os limites do flange simplesmente se encaixam nos flanges.
4. Proteja o compartimento no rack instalando um parafuso de cabeça Phillips fornecido por cada flange, à esquerda e à direita.
5. Instale as tampas do flange pressionando-as na posição e encaixando-as no lugar.<br/>
   
     ![Instalando as tampas do flange](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalando as tampas do flange**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Parafuso de fixação do compartimento |

A próxima etapa é conectar o dispositivo a energia, rede e acesso serial.

## <a name="cable-your-storsimple-8100-device"></a>Cabear o dispositivo StorSimple 8100
Os procedimentos a seguir explicam como conectar o dispositivo StorSimple 8100 para conexões de energia, rede e série.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a cablagem do dispositivo, é necessário:

* Seu dispositivo de armazenamento, completamente desempacotado e montado em rack.
* 2 cabos de energia que vieram com seu dispositivo
* Acesso a 2 unidades de distribuição de energia (recomendado).
* Cabos de rede
* Cabos de série fornecidos
* Conversor serial USB com o driver apropriado instalado no seu PC (se necessário)
* Fornecidos 4 adaptadores QSFP-to-SFP + para uso com interfaces de rede de 10 GbE
* [Hardware com suporte para as interfaces de rede de 10 GbE em seu dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Cabeamento de energia
Seu dispositivo inclui módulos de energia e resfriamento redundantes (PCMs). Ambos os PCMs devem ser instalados e conectados a diferentes fontes de alimentação para garantir a alta disponibilidade.

Execute as etapas a seguir para conectar o dispositivo à energia.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Cabeamento de rede
Seu dispositivo é uma configuração ativa-em espera: a qualquer momento, um módulo do controlador está ativo e processando todas as operações de disco e rede enquanto o outro módulo do controlador está em espera. Se um controlador falhar, o controlador em espera será ativado imediatamente e continuará todas as operações de disco e de rede.

Para dar suporte a esse failover de controlador redundante, você precisará conectar a rede do dispositivo conforme descrito nas etapas a seguir.

#### <a name="to-cable-for-network-connection"></a>Para cabo para conexão de rede
1. Seu dispositivo tem seis interfaces de rede em cada controlador: quatro portas Ethernet de 1 Gbps e 2 10 Gbps. Identifique as várias portas de dados no plano posterior do seu dispositivo.
   
    ![Plano posterior do dispositivo 8100](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Parte traseira do dispositivo mostrando portas de dados**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   0,1,4,5 |interfaces de rede de 1 GbE |
   |   2,3 |interfaces de rede de 10 GbE |
   |   6 |Portas seriais |
2. Consulte o diagrama a seguir para o cabeamento de rede. (A configuração mínima de rede é mostrada por linhas azuis sólidas. A configuração adicional necessária para alta disponibilidade e desempenho é mostrada por linhas pontilhadas.)

    ![Cabear seu dispositivo 2U para rede](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Cabeamento de rede para seu dispositivo**

   |Etiqueta | Descrição |
   |----- | ----------- |
   | A    | LAN com acesso à Internet |
   | B    | Controlador 0 |
   | C    | PCM 0 |
   | D    | Controlador 1 |
   | E    | PCM 1 |
   | F, G | Anfitriões |
   | 0-5  | Interfaces de rede |



Ao cabear o dispositivo, a configuração mínima requer:

* Pelo menos duas interfaces de rede conectadas em cada controlador com uma para acesso à nuvem e outra para iSCSI. A porta DATA 0 é automaticamente habilitada e configurada por meio do console serial do dispositivo. Além do DATA 0, outra porta de dados também precisa ser configurada por meio do portal clássico do Azure. Nesse caso, conecte a porta DATA 0 à LAN primária (rede com acesso à Internet). As outras portas de dados podem ser conectadas ao segmento de rede de área de LAN (VLAN) SAN/iSCSI, dependendo da função pretendida.
* Interfaces idênticas em cada controlador conectado à mesma rede para garantir a disponibilidade se ocorrer um failover do controlador. Por exemplo, se você optar por conectar DATA 0 e DATA 3 para um dos controladores, será necessário conectar os dados correspondentes 0 e 3 no outro controlador.

Tenha em mente para alta disponibilidade e desempenho:

* Quando possível, configure um par de interface de rede para acesso à nuvem (1 GbE) e outro par para iSCSI (10 GbE recomendado) em cada controlador.
* Quando possível, conecte as interfaces de rede de cada controlador a dois comutadores diferentes para garantir a disponibilidade contra uma falha de comutador. A figura ilustra as interfaces de rede 2 10 GbE, DATA 2 e DATA 3, de cada controlador conectado a dois comutadores diferentes.

Para obter mais informações, consulte as **interfaces de rede** sob os [requisitos de alta disponibilidade para seu dispositivo StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Se você estiver usando transceptores SFP + com suas interfaces de rede de 10 GbE, use os adaptadores QSFP-SFP + fornecidos. Para obter mais informações, acesse [hardware com suporte para as interfaces de rede de 10 GbE em seu dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Cabeamento de porta serial
Execute as etapas a seguir para cabear a porta serial.

#### <a name="to-cable-for-serial-connection"></a>Para cabo para conexão serial
1. Seu dispositivo tem uma porta serial em cada controlador que é identificado por um ícone de chave inglesa. Consulte a ilustração na seção cabeamento de [rede](#network-cabling) para localizar as portas seriais no plano posterior do seu dispositivo.
2. Identifique o controlador ativo no backplane do seu dispositivo. Um LED azul piscando indica que o controlador está ativo.
3. Use os cabos de série fornecidos (se necessário, o conversor serial USB para seu laptop) e conecte seu console ou computador (com emulação de terminal ao dispositivo) à porta serial do controlador ativo.
4. Instale os drivers Serial USB (fornecidos com o dispositivo) em seu computador.
5. Configure a conexão serial da seguinte maneira: 115.200 baud, 8 bits de dados, 1 bit de parada, sem paridade e controle de fluxo definidos como nenhum.
6. Verifique se a conexão está funcionando pressionando Enter no console. Um menu do console serial deve aparecer.

> [!NOTE]
> **Gerenciamento de luzes**: Quando o dispositivo for instalado em um datacenter remoto ou em uma sala de computadores com acesso limitado, verifique se as conexões de série para ambos os controladores estão sempre conectadas a um comutador de console serial ou equipamento semelhante. Isso permite operações de suporte e controle remoto fora de banda se houver interrupções de rede ou falhas inesperadas.
> 
> 

Seu dispositivo agora está cabeado por energia, acesso à rede e conectividade serial. A próxima etapa é configurar o software e implantar seu dispositivo.

## <a name="next-steps"></a>Passos seguintes
Saiba como [implantar e configurar seu dispositivo StorSimple local](storsimple-8000-deployment-walkthrough-u2.md).

