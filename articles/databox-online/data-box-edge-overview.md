---
title: Visão geral do Microsoft Azure Stack Edge | Microsoft Docs
description: Descreve Azure Stack Edge, uma solução de armazenamento que usa um dispositivo físico para transferência baseada em rede para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 11/04/2019
ms.author: alkohli
ms.openlocfilehash: 38aa45a9fe9e182fa98756725a8b20d737929d23
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585383"
---
# <a name="what-is-azure-stack-edge"></a>O que é Azure Stack Edge? 

[!INCLUDE [data-box-edge-gateway-rename-note](../../includes/data-box-edge-gateway-rename-note.md)]

O Azure Stack Edge é um dispositivo de computação de borda habilitado para ia com recursos de transferência de dados de rede. Este artigo fornece uma visão geral da solução Azure Stack Edge, os benefícios, os principais recursos e os cenários em que você pode implantar esse dispositivo. 

O Azure Stack Edge é uma solução de hardware como serviço. A Microsoft envia a você um dispositivo gerenciado por nuvem com uma FPGA (matriz de porta programável) de campo interno que habilita o ia-inferência acelerado e tem todos os recursos de um gateway de armazenamento de rede. 

## <a name="use-cases"></a>Casos de utilização

Aqui estão os vários cenários em que Azure Stack borda pode ser usada para inferência de Machine Learning rápida (ML) na borda e pré-processamento de dados antes de enviá-los para o Azure.

- **Inferência com Azure Machine Learning** -com Azure Stack Edge, você pode executar modelos de ml para obter resultados rápidos que podem ser afetados antes que os dados sejam enviados para a nuvem. O conjunto de dados completo pode, opcionalmente, ser transferido para continuar a treinar novamente e aprimorar seus modelos de ML. Para obter mais informações sobre como usar os modelos acelerados de hardware do Azure ML no dispositivo Azure Stack Edge, consulte [implantar modelos de hardware acelerado do Azure ml no Azure Stack Edge](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Pré-processar** dados – Transforme dados antes de enviá-los para o Azure para criar um DataSet mais acionável. O pré-processamento pode ser utilizado para: 

    - Agregar dados.
    - Modifique os dados, por exemplo, para remover dados pessoais.
    - Subconjunto dados para otimizar o armazenamento e a largura de banda ou para análise posterior.
    - Analisar e reagir a eventos de IoT. 

- **Transferir dados pela rede para o Azure** -use Azure Stack Edge para transferir dados com facilidade e rapidez para o Azure para permitir a computação e análise posteriores ou para fins de arquivamento. 


## <a name="key-capabilities"></a>Principais capacidades

O Azure Stack Edge tem os seguintes recursos:

|Capacidade |Descrição  |
|---------|---------|
|Inferência de ia acelerado| Habilitado pelo FPGA interno.|
|Computação       |Permite a análise, o processamento e a filtragem de dados.|
|Elevado desempenho | Computação de alto desempenho e transferências de dados.|
|Acesso a dados     | Direcione o acesso aos dados de Blobs de Armazenamento do Azure e Ficheiros do Azure através de APIs da cloud para processamento adicional de dados na cloud. O cache local no dispositivo é usado para acesso rápido aos arquivos usados mais recentemente.|
|Gerenciado na nuvem     |O dispositivo e o serviço são gerenciados por meio do portal do Azure.  |
|Carregamento offline     | O modo desligado suporta cenários de carregamento offline.|
|Protocolos suportados     | Suporte para protocolos SMB e NFS standard de ingestão de dados. <br> Para obter mais informações sobre as versões com suporte, vá para [Azure Stack requisitos de sistema do Edge](data-box-edge-system-requirements.md).|
|Atualização de dados     | Capacidade de atualizar os ficheiros locais com a versão mais recente da cloud.|
|Encriptação    | Suporte de BitLocker para encriptar localmente os dados e proteger a transferência de dados para a cloud através de *https*.|
|Limitação de largura de banda| Limitação para limitar o uso de largura de banda durante horários de pico.|


## <a name="components"></a>Componentes

A solução de borda Azure Stack é composta de Azure Stack recurso de borda, Azure Stack dispositivo físico de borda e uma interface do usuário da Web local.

* **Dispositivo físico do Azure Stack Edge** – um servidor montado em rack 1U fornecido pela Microsoft que pode ser configurado para enviar dados para o Azure. 
    
* **Azure Stack recurso do Edge** – um recurso no portal do Azure que permite gerenciar um dispositivo de borda Azure Stack de uma interface da Web que você pode acessar de diferentes localizações geográficas. Use o Azure Stack recurso do Edge para criar e gerenciar recursos, exibir e gerenciar dispositivos e alertas e gerenciar compartilhamentos.  

    <!--![The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Para obter mais informações, vá para [criar um pedido para seu dispositivo Azure Stack Edge](data-box-edge-deploy-prep.md#create-a-new-resource).

* **Interface do usuário da Web local do Azure Stack Edge** -use a interface do usuário da Web local para executar o diagnóstico, desligar e reiniciar o dispositivo Azure Stack Edge, exibir logs de cópia e contatar suporte da Microsoft para arquivar uma solicitação de serviço.

    <!--![The Azure Stack Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Para obter informações sobre como usar a interface do usuário baseada na Web, acesse [usar a interface do usuário baseada na Web para administrar seu Azure Stack Edge](data-box-edge-manage-access-power-connectivity-mode.md).


## <a name="region-availability"></a>Disponibilidade de região

Azure Stack dispositivo físico do Edge, o recurso do Azure e a conta de armazenamento de destino para a qual você transfere os dados não precisam estar na mesma região.

- **Disponibilidade de recursos** -para obter uma lista de todas as regiões em que o recurso Azure Stack Edge está disponível, vá para [produtos do Azure disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). O Azure Stack Edge também pode ser implantado na nuvem do Azure governamental. Para obter mais informações, consulte [o que é o Azure governamental?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).
    
- **Contas de Armazenamento de Destino** - as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure. As regiões nas quais as contas de armazenamento armazenam Azure Stack dados de borda devem estar localizadas perto de onde o dispositivo está localizado para o desempenho ideal. Uma conta de armazenamento localizada longe do dispositivo resulta em latências longas e desempenho mais lento. 


## <a name="next-steps"></a>Passos seguintes

- Examine os [requisitos de sistema do Azure Stack Edge](data-box-edge-system-requirements.md).
- Entenda os [limites de borda de Azure Stack](data-box-edge-limits.md).
- Implante o [Azure Azure Stack Edge](data-box-edge-deploy-prep.md) no portal do Azure.




