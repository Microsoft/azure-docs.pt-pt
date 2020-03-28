---
title: Tutorial para instalar - Desembalar, rack, cabo Azure Data Box Edge dispositivo físico [ Microsoft Docs
description: O segundo tutorial sobre a instalação do Azure Data Box Edge envolve como desembalar, grelhar e cabo o dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/17/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: fe74db34e62a80935954c6cfc2e591d49a84b0b7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76263953"
---
# <a name="tutorial-install-azure-data-box-edge"></a>Tutorial: Instale borda de caixa de dados azure

Este tutorial descreve como instalar um dispositivo físico do Data Box Edge. O procedimento de instalação envolve desembalar, montar o rack e cabor o dispositivo. 

A instalação pode demorar cerca de duas horas a ser concluída.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Desembalar o dispositivo
> * Montagem do dispositivo
> * Ligar o dispositivo

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para a instalação de um dispositivo físico da seguinte forma:

### <a name="for-the-data-box-edge-resource"></a>Para o recurso do Data Box Edge

Antes de começar, certifique-se de que:

* Completou todos os passos em [Prepare-se para implementar o Azure Data Box Edge](data-box-edge-deploy-prep.md).
    * Criou um recurso Data Box Edge para implementar o seu dispositivo.
    * Gerou a chave de ativação para ativar o dispositivo com o recurso do Data Box Edge.

 
### <a name="for-the-data-box-edge-physical-device"></a>Para o dispositivo físico do Data Box Edge

Antes de implementar um dispositivo:

- Certifique-se de que o dispositivo assenta em segurança sobre uma superfície de trabalho plana, estável e nivelada.
- Verifique se o local onde quer configurar o dispositivo tem:
    - Potência AC padrão de uma fonte independente

        -OU-
    - Uma unidade de distribuição de energia de rack (PDU) com uma fonte de alimentação ininterrupta (UPS)
    - Uma ranhura de 1U disponível na prateleira na qual pretende montar o dispositivo

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter

Antes de começar:

- Reveja os requisitos de networking para a implementação do Data Box Edge e configure a rede datacenter de acordo com os requisitos. Para obter mais informações, veja [Requisitos de rede do Data Box Edge](data-box-edge-system-requirements.md#networking-port-requirements).

- Certifique-se de que a largura de banda mínima da Internet é de 20 Mbps para um ótimo funcionamento do dispositivo.


## <a name="unpack-the-device"></a>Desembalar o dispositivo

Este dispositivo é enviado numa única caixa. Conclua os passos seguintes para desembalar o dispositivo. 

1. Coloque a caixa numa superfície plana e uniforme.
2. Inspecione a caixa e a espuma de empacotamento para verificar se existem partes esmagadas, cortes, danos causados por água ou quaisquer outros danos óbvios. Se a caixa ou a embalagem estiverem severamente danificadas, não a abra. Contacte o Suporte da Microsoft para ajudar a avaliar se o dispositivo está funcional.
3. Desembale a caixa. Depois de desembalar a caixa, certifique-se de que tem:
    - Um único dispositivo de caixa de dados de caixa de dados
    - Dois cabos de alimentação
    - Um conjunto de kit ferroviário
    - Um folheto de Informação de Segurança, Ambiente e Regulação

Se não recebeu todos os itens listados aqui, contacte o suporte Data Box Edge. O próximo passo é montar o seu dispositivo.


## <a name="rack-the-device"></a>Montar o dispositivo em bastidor

O aparelho deve ser instalado numa prateleira padrão de 19 polegadas. Utilize o seguinte procedimento para montar o seu dispositivo numa prateleira padrão de 19 polegadas.

> [!IMPORTANT]
> Os dispositivos Data Box Edge têm de ser montados em bastidor para um funcionamento correto.


### <a name="prerequisites"></a>Pré-requisitos

- Antes de começar, leia as instruções de segurança no seu folheto de Informação De Segurança, Ambiente e Regulação. Este folheto foi enviado com o dispositivo.
- Comece a instalar os trilhos no espaço atribuído que está mais próximo do fundo do recinto do cesto.
- Para a configuração de montagem do trilho:
    -  Precisa fornecer oito parafusos: #10-32, #12-24, #M5 ou #M6. O diâmetro da cabeça dos parafusos deve ser inferior a 10 mm (0,4").
    -  Precisa de uma chave de fendas de ponta plana.

### <a name="identify-the-rail-kit-contents"></a>Identifique o conteúdo do kit ferroviário

Localize os componentes para a instalação do conjunto do kit ferroviário:
1. Dois conjuntos ferroviários deslizantes A7 Dell ReadyRails II
2. Duas correias de gancho e laço

    ![Identificar conteúdos de kits ferroviários](./media/data-box-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Instale e remova trilhos sem ferramentas (furos quadrados ou prateleiras redondas)

> [!TIP]
> Esta opção é sem ferramentas porque não requer ferramentas para instalar e remover os trilhos no quadrado ou nos orifícios redondos não roscadas nas prateleiras.

1. Posicione as peças laterais direitas esquerda e direita rotuladas **em frente** para dentro e oriente cada peça final para o assento nos orifícios do lado frontal das flanges verticais.
2. Alinhe cada peça final nos orifícios inferiores e superiores dos espaços U desejados.
3. Engatar a parte de trás do trilho até que se encaixe completamente na flange vertical da cremalheira e o trinco se encaixe no lugar. Repita estes passos para posicionar e coloque a peça frontal na flange vertical do cesto.
4. Para retirar os trilhos, puxe o botão de desbloqueio do trinco no ponto médio da peça final e desencaixe cada trilho.

    ![Instalar e remover trilhos sem ferramentas](./media/data-box-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Instalar e remover trilhos tooled (prateleiras de furos roscados)

> [!TIP]
> Esta opção é ferramenta porque requer uma ferramenta ( uma chave de_fendas de ponta plana_- para instalar e remover os trilhos nos orifícios redondos roscados nas prateleiras.

1. Retire os pinos dos suportes de montagem dianteiro e traseiro utilizando uma chave de fendas de ponta plana.
2. Puxe e rode os subconjuntos do trinco ferroviário para os retirar dos suportes de montagem.
3. Fixe os trilhos de montagem esquerdo e direita às flanges da cremalheira vertical dianteira utilizando dois pares de parafusos.
4. Deslize os suportes traseiros esquerdo e direito para a frente contra as flanges verticais traseiras e prenda-os utilizando dois pares de parafusos.

    ![Instalar e remover trilhos tooled](./media/data-box-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Instale o sistema numa prateleira

1. Puxe os trilhos de deslizamento interno para fora da prateleira até que se bloqueiem no lugar.
2. Localize o impasse do trilho traseiro em cada lado do sistema e baixe-os para as ranhuras J traseiras nos conjuntos de diapositivos. Rode o sistema para baixo até que todos os impasses ferroviários estejam sentados nas ranhuras J.
3. Empurre o sistema para dentro até que as alavancas de bloqueio clicem no lugar.
4. Pressione os botões de bloqueio de desbloqueio de diapositivos em ambos os trilhos e deslize o sistema para dentro da prateleira.

    ![Instalar o sistema numa prateleira](./media/data-box-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Retire o sistema da prateleira

1. Localize as alavancas de bloqueio nos lados dos trilhos internos.
2. Desbloqueie cada alavanca rodando-a até à sua posição de desbloqueio.
3. Segure os lados do sistema firmemente e puxe-o para a frente até que os impasses ferroviários estejam na frente das ranhuras J. Levante o sistema para cima e para longe da cremalheira e coloque-o sobre uma superfície nivelada.

    ![Remover o sistema da cremalheira](./media/data-box-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Engatar e soltar o trinco slam

> [!NOTE]
> Para sistemas não equipados com fechos de fecho, fixe o sistema utilizando parafusos, conforme descrito na etapa 3 deste procedimento.

1. Virado para a frente, localize o fecho de cada lado do sistema.
2. Os fechos atam-se automaticamente à medida que o sistema é empurrado para a cremalheira e são libertados puxando para cima nos fechos.
3. Para fixar o sistema de envio na cremalheira ou para outros ambientes instáveis, localize o parafuso de montagem em baixo de cada trinco e aperte cada parafuso com uma chave de fendas #2 Phillips.

    ![Engatar e soltar o trinco slam](./media/data-box-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>Ligar o dispositivo

Encaminhe os cabos e, em seguida, envie o seu dispositivo. Os seguintes procedimentos explicam como cabo o seu dispositivo Data Box Edge para energia e rede.

Antes de começar a ligar o seu dispositivo, precisa do seguinte:

- O dispositivo físico Data Box Edge, desembalado e montado na cremalheira.
- Dois cabos de alimentação.
- Pelo menos um cabo de rede de 1 GbE RJ-45 para ligar à interface de gestão. Existem duas interfaces de rede de 1 GbE, uma de gestão e uma de dados, no dispositivo.
- Um cabo de cobre de 25 GbE SFP+ para cada interface de rede de dados a ser configurada. Pelo menos uma interface de rede de dados entre a PORTA 2, A PORTA 3, a PORTA 4, a PORTA 5 ou a PORTA 6 tem de ser ligada à Internet (com conectividade com o Azure).  
- Acesso a duas unidades de distribuição de energia (recomendado).

> [!NOTE]
> - Se estiver a ligar apenas uma interface de rede de dados, recomendamos que utilize uma interface de rede 25/10-GbE, como port 3, PORT 4, PORT 5 ou PORT 6 para enviar dados para o Azure. 
> - Para obter um melhor desempenho e lidar com grandes volumes de dados, considere ligar todas as portas de dados.
> - O dispositivo Data Box Edge deve ser ligado à rede datacenter para que possa ingerir dados a partir de servidores de fonte de dados.

No seu dispositivo Data Box Edge:

- O painel frontal tem discos e um botão de alimentação.

    - Há 10 ranhuras de disco na parte da frente do seu dispositivo.
    - A ranhura 0 tem uma unidade SATA de 240 GB usada como um disco do sistema operativo. A ranhura 1 está vazia e as ranhuras 2 a 9 são SSDs NVMe usados como discos de dados.
- O plano traseiro inclui unidades de alimentação redundantes (PSUs).
- O avião traseiro tem seis interfaces de rede:

    - Duas interfaces de 1-Gbps.
    - Quatro interfaces de 25 gbps que também podem servir como interfaces de 10 Gbps.
    - Um controlador de gestão de quadros base (BMC).

- O avião traseiro tem dois cartões de rede correspondentes às 6 portas:

    - Qlogic Fastlinq 41264
    - Qlogic Fastlinq 41262

Para obter uma lista completa de cabos, interruptores e transceptores suportados para estes cartões de rede, vá à Matriz de [Interoperabilidade da Série Cavium FastlinQ 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
 
Tome os seguintes passos para ligar o seu dispositivo para obter energia e rede.

1. Identifique as várias portas no plano traseiro do seu dispositivo.

    ![Plano de volta de um dispositivo por cabo](./media/data-box-edge-deploy-install/backplane-cabled.png)

2. Localize as ranhuras do disco e o botão de alimentação na parte frontal do dispositivo.

    ![Plano frontal de um dispositivo](./media/data-box-edge-deploy-install/device-front-plane-labeled-1.png)

3. Ligue os cabos de alimentação a cada uma das PSUs no bastidor. Para garantir uma elevada disponibilidade, instale e ligue as PSUs a diferentes fontes de alimentação.
4. Ligue os cabos de alimentação às unidades de distribuição de energia (PDUs) em bastidor. Certifique-se de que as duas PSUs utilizam fontes de alimentação separadas.
5. Pressione o botão de alimentação para ligar o dispositivo.
6. Ligue a interface de rede 1-GbE PORT 1 ao computador utilizado para configurar o dispositivo físico. A PORTA 1 é a interface de gestão dedicada.
7. Ligue uma ou mais das PORTAS 2, 3, 4, 5 ou 6 à rede/Internet do datacenter.

    - Se ligar a PORTA 2, utilize o cabo de rede RJ-45.
    - Para as interfaces de rede 10/25-GbE, utilize os cabos de cobre SFP+.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre tópicos de Data Box Edge, tais como:

> [!div class="checklist"]
> * Desembalar o dispositivo
> * Montar o dispositivo em bastidor
> * Ligar o dispositivo

Avance para o próximo tutorial para aprender a ligar, configurar e ativar o seu dispositivo.

> [!div class="nextstepaction"]
> [Ligar e configurar data box edge](./data-box-edge-deploy-connect-setup-activate.md)
