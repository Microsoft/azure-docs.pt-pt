---
title: Tutorial para instalar dispositivo físico Azure Stack Edge Pro R Microsoft Docs
description: O segundo tutorial sobre a instalação do Azure Stack Edge Pro R envolve como cabo o dispositivo físico para a energia e rede.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/18/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Pro R in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: b67da2607d206424f69f53645eda148495ea58ec
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468722"
---
# <a name="tutorial-install-azure-stack-edge-pro-r"></a>Tutorial: Instalar Azure Stack Edge Pro R

Este tutorial descreve como instalar um dispositivo físico Azure Stack Edge Pro R. O procedimento de instalação envolve a cablagem do aparelho.

A instalação pode demorar cerca de 30 minutos a ser concluída.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Inspecione o dispositivo
> * Ligar o dispositivo

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para a instalação de um dispositivo físico da seguinte forma:

### <a name="for-the-azure-stack-edge-resource"></a>Para o recurso Azure Stack Edge

Antes de começar, certifique-se de que:

* Completou todos os passos em [Prepare-se para implementar Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md).
    * Criou um recurso Azure Stack Edge para implementar o seu dispositivo.
    * Gerou a chave de ativação para ativar o seu dispositivo com o recurso Azure Stack Edge.

 
### <a name="for-the-azure-stack-edge-pro-r-physical-device"></a>Para o dispositivo físico Azure Stack Edge Pro R

Antes de implementar um dispositivo:

- Certifique-se de que o aparelho assenta em segurança numa superfície de trabalho plana, estável e nivelada.
- Verifique se o local onde quer configurar o dispositivo tem:
    - Potência padrão AC de uma fonte independente

        -OU-
    - Uma unidade de distribuição de energia do rack (PDU). O dispositivo é enviado com uma fonte de alimentação ininterrupta (UPS)
    

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter

Antes de começar:

- Reveja os requisitos de networking para a implementação do Azure Stack Edge Pro R e configuure a rede do datacenter de acordo com os requisitos. Para obter mais informações, consulte [os requisitos de rede Azure Stack Edge Pro R](azure-stack-edge-pro-r-system-requirements.md#networking-port-requirements).

- Certifique-se de que a largura de banda mínima da Internet é de 20 Mbps para um melhor funcionamento do dispositivo.


## <a name="inspect-the-device"></a>Inspecione o dispositivo

Este dispositivo é enviado como uma única unidade. Conclua os passos seguintes para desembalar o dispositivo.

1. Coloque a caixa numa superfície plana e uniforme.
2. Inspecione a caixa do aparelho para verificar se há danos. Abra a caixa e inspecione o dispositivo. Se a caixa ou o dispositivo parecer estar danificado, contacte o Microsoft Support para o ajudar a avaliar se o dispositivo está em bom estado de funcionamento.
3. Depois de aberto o caso, certifique-se de que tem:
    - Um único invólucro Azure Stack Edge Pro R dispositivo
    - Uma fonte de alimentação ininterrupta (UPS)
    - 2 cabos de alimentação curtos para ligar o dispositivo à UPS
    - 1 cabo de alimentação para ligar UPS à fonte de alimentação

Se não recebeu todos os itens listados aqui, contacte o suporte Azure Stack Edge Pro R. O próximo passo é a televisão por cabo do seu dispositivo.


## <a name="cable-the-device"></a>Ligar o dispositivo

Os seguintes procedimentos explicam como cabo o seu dispositivo Azure Stack Edge Pro R para alimentação e rede.

Antes de começar a ligar o seu dispositivo, precisa do seguinte:

- O seu dispositivo físico Azure Stack Edge Pro R no local de instalação.
- Um cabo de alimentação.
- Pelo menos um cabo de rede de 1 GbE RJ-45 para ligar à interface de gestão. Existem duas interfaces de rede de 1 GbE, uma de gestão e uma de dados, no dispositivo.
- Um cabo de cobre SFP+ de 10/25-GbE para cada interface de rede de dados ser configurado. Pelo menos uma interface de rede de dados entre os PORT 3 ou PORT 4 tem de ser ligada à Internet (com conectividade com o Azure).  
- Acesso a uma unidade de distribuição de energia (recomendada).

> [!NOTE]
> - Se estiver a ligar apenas uma interface de rede de dados, recomendamos que utilize uma interface de rede de 25/10 GbE, como o PORT 3 ou o PORT 4, para enviar dados para o Azure. 
> - Para obter um melhor desempenho e lidar com grandes volumes de dados, considere ligar todas as portas de dados.
> - O dispositivo Azure Stack Edge Pro R deve ser ligado à rede do datacenter para que possa ingerir dados a partir de servidores de fontes de dados.

No seu dispositivo Azure Stack Edge Pro R:

- O painel frontal tem discos e um botão de alimentação.

    - Existem 8 ranhuras de disco na parte da frente do seu dispositivo.
    - O dispositivo também tem 2 discos SATA X M.2 no interior que servem como discos do sistema operativo. 

- O avião de volta inclui unidades de alimentação redundantes (PSUs).
- O avião traseiro tem quatro interfaces de rede:

    - Duas interfaces de 1-Gbps.
    - Duas interfaces de 25 Gbps que também podem servir como interfaces de 10 Gbps.
    - Um controlador de gestão de rodapé (BMC).

<!--- The back plane has two network cards corresponding to the 4 ports:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

For a full list of supported cables, switches, and transceivers for these network cards, go to [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).-->
 
Tome os seguintes passos para teleférico para a alimentação e rede.

1. Identifique as várias portas no plano traseiro do seu dispositivo.

    ![Plano traseiro de um dispositivo por cabo](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)

2. Localize as ranhuras do disco e o botão de alimentação na parte frontal do dispositivo.

    ![Avião frontal de um dispositivo](./media/azure-stack-edge-pro-r-deploy-install/device-front-plane-labeled-1.png)

3. Ligue uma extremidade do cabo de alimentação à UPS. Fixe a outra extremidade do cabo de alimentação à unidade de distribuição de energia do rack (PDUs). 
5. Pressione o botão de alimentação para ligar o dispositivo.
6. Ligue a interface de rede de 1 GbE PORT 1 ao computador que é utilizado para configurar o dispositivo físico. A PORTA 1 é a interface de gestão dedicada.
7. Ligue um ou mais de PORT 2, PORT 3 ou PORT 4 à rede de datacenter/Internet.

    - Se ligar a PORTA 2, utilize o cabo de rede RJ-45.
    - Para as interfaces de rede de 10/25 GbE, utilize os cabos de cobre SFP+.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu sobre tópicos Azure Stack Edge Pro R, tais como:

> [!div class="checklist"]
> * Desembalar o dispositivo
> * Ligar o dispositivo

Avance para o próximo tutorial para aprender a ligar-se ao seu dispositivo.

> [!div class="nextstepaction"]
> [Ligue-se ao Azure Stack Edge Pro R](./azure-stack-edge-pro-r-deploy-connect.md)
