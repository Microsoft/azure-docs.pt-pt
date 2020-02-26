---
title: Limites de recursos para ficheiros Azure NetApp  Microsoft Docs
description: Descreve limites para os recursos do Azure NetApp Files e como solicitar o aumento do limite de recursos.
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
ms.date: 02/25/2020
ms.author: b-juche
ms.openlocfilehash: 7637d18017f5bdc76c8a271198a88f21a59a6aac
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604972"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limites de recurso para os Azure NetApp Files

Compreender os limites de recursos para os Ficheiros Azure NetApp ajuda-o a gerir os seus volumes.

## <a name="resource-limits"></a>Limites de recursos

O quadro seguinte descreve os limites de recursos para ficheiros Azure NetApp:

|  Recurso  |  Limite predefinido  |  Ajustável através de pedido de apoio  |
|----------------|---------------------|--------------------------------------|
|  Número de contas NetApp por região de Azure   |  10    |  Sim   |
|  Número de piscinas de capacidade por conta NetApp   |    25     |   Sim   |
|  Número de volumes por piscina de capacidade     |    500   |    Sim     |
|  Número de instantâneos por volume       |    255     |    Não        |
|  Número de subnets delegadas nos Ficheiros Azure NetApp (Microsoft.NetApp/volumes) por Rede Virtual Azure    |   1   |    Não    |
|  Número de IPs usados num VNet (incluindo VNets imediatamente espreitados) com Ficheiros Azure NetApp   |    1000   |    Sim   |
|  Tamanho mínimo de uma única piscina de capacidade   |  4 TiB     |    Não  |
|  Tamanho máximo de uma piscina de capacidade única    |  500 TiB   |   Não   |
|  Tamanho mínimo de um único volume    |    100 GiB    |    Não    |
|  Tamanho máximo de um único volume     |    100 Tib    |    Não    |
|  Número máximo de ficheiros[(maxfiles)](#maxfiles)por volume     |    100 milhões    |    Sim    |    
|  Tamanho máximo de um único ficheiro     |    16 Tib    |    Não    |    

## Limites de maxfiles<a name="maxfiles"></a> 

Os volumes de Ficheiros Azure NetApp têm um limite chamado *maxfiles*. O limite máximo de ficheiros é o número de ficheiros que um volume pode conter. O limite máximo de ficheiros máximos para um volume de Ficheiros Azure NetApp é indexado com base no tamanho (quota) do volume. O limite máximo de ficheiros máximos para um volume aumenta ou diminui à taxa de 20 milhões de ficheiros por TiB do tamanho do volume provisionado. 

O serviço ajusta dinamicamente o limite máximo de ficheiros máximos para um volume com base no seu tamanho provisionado. Por exemplo, um volume configurado inicialmente com um tamanho de 1 TiB teria um limite máximo de 20 milhões de ficheiros. Alterações subsequentes na dimensão do volume resultariam num reajustamento automático do limite máximo de ficheiros máximos com base nas seguintes regras: 

|    Tamanho do volume (quota)     |  Reajuste automático do limite de ficheiros maxfiles    |
|----------------------------|-------------------|
|    < 1 TiB                 |    20 milhões     |
|    >= 1 TiB mas < 2 TiB    |    40 milhões     |
|    >= 2 TiB mas < 3 TiB    |    60 milhões     |
|    >= 3 TiB mas < 4 TiB    |    80 milhões     |
|    >= 4 TiB                |    100 milhões    |

Para qualquer tamanho de volume, pode iniciar um pedido de [suporte](#limit_increase) para aumentar o limite de ficheiros máximos para além de 100 milhões.

## Aumento do limite de pedidos<a name="limit_increase"></a> 

Pode criar um pedido de apoio Azure para aumentar os limites ajustáveis da tabela acima. 

A partir do portal Azure, avião de navegação: 

1. Clique em **Ajuda + suporte**.
2. Clique **+ Novo pedido de suporte**.
3. No separador Basics, forneça as seguintes informações: 
    1. Tipo de problema: Selecione limites de **serviço e subscrição (quotas)** .
    2. Assinaturas: Selecione a subscrição do recurso de que necessita, a quota aumentada.
    3. Tipo de quota: Selecionar **armazenamento: Limites de ficheiros Azure NetApp**.
    4. Clique **em seguir: Soluções**.
4. No separador Detalhes:
    1. Na caixa Descrição, forneça as seguintes informações para o tipo de recurso correspondente:

        |  Recurso  |    Recursos parentais      |    Novos limites solicitados     |    Razão para o aumento das quotas       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Conta |  *ID de subscrição*   |  *Novo número máximo **de conta** solicitado*    |  *Que cenário ou caso de uso motivou o pedido?*  |
        |  Conjunto    |  *ID de assinatura, Conta URI*  |  *Solicitado novo número máximo **de piscina***   |  *Que cenário ou caso de uso motivou o pedido?*  |
        |  Volume  |  *ID de assinatura, Conta URI, Pool URI*   |  *Solicitado novo número máximo de **volume***     |  *Que cenário ou caso de uso motivou o pedido?*  |
        |  Maxfiles  |  *ID de assinatura, Conta URI, Pool URI, Volume URI*   |  *Novo número máximo de **maxfiles** solicitado*     |  *Que cenário ou caso de uso motivou o pedido?*  |    

    2. Especifique o método de suporte adequado e forneça as informações do seu contrato.

    3. Clique **em Seguir: Rever + criar** para criar o pedido. 


## <a name="next-steps"></a>Passos seguintes  

- [Compreender a hierarquia de armazenamento dos Ficheiros Azure NetApp](azure-netapp-files-understand-storage-hierarchy.md)
- [Modelo de custo para ficheiros Azure NetApp](azure-netapp-files-cost-model.md)
