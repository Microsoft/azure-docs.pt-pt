---
title: Visão geral do Microsoft Azure Stack Edge Pro | Microsoft Docs
description: Descreve o Azure Stack Edge Pro, uma solução de armazenamento que utiliza um dispositivo físico para transferência baseada em rede para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/15/2021
ms.author: alkohli
ms.openlocfilehash: e6cd1f8a1f7d1777e786ab91637b4065a2c5e850
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585949"
---
# <a name="what-is-azure-stack-edge-pro-with-fpga"></a>O que é o Azure Stack Edge Pro com FPGA?

[!INCLUDE [Azure Stack Edge Pro FPGA end-of-life](../../includes/azure-stack-edge-fpga-eol.md)]

Azure Stack Edge Pro com FPGA é um dispositivo de computação de borda ativado por IA com capacidades de transferência de dados de rede. Este artigo fornece-lhe uma visão geral do Azure Stack Edge Pro com solução FPGA, benefícios, capacidades-chave e cenários de implementação.

Azure Stack Edge Pro com FPGA é uma solução de Hardware-as-a-service. A Microsoft envia-lhe um dispositivo gerido pela nuvem com um Field Programmable Gate Array (FPGA) incorporado que permite inferição acelerada de IA e tem todas as capacidades de um gateway de armazenamento de rede.

Azure Data Box Edge é remarcado como Azure Stack Edge.

## <a name="use-cases"></a>Casos de utilização

Aqui estão os vários cenários em que o Azure Stack Edge Pro pode ser usado para a rápida aprendizagem automática (ML) inferening on the edge and preprocessing data antes de enviá-lo para Azure.

- **Inferência com Azure Machine Learning** - Com O Azure Stack Edge Pro, pode executar modelos ML para obter resultados rápidos que podem ser atuados antes de os dados serem enviados para a nuvem. O conjunto completo de dados pode opcionalmente ser transferido para continuar a treinar e melhorar os seus modelos ML. Para obter mais informações sobre como utilizar os modelos acelerados de hardware Azure ML no dispositivo Azure Stack Edge Pro, consulte [os modelos acelerados de hardware Azure ML no Azure Stack Edge Pro](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- **Dados de pré-processamento** - Transforme os dados antes de enviá-los para a Azure para criar um conjunto de dados mais accível. O pré-processamento pode ser utilizado para: 

    - Agregar dados.
    - Modificar dados, por exemplo, para remover dados pessoais.
    - Subconjunto de dados para otimizar o armazenamento e largura de banda, ou para uma análise mais aprofundada.
    - Analisar e reagir a eventos de IoT. 

- **Transfira dados através da rede para a Azure** - Use o Azure Stack Edge Pro para transferir facilmente e rapidamente os dados para o Azure para permitir mais computação e análise ou para fins de arquivo. 

## <a name="key-capabilities"></a>Principais capacidades

Azure Stack Edge Pro tem as seguintes capacidades:

|Funcionalidade |Description  |
|---------|---------|
|Inferencing acelerado da IA| Habilitado pela FPGA incorporada.|
|Computação       |Permite a análise, o processamento e a filtragem de dados.|
|Elevado desempenho | Computação de alto desempenho e transferências de dados.|
|Acesso a dados     | Direcione o acesso aos dados de Blobs de Armazenamento do Azure e Ficheiros do Azure através de APIs da cloud para processamento adicional de dados na cloud. A cache local do dispositivo é utilizada para o acesso rápido de ficheiros mais utilizados recentemente.|
|Gerido em nuvem     |O dispositivo e o serviço são geridos através do portal Azure.  |
|Carregamento offline     | O modo desligado suporta cenários de carregamento offline.|
|Protocolos suportados     | Suporte para protocolos SMB e NFS standard de ingestão de dados. <br> Para obter mais informações sobre versões suportadas, consulte [os requisitos do sistema Azure Stack Edge Pro](azure-stack-edge-system-requirements.md).|
|Atualização de dados     | Capacidade de atualizar os ficheiros locais com a versão mais recente da cloud.|
|Encriptação    | Suporte de BitLocker para encriptar localmente os dados e proteger a transferência de dados para a cloud através de *https*.|
|Estrangulamento de largura de banda| Acelerador para limitar o uso da largura de banda durante as horas de ponta.|
|ExpressRoute | Segurança acrescida através do ExpressRoute. Utilize a configuração de peering onde o tráfego de dispositivos locais para os pontos finais de armazenamento de nuvem viaja sobre o ExpressRoute. Para obter mais informações, veja [Descrição geral do ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="components"></a>Componentes

A solução Azure Stack Edge Pro é composta por recurso Azure Stack Edge, dispositivo físico Azure Stack Edge Pro e uma UI web local.

* **Dispositivo físico Azure Stack Edge Pro**: Um servidor montado em rack 1U fornecido pela Microsoft que pode ser configurado para enviar dados para o Azure.
    
* **Recurso Azure Stack Edge**: Um recurso no portal Azure que permite gerir um dispositivo Azure Stack Edge Pro a partir de uma interface web que pode aceder a partir de diferentes localizações geográficas. Utilize o recurso Azure Stack Edge para criar e gerir recursos, gerir partilhas e ver e gerir dispositivos e alertas.
  
   <!--[The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

   À medida que o Azure Stack Edge Pro se aproxima do seu fim de vida, não estão a ser preenchidas encomendas para novos dispositivos Azure Stack Edge Pro. Se for um novo cliente, recomendamos que explore a utilização de dispositivos Azure Stack Edge Pro - GPU para as suas cargas de trabalho. Para mais informações, aceda ao [Que é Azure Stack Edge Pro com GPU](azure-stack-edge-gpu-overview.md). Para obter informações sobre a encomenda de um Azure Stack Edge Pro com dispositivo GPU, vá para [criar um novo recurso para Azure Stack Edge Pro - GPU](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).

   Se for um cliente existente, ainda pode criar um novo recurso Azure Stack Edge Pro se precisar de substituir ou redefinir o dispositivo Azure Stack Edge Pro. Para obter instruções, vá a [Criar um pedido para o seu dispositivo Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md#create-new-resource-for-existing-device).

* **Azure Stack Edge Pro web uI local** - Use o UI web local para executar diagnósticos, desligar e reiniciar o dispositivo Azure Stack Edge Pro, ver registos de cópias e contactar o Microsoft Support para arquivar um pedido de serviço.

    <!--![The Azure Stack Edge Pro local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Para obter informações sobre a utilização da UI baseada na web, vá ao [UI baseado na web para administrar o seu Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Disponibilidade de região

Dispositivo físico Azure Stack Edge Pro, recurso Azure e conta de armazenamento alvo para a qual transfere dados nem todos têm de estar na mesma região.

- **Disponibilidade de recursos** - Para uma lista de todas as regiões onde o recurso Azure Stack Edge está disponível, consulte [os produtos Azure disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) O Azure Stack Edge Pro também pode ser implantado na Nuvem do Governo de Azure. Para mais informações, veja [o que é o Governo Azure?](../azure-government/documentation-government-welcome.md)
    
- **Contas de Armazenamento de Destino** - as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure. As regiões onde as contas de armazenamento armazenam os dados do Azure Stack Edge Pro devem estar localizadas perto do local onde o dispositivo está localizado para um desempenho ótimo. Uma conta de armazenamento localizada longe do dispositivo resulta em latências longas e desempenho mais lento.

O serviço Azure Stack Edge é um serviço não regional. Para mais informações, consulte [Regiões e Zonas de Disponibilidade em Azure.](https://docs.microsoft.com/azure/availability-zones/az-overview) O serviço Azure Stack Edge não tem dependência de uma região específica de Azure, tornando-o resiliente a paragens em toda a zona e paragens em toda a região.

## <a name="next-steps"></a>Passos seguintes

- Reveja os requisitos do [sistema Azure Stack Edge Pro](azure-stack-edge-system-requirements.md).
- Compreenda os [limites Azure Stack Edge Pro](azure-stack-edge-limits.md).
- Implementar [Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md) no portal Azure.
