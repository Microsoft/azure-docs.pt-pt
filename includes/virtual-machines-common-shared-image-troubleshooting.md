---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 40ba5a935e78cd75c4fcd7729e44f1cdf6c2859b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772941"
---
Se se deparar com problemas ao realizar qualquer operação em galerias de imagens partilhadas, definições de imagens e versões de imagens, execute o comando de falha novamente no modo de depuração. O modo de depuração é ativado passando a opção **-debug** com a CLI e a opção **-debug** com o PowerShell. Depois de localizar o erro, siga este documento para solucionar os erros.


## <a name="unable-to-create-a-shared-image-gallery"></a>Não é possível criar uma galeria de imagens compartilhada

Causas possíveis:

*O nome da galeria é inválido.*

Os caracteres permitidos para o nome da galeria são letras maiúsculas ou minúsculas, dígitos, pontos e pontos. O nome da Galeria não pode conter traços. Altere o nome da galeria e tente novamente. 

*O nome da Galeria não é exclusivo na sua assinatura.*

Escolha outro nome de galeria e tente novamente.


## <a name="unable-to-create-an-image-definition"></a>Não é possível criar uma definição de imagem 

Causas possíveis:

*o nome da definição da imagem é inválido.*

Os caracteres permitidos para a definição de imagem são letras maiúsculas ou minúsculas, dígitos, pontos, traços e pontos. Altere o nome da definição da imagem e tente novamente.

*As propriedades obrigatórias para a criação de uma definição de imagem não são populadas.*

As propriedades como nome, editor, oferta, SKU e tipo de so são obrigatórias. Verifique se todas as propriedades estão sendo passadas.

Verifique se o **OSType**, Linux ou Windows, da definição da imagem é igual à imagem gerenciada de origem que você está usando para criar a versão da imagem. 


## <a name="unable-to-create-an-image-version"></a>Não é possível criar uma versão de imagem 

Causas possíveis:

*O nome da versão da imagem é inválido.*

Os caracteres permitidos para a versão da imagem são números e pontos. Os números devem estar dentro do intervalo de um inteiro de 32 bits. Formato: *MajorVersion. MinorVersion. patch*. Altere o nome da versão da imagem e tente novamente.

*A imagem gerenciada de origem da qual a versão da imagem está sendo criada não foi encontrada.* 

Verifique se a imagem de origem existe e se está na mesma região que a versão da imagem.

*A imagem gerenciada não está sendo provisionada.*

Verifique se o estado de provisionamento da imagem gerenciada de origem foi **bem-sucedido**.

*A lista região de destino não inclui a região de origem.*

A lista região de destino deve incluir a região de origem da versão da imagem. Verifique se você incluiu a região de origem na lista de regiões de destino em que você deseja que o Azure replique a versão da imagem.

*A replicação para todas as regiões de destino não foi concluída.*

Use o sinalizador **--Expand ReplicationStatus** para verificar se a replicação para todas as regiões de destino especificadas foi concluída. Caso contrário, aguarde até 6 horas para que o trabalho seja concluído. Se ele falhar, execute o comando novamente para criar e replicar a versão da imagem. Se houver muitas regiões de destino nas quais a versão da imagem está sendo replicada, considere fazer a replicação em fases.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Não é possível criar uma VM ou um conjunto de dimensionamento 

Causas possíveis:

*O usuário que está tentando criar uma VM ou um conjunto de dimensionamento de máquinas virtuais não tem o acesso de leitura para a versão da imagem.*

Entre em contato com o proprietário da assinatura e peça para conceder acesso de leitura à versão da imagem ou aos recursos pai (como a Galeria de imagens compartilhada ou definição de imagem) por meio do RBAC ( [controle de acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) ). 

*A versão da imagem não foi encontrada.*

Verifique se a região em que você está tentando criar uma VM ou uma escala de máquina virtual está incluída na lista de regiões de destino da versão da imagem. Se a região já estiver na lista de regiões de destino, verifique se o trabalho de replicação foi concluído. Você pode usar o sinalizador **-ReplicationStatus** para verificar se a replicação para todas as regiões de destino especificadas foi concluída. 

*A criação da VM ou do conjunto de dimensionamento de máquinas virtuais leva muito tempo.*

Verifique se o **OSType** da versão da imagem da qual você está tentando criar a VM ou o conjunto de dimensionamento de máquinas virtuais tem o mesmo **OSType** da imagem gerenciada de origem que você usou para criar a versão da imagem. 

## <a name="unable-to-share-resources"></a>Não é possível compartilhar recursos

O compartilhamento de galeria de imagens compartilhada, definição de imagem e recursos de versão de imagem entre assinaturas é habilitado usando o RBAC ( [controle de acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) ). 

## <a name="replication-is-slow"></a>A replicação está lenta

Use o sinalizador **--Expand ReplicationStatus** para verificar se a replicação para todas as regiões de destino especificadas foi concluída. Caso contrário, aguarde até 6 horas para que o trabalho seja concluído. Se falhar, acione o comando novamente para criar e replicar a versão da imagem. Se houver muitas regiões de destino nas quais a versão da imagem está sendo replicada, considere fazer a replicação em fases.

## <a name="azure-limits-and-quotas"></a>Limites e quotas do Azure 

Os [limites e as cotas do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) se aplicam a todos os recursos da Galeria de imagens, da definição de imagem e da imagem compartilhada. Verifique se você está dentro dos limites de suas assinaturas. 



