---
title: Problemas de resolução de problemas com imagens partilhadas em Azure
description: Aprenda a resolver problemas com galerias de imagens partilhadas.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 06/15/2020
ms.author: cynthn
ms.reviewer: cynthn
ms.openlocfilehash: 3a206a7aabee9f75524ab4715afa30ec05c612bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328068"
---
# <a name="troubleshooting-shared-image-galleries-in-azure"></a>Resolução de problemas partilhadas galerias de imagens em Azure

Se se deparar com problemas ao realizar qualquer operação em galerias de imagens partilhadas, definições de imagens e versões de imagens, execute o comando de falha novamente no modo de depuração. O modo Debug é ativado passando o `--debug` interruptor com CLI e o `-Debug` interruptor com PowerShell. Assim que localizar o erro, siga este documento para resolver os erros.


## <a name="unable-to-create-a-shared-image-gallery"></a>Unable to create a shared image gallery (Não é possível criar uma galeria de imagens partilhadas)

Causas possíveis:

*O nome da galeria é inválido.*

Os caracteres permitidos para o nome da Galeria são letras maiúsculas ou minúsculas, dígitos, pontos e períodos. O nome da galeria não pode conter traços. Mude o nome da galeria e tente novamente. 

*O nome da galeria não é único dentro da sua subscrição.*

Escolha outro nome de galeria e tente de novo.


## <a name="unable-to-create-an-image-definition"></a>Unable to create an image definition (Não é possível criar uma definição de imagem) 

Causas possíveis:

*o nome da definição de imagem é inválido.*

Os caracteres permitidos para a definição de imagem são letras maiúsculas ou minúsculas, dígitos, pontos, traços e períodos. Mude o nome da definição de imagem e tente novamente.

*As propriedades obrigatórias para a criação de uma definição de imagem não são povoadas.*

As propriedades como nome, editor, oferta, sku e tipo de SO são obrigatórias. Verifique se todas as propriedades estão sendo passadas.

Certifique-se de que o **OSType**, linux ou Windows, da definição de imagem é a mesma que está a utilizar para criar a versão de imagem. 


## <a name="unable-to-create-an-image-version"></a>Unable to create an image version (Não é possível criar uma versão de imagem) 

Causas possíveis:

*O nome da versão de imagem é inválido.*

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion.MinorVersion.Patch*. Mude o nome da versão de imagem e tente novamente.

*Não se encontra a imagem gerida por origem a partir da qual a versão de imagem está a ser criada.* 

Verifique se a imagem de origem existe e está na mesma região que a versão de imagem.

*A imagem gerida não é feita sendo a provisionada.*

Certifique-se de que o estado de provisionamento da imagem gerida pela fonte é **bem sucedido**.

*A lista da região-alvo não inclui a região de origem.*

A lista da região-alvo deve incluir a região de origem da versão de imagem. Certifique-se de que incluiu a região de origem na lista de regiões-alvo onde pretende que a Azure replique a sua versão de imagem.

*Replicação a todas as regiões-alvo não concluídas.*

Utilize a bandeira **replicationStatus** de expansão para verificar se a replicação de todas as regiões-alvo especificadas foi concluída. Caso contrário, espere até 6 horas para o trabalho estar concluído. Se falhar, volte a executar o comando para criar e replicar a versão de imagem. Se existem muitas regiões-alvo a que a versão de imagem está a ser replicada, considere fazer a replicação por fases.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Unable to create a VM or a scale set (Não é possível criar uma VM ou um conjunto de dimensionamento) 

Causas possíveis:

*O utilizador que tenta criar um conjunto de escala de VM ou de máquina virtual não tem acesso à versão de imagem.*

Contacte o proprietário da subscrição e peça-lhes que dêem acesso à versão de imagem ou aos recursos-mãe (como a galeria de imagens partilhada ou a definição de imagem) através do [controlo de acesso baseado em funções Azure (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles). 

*A versão de imagem não é encontrada.*

Verifique se a região em que está a tentar criar uma escala de VM ou máquina virtual está incluída na lista de regiões-alvo da versão de imagem. Se a região já se encontra na lista das regiões-alvo, verifique se o trabalho de replicação foi concluído. Pode utilizar a bandeira **-ReplicationStatus** para verificar se a replicação de todas as regiões-alvo especificadas foi concluída. 

*A criação de conjunto de escala de VM ou de máquina virtual demora muito tempo.*

Verifique se o **OSType** da versão de imagem que está a tentar criar o VM ou a escala de máquina virtual definido tem o mesmo **OSType** da fonte que usou para criar a versão de imagem. 

## <a name="unable-to-share-resources"></a>Unable to share resources (Não é possível partilhar recursos)

A partilha de recursos de galeria de imagens partilhadas, definição de imagem e versão de imagem através de subscrições é ativada através do [controlo de acesso baseado em funções Azure (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles). 

## <a name="replication-is-slow"></a>A replicação é lenta.

Utilize a bandeira **replicationStatus** de expansão para verificar se a replicação de todas as regiões-alvo especificadas foi concluída. Caso contrário, espere até 6 horas para o trabalho estar concluído. Se falhar, desencadeie novamente o comando para criar e replicar a versão de imagem. Se existem muitas regiões-alvo a que a versão de imagem está a ser replicada, considere fazer a replicação por fases.

## <a name="azure-limits-and-quotas"></a>Limites e quotas do Azure 

[Limites e quotas azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) aplicam-se a todos os recursos de galeria de imagens partilhadas, definição de imagem e versão de imagem. Certifique-se de que está dentro dos limites das suas subscrições. 


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [galerias de imagens partilhadas.](./linux/shared-image-galleries.md)
