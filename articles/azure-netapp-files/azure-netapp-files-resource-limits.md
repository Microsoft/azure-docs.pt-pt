---
title: Limites de recursos para Azure NetApp Files | Microsoft Docs
description: Descreve os limites para Azure NetApp Files recursos, incluindo limites para contas do NetApp, pools de capacidade, volumes, instantâneos e a sub-rede delegada.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: b9dd89e38f5cab6dceca36201695c068e0521943
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034862"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limites de recurso para os Azure NetApp Files

Entender os limites de recursos do Azure NetApp Files ajuda a gerenciar seus volumes.

## <a name="resource-limits"></a>Limites de recursos

A tabela a seguir descreve os limites de recursos para Azure NetApp Files:

|  Resource  |  Limite predefinido  |  Ajustável por meio de solicitação de suporte  |
|----------------|---------------------|--------------------------------------|
|  Número de contas do NetApp por assinatura do Azure   |  10    |  Sim   |
|  Número de pools de capacidade por conta do NetApp   |    25     |   Sim   |
|  Número de volumes por pool de capacidade     |    500   |    Sim     |
|  Número de instantâneos por volume       |    255     |    Não        |
|  Número de sub-redes delegadas a Azure NetApp Files (Microsoft. NetApp/volumes) por rede virtual do Azure    |   1   |    Não    |
|  Número máximo de IPs em uso em uma VNet (incluindo VNets emparelhados) com Azure NetApp Files    |    1000   |    Não   |
|  Tamanho mínimo de um pool de capacidade única   |  4 TiB     |    Não  |
|  Tamanho máximo de um pool de capacidade única    |  500 TiB   |   Não   |
|  Tamanho mínimo de um único volume    |    100 GiB    |    Não    |
|  Tamanho máximo de um único volume     |    100 TiB    |    Não       |
|  Número máximo de arquivos (inodes) por volume     |    50 milhões    |    Não    |    

## <a name="request-limit-increase"></a>Aumento do limite de solicitação 

Você pode criar uma solicitação de suporte do Azure para aumentar os limites ajustáveis da tabela acima. 

Do plano de navegação portal do Azure: 

1. Clique em **ajuda + suporte**.
2. Clique em **+ nova solicitação de suporte**.
3. Na guia noções básicas, forneça as seguintes informações: 
    1. Tipo de problema: Selecione **limites de serviço e assinatura (cotas)** .
    2. Subscrições: Selecione a assinatura para o recurso que você precisa que a cota tenha aumentado.
    3. Tipo de cota: Selecionar **armazenamento: Limites**de Azure NetApp files.
    4. Clique **em Avançar: Soluções**.
4. Na guia detalhes:
    1. Na caixa Descrição, forneça as seguintes informações para o tipo de recurso correspondente:

        |  Resource  |    Recursos pai      |    Novos limites solicitados     |    Motivo para aumento de cota       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Conta |  *ID da assinatura*   |  *Número de Nova **conta** máxima solicitado*    |  *Qual cenário ou caso de uso solicitou a solicitação?*  |
        |  Conjunto    |  *ID da assinatura, URI da conta*  |  *Novo número máximo de **pool** solicitado*   |  *Qual cenário ou caso de uso solicitou a solicitação?*  |
        |  Volume  |  *ID da assinatura, URI da conta, URI do pool*   |  *Novo número de **volume** máximo solicitado*     |  *Qual cenário ou caso de uso solicitou a solicitação?*  |

    2. Especifique o método de suporte apropriado e forneça suas informações de contrato.

    3. Clique **em Avançar: Examine + criar** para criar a solicitação. 


## <a name="next-steps"></a>Passos Seguintes  

- [Entender a hierarquia de armazenamento de Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Modelo de custo para Azure NetApp Files](azure-netapp-files-cost-model.md)
