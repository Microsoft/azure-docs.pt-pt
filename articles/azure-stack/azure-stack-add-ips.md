---
title: Adicionar endereços IP públicos no Azure Stack | Documentos da Microsoft
description: Saiba como adicionar mais endereços IP públicos ao Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: scottnap
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: cbfe4fcf30e9529f191f2a80de8ab83e7e0811f7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735903"
---
# <a name="add-public-ip-addresses"></a>Adicionar endereços IP públicos
*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*  

Saiba como adicionar mais endereços IP públicos ao Azure Stack.  Neste artigo, nos Referimos a endereços externos como endereços IP públicos, mas no Azure Stack serve para fazer referência à adição de blocos de endereços IP à sua rede externa.  Realmente não importam se essa rede externa é encaminhável de Internet pública ou está numa Intranet e utiliza o espaço de endereços privados para as finalidades deste artigo.  Os passos são os mesmos. 

## <a name="add-a-public-ip-address-pool"></a>Adicionar um conjunto de endereços IP públicos
Pode adicionar endereços IP públicos ao seu sistema do Azure Stack em qualquer altura após a implementação inicial do sistema do Azure Stack. Verificar como [consumo de endereços IP públicos de vista](azure-stack-viewing-public-ip-address-consumption.md) para ver a utilização atual e o endereço IP público que disponibilidade está no seu Azure Stack.

Num alto nível, o processo de adicionar um novo bloco de endereço IP público para o Azure Stack fica assim:

 ![Adicionar fluxo de IP](media/azure-stack-add-ips/flow.PNG)

## <a name="obtain-the-address-block-from-your-provider"></a>Obter o bloco de endereços do seu fornecedor
A primeira coisa que precisará fazer é obter o bloco de endereços que pretende adicionar ao Azure Stack.  Dependendo de onde obter o seu bloco de endereços do, terá de considerar o que o tempo de antecedência é e gerenciar isso em relação a taxa a que está a consumir os endereços IP públicos no Azure Stack.  

> [!IMPORTANT]
> O Azure Stack irá aceitar qualquer bloco de endereços que fornece, desde que ele é um bloco de endereços válido e não se sobreponha com um intervalo de endereços existente no Azure Stack.  Certifique-se de que obtém um bloco de endereços válido que seja encaminháveis e sem sobreposição com a rede externa à qual o Azure Stack está ligado.  Depois de adicionar o intervalo para o Azure Stack, não é possível removê-lo.

## <a name="add-the-ip-address-range-to-azure-stack"></a>Adicionar o intervalo de endereços IP para o Azure Stack

1. Num browser da Internet, navegue para o dashboard do portal de administração.  Neste exemplo, vamos utilizar https://adminportal.local.azurestack.external.  
2.  Inicie sessão no portal de administração do Azure Stack como um operador de cloud.
3.  No dashboard padrão – encontrar a lista de gestão da região e selecione a região que pretende gerir, para este exemplo, local.
4.  Encontre os fornecedores de recursos mosaico e clique no fornecedor de recursos de rede.
5.  Clique no IP público agrupamentos mosaico de utilização.
6.  Clique no botão de conjunto de IP de adicionar.
7.  Forneça um nome para o conjunto IP.  O nome que escolher é apenas para que possa identificar facilmente o conjunto IP para que pode chamá-lo se preferir.  É uma boa prática para tornar o nome, o mesmo que o intervalo de endereços, mas que não é necessária.
8.   Introduza o bloco de endereços que pretende adicionar na notação CIDR.  Por exemplo: 192.168.203.0/24
9.  Quando fornecer um intervalo CIDR válido no campo Endereço do intervalo (bloco CIDR) o endereço IP inicial, endereço IP final e campos de endereços IP disponíveis são preenchidos automaticamente.  Eles são só de leitura e gerados automaticamente, de modo que não é possível alterá-las sem modificar o valor no campo do intervalo de endereço.
10. Depois de rever as informações sobre o painel e confirmar tudo parece corrigir, clique em Ok para confirmar a alteração e adicionar o intervalo de endereços para o Azure Stack.


## <a name="next-steps"></a>Passos Seguintes 
[Ações de nó de unidade de dimensionamento de revisão](azure-stack-node-actions.md) 
