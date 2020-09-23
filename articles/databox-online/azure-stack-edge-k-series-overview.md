---
title: Visão geral do Azure Stack Edge Mini R Microsoft Docs
description: Descreve o Azure Stack Edge Mini R, uma solução de armazenamento para aplicações militares que usa um dispositivo físico portátil com uma bateria para transferir sobre wi-fi para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 20efae411ae4d2fae9bf3b5e69dbfdd98da1603a
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985637"
---
# <a name="what-is-the-azure-stack-edge-mini-r"></a>O que é o Azure Stack Edge Mini R?

O Azure Stack Edge Mini R é uma solução de hardware-as-a-service. A Microsoft envia-lhe um dispositivo robusto, gerido em nuvem, ultra portátil com uma Unidade de Processamento de Visão incorporada (VPU) que permite inferição acelerada de IA e tem todas as capacidades de um gateway de armazenamento de rede. Estes dispositivos são adequados para serem utilizados nos ambientes mais severos e são otimizados para AI, análise e computação sem servidores.

Este artigo fornece-lhe uma visão geral da solução Azure Stack Edge Mini R, capacidades-chave e os cenários onde pode implementar este dispositivo.


## <a name="key-capabilities"></a>Principais capacidades

O Azure Stack Edge Mini R tem as seguintes capacidades:

|Funcionalidade |Descrição  |
|---------|---------|
|Hardware acidentado| Hardware robusto projetado para ambientes mais severos.|
|Ultra portátil| Ultra portátil, fator de forma acionado por bateria.|
|Gerido em nuvem|O dispositivo e o serviço são geridos através do portal Azure.|
|Cargas de trabalho de cálculo de borda|Permite a análise, o processamento e a filtragem de dados.<br>Suporta VMs e cargas de trabalho contentorizadas. |
|Inferencing acelerado da IA| Habilitado pela Intel Movidius Myriad X VPU.|
|Com fios e sem fios | Permite transferências de dados com fios e sem fios.|
|Acesso a dados     | Direcione o acesso aos dados de Blobs de Armazenamento do Azure e Ficheiros do Azure através de APIs da cloud para processamento adicional de dados na cloud. A cache local do dispositivo é utilizada para o acesso rápido de ficheiros mais utilizados recentemente.|
|Modo desligado|  O dispositivo e o serviço podem ser geridos opcionalmente através do Azure Stack Hub. Implementar, executar, gerir aplicações em modo offline. <br> O modo desligado suporta cenários de carregamento offline.|
|Protocolos suportados     |Suporta protocolos padrão de SMB, NFS e REST para a ingestão de dados. <br> Para obter mais informações sobre versões suportadas, aceda aos [requisitos do sistema Azure Stack Edge Mini R](azure-stack-edge-gpu-system-requirements.md).|
|Atualização de dados     | Capacidade de atualizar os ficheiros locais com a versão mais recente da cloud.|
|Encriptação dupla    | O uso de unidade auto-encriptada fornece a primeira camada de encriptação. A VPN fornece a segunda camada de encriptação. Suporte bitLocker para encriptar dados localmente e proteger transferência de dados para cloud em *https* .|
|Estrangulamento de largura de banda| Acelerador para limitar o uso da largura de banda durante as horas de ponta.|

## <a name="use-cases"></a>Casos de utilização

Aqui estão os vários cenários em que o Azure Stack Edge Mini R pode ser usado para a rápida aprendizagem automática (ML) inferenização na borda e dados de pré-processamento antes de enviá-lo para a Azure.

- **Inferência com Azure Machine Learning** - Com O Azure Stack Edge Mini R, pode executar modelos ML para obter resultados rápidos que podem ser atuados antes de os dados serem enviados para a nuvem. O conjunto completo de dados pode opcionalmente ser transferido para continuar a treinar e melhorar os seus modelos ML. Para obter mais informações sobre como utilizar os modelos acelerados de hardware Azure ML no dispositivo Azure Stack Edge Mini R, consulte [os modelos acelerados de hardware Azure ML no Azure Stack Edge Mini R](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Dados de pré-processamento** - Transforme os dados através de opções de computação, tais como contentores ou máquinas virtuais, antes de os enviar para a Azure para criar um conjunto de dados mais aactivo. O pré-processamento pode ser utilizado para:

    - Agregar dados.
    - Modificar dados, por exemplo, para remover dados pessoais.
    - Subconjunto de dados para otimizar o armazenamento e largura de banda, ou para uma análise mais aprofundada.
    - Analisar e reagir a eventos de IoT.

- **Transfira dados através da rede para a Azure** - Use O Azure Stack Edge Mini R para transferir facilmente e rapidamente os dados para o Azure para permitir mais computação e análise ou para fins de arquivo.

## <a name="components"></a>Componentes

A solução Azure Stack Edge Mini R é composta por um recurso Azure Stack Edge Edge, um dispositivo físico robusto e ultra portátil da Azure Stack Edge e um UI web local.

* **Dispositivo físico Azure Stack Edge Mini R** - Um dispositivo ultra portátil com uma bateria a bordo e uma envolvente robusta fornecida pela Microsoft que pode ser configurada para enviar dados para o Azure. O aparelho com a bateria pesa menos de 7 libras.

    ![Dispositivo Azure Stack Edge Mini R](media/azure-stack-edge-k-series-overview/perspective-view-1.png)

* **Recurso Azure Stack Edge** – um recurso no portal Azure que permite gerir um dispositivo robusto, Azure Stack Edge Mini R a partir de uma interface web que pode aceder a partir de diferentes localizações geográficas. Utilize o recurso Azure Stack Edge para criar e gerir recursos, ver e gerir dispositivos e alertas e gerir partilhas.  

* **Azure Stack Edge Mini R web uI local** - Utilize o UI web local para configuração inicial do dispositivo, para executar diagnósticos, desligar e reiniciar o dispositivo Azure Stack Edge Mini R, ver registos de cópias e contactar o Microsoft Support para arquivar um pedido de serviço.


## <a name="region-availability"></a>Disponibilidade de região

Dispositivo físico Azure Stack Edge Mini R, recurso Azure e conta de armazenamento alvo para a qual transfere dados nem todos têm de estar na mesma região.

- **Disponibilidade de recursos** - Para uma lista de todas as regiões onde o recurso Azure Stack Edge está disponível, vá aos [produtos Azure disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) 

- **Contas de Armazenamento de Destino** - as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure. As regiões onde as contas de armazenamento armazenam os dados Azure Stack Edge Mini R devem estar localizadas perto do local onde o dispositivo está localizado para um desempenho ótimo. Uma conta de armazenamento localizada longe do dispositivo resulta em latências longas e desempenho mais lento.


## <a name="next-steps"></a>Passos seguintes

- Reveja os requisitos do [sistema Azure Stack Edge Mini R](azure-stack-edge-gpu-system-requirements.md).


