---
title: Tutorial para instalar - Desembalar, cremalheira, cabo Azure Stack Edge Pro dispositivo físico | Microsoft Docs
description: O segundo tutorial sobre a instalação do Azure Stack Edge Pro envolve como desempacotar, rack e cabo do dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/17/2020
ms.author: alkohli
ms.openlocfilehash: caf64de55c48d763b8600988e5ff2aba2c83e4f9
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060198"
---
# <a name="tutorial-install-azure-stack-edge-pro"></a>Tutorial: Instalar Azure Stack Edge Pro

Este tutorial descreve como instalar um dispositivo físico Azure Stack Edge Pro. O procedimento de instalação envolve desembalar, montar a cremalheira e cabos do dispositivo. 

A instalação pode demorar cerca de duas horas a ser concluída.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Desembalar o dispositivo
> * Montagem do rack o dispositivo
> * Ligar o dispositivo

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para a instalação de um dispositivo físico da seguinte forma:

### <a name="for-the-azure-stack-edge-resource"></a>Para o recurso Azure Stack Edge

Antes de começar, certifique-se de que:

* Completou todos os passos em [Prepare-se para implementar o Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md).
    * Criou um recurso Azure Stack Edge para implementar o seu dispositivo.
    * Gerou a chave de ativação para ativar o seu dispositivo com o recurso Azure Stack Edge.

 
### <a name="for-the-azure-stack-edge-pro-physical-device"></a>Para o dispositivo físico Azure Stack Edge Pro

Antes de implementar um dispositivo:

- Certifique-se de que o aparelho assenta em segurança numa superfície de trabalho plana, estável e nivelada.
- Verifique se o local onde quer configurar o dispositivo tem:
    - Potência padrão AC de uma fonte independente

        -OU-
    - Uma unidade de distribuição de energia de cremalheira (PDU) com uma alimentação ininterrupta (UPS)
    - Uma ranhura 1U disponível na prateleira na qual pretende montar o dispositivo

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter

Antes de começar:

- Reveja os requisitos de networking para a implementação do Azure Stack Edge Pro e configuure a rede do datacenter de acordo com os requisitos. Para obter mais informações, consulte [os requisitos de networking Azure Stack Edge Pro](azure-stack-edge-system-requirements.md#networking-port-requirements).

- Certifique-se de que a largura de banda mínima da Internet é de 20 Mbps para um melhor funcionamento do dispositivo.


## <a name="unpack-the-device"></a>Desembalar o dispositivo

Este dispositivo é enviado numa única caixa. Conclua os passos seguintes para desembalar o dispositivo. 

1. Coloque a caixa numa superfície plana e uniforme.
2. Inspecione a caixa e a espuma de empacotamento para verificar se existem partes esmagadas, cortes, danos causados por água ou quaisquer outros danos óbvios. Se a caixa ou a embalagem estiverem gravemente danificadas, não a abra. Contacte o Suporte da Microsoft para ajudar a avaliar se o dispositivo está funcional.
3. Desembale a caixa. Depois de desembalar a caixa, certifique-se de que tem:
    - Um único dispositivo Azure Stack Edge Pro
    - Dois cabos de alimentação
    - Um conjunto de kit de trilho
    - Um folheto de Informação de Segurança, Ambiente e Regulação

Se não recebeu todos os itens listados aqui, contacte o suporte Azure Stack Edge Pro. O próximo passo é montar o seu dispositivo.


## <a name="rack-the-device"></a>Montar o dispositivo em bastidor

O aparelho deve ser instalado num cesto normal de 19 polegadas. Utilize o seguinte procedimento para montar o seu dispositivo num cesto normal de 19 polegadas.

> [!IMPORTANT]
> Os dispositivos Azure Stack Edge Pro devem ser montados na cremalheira para um bom funcionamento.


### <a name="prerequisites"></a>Pré-requisitos

- Antes de começar, leia as instruções de segurança no seu folheto de Informação de Segurança, Ambiente e Regulação. Este folheto foi enviado com o dispositivo.
- Comece a instalar os trilhos no espaço atribuído mais próximo do fundo da caixa do rack.
- Para a configuração de montagem do trilho com ferramentas:
    -  É necessário fornecer oito parafusos: #10-32, #12-24, #M5 ou #M6. O diâmetro da cabeça dos parafusos deve ser inferior a 10 mm (0,4").
    -  Precisa de uma chave de fendas de ponta plana.

### <a name="identify-the-rail-kit-contents"></a>Identifique o conteúdo do kit ferroviário

Localize os componentes para a instalação do conjunto do kit ferroviário:
1. Dois conjuntos ferroviários de deslizamento A7 Dell ReadyRails II
2. Duas correias de gancho e laço

    ![Identificar o conteúdo do kit ferroviário](./media/azure-stack-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Instale e remova trilhos sem ferramentas (furo quadrado ou prateleiras redondas)

> [!TIP]
> Esta opção não necessita de ferramentas para instalar e remover os trilhos nos furos quadrados ou redondos não lidos nas prateleiras.

1. Posicione as peças finais do carril esquerdo e direito rotuladas **frontalmente** viradas para dentro e oriente cada peça final para se sentar nos orifícios do lado frontal das flanges de cremalheira vertical.
2. Alinhe cada peça final nos orifícios inferiores e superiores dos espaços U desejados.
3. Engate a parte de trás do trilho até que se coloque completamente sobre a flange da cremalheira vertical e o fecho clica no lugar. Repita estes passos para a posição e coloque a peça frontal na flange vertical da cremalheira.
4. Para retirar os trilhos, puxe o botão de desbloqueio do trinco no ponto médio da peça final e desataça cada trilho.

    ![Instale e remova trilhos sem ferramentas](./media/azure-stack-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Instale e remova trilhos com ferramentas (prateleiras de furos roscados)

> [!TIP]
> Esta opção é ferramenta porque requer uma ferramenta (_uma chave de fendas de ponta plana_) para instalar e remover os trilhos nos orifícios redondos roscados nas prateleiras.

1. Retire os pinos dos suportes de montagem dianteiro e traseiro utilizando uma chave de fendas de ponta plana.
2. Puxe e rode as submontagens do trinco para removê-las dos suportes de montagem.
3. Fixe os trilhos de montagem esquerdo e direito às flanges de cremalheira vertical dianteiras utilizando dois pares de parafusos.
4. Deslize os suportes traseiros esquerdo e direito para a frente contra as flanges de cremalheira vertical traseira e prenda-os com dois pares de parafusos.

    ![Instale e remova trilhos com ferramentas](./media/azure-stack-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Instale o sistema num rack

1. Retire os trilhos internos do cesto até que se tranquem no lugar.
2. Localize o impasse do trilho traseiro em cada lado do sistema e baixe-os nas ranhuras traseiras J nos conjuntos de diapositivos. Rode o sistema para baixo até que todos os impasses ferroviários estejam sentados nas ranhuras J.
3. Empurre o sistema para dentro até que as alavancas de bloqueio cliquem no lugar.
4. Pressione os botões de bloqueio de desbloqueio de diapositivos em ambos os carris e deslize o sistema para dentro da cremalheira.

    ![Instale o sistema num rack](./media/azure-stack-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Retire o sistema da cremalheira

1. Localize as alavancas de bloqueio nos lados dos trilhos internos.
2. Desbloqueie cada alavanca rodando-a até à sua posição de desbloqueio.
3. Segure firmemente os lados do sistema e puxe-o para a frente até que os impasses ferroviários estejam na frente das ranhuras J. Levante o sistema para cima e para longe da cremalheira e coloque-o sobre uma superfície nivelada.

    ![Remover o sistema da cremalheira](./media/azure-stack-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Engate e liberte o trinco slam

> [!NOTE]
> Para sistemas não equipados com fechos de encadação, fixe o sistema utilizando parafusos, conforme descrito no passo 3 deste procedimento.

1. Virado para a frente, localize o fecho do fecho de ambos os lados do sistema.
2. Os fechos ligam-se automaticamente à medida que o sistema é empurrado para dentro da cremalheira e são libertados puxando para cima os fechos.
3. Para fixar o sistema de envio na cremalheira ou para outros ambientes instáveis, coloque o parafuso de montagem dura debaixo de cada trinco e aperte cada parafuso com uma chave de fendas #2 Phillips.

    ![Engate e solte o trinco slam](./media/azure-stack-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>Ligar o dispositivo

Encaminhe os cabos e, em seguida, teleférico o seu dispositivo. Os seguintes procedimentos explicam como cabo o seu dispositivo Azure Stack Edge Pro para energia e rede.

Antes de começar a ligar o seu dispositivo, precisa do seguinte:

- O seu dispositivo físico Azure Stack Edge Pro, desembalado e montado na cremalheira.
- Dois cabos de alimentação.
- Pelo menos um cabo de rede de 1 GbE RJ-45 para ligar à interface de gestão. Existem duas interfaces de rede de 1 GbE, uma de gestão e uma de dados, no dispositivo.
- Um cabo de cobre de 25 GbE SFP+ para cada interface de rede de dados a ser configurada. Pelo menos uma interface de rede de dados entre os PORT 2, PORT 3, PORT 4, PORT 5 ou PORT 6 tem de ser ligada à Internet (com conectividade com a Azure).  
- Acesso a duas unidades de distribuição de energia (recomendada).

> [!NOTE]
> - Se estiver a ligar apenas uma interface de rede de dados, recomendamos que utilize uma interface de rede de 25/10 GbE, como PORT 3, PORT 4, PORT 5 ou PORT 6 para enviar dados para o Azure. 
> - Para obter um melhor desempenho e lidar com grandes volumes de dados, considere ligar todas as portas de dados.
> - O dispositivo Azure Stack Edge Pro deve ser ligado à rede do datacenter para que possa ingerir dados a partir de servidores de fontes de dados.

No seu dispositivo Azure Stack Edge Pro:

- O painel frontal tem discos e um botão de alimentação.

    - Há 10 ranhuras de disco na parte da frente do seu dispositivo.
    - A ranhura 0 tem uma unidade SATA de 240 GB utilizada como disco de sistema operativo. A ranhura 1 está vazia e as ranhuras 2 a 9 são SSDs NVMe utilizados como discos de dados.
- O avião de volta inclui unidades de alimentação redundantes (PSUs).
- O avião traseiro tem seis interfaces de rede:

    - Duas interfaces de 1-Gbps.
    - Quatro interfaces de 25 Gbps que também podem servir como interfaces de 10 Gbps.
    - Um controlador de gestão de rodapé (BMC).

- O avião traseiro tem dois cartões de rede correspondentes às 6 portas:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

Para obter uma lista completa de cabos, interruptores e transceptores suportados para estes cartões de rede, vá à [Matriz de Interoperabilidade da Série Cavium FastlinQ 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
 
Tome os seguintes passos para teleférico para a alimentação e rede.

1. Identifique as várias portas no plano traseiro do seu dispositivo.

    ![Plano traseiro de um dispositivo por cabo](./media/azure-stack-edge-deploy-install/backplane-cabled.png)

2. Localize as ranhuras do disco e o botão de alimentação na parte frontal do dispositivo.

    ![Avião frontal de um dispositivo](./media/azure-stack-edge-deploy-install/device-front-plane-labeled-1.png)

3. Ligue os cabos de alimentação a cada uma das PSUs no bastidor. Para garantir uma elevada disponibilidade, instale e ligue as PSUs a diferentes fontes de alimentação.
4. Ligue os cabos de alimentação às unidades de distribuição de energia (PDUs) em bastidor. Certifique-se de que as duas PSUs utilizam fontes de alimentação separadas.
5. Pressione o botão de alimentação para ligar o dispositivo.
6. Ligue a interface de rede de 1 GbE PORT 1 ao computador que é utilizado para configurar o dispositivo físico. A PORTA 1 é a interface de gestão dedicada.
7. Ligue uma ou mais das PORTAS 2, 3, 4, 5 ou 6 à rede/Internet do datacenter.

    - Se ligar a PORTA 2, utilize o cabo de rede RJ-45.
    - Para as interfaces de rede de 10/25 GbE, utilize os cabos de cobre SFP+.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu sobre tópicos Azure Stack Edge Pro, tais como:

> [!div class="checklist"]
> * Desembalar o dispositivo
> * Montar o dispositivo em bastidor
> * Ligar o dispositivo

Avance para o próximo tutorial para aprender a ligar, configurar e ativar o seu dispositivo.

> [!div class="nextstepaction"]
> [Conecte e instale o Azure Stack Edge Pro](./azure-stack-edge-deploy-connect-setup-activate.md)
