---
title: Tutorial para instalar O dispositivo físico Azure Stack Edge Mini R | Microsoft Docs
description: O segundo tutorial sobre a instalação do dispositivo Azure Stack Edge Mini R envolve como cabo o dispositivo físico para a energia e rede.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 8b154dabd6f672c6fdaf77c5f8d48f80fb40d5d8
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060113"
---
# <a name="tutorial-install-azure-stack-edge-mini-r"></a>Tutorial: Instalar Azure Stack Edge Mini R

Este tutorial descreve como instalar um dispositivo físico Azure Stack Edge Mini R. O procedimento de instalação envolve a cablagem do aparelho.

A instalação pode demorar cerca de 30 minutos a ser concluída.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Inspecione o dispositivo
> * Ligar o dispositivo

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para a instalação de um dispositivo físico da seguinte forma:

### <a name="for-the-azure-stack-edge-resource"></a>Para o recurso Azure Stack Edge

Antes de começar, certifique-se de que:

* Completou todos os passos em [Prepare-se para implantar o Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-prep.md).
    * Criou um recurso Azure Stack Edge para implementar o seu dispositivo.
    * Gerou a chave de ativação para ativar o seu dispositivo com o recurso Azure Stack Edge.

 
### <a name="for-the-azure-stack-edge-mini-r-physical-device"></a>Para o dispositivo físico Azure Stack Edge Mini R

Antes de implementar um dispositivo:

- Certifique-se de que o aparelho assenta em segurança numa superfície de trabalho plana, estável e nivelada.
- Verifique se o local onde quer configurar o dispositivo tem:
    - Energia padrão ac de uma fonte independente -OR-
    - Uma unidade de distribuição de energia do rack (PDU). 
    

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter

Antes de começar:

- Reveja os requisitos de networking para a implementação do Azure Stack Edge Mini R e configuure a rede do datacenter de acordo com os requisitos. Para obter mais informações, consulte [os requisitos de networking Azure Stack Edge](azure-stack-edge-mini-r-system-requirements.md#networking-port-requirements).

- Certifique-se de que a largura de banda mínima da Internet é de 20 Mbps para o melhor funcionamento do dispositivo. <!-- engg TBC -->


## <a name="inspect-the-device"></a>Inspecione o dispositivo

Este dispositivo é enviado como uma única unidade. Conclua os passos seguintes para desembalar o dispositivo.

1. Coloque a caixa numa superfície plana e uniforme.
2. Inspecione a caixa do aparelho para verificar se há danos. Abra a caixa e inspecione o dispositivo. Se a caixa ou o dispositivo parecer estar danificado, contacte o Microsoft Support para o ajudar a avaliar se o dispositivo está em bom estado de funcionamento.
3. Depois de aberto o caso, certifique-se de que tem:
    - Um dispositivo portátil Azure Stack Edge Mini R com para-choques laterais ligados
    - Uma bateria e a contracapa ligada ao aparelho. 
    - Um cabo de alimentação para ligar a bateria à fonte de energia 

Se não recebeu todos os itens listados aqui, contacte o suporte do Azure Stack Edge. O próximo passo é a televisão por cabo do seu dispositivo.


## <a name="cable-the-device"></a>Ligar o dispositivo

Os seguintes procedimentos explicam como cabo o seu dispositivo Azure Stack Edge para energia e rede.

Antes de começar a ligar o seu dispositivo, precisa do seguinte:

- O seu dispositivo físico Azure Stack Edge Mini R no local de instalação.
- Um cabo de alimentação.
- Pelo menos um cabo de rede de 1 GbE RJ-45 para ligar à interface de gestão. Existem duas interfaces de rede de 1 GbE, uma de gestão e uma de dados, no dispositivo.
- Um cabo de cobre SFP+ de 10 GbE para que cada interface de rede de dados seja configurado. Pelo menos uma interface de rede de dados entre os PORT 3 ou PORT 4 tem de ser ligada à Internet (com conectividade com o Azure).  
- Acesso a uma unidade de distribuição de energia (recomendada).

> [!NOTE]
> - Se estiver a ligar apenas uma interface de rede de dados, recomendamos que utilize uma interface de rede de 10 GbE, como PORT 3 ou PORT 4, para enviar dados para o Azure. 
> - Para obter um melhor desempenho e lidar com grandes volumes de dados, considere ligar todas as portas de dados.
> - O dispositivo Azure Stack Edge deve ser ligado à rede do datacenter para que possa ingerir dados a partir de servidores de fontes de dados. <!-- engg TBC -->

No seu dispositivo Azure Stack Edge:

- O painel frontal tem um porta-aviões SSD. 

    - O dispositivo tem 1 disco SSD na ranhura. 
    - O dispositivo também tem um cartão CFx que serve de armazenamento para o disco do sistema operativo.
    
- O painel frontal tem interfaces de rede e acesso a Wi-Fi.

    - 2 X 1 Interfaces de rede GbE RJ 45. Estes são PORT 1 e PORT 2 na UI local do dispositivo.
    - 2 2 interfaces de rede GbE SFP+ de 2 X 10 GbE. Estes são OS PORT 3 e PORT 4 na UI local do dispositivo. 
    - Um Wi-Fi porto com um Wi-Fi transmissor ligado a ele.

- O painel frontal também tem um botão de alimentação. 

- O painel traseiro inclui uma bateria e uma tampa que estão instaladas no aparelho. 


Tome os seguintes passos para teleférico para a alimentação e rede.

1. Identifique os vários componentes de rede e armazenamento no plano frontal do seu dispositivo.

    ![Interfaces de rede e armazenamento no dispositivo](./media/azure-stack-edge-mini-r-deploy-install/ports-front-plane.png)

2. Localize o botão de alimentação no canto inferior esquerdo da parte frontal do dispositivo. 

    ![Plano frontal de um dispositivo com botão de energia no dispositivo](./media/azure-stack-edge-mini-r-deploy-install/device-power-button.png)

3. A bateria está ligada ao plano traseiro do seu dispositivo. Identifique o segundo botão de alimentação localizado na bateria. 

    ![Plano frontal de um dispositivo com botão de energia na bateria](./media/azure-stack-edge-mini-r-deploy-install/battery-power-button.png)


    Ligue uma extremidade do cabo de alimentação à bateria e a outra à tomada de corrente. 

    ![Ligação do cabo de alimentação](./media/azure-stack-edge-mini-r-deploy-install/power-cord-connector-1.png) 

    Ao funcionar apenas com a bateria (a bateria não está ligada à fonte de energia), tanto o interruptor de alimentação na frente como o interruptor da bateria devem ser ligados à posição ON. Quando a bateria está ligada a uma fonte de alimentação, apenas o botão de alimentação na parte frontal do dispositivo deve ser ligado à posição ON. 

4. Pressione o botão de alimentação no plano dianteiro para ligar o dispositivo. 
    
    > [!NOTE]
    > Para ligar ou desligar a alimentação do aparelho, tem de premir o botão preto em cima do botão de alimentação e, em seguida, alternar o botão de alimentação para a posição ON ou OFF. 

5. Se configurar Wi-Fi neste dispositivo, utilize o seguinte diagrama de cablagem:

    ![Cablagem para Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)  

    - Ligue a interface de rede de 1 GbE PORT 1 ao computador que é utilizado para configurar o dispositivo físico. A PORTA 1 é a interface de gestão dedicada.


    Se utilizar uma configuração com fios para este dispositivo, utilize o seguinte diagrama:
     
    ![Cablagem para Wired](./media/azure-stack-edge-mini-r-deploy-install/wired-cabled.png)     
    - Ligue a interface de rede de 1 GbE PORT 1 ao computador que é utilizado para configurar o dispositivo físico. A PORTA 1 é a interface de gestão dedicada.
    - Ligue um ou mais de PORT 2, PORT 3 ou PORT 4 à rede de datacenter/Internet.
    
        - Se ligar a PORTA 2, utilize o cabo de rede RJ-45.
        - Para as interfaces de rede de 10 GbE, utilize os cabos de cobre SFP+.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu sobre tópicos Azure Stack Edge, tais como como:

> [!div class="checklist"]
> * Desembalar o dispositivo
> * Ligar o dispositivo

Avance para o próximo tutorial para aprender a ligar, configurar e ativar o seu dispositivo.

> [!div class="nextstepaction"]
> [Conecte e instale o Azure Stack Edge](./azure-stack-edge-mini-r-deploy-connect.md)
