---
title: Tutorial sobre como instalar um dispositivo físico do Edge de caixa de dados do Azure | Documentos da Microsoft
description: O segundo tutorial sobre como instalar o Edge de caixa de dados do Azure envolve como descompactar, bastidor e instalar os cabos do dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: a1357e92b868f85556fc4d665eb475abd095fece
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400013"
---
# <a name="tutorial-install-azure-data-box-edge"></a>Tutorial: Instalar o Edge de caixa de dados do Azure

Este tutorial descreve como instalar um dispositivo físico do Data Box Edge. O procedimento de instalação envolve desempacotar, montagem de rack e cabos do dispositivo. 

A instalação pode demorar cerca de duas horas a concluir.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Desembalar o dispositivo
> * Montar em Bastidor o dispositivo
> * Ligar o dispositivo

## <a name="prerequisites"></a>Pré-requisitos

Pré-requisitos para instalar um dispositivo físico da seguinte forma:

### <a name="for-the-data-box-edge-resource"></a>Para o recurso do Data Box Edge

Antes de começar, certifique-se de que:

* Concluiu todos os passos [preparar a implementação de borda de caixa de dados do Azure](data-box-edge-deploy-prep.md).
    * Acabou de criar um recurso de borda de caixa de dados para implementar o dispositivo.
    * Gerou a chave de ativação para ativar o dispositivo com o recurso do Data Box Edge.

 
### <a name="for-the-data-box-edge-physical-device"></a>Para o dispositivo físico do Data Box Edge

Antes de implementar um dispositivo:

- Certifique-se de que o dispositivo com segurança assenta numa superfície plana, estável e nível de trabalho.
- Verifique se o local onde quer configurar o dispositivo tem:
    - Padrão alternada a partir de uma origem independente

        -OU-
    - Uma unidade de distribuição de energia (PDU) de rack com uma alimentação ininterrupta (UPS)
    - Um slot de 1U disponíveis no rack no qual pretende montar o dispositivo

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter

Antes de começar:

- Reveja os requisitos de rede para a implementação de borda de caixa de dados e configurar a rede de centro de dados pelos requisitos. Para obter mais informações, veja [Requisitos de rede do Data Box Edge](data-box-edge-system-requirements.md#networking-port-requirements).

- Certifique-se de que a largura de banda de Internet mínima é 20 Mbps para funcionar ideal do dispositivo.


## <a name="unpack-the-device"></a>Desembalar o dispositivo

Este dispositivo é enviado numa única caixa. Conclua os passos seguintes para desembalar o dispositivo. 

1. Coloque a caixa numa superfície plana e uniforme.
2. Inspecione a caixa e a espuma de empacotamento para verificar se existem partes esmagadas, cortes, danos causados por água ou quaisquer outros danos óbvios. Se a caixa ou empacotamento gravemente está danificado, não abri-lo. Contacte o Suporte da Microsoft para ajudar a avaliar se o dispositivo está funcional.
3. Desembale a caixa. Depois de desembalar a caixa, certifique-se de que tem:
    - Um dispositivo Edge de bastidor único
    - Dois cabos de alimentação
    - Assembly do um rail kit
    - Um livreto de segurança, ambientais e normas de informações

Se não tiver recebido todos os itens listados aqui, contacte o suporte de dados caixa Edge. O passo seguinte consiste em rack montar o seu dispositivo.


## <a name="rack-the-device"></a>Montar o dispositivo em bastidor

O dispositivo tem de ser instalado num bastidor de 19 polegadas padrão. Utilize o procedimento seguinte para rack montar o seu dispositivo no bastidor 19 polegadas padrão.

> [!IMPORTANT]
> Os dispositivos Data Box Edge têm de ser montados em bastidor para um funcionamento correto.


### <a name="prerequisites"></a>Pré-requisitos

- Antes de começar, leia as instruções de segurança no seu livreto de segurança, ambientais e informações de regulamentação. Este livreto foi lançado com o dispositivo.
- Começa a instalar os rails no espaço alocado que está mais próximo da parte inferior do bastidor de rack.
- Para a configuração de montagem do tooled rail, precisa fornecer oito screws: #10-32, #12 a 24, #M5 ou #M6. O diâmetro principal dos screws tem de ser inferior a 10 mm (0.4").

### <a name="identify-the-rail-kit-contents"></a>Identificar o conteúdo do kit rail

Localize os componentes para instalar o assembly do rail kit:
1. II de ReadyRails Dell dois A7 deslizante assemblies do rail
2. Dois straps hook e loop

![Identificar o conteúdo do kit de rail](./media/data-box-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Instalar e remover a ferramenta sem rails (buraco quadrado ou buraco redondo racks)

1. Posicionar as partes de fim de left e right rail rotuladas **FRONT** com acesso à inward e orientar a cada peça final para os buracos no lado do front-de flanges o bastidor vertical de usuários.
2. Alinhe cada peça final os buracos na parte inferior e superior dos espaços de U pretendidos.
3. Interagir com o back-end do rail até ele totalmente o flange vertical rack e os cliques de bloqueio temporário no lugar certo. Repita estes passos para posicionar e a parte de front-end no flange bastidor vertical de usuários.
4. Para remover os rails, extrair o botão de versão do bloqueio temporário no ponto de médio de peça final e unseat cada rail.

![Instalar e remover a ferramenta sem rails](./media/data-box-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Instalar e remover rails tooled (Threaded buraco racks)

1. Remova os pins de frente e traseiro montar Retos usando um screwdriver colocado para fixa.
2. Extrair e girar os subassemblies de bloqueio temporário do rail para removê-los dos colchetes de montagem.
3. Anexe à esquerda e direita montar rails para as flanges de front-rack vertical através de dois pares de screws.
4. Deslize o lado esquerda e direita de cópia de parênteses Retos reencaminhar contra os flanges rear vertical rack e anexe-os através de dois pares de screws.

![Instalar e remover tooled rails](./media/data-box-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Instalar o sistema num bastidor

1. Extrair os rails slide interna fora do rack até que eles bloquear no lugar certo.
2. Localize o standoff rear rail em cada lado do sistema e reduzi-los para o traseiro J ranhuras nos assemblies de slide. Roda para baixo o sistema até que todos os standoffs de rail são sentados nas ranhuras-J.
3. Enviar por push o sistema inward até que o alavancas de bloqueio, clique no lugar certo.
4. Prima os botões de bloqueio de lançamento de slide on rails e slide o sistema em rack.

![Instalar o sistema num bastidor](./media/data-box-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Remover o sistema de bastidor

1. Localize as alavancas de bloqueio nos lados dos rails internas.
2. Desbloquear cada alavanca por girando-o até sua posição de versão.
3. Entender os lados do sistema firmemente e recebê-los para frente até que o standoffs da grade são na frente da J-ranhuras. Leve o sistema de cima e para fora do rack e coloque-o numa superfície de nível.

![Remover o sistema de bastidor](./media/data-box-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Se envolver e liberar o bloqueio temporário mais

NOTA: Para sistemas não equipados com mais travas, proteger o sistema usando screws, conforme descrito no passo 3 deste procedimento.

1. Com acesso à frente, localize o bloqueio temporário mais em ambos os lados do sistema.
2. As travas envolver-se automaticamente à medida que o sistema é enviada para o bastidor e são lançadas ao extrair a cópia de segurança nas travas.
3. Para proteger o sistema de remessa no rack ou de outros ambientes instáveis, localize o screw de montagem disco rígido em cada bloqueio temporário e reforçar a cada screw com um #2 Phillips screwdriver.

![Interaja com e sem bloqueios temporários de mais de versão](./media/data-box-edge-deploy-install/engaging-releasing-slam-latch.png)

### <a name="route-the-cables"></a>Encaminhar os cabos

> [!NOTE]
>  Se não o solicitar o cabo de gestão Arm (CMA) opcional, usar o gancho de duas e fazer um loop straps fornecidos no kit do rail para encaminhar os cabos remissivo seu sistema.

1. Localize os colchetes CMA externos nos lados interior de ambos os flanges rack.
2. Agrupar os cabos moderadamente, colocá-los limpar dos conectores do sistema para as laterais esquerda e direita.
3. Os straps hook e o loop por meio de ranhuras tooled nos colchetes CMA externas de cada lado do sistema para proteger os pacotes de cabo de thread.


![Encaminhar os cabos](./media/data-box-edge-deploy-install/routing-cables.png)

## <a name="cable-the-device"></a>Ligar o dispositivo

Os procedimentos seguintes explicam como ligar o dispositivo Edge em termos de alimentação e rede.

Antes de começar a cablagem o seu dispositivo, precisa do seguinte:

- Desembalar e montar o dispositivo físico do Edge em bastidor.
- Dois cabos de alimentação. 
- Pelo menos um cabo de rede de 1 GbE RJ-45 para ligar à interface de gestão. Existem duas interfaces de rede de 1 GbE, uma de gestão e uma de dados, no dispositivo.
- Um cabo de cobre de 25 GbE SFP+ para cada interface de rede de dados a ser configurada. Interface de rede de dados, pelo menos, um dentre porta 2, porta 3, 4 de porta, porta 5 ou 6 da porta tem de estar ligado à Internet (com uma conectividade para o Azure).  
- Acesso para duas unidades de distribuição de energia (recomendado).

> [!NOTE]
> - Se estiver a ligar apenas uma interface de rede de dados, recomendamos que utilize uma interface de rede de 25/10 GbE, como a porta 3, 4 de porta, porta 5 ou 6 de porta para enviar dados para o Azure. 
> - Para obter um melhor desempenho e lidar com grandes volumes de dados, considere ligar todas as portas de dados.
> - O dispositivo Edge deve estar ligado à rede do datacenter para que possa ingerir dados dos servidores de origem de dados.

O dispositivo Edge tem 8 SSDs NVMe. O painel frontal também tem LEDs de estado e botões de energia. O dispositivo inclui unidades de fonte de alimentação de energia redundantes (PSUs), no fundo. O dispositivo tem seis interfaces de rede:

- Duas interfaces de 1 Gbps
- Interfaces de 25 quatro Gbps que também podem servir como interfaces de 10 Gbps.
- Um controlador de gestão de placas base (BMC). 

Identifique as várias portas no painel posterior do dispositivo.
 
  ![Painel posterior de um dispositivo ligado por cabo](./media/data-box-edge-deploy-install/backplane-cabled.png)

O dispositivo tiver duas placas de rede correspondente para as 6 portas: 

 - QLogic FastLinQ 41264
 - QLogic FastLinQ 41262

Para obter uma lista completa de transcetores para essas placas de rede, comutadores e cabos suportados, aceda a [Cavium FastlinQ 41000 série interoperabilidade matriz](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
 
Siga os passos seguintes para instalar os cabos do dispositivo para poder e a rede.

1. Ligue os cabos de alimentação a cada uma das PSUs no bastidor. Para garantir uma elevada disponibilidade, instale e ligue as PSUs a diferentes fontes de alimentação.

2. Ligue os cabos de alimentação às unidades de distribuição de energia (PDUs) em bastidor. Certifique-se de que as duas PSUs utilizam fontes de alimentação separadas.

3. Ligue-se a interface de rede de 1 GbE 1 de porta para o computador que é utilizado para configurar o dispositivo físico. A PORTA 1 é a interface de gestão dedicada.

4. Ligue uma ou mais das PORTAS 2, 3, 4, 5 ou 6 à rede/Internet do datacenter. Se ligar a PORTA 2, utilize o cabo de rede RJ-45. Para as interfaces de rede de 10/25 GbE, utilize os cabos SFP + cobre.  

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu sobre tópicos de borda de caixa de dados, como:

> [!div class="checklist"]
> * Desembalar o dispositivo
> * Montar o dispositivo em bastidor
> * Ligar o dispositivo

Avance para o próximo tutorial para aprender a ligar, configurar e ativar o seu dispositivo.

> [!div class="nextstepaction"]
> [Ligar e configurar dados de caixa de borda](./data-box-edge-deploy-connect-setup-activate.md)


