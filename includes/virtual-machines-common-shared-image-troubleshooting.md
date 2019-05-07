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
ms.openlocfilehash: 1476830313296615591a69a2cadd04bcc56b22bc
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149643"
---
Se se deparar com problemas ao realizar qualquer operação em galerias de imagens partilhadas, definições de imagens e versões de imagens, execute o comando de falha novamente no modo de depuração. Modo de depuração está ativado, passando a **-debug** mudar com a CLI e o **-depurar** mudar com o PowerShell. Depois de ter de localizar o erro, siga este documento para resolver os erros.


## <a name="unable-to-create-a-shared-image-gallery"></a>Não é possível criar uma galeria de imagem partilhada

Causas possíveis:

*O nome de galeria é inválido.*

Carateres permitidos para o nome de galeria são letras em maiúsculas ou minúsculas, dígitos, pontos e períodos. O nome de Galeria não pode conter traços. Altere o nome de galeria e tente novamente. 

*O nome de Galeria não é exclusivo na sua subscrição.*

Escolha outro nome de galeria e tente novamente.


## <a name="unable-to-create-an-image-definition"></a>Não é possível criar uma definição de imagem 

Causas possíveis:

*nome de definição de imagem é inválido.*

Carateres permitidos para a definição de imagem são letras em maiúsculas ou minúsculas, dígitos, pontos, travessões e períodos. Altere o nome da definição de imagem e tente novamente.

*As propriedades obrigatórias para criar uma definição de imagem não são preenchidas.*

As propriedades, tais como o nome, publicador, oferta, sku e tipo de SO são obrigatórias. Certifique-se de que se todas as propriedades estão sendo passadas.

Certifique-se de que o **OSType**, Linux ou Windows, da definição de imagem são o mesmo como gerido de origem de imagem que está a utilizar para criar a versão da imagem. 


## <a name="unable-to-create-an-image-version"></a>Não é possível criar uma versão de imagem 

Causas possíveis:

*Nome da versão de imagem é inválido.*

Carateres permitidos para a versão da imagem são números e pontos finais. Tem de ser números dentro do intervalo de um número inteiro de 32 bits. Formato: *MajorVersion.MinorVersion.Patch*. Altere o nome da versão de imagem e tente novamente.

*Imagem gerida de origem do qual está a ser criada a versão da imagem não foi encontrada.* 

Verifique se a imagem de origem existe e se está na mesma região que a versão da imagem.

*A imagem gerida não é feita a ser aprovisionada.*

Certifique-se o estado de aprovisionamento da imagem gerida de origem está **bem-sucedido**.

*A lista de região de destino não inclui a região de origem.*

A lista de região de destino tem de incluir a região de origem da versão de imagem. Certifique-se de que tenha incluído a região de origem na lista de regiões de destino onde pretende que o Azure para replicar a sua versão de imagem para.

*Replicação para todas as regiões de destino não foi concluída.*

Utilize o **, expanda ReplicationStatus** sinalizador para verificar se foi concluída a replicação para todas as regiões de destino especificado. Caso contrário, aguarde a conclusão da tarefa até seis horas. Se falhar, execute o comando novamente para criar e replicar a versão da imagem. Se existirem muitas regiões de destino replicada para a versão da imagem, optar por fazer a replicação em fases.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Não é possível criar uma VM ou um dimensionamento definido 

Causas possíveis:

*O utilizador tentar criar um conjunto de dimensionamento VM ou numa máquina virtual não tem o acesso de leitura para a versão da imagem.*

Contacte o proprietário da subscrição e peça-lhe para dar acesso de leitura para a versão da imagem ou o principal de recursos (como a Galeria de imagem partilhada ou a definição de imagem) por meio [controlo de acesso baseado em funções](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

*A versão da imagem não foi encontrada.*

Certifique-se de que a região que está a tentar criar um dimensionamento VM ou numa máquina virtual no está incluída na lista de regiões de destino da versão de imagem. Se já estiver a região na lista de regiões de destino, em seguida, verifique se a tarefa de replicação foi concluída. Pode utilizar o **- ReplicationStatus** sinalizador para verificar se foi concluída a replicação para todas as regiões de destino especificado. 

*A máquina virtual VM ou conjunto de dimensionamento criação demora muito tempo.*

Certifique-se de que o **OSType** da imagem de versão que está a tentar criar a VM ou do conjunto de dimensionamento de máquina virtual de tem o mesmo **OSType** da origem de imagem que utilizou para criar a versão da imagem de geridos. 

## <a name="unable-to-share-resources"></a>Não é possível partilhar recursos

A partilha de recursos da versão de imagem, definição de imagem e Galeria de imagem partilhada entre subscrições é habilitada com [controlo de acesso baseado em funções](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

## <a name="replication-is-slow"></a>A replicação está lenta

Utilize o **, expanda ReplicationStatus** sinalizador para verificar se foi concluída a replicação para todas as regiões de destino especificado. Caso contrário, aguarde a conclusão da tarefa até seis horas. Se falhar, dispara o comando novamente para criar e replicar a versão da imagem. Se existirem muitas regiões de destino replicada para a versão da imagem, optar por fazer a replicação em fases.

## <a name="azure-limits-and-quotas"></a>Limites e quotas do Azure 

[Quotas e limites do Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits) aplicam-se a todos os Galeria de imagem partilhada, definição de imagem e recursos da versão de imagem. Certifique-se de que está dentro dos limites para as suas subscrições. 



