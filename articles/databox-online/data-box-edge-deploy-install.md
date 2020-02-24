---
title: Tutorial para instalar-Unpack, rack, cabo Azure Data Box Edge dispositivo físico | Microsoft Docs
description: O segundo tutorial sobre a instalação de Azure Data Box Edge envolve como desempacotar, colocar em rack e conectar o dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/17/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: fe74db34e62a80935954c6cfc2e591d49a84b0b7
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263953"
---
# <a name="tutorial-install-azure-data-box-edge"></a>Tutorial: instalar o Azure Data Box Edge

Este tutorial descreve como instalar um dispositivo físico do Data Box Edge. O procedimento de instalação envolve desempacotamento, montagem em rack e cabeamento do dispositivo. 

A instalação pode levar cerca de duas horas para ser concluída.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Desembalar o dispositivo
> * Montar o dispositivo no rack
> * Ligar o dispositivo

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para instalar um dispositivo físico da seguinte maneira:

### <a name="for-the-data-box-edge-resource"></a>Para o recurso do Data Box Edge

Antes de começar, certifique-se de que:

* Você concluiu todas as etapas em [preparar para implantar Azure data Box Edge](data-box-edge-deploy-prep.md).
    * Você criou um recurso de Data Box Edge para implantar seu dispositivo.
    * Gerou a chave de ativação para ativar o dispositivo com o recurso do Data Box Edge.

 
### <a name="for-the-data-box-edge-physical-device"></a>Para o dispositivo físico do Data Box Edge

Antes de implementar um dispositivo:

- Certifique-se de que o dispositivo fique com segurança em uma superfície de trabalho plana, estável e nivelada.
- Verifique se o local onde quer configurar o dispositivo tem:
    - Alimentação AC padrão de uma fonte independente

        -OU-
    - Uma PDU (unidade de distribuição de energia) de rack com uma fonte de alimentação ininterrupta (UPS)
    - Um slot de 1U disponível no rack no qual você pretende montar o dispositivo

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter

Antes de começar:

- Examine os requisitos de rede para implantar Data Box Edge e configure a rede do datacenter de acordo com os requisitos. Para obter mais informações, veja [Requisitos de rede do Data Box Edge](data-box-edge-system-requirements.md#networking-port-requirements).

- Certifique-se de que a largura de banda mínima da Internet seja de 20 Mbps para o funcionamento ideal do dispositivo.


## <a name="unpack-the-device"></a>Desembalar o dispositivo

Este dispositivo é enviado numa única caixa. Conclua os passos seguintes para desembalar o dispositivo. 

1. Coloque a caixa numa superfície plana e uniforme.
2. Inspecione a caixa e a espuma de empacotamento para verificar se existem partes esmagadas, cortes, danos causados por água ou quaisquer outros danos óbvios. Se a caixa ou o empacotamento estiver seriamente danificado, não o abra. Contacte o Suporte da Microsoft para ajudar a avaliar se o dispositivo está funcional.
3. Desembale a caixa. Depois de desembalar a caixa, certifique-se de que tem:
    - Um único compartimento Data Box Edge dispositivo
    - Dois cabos de alimentação
    - Assembly de kit de um trilho
    - Um livreto de segurança, ambiente e informações regulatórias

Se você não recebeu todos os itens listados aqui, entre em contato com o suporte Data Box Edge. A próxima etapa é montar o dispositivo em rack.


## <a name="rack-the-device"></a>Montar o dispositivo em bastidor

O dispositivo deve ser instalado em um rack padrão de 19 polegadas. Use o procedimento a seguir para montar o dispositivo em rack em um rack padrão de 19 polegadas.

> [!IMPORTANT]
> Os dispositivos Data Box Edge têm de ser montados em bastidor para um funcionamento correto.


### <a name="prerequisites"></a>Pré-requisitos

- Antes de começar, leia as instruções de segurança em seu livreto de segurança, ambiente e informações regulatórias. Este livreto foi enviado com o dispositivo.
- Comece a instalar os trilhos no espaço alocado mais próximo à parte inferior do compartimento do rack.
- Para a configuração de montagem do trilho de ferramentas:
    -  Você precisa fornecer oito parafusos: #10-32, #12-24, #M5 ou #M6. O diâmetro de cabeça dos parafusos deve ser inferior a 10 mm (0,4 ").
    -  Você precisa de uma chave de fenda com gorjetas planas.

### <a name="identify-the-rail-kit-contents"></a>Identificar o conteúdo do kit do trilho

Localize os componentes para instalar o assembly do kit do trilho:
1. Dois assemblies de trilho deslizante do Dell ReadyRails II a7
2. Duas pulseiras de gancho e loop

    ![Identificar conteúdo do kit do trilho](./media/data-box-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Instalar e remover trilhos sem ferramentas (orifício quadrado ou racks de orifícios redondos)

> [!TIP]
> Essa opção é sem ferramentas porque não requer ferramentas para instalar e remover os trilhos no quadrado não-thread ou nos orifícios arredondados nos racks.

1. Posicione as peças de extremidade do trilho esquerda e direita rotuladas na **frente** para dentro e oriente cada peça final para colocar nos orifícios no lado frontal dos flanges verticais do rack.
2. Alinhe cada parte final nos orifícios inferior e superior dos espaços em U desejados.
3. Envolva o back-end do trilho até que ele se encaixe totalmente no flange vertical do rack e que os cliques da trava estejam no lugar. Repita essas etapas para posicionar e colocar a peça de front-end no flange vertical do rack.
4. Para remover os trilhos, puxe o botão de liberação de trava no ponto médio de extremidade e desencaixe cada trilho.

    ![Instalar e remover trilhos sem ferramentas](./media/data-box-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Instalar e remover trilhos de ferramentas (racks de orifícios segmentados)

> [!TIP]
> Essa opção é orientada porque requer uma ferramenta (_uma chave de fenda com gorjetas planas_) para instalar e remover os trilhos nos orifícios de round-Threading nos racks.

1. Remova os PINs dos colchetes de montagem frontal e traseira usando uma chave de fenda com gorjetas planas.
2. Puxe e gire os subconjuntos de travas do trilho para removê-los dos colchetes de montagem.
3. Anexe os trilhos de montagem esquerdo e direito aos flangeos do rack vertical frontal usando dois pares de parafusos.
4. Deslize os colchetes à esquerda e à direita para frente nos flanges verticais do rack traseiro e anexe-os usando dois pares de parafusos.

    ![Instalar e remover trilhos com ferramentas](./media/data-box-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Instalar o sistema em um rack

1. Puxe os trilhos do slide interno para fora do rack até que eles travem no local.
2. Localize o ressalto do trilho traseiro em cada lado do sistema e abaixe-o nos slots J traseiros nos assemblies do slide. Gire o sistema para baixo até que todos os espaçadores do trilho estejam encaixados nos slots J.
3. Pressione o sistema para dentro até que as alavancas de bloqueio cliquem no lugar.
4. Pressione os botões de bloqueio de liberação de slides em ambos os trilhos e deslize o sistema para o rack.

    ![Instalar o sistema em um rack](./media/data-box-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Remover o sistema do rack

1. Localize as alavancas de bloqueio nos lados dos trilhos internos.
2. Desbloqueie cada alavanca girando-a para sua posição de liberação.
3. Segure os lados do sistema firmemente e empurre-os para frente até que os espaçadores do trilho estejam na frente dos J-slots. Levante o sistema para cima e para fora do rack e coloque-o em uma superfície de nível.

    ![Remover o sistema do rack](./media/data-box-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Envolver e liberar a trava térrea

> [!NOTE]
> Para sistemas não equipados com travas térrea, proteja o sistema usando parafusos, conforme descrito na etapa 3 deste procedimento.

1. Voltado para a frente, localize a trava térrea em qualquer lado do sistema.
2. As travas se encontram automaticamente à medida que o sistema é colocado no rack e são liberados com o pull das travas.
3. Para proteger o sistema para envio no rack ou para outros ambientes instável, localize o parafuso de montagem rígida em cada trava e aperte cada parafuso com uma chave de fenda Phillips #2.

    ![Envolver e liberar trava térrea](./media/data-box-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>Ligar o dispositivo

Encaminhe os cabos e conecte o dispositivo. Os procedimentos a seguir explicam como conectar seu dispositivo de Data Box Edge para energia e rede.

Antes de iniciar o cabeamento do dispositivo, você precisará do seguinte:

- Seu Data Box Edge dispositivo físico, desempacotado e montado em rack.
- Dois cabos de alimentação.
- Pelo menos um cabo de rede de 1 GbE RJ-45 para ligar à interface de gestão. Existem duas interfaces de rede de 1 GbE, uma de gestão e uma de dados, no dispositivo.
- Um cabo de cobre de 25 GbE SFP+ para cada interface de rede de dados a ser configurada. Pelo menos uma interface de rede de dados entre a porta 2, a porta 3, a porta 4, a porta 5 ou a porta 6 precisa estar conectada à Internet (com conectividade com o Azure).  
- Acesso a duas unidades de distribuição de energia (recomendado).

> [!NOTE]
> - Se você estiver conectando apenas uma interface de rede de dados, recomendamos que você use uma interface de rede de 25/10 GbE, como a porta 3, a porta 4, a porta 5 ou a porta 6 para enviar dados para o Azure. 
> - Para obter um melhor desempenho e lidar com grandes volumes de dados, considere ligar todas as portas de dados.
> - O dispositivo Data Box Edge deve estar conectado à rede do datacenter para que ele possa ingerir dados de servidores de fonte de dados.

Em seu dispositivo de Data Box Edge:

- O painel frontal tem unidades de disco e um botão de energia.

    - Há 10 slots de disco na frente do seu dispositivo.
    - O slot 0 tem uma unidade SATA de 240 GB usada como um disco do sistema operacional. O slot 1 está vazio e os slots de 2 a 9 são SSDs de NVMe usados como discos de dados.
- O plano de fundo inclui unidades de fonte de alimentação redundantes (PSUs).
- O plano de fundo tem seis interfaces de rede:

    - Duas interfaces de 1 Gbps.
    - Interfaces 4 25-Gbps que também podem servir como interfaces de 10 Gbps.
    - Um Baseboard Management Controller (BMC).

- O plano de fundo tem duas placas de rede correspondentes às 6 portas:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

Para obter uma lista completa de cabos, comutadores e transceptores com suporte para essas placas de rede, acesse a [matriz de interoperabilidade do Cavium FastlinQ 41000 Series](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
 
Execute as etapas a seguir para conectar o dispositivo à energia e à rede.

1. Identifique as várias portas no plano de fundo do seu dispositivo.

    ![Plano traseiro de um dispositivo cabeado](./media/data-box-edge-deploy-install/backplane-cabled.png)

2. Localize os slots de disco e o botão de energia na frente do dispositivo.

    ![Plano frontal de um dispositivo](./media/data-box-edge-deploy-install/device-front-plane-labeled-1.png)

3. Ligue os cabos de alimentação a cada uma das PSUs no bastidor. Para garantir uma elevada disponibilidade, instale e ligue as PSUs a diferentes fontes de alimentação.
4. Ligue os cabos de alimentação às unidades de distribuição de energia (PDUs) em bastidor. Certifique-se de que as duas PSUs utilizam fontes de alimentação separadas.
5. Pressione o botão de energia para ligar o dispositivo.
6. Conecte a porta 1 da interface de rede de 1 GbE ao computador que é usado para configurar o dispositivo físico. A PORTA 1 é a interface de gestão dedicada.
7. Ligue uma ou mais das PORTAS 2, 3, 4, 5 ou 6 à rede/Internet do datacenter.

    - Se ligar a PORTA 2, utilize o cabo de rede RJ-45.
    - Para as interfaces de rede de 10/25 GbE, use os cabos de cobre +.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu sobre tópicos de Data Box Edge como:

> [!div class="checklist"]
> * Desembalar o dispositivo
> * Montar o dispositivo em bastidor
> * Ligar o dispositivo

Avance para o próximo tutorial para aprender a ligar, configurar e ativar o seu dispositivo.

> [!div class="nextstepaction"]
> [Conectar e configurar Data Box Edge](./data-box-edge-deploy-connect-setup-activate.md)
