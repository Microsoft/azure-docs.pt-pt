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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75772941"
---
Se se deparar com problemas ao realizar qualquer operação em galerias de imagens partilhadas, definições de imagens e versões de imagens, execute o comando de falha novamente no modo de depuração. O modo dedepuração é ativado passando o interruptor **de depuração** com CLI e o interruptor **-Debug** com PowerShell. Depois de localizar o erro, siga este documento para resolver os erros.


## <a name="unable-to-create-a-shared-image-gallery"></a>Unable to create a shared image gallery (Não é possível criar uma galeria de imagens partilhadas)

Causas possíveis:

*O nome da galeria é inválido.*

Os caracteres permitidos para o nome da Galeria são letras maiúsculas ou minúsculas, dígitos, pontos e períodos. O nome da galeria não pode conter traços. Mude o nome da galeria e tente de novo. 

*O nome da galeria não é exclusivo dentro da sua subscrição.*

Escolha outro nome da galeria e tente de novo.


## <a name="unable-to-create-an-image-definition"></a>Unable to create an image definition (Não é possível criar uma definição de imagem) 

Causas possíveis:

*o nome da definição de imagem é inválido.*

Os caracteres permitidos para a definição de imagem são letras maiúsculas ou minúsculas, dígitos, pontos, traços e períodos. Mude o nome da definição de imagem e tente novamente.

*As propriedades obrigatórias para criar uma definição de imagem não são povoadas.*

As propriedades como nome, editor, oferta, sku e tipo de SO são obrigatórias. Verifique se todas as propriedades estão sendo passadas.

Certifique-se de que o **OSType**, quer o Linux quer o Windows, da definição de imagem é o mesmo que a imagem gerida pela fonte que está a utilizar para criar a versão de imagem. 


## <a name="unable-to-create-an-image-version"></a>Unable to create an image version (Não é possível criar uma versão de imagem) 

Causas possíveis:

*O nome da versão de imagem é inválido.*

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion.MinorVersion.Patch*. Mude o nome da versão da imagem e tente novamente.

*A imagem gerida pela fonte a partir da qual a versão de imagem está a ser criada não é encontrada.* 

Verifique se a imagem de origem existe e está na mesma região que a versão de imagem.

*A imagem gerida não está a ser feita.*

Certifique-se de que o estado de provisionamento da imagem gerida pela fonte é **bem sucedido**.

*A lista da região-alvo não inclui a região-fonte.*

A lista da região-alvo deve incluir a região de origem da versão de imagem. Certifique-se de que incluiu a região de origem na lista de regiões-alvo para as quais pretende que o Azure reproduza a sua versão de imagem.

*Replicação a todas as regiões-alvo não concluídas.*

Utilize a bandeira **de ReplicationStatus --expandir** para verificar se a replicação a todas as regiões-alvo especificadas foi concluída. Caso contrário, espere até 6 horas para o trabalho estar concluído. Se falhar, volte a executar o comando para criar e replicar a versão de imagem. Se há muitas regiões-alvo para as quais a versão de imagem está a ser replicada, considere fazer a replicação por fases.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Unable to create a VM or a scale set (Não é possível criar uma VM ou um conjunto de dimensionamento) 

Causas possíveis:

*O utilizador que tenta criar um conjunto de escala de vm ou máquina virtual não tem o acesso lido à versão de imagem.*

Contacte o proprietário da subscrição e peça-lhes que dêem acesso à versão de imagem ou aos recursos parentais (como a galeria de imagem partilhada ou definição de imagem) através do Controlo de [Acesso baseado em Funções](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

*A versão da imagem não é encontrada.*

Verifique se a região em que está a tentar criar uma escala de VM ou máquina virtual está incluída na lista das regiões-alvo da versão de imagem. Se a região já está na lista das regiões-alvo, verifique se o trabalho de replicação foi concluído. Pode utilizar a bandeira **-ReplicationStatus** para verificar se a replicação a todas as regiões-alvo especificadas foi concluída. 

*A criação de conjunto de máquinas VM ou virtual demora muito tempo.*

Verifique se o **OSType** da versão de imagem a que está a tentar criar o conjunto de escala de máquina seleções VM ou virtual tem o mesmo **OSType** da imagem gerida pela fonte que utilizou para criar a versão de imagem. 

## <a name="unable-to-share-resources"></a>Unable to share resources (Não é possível partilhar recursos)

A partilha de recursos de galeria de imagem partilhada, definição de imagem e versão de imagem através de subscrições é ativada usando o Controlo de [Acesso baseado em Funções](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

## <a name="replication-is-slow"></a>A replicação é lenta.

Utilize a bandeira **de ReplicationStatus --expandir** para verificar se a replicação a todas as regiões-alvo especificadas foi concluída. Caso contrário, aguarde até 6 horas para o trabalho estar concluído. Se falhar, volte a acionar o comando para criar e replicar a versão de imagem. Se há muitas regiões-alvo para as quais a versão de imagem está a ser replicada, considere fazer a replicação por fases.

## <a name="azure-limits-and-quotas"></a>Limites e quotas do Azure 

[Os limites e quotas azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) aplicam-se a todos os recursos de galeria de imagem partilhada, definição de imagem e versão de imagem. Certifique-se de que está dentro dos limites para as suas subscrições. 



