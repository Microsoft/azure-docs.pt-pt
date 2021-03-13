---
title: Visão geral do Microsoft Azure Stack Edge Pro R | Microsoft Docs
description: Descreve os dispositivos Azure Stack Edge Pro R, uma solução de armazenamento para aplicações militares que usa um dispositivo físico para transferência baseada em rede para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 2ff07995cab2da6e53600be9bf60b30d1896cc9d
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102664511"
---
# <a name="what-is-the-azure-stack-edge-pro-r"></a>O que é o Azure Stack Edge Pro R?

O Azure Stack Edge Pro R é um dispositivo de computação de borda robusto e concebido para ser utilizado em ambientes agressivos. O Azure Stack Edge Pro R é entregue como uma solução de hardware-as-a-service. A Microsoft envia-lhe um dispositivo gerido pela nuvem que funciona como porta de armazenamento de rede e tem uma Unidade de Processamento Gráfico incorporada (GPU) que permite inferição acelerada de IA.

Este artigo fornece-lhe uma visão geral da solução Azure Stack Edge Pro R, capacidades-chave e os cenários onde pode implementar este dispositivo.


## <a name="key-capabilities"></a>Principais capacidades

Azure Stack Edge Pro R tem as seguintes capacidades:

|Funcionalidade |Description  |
|---------|---------|
|Hardware acidentado| Hardware de classe de servidor acidentado projetado para ambientes agressivos. Dispositivo contido num caso de trânsito portátil. |
|Gerido em nuvem     |O dispositivo e o serviço são geridos através do portal Azure.|
|Cargas de trabalho de cálculo de borda   |Permite a análise, o processamento e a filtragem de dados. Suporta VMs e cargas de trabalho contentorizadas.|
|Inferencing acelerado da IA| Habilitado por um GPU Nvidia T4.|
|Acesso a dados     | Direcione o acesso aos dados de Blobs de Armazenamento do Azure e Ficheiros do Azure através de APIs da cloud para processamento adicional de dados na cloud. A cache local do dispositivo é utilizada para o acesso rápido de ficheiros mais utilizados recentemente.|
|Modo desligado| O dispositivo e o serviço podem ser geridos opcionalmente através do Azure Stack Hub. Implementar, executar, gerir aplicações em modo offline. <br> O modo desligado suporta cenários de carregamento offline.|
|Protocolos de transferência de ficheiros suportados     |Suporte para protocolos padrão SMB, NFS e REST para ingestão de dados. <br> Para obter mais informações sobre versões suportadas, aceda aos [requisitos do sistema Azure Stack Edge Pro R](azure-stack-edge-gpu-system-requirements.md).|
|Atualização de dados     | Capacidade de atualizar os ficheiros locais com a versão mais recente da cloud.|
|Encriptação dupla    | O uso de unidades auto-encriptadas fornece a primeira camada de encriptação. A VPN fornece a segunda camada de encriptação. Suporte bitLocker para encriptar dados localmente e proteger transferência de dados para cloud em *https* .|
|Estrangulamento de largura de banda| Acelerador para limitar o uso da largura de banda durante as horas de ponta.|

<!--|Scale out file server| Available as 1-node and 4-node cluster configurations|-->

## <a name="use-cases"></a>Casos de utilização

Aqui estão os vários cenários em que o Azure Stack Edge Pro R pode ser usado para a rápida aprendizagem automática (ML) inferenização na borda e dados de pré-processamento antes de enviá-lo para a Azure.

- **Inferência com Azure Machine Learning** - Com Azure Stack Edge Pro R, pode executar modelos ML para obter resultados rápidos que podem ser atuados antes de os dados serem enviados para a nuvem. O conjunto completo de dados pode opcionalmente ser transferido para continuar a treinar e melhorar os seus modelos ML. Para obter mais informações sobre como utilizar os modelos acelerados de hardware Azure ML no dispositivo Azure Stack Edge Pro R, consulte [os modelos acelerados de hardware Azure ML no Azure Stack Edge Pro R](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- **Dados de pré-processamento** - Transforme os dados antes de enviá-los para a Azure para criar um conjunto de dados mais accível. O pré-processamento pode ser utilizado para:

    - Agregar dados.
    - Modificar dados, por exemplo, para remover dados pessoais.
    - Subconjunto de dados para otimizar o armazenamento e largura de banda, ou para uma análise mais aprofundada.
    - Analisar e reagir a eventos de IoT.

- **Transfira dados através da rede para a Azure** - Use O Azure Stack Edge Pro R para transferir facilmente e rapidamente os dados para o Azure para permitir mais computação e análise ou para fins de arquivo.

## <a name="components"></a>Componentes

A solução Azure Stack Edge Pro R é composta por um recurso Azure Stack Edge Edge, um dispositivo físico robusto, robusto e um UI web local.

- **Dispositivo físico Azure Stack Edge Pro R** - Um dispositivo de computação e armazenamento de 1 nó contido num caso de trânsito acidentado. Está também disponível uma fonte de alimentação ininterrupta (UPS) opcional.

    ![O dispositivo Azure Stack Edge Pro R 1-node](media/azure-stack-edge-pro-r-overview/device-image-1.png)

- **Recurso Azure Stack Edge** – um recurso no portal Azure que permite gerir um dispositivo robusto, Azure Stack Edge Pro R a partir de uma interface web que pode aceder a partir de diferentes localizações geográficas. Utilize o recurso Azure Stack Edge para criar e gerir recursos, ver e gerir dispositivos e alertas e gerir partilhas.  

- **Azure Stack Edge Pro R web uI local UI** - Uma interface de utilizador local baseada no navegador no seu dispositivo Azure Stack Edge Pro R destinado principalmente à configuração inicial do dispositivo. Utilize o UI web local também para executar diagnósticos, desligar e reiniciar o dispositivo Azure Stack Edge Pro, ver registos de cópias e contactar o Microsoft Support para arquivar um pedido de serviço.


## <a name="region-availability"></a>Disponibilidade de região

Dispositivo físico Azure Stack Edge Pro R, recurso Azure e conta de armazenamento alvo para a qual transfere dados nem todos têm de estar na mesma região.

- **Disponibilidade de recursos** - Para uma lista de todas as regiões onde o recurso Azure Stack Edge está disponível, vá aos [produtos Azure disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) 

- **Disponibilidade do dispositivo** - Para uma lista de todos os países onde o dispositivo Azure Stack Edge Pro R está disponível, aceda à secção **Disponibilidade** no separador **Azure Stack Edge Pro R** para preços [Azure Stack Edge Pro R](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgeProR).

- **Contas de Armazenamento de Destino** - as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure. As regiões onde as contas de armazenamento armazenam os dados do Azure Stack Edge Pro R devem estar localizadas perto do local onde o dispositivo está localizado para um desempenho ótimo. Uma conta de armazenamento localizada longe do dispositivo resulta em latências longas e desempenho mais lento.

## <a name="next-steps"></a>Passos seguintes

- Reveja os requisitos do [sistema Azure Stack Edge Pro R](azure-stack-edge-gpu-system-requirements.md).
<!--- Understand the [Azure Stack Edge Pro R limits](azure-stack-edge-limits.md).-->