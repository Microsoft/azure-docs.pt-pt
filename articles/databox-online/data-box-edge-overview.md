---
title: Descrição geral do Microsoft Azure Data Box Edge | Microsoft Docs
description: Descreve Azure Data Box Edge, uma solução de armazenamento que usa um dispositivo físico para transferência baseada em rede para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 07/17/2019
ms.author: alkohli
ms.openlocfilehash: 69580f956b603423ef302353953a45ad5d00391e
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305428"
---
# <a name="what-is-azure-data-box-edge"></a>O que é o Azure Data Box Edge? 

O Azure Data Box Edge é um dispositivo de computação de ponta ativado por IA com funções de transferência de dados de rede. Este artigo fornece uma descrição geral da solução Data Box Edge, os benefícios, as principais funções e os cenários nos quais pode implementar este dispositivo. 

Data Box Edge é uma solução de hardware como serviço. A Microsoft envia a você um dispositivo gerenciado por nuvem com uma FPGA (matriz de porta programável) de campo interno que habilita o ia-inferência acelerado e tem todos os recursos de um gateway de armazenamento. 

## <a name="use-cases"></a>Casos de utilização

Aqui estão os vários cenários em que Data Box Edge pode ser usado para inferências rápidos de Machine Learning (ML) na borda e pré-processamento de dados antes de enviá-los para o Azure.

- **Inferência com Azure Machine Learning** com data Box Edge, você pode executar modelos de ml para obter resultados rápidos que podem ser afetados antes que os dados sejam enviados para a nuvem. O conjunto de dados completo pode, opcionalmente, ser transferido para continuar a treinar novamente e aprimorar seus modelos de ML. Para obter mais informações sobre como usar os modelos acelerados de hardware do Azure ML no dispositivo Data Box Edge, consulte [implantar modelos de hardware acelerado do Azure ml no data Box Edge](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Pré-processar** dados – Transforme dados antes de enviá-los para o Azure para criar um DataSet mais acionável. O pré-processamento pode ser utilizado para: 

    - Agregar dados.
    - Modifique os dados, por exemplo, para remover dados pessoais.
    - Subconjunto dados para otimizar o armazenamento e a largura de banda ou para análise posterior.
    - Analisar e reagir a eventos de IoT. 

- **Transferir dados através de rede para o Azure** - utilize o Data Box Edge para transferir dados fácil e rapidamente para o Azure, para permitir mais computação e análise ou para arquivo. 


## <a name="key-capabilities"></a>Principais capacidades

O Data Box Edge possui as seguintes funções:

|Funcionalidade |Descrição  |
|---------|---------|
|Inferência de ia acelerado| Habilitado pelo FPGA interno.|
|Computação       |Permite a análise, o processamento e a filtragem de dados.|
|Elevado desempenho | Computação de alto desempenho e transferências de dados.|
|Acesso a dados     | Direcione o acesso aos dados de Blobs de Armazenamento do Azure e Ficheiros do Azure através de APIs da cloud para processamento adicional de dados na cloud. O cache local no dispositivo é usado para acesso rápido aos arquivos usados mais recentemente.|
|Gerenciado na nuvem     |O dispositivo e o serviço são gerenciados por meio do portal do Azure.  |
|Carregamento offline     | O modo desligado suporta cenários de carregamento offline.|
|Protocolos suportados     | Suporte para protocolos SMB e NFS standard de ingestão de dados. <br> Para obter mais informações sobre as versões suportadas, aceda a [Requisitos de sistema do Data Box Edge](data-box-edge-system-requirements.md).|
|Atualização de dados     | Capacidade de atualizar os ficheiros locais com a versão mais recente da cloud.|
|Encriptação    | Suporte de BitLocker para encriptar localmente os dados e proteger a transferência de dados para a cloud através de *https*.|
|Limitação de largura de banda| Limitação para limitar o uso de largura de banda durante horários de pico.|


## <a name="components"></a>Componentes

A solução Data Box Edge inclui o recurso Data Box Edge, o dispositivo físico Data Box Edge e uma IU da Web local.

* **Dispositivo físico do Data Box Edge** - um servidor montado num rack de 1U fornecido pela Microsoft, que pode ser configurado para enviar dados para o Azure. 
    
* **Recurso Data Box Edge** – um recurso no portal do Azure que permite gerir um dispositivo Data Box Edge a partir de uma interface Web à qual pode aceder a partir de localizações geográficas diferentes. Utilize o recurso Data Box Edge para criar e gerir recursos, ver e gerir dispositivos e alertas, e gerir partilhas.  

    <!--![The Data Box Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Para obter mais informações, vá para [criar um pedido para seu dispositivo de data Box Edge](data-box-edge-deploy-prep.md#create-a-new-resource).

* **IU da Web local do Data Box** - utilize a IU da Web local para executar diagnósticos, encerrar e reiniciar o dispositivo Data Box Edge, ver registos de cópias e entrar em contacto com o Suporte da Microsoft para fazer um pedido de serviço.

    <!--![The Data Box Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Para obter informações sobre como utilizar a interface de utilizador baseada na Web, aceda a [Utilizar a interface de utilizador baseada na Web para administrar o Data Box](data-box-edge-manage-access-power-connectivity-mode.md).


## <a name="region-availability"></a>Disponibilidade de região

O dispositivo físico Data Box Edge, o recurso do Azure e a conta de armazenamento de destino para a qual transfere dados não têm de estar todos na mesma região.

- **Disponibilidade de recursos** -para obter uma lista de todas as regiões em que o recurso Data Box Edge está disponível, vá para [produtos do Azure disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Data Box Edge também pode ser implantado na nuvem do Azure governamental. Para obter mais informações, consulte [o que é o Azure governamental?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).
    
- **Contas de Armazenamento de Destino** - as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure. As regiões nas quais as contas de armazenamento armazenam dados Data Box Edge devem estar localizadas perto de onde o dispositivo está localizado para obter o desempenho ideal. Uma conta de armazenamento localizada longe do dispositivo resulta em latências longas e desempenho mais lento. 


## <a name="next-steps"></a>Passos Seguintes

- Reveja os [requisitos de sistema do Data Box Edge](data-box-edge-system-requirements.md).
- Compreenda os [Limites do Data Box Edge](data-box-edge-limits.md).
- Implemente o [Azure Data Box Edge](data-box-edge-deploy-prep.md) no portal do Azure.




