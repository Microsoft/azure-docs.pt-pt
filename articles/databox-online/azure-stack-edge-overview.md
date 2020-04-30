---
title: Visão geral do Microsoft Azure Stack Edge [ Microsoft Docs
description: Descreve o Azure Stack Edge, uma solução de armazenamento que utiliza um dispositivo físico para transferência baseada em rede para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 11/04/2019
ms.author: alkohli
ms.openlocfilehash: c44d15e36135b8cf8c97484718d4600f8ba03521
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82571113"
---
# <a name="what-is-azure-stack-edge"></a>O que é o Azure Stack Edge?

[!INCLUDE [data-box-edge-gateway-rename-note](../../includes/data-box-edge-gateway-rename-note.md)]

O Azure Stack Edge é um dispositivo de computação de borda ativado por IA com capacidades de transferência de dados de rede. Este artigo fornece-lhe uma visão geral da solução Azure Stack Edge, benefícios, capacidades-chave e os cenários onde pode implementar este dispositivo.

Azure Stack Edge é uma solução de hardware-as-a-service. A Microsoft envia-lhe um dispositivo gerido pela nuvem com um Field Programmable Gate Array (FPGA) incorporado que permite a inferência acelerada da IA e tem todas as capacidades de um portal de armazenamento de rede. 

## <a name="use-cases"></a>Casos de utilização

Aqui estão os vários cenários onde o Azure Stack Edge pode ser usado para inferência rápida de Machine Learning (ML) na borda e dados de pré-processamento antes de enviá-lo para o Azure.

- **Inferência com Azure Machine Learning** - Com Azure Stack Edge, você pode executar modelos ML para obter resultados rápidos que podem ser agidos antes que os dados sejam enviados para a nuvem. O conjunto completo de dados pode ser transferido opcionalmente para continuar a treinar e melhorar os seus modelos ML. Para obter mais informações sobre como utilizar os modelos acelerados de hardware Azure ML no dispositivo Azure Stack Edge, consulte os [modelos acelerados de hardware Azure ML no Azure Stack Edge](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Pré-processar dados** - Transforme os dados antes de enviá-los para o Azure para criar um conjunto de dados mais acionável. O pré-processamento pode ser utilizado para: 

    - Agregar dados.
    - Modificar dados, por exemplo, para remover dados pessoais.
    - Subset dados para otimizar armazenamento e largura de banda, ou para uma análise mais aprofundada.
    - Analisar e reagir a eventos de IoT. 

- **Transfira dados através** da rede para o Azure - Utilize o Azure Stack Edge para transferir dados de forma fácil e rápida para o Azure para permitir mais computação e análise ou para fins de arquivo. 

## <a name="key-capabilities"></a>Principais capacidades

O Azure Stack Edge tem as seguintes capacidades:

|Capacidade |Descrição  |
|---------|---------|
|Inferência acelerada da IA| Habilitado pela FPGA incorporada.|
|Computação       |Permite a análise, o processamento e a filtragem de dados.|
|Elevado desempenho | Computação de alto desempenho e transferências de dados.|
|Acesso a dados     | Direcione o acesso aos dados de Blobs de Armazenamento do Azure e Ficheiros do Azure através de APIs da cloud para processamento adicional de dados na cloud. A cache local no dispositivo é utilizada para acesso rápido dos ficheiros mais recentemente utilizados.|
|Gerido em nuvem     |O dispositivo e o serviço são geridos através do portal Azure.  |
|Carregamento offline     | O modo desligado suporta cenários de carregamento offline.|
|Protocolos suportados     | Suporte para protocolos SMB e NFS standard de ingestão de dados. <br> Para obter mais informações sobre versões suportadas, consulte [os requisitos do sistema Azure Stack Edge](azure-stack-edge-system-requirements.md).|
|Atualização de dados     | Capacidade de atualizar os ficheiros locais com a versão mais recente da cloud.|
|Encriptação    | Suporte de BitLocker para encriptar localmente os dados e proteger a transferência de dados para a cloud através de *https*.|
|Estrangulamento da largura de banda| Acelerador para limitar o uso da largura de banda durante as horas de ponta.|
|ExpressRoute | Segurança adicional através da ExpressRoute. Utilize a configuração de observação onde o tráfego de dispositivos locais para os pontos finais de armazenamento em nuvem viaja sobre a Rota Expresso. Para obter mais informações, veja [Descrição geral do ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="components"></a>Componentes

A solução Azure Stack Edge é composta por recurso Azure Stack Edge, dispositivo físico Azure Stack Edge e uma UI web local.

* **Dispositivo físico Azure Stack Edge** - Um servidor montado em rack 1U fornecido pela Microsoft que pode ser configurado para enviar dados para o Azure.
    
* **Recurso Azure Stack Edge** – um recurso no portal Azure que lhe permite gerir um dispositivo Azure Stack Edge a partir de uma interface web a que pode aceder a partir de diferentes localizações geográficas. Utilize o recurso Azure Stack Edge para criar e gerir recursos, visualizar e gerir dispositivos e alertas e gerir ações.  

    <!--![The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Para mais informações, vá criar [uma encomenda para o seu dispositivo Azure Stack Edge](azure-stack-edge-deploy-prep.md#create-a-new-resource).

* **Azure Stack Edge web local UI** - Use o UI web local para executar diagnósticos, desligar e reiniciar o dispositivo Azure Stack Edge, ver registos de cópias e contactar o Microsoft Support para arquivar um pedido de serviço.

    <!--![The Azure Stack Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Para obter informações sobre a utilização do UI baseado na web, vá utilizar [o UI baseado na web para administrar o seu Azure Stack Edge](azure-stack-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Disponibilidade de região

O dispositivo físico Azure Stack Edge, o recurso Azure e a conta de armazenamento alvo para a qual transfere dados nem todos têm de estar na mesma região.

- **Disponibilidade de recursos** - Para uma lista de todas as regiões onde o recurso Azure Stack Edge está disponível, consulte [os produtos Azure disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) Azure Stack Edge também pode ser implantado na Nuvem do Governo Azure. Para mais informações, veja o que é o [Governo de Azure?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)
    
- **Contas de Armazenamento de Destino** - as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure. As regiões onde as contas de armazenamento armazenam dados do Azure Stack Edge devem estar localizadas perto do local onde o dispositivo está localizado para um desempenho ótimo. Uma conta de armazenamento localizada longe do dispositivo resulta em latências longas e desempenho mais lento.

## <a name="next-steps"></a>Passos seguintes

- Reveja os requisitos do [sistema Azure Stack Edge](azure-stack-edge-system-requirements.md).
- Compreenda os [limites da Borda da Pilha Azure.](azure-stack-edge-limits.md)
- Implemente o [Azure Stack Edge](azure-stack-edge-deploy-prep.md) no portal Azure.
