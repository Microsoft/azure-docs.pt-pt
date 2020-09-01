---
title: Visão geral do Microsoft Azure Stack Edge Microsoft Docs
description: Descreve a Azure Stack Edge, uma solução de armazenamento que utiliza um dispositivo físico para transferência baseada em rede para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: e3caf4e0e60b8ef9fb0b30b599b22e0f07d8998c
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89088418"
---
# <a name="what-is-azure-stack-edge-with-gpu-preview"></a>O que é Azure Stack Edge com GPU (Pré-visualização)?

Azure Stack Edge com GPU é um dispositivo de computação de borda ativado por IA com capacidades de transferência de dados de rede. Este artigo fornece-lhe uma visão geral da solução Azure Stack Edge, benefícios, capacidades-chave e os cenários onde pode implementar este dispositivo.

Azure Stack Edge com GPU é uma solução de hardware-as-a-service. A Microsoft envia-lhe um dispositivo gerido pela nuvem que funciona como porta de armazenamento de rede e tem uma Unidade de Processamento Gráfico incorporada (GPU) que permite inferição acelerada de IA. 

> [!IMPORTANT]
> Azure Stack Edge com GPU está atualmente em pré-visualização. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="use-cases"></a>Casos de utilização

Aqui estão os vários cenários em que o Azure Stack Edge pode ser usado para a rápida aprendizagem automática (ML) inferenculando na borda e dados de pré-processamento antes de enviá-lo para Azure.

- **Inferência com Azure Machine Learning** - Com Azure Stack Edge, pode executar modelos ML para obter resultados rápidos que podem ser atuados antes de os dados serem enviados para a nuvem. O conjunto completo de dados pode opcionalmente ser transferido para continuar a treinar e melhorar os seus modelos ML. Para obter mais informações sobre como utilizar os modelos acelerados de hardware Azure ML no dispositivo Azure Stack Edge, consulte [os modelos acelerados de hardware Azure ML no Azure Stack Edge](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Dados de pré-processamento** - Transforme os dados antes de enviá-los para a Azure para criar um conjunto de dados mais accível. O pré-processamento pode ser utilizado para: 

    - Agregar dados.
    - Modificar dados, por exemplo, para remover dados pessoais.
    - Subconjunto de dados para otimizar o armazenamento e largura de banda, ou para uma análise mais aprofundada.
    - Analisar e reagir a eventos de IoT. 

- **Transfira dados através da rede para a Azure** - Use o Azure Stack Edge para transferir facilmente e rapidamente os dados para o Azure para permitir mais computação e análise ou para fins de arquivo. 

## <a name="key-capabilities"></a>Principais capacidades

Azure Stack Edge tem as seguintes capacidades:

|Funcionalidade |Descrição  |
|---------|---------|
|Inferencing acelerado da IA| Ativada pela GPU incorporada (uma ou duas dependendo do modelo).|
|Computação edge      |Permite a análise, o processamento e a filtragem de dados. Suporta clusters VMs e Kubernetes.|
|Elevado desempenho | Computação de alto desempenho e transferências de dados.|
|Acesso a dados     | Direcione o acesso aos dados de Blobs de Armazenamento do Azure e Ficheiros do Azure através de APIs da cloud para processamento adicional de dados na cloud. A cache local do dispositivo é utilizada para o acesso rápido de ficheiros mais utilizados recentemente.|
|Gerido em nuvem     |O dispositivo e o serviço são geridos através do portal Azure.  |
|Carregamento offline     | O modo desligado suporta cenários de carregamento offline.|
|Protocolos suportados     | Suporte para protocolos padrão SMB, NFS e REST para ingestão de dados. <br> Para obter mais informações sobre versões suportadas, consulte [os requisitos do sistema Azure Stack Edge](azure-stack-edge-system-requirements.md).|
|Atualização de dados     | Capacidade de atualizar os ficheiros locais com a versão mais recente da cloud.|
|Encriptação    | Suporte de BitLocker para encriptar localmente os dados e proteger a transferência de dados para a cloud através de *https*.|
|Estrangulamento de largura de banda| Acelerador para limitar o uso da largura de banda durante as horas de ponta.|
|ExpressRoute | Segurança acrescida através do ExpressRoute. Utilize a configuração de peering onde o tráfego de dispositivos locais para os pontos finais de armazenamento de nuvem viaja sobre o ExpressRoute. Para obter mais informações, veja [Descrição geral do ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="components"></a>Componentes

A solução Azure Stack Edge é composta por recurso Azure Stack Edge, dispositivo físico Azure Stack Edge e uma UI web local.

* **Dispositivo físico Azure Stack Edge** - um servidor montado em rack 1U fornecido pela Microsoft que pode ser configurado para enviar dados para o Azure.
    
* **Recurso Azure Stack Edge** – um recurso no portal Azure que permite gerir um dispositivo Azure Stack Edge a partir de uma interface web que pode aceder a partir de diferentes localizações geográficas. Utilize o recurso Azure Stack Edge para criar e gerir recursos, ver e gerir dispositivos e alertas e gerir partilhas.  

    Para mais informações, aceda a [Criar um pedido para o seu dispositivo Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).

* **Azure Stack Edge web uI local** - Use o UI web local para executar diagnósticos, desligar e reiniciar o dispositivo Azure Stack Edge, ver registos de cópias e contactar o Microsoft Support para arquivar um pedido de serviço.

    Para obter informações sobre a utilização da UI baseada na web, vá ao [UI baseado na web para administrar o seu Azure Stack Edge](azure-stack-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Disponibilidade de região

O dispositivo físico Azure Stack Edge, o recurso Azure e a conta de armazenamento alvo para a qual transfere dados nem todos têm de estar na mesma região.

- **Disponibilidade de recursos** - Para esta versão de pré-visualização, o recurso está disponível nas regiões leste dos EUA, DaE e do Sudeste Asiático.
    
- **Contas de Armazenamento de Destino** - as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure. As regiões onde as contas de armazenamento armazenam os dados do Azure Stack Edge devem estar localizadas perto do local onde o dispositivo está localizado para um desempenho ótimo. Uma conta de armazenamento localizada longe do dispositivo resulta em latências longas e desempenho mais lento.

## <a name="next-steps"></a>Passos seguintes

- Reveja os requisitos do [sistema Azure Stack Edge](azure-stack-edge-gpu-system-requirements.md).
- Compreenda os limites da Borda da [Pilha de Azure](azure-stack-edge-limits.md).
- Implementar [Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md) no portal Azure.
