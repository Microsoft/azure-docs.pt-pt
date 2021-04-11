---
title: Limites de recursos para ficheiros Azure NetApp | Microsoft Docs
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
ms.date: 03/30/2021
ms.author: b-juche
ms.openlocfilehash: 9b061184f97abeea79912aadbae2c2b188206c72
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058005"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limites de recurso para os Azure NetApp Files

Compreender os limites de recursos dos Ficheiros Azure NetApp ajuda-o a gerir os seus volumes.

## <a name="resource-limits"></a>Limites de recursos

A tabela que se segue descreve os limites de recursos para ficheiros Azure NetApp:

|  Recurso  |  Limite predefinido  |  Ajustável através de pedido de apoio  |
|----------------|---------------------|--------------------------------------|
|  Número de contas do NetApp por região de Azure por subscrição  |  10    |  Yes   |
|  Número de piscinas de capacidade por conta NetApp   |    25     |   Yes   |
|  Número de volumes por subscrição   |    500     |   Yes   |
|  Número de volumes por piscina de capacidade     |    500   |    Yes     |
|  Número de instantâneos por volume       |    255     |    No        |
|  Número de sub-redes delegadas em Ficheiros Azure NetApp (Microsoft.NetApp/volumes) por Rede Virtual Azure    |   1   |    No    |
|  Número de IPs usados num VNet (incluindo VNets imediatamente espreitados) com ficheiros Azure NetApp   |    1000   |    No   |
|  Tamanho mínimo de uma piscina de capacidade única   |  4 TiB     |    No  |
|  Tamanho máximo de uma piscina de capacidade única    |  500 TiB   |   No   |
|  Tamanho mínimo de um único volume    |    100 GiB    |    No    |
|  Tamanho máximo de um único volume     |    100 TiB    |    No    |
|  Tamanho máximo de um único ficheiro     |    16 TiB    |    No    |    
|  Tamanho máximo dos metadados do diretório num único diretório      |    320 MB    |    No    |    
|  Número máximo de ficheiros[(maxfiles)](#maxfiles)por volume     |    100 milhões    |    Yes    |    
|  Número máximo de regras de política de exportação por volume     |    5  |    No    | 
|  Produção mínima atribuída para um volume QoS manual     |    1 MiB/s   |    No    |    
|  Produção máxima atribuída para um volume QoS manual     |    4.500 MiB/s    |    No    |    
|  Número de volumes de proteção de dados de replicação entre regiões (volumes de destino)     |    5    |    Yes    |     

Para ver se um diretório está a aproximar-se do limite máximo de tamanho para metadados de diretório (320 MB), ver [como determino se um diretório se aproxima do tamanho limite](azure-netapp-files-faqs.md#how-do-i-determine-if-a-directory-is-approaching-the-limit-size).   

Para mais informações, consulte [as PERGUNTAS Frequentes de Gestão de Capacidade.](azure-netapp-files-faqs.md#capacity-management-faqs)

## <a name="maxfiles-limits"></a>Limites máximos <a name="maxfiles"></a> 

Os volumes de ficheiros Azure NetApp têm um limite chamado *maxfiles*. O limite máximo é o número de ficheiros que um volume pode conter. O limite máximo para um volume de Ficheiros Azure NetApp é indexado com base no tamanho (quota) do volume. O limite máximo para um volume aumenta ou diminui à taxa de 20 milhões de ficheiros por TiB do tamanho do volume provisionado. 

O serviço ajusta dinamicamente o limite máximo para um volume baseado no seu tamanho a provisionado. Por exemplo, um volume configurado inicialmente com um tamanho de 1 TiB teria um limite máximo de 20 milhões. Alterações subsequentes ao tamanho do volume resultariam num reajustamento automático do limite máximo baseado nas seguintes regras: 

|    Tamanho do volume (quota)     |  Reajustamento automático do limite máximo    |
|----------------------------|-------------------|
|    <= 1 TiB                |    20 milhões     |
|    > 1 TiB mas <= 2 TiB    |    40 milhões     |
|    > 2 TiB mas <= 3 TiB    |    60 milhões     |
|    > 3 TiB mas <= 4 TiB    |    80 milhões     |
|    > 4 TiB                 |    100 milhões    |

Se já alocou pelo menos 4 TiB de quota para um volume, pode iniciar um pedido de [apoio](#limit_increase) para aumentar o limite máximo para além de 100 milhões. Por cada 100 milhões de ficheiros que aumenta (ou uma fração dos mesmos), é necessário aumentar a quota de volume correspondente em 4 TiB.  Por exemplo, se aumentar o limite máximo de 100 milhões de ficheiros para 200 milhões de ficheiros (ou qualquer número no meio), precisa de aumentar a quota de volume de 4 TiB para 8 TiB.

## <a name="request-limit-increase"></a>Aumento do limite de pedido <a name="limit_increase"></a> 

Pode criar um pedido de suporte Azure para aumentar os limites ajustáveis da tabela acima. 

Do plano de navegação do portal Azure: 

1. Clique **em Ajuda + suporte.**
2. Clique **+ Novo pedido de suporte.**
3. No separador Básicos, forneça as seguintes informações: 
    1. Tipo de emissão: Selecione **o serviço e os limites de subscrição (quotas)**.
    2. Subscrições: Selecione a subscrição do recurso de que necessita do contingente aumentado.
    3. Tipo de quota: Selecione **Armazenamento: Limites de ficheiros Azure NetApp**.
    4. Clique **em seguida: Soluções**.
4. No separador Detalhes:
    1. Na caixa Descrição, forneça as seguintes informações para o tipo de recurso correspondente:

        |  Recurso  |    Recursos-mãe      |    Novos limites solicitados     |    Motivo para o aumento de quota       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Conta |  *ID da Subscrição*   |  *Novo número de **conta** máxima solicitado*    |  *Que cenário ou caso de utilização motivou o pedido?*  |
        |  Conjunto    |  *ID de subscrição, conta NetApp URI*  |  *Solicitado novo número máximo **de piscina***   |  *Que cenário ou caso de utilização motivou o pedido?*  |
        |  Volume  |  *ID de subscrição, conta NetApp URI, pool de capacidade URI*   |  *Novo número máximo de **volume** solicitado*     |  *Que cenário ou caso de utilização motivou o pedido?*  |
        |  Maxfiles  |  *ID de subscrição, conta NetApp URI, pool de capacidade URI, volume URI*   |  *Pedido novo número máximo **de maxfiles***     |  *Que cenário ou caso de utilização motivou o pedido?*  |    
        |  Volumes de proteção de dados de replicação entre regiões  |  *ID de subscrição, conta NetApp de destino URI, pool de capacidade de destino URI, conta uri fonte NetApp, pool de capacidade de origem URI, volume de origem URI*   |  *Solicitou novo número máximo de **volumes de proteção de dados de replicação entre regiões (volumes de destino)** _     |  _What cenário ou caso de utilização motivou o pedido?*  |    

    2. Especifique o método de suporte adequado e forneça as informações do seu contrato.

    3. Clique **em Seguinte: Rever + criar** para criar o pedido. 


## <a name="next-steps"></a>Passos seguintes  

- [Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Modelo de custo para os Azure NetApp Files](azure-netapp-files-cost-model.md)
