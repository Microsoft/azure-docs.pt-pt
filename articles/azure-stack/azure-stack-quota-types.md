---
title: Os tipos de ficheiro no Azure Stack | Documentos da Microsoft
description: Ver e editar os tipos de quota diferentes disponíveis para serviços e recursos no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: sethm
ms.reviewer: xiaofmao
ms.lastreviewed: 12/07/2018
ms.openlocfilehash: 7872ed2c9d6f094907ae4d11adef6bc7a99be8e4
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57782892"
---
# <a name="quota-types-in-azure-stack"></a>Tipos de ficheiro no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

[Quotas](azure-stack-plan-offer-quota-overview.md#plans) definem os limites de recursos que uma subscrição de utilizador pode aprovisionar ou consumir. Por exemplo, uma quota pode permitir que um usuário criar até cinco VMs. Cada recurso pode ter seus próprios tipos de quotas.

## <a name="compute-quota-types"></a>Tipos de quota de computação 

| **Tipo** | **Default value** (Valor predefinido) | **Descrição** |
| --- | --- | --- |
| Número máximo de máquinas virtuais | 50 | O número máximo de máquinas virtuais que uma subscrição pode criar nesta localização. |
| Número máximo de núcleos de máquina virtual | 100 | O número máximo de núcleos que uma subscrição pode criar nesta localização (por exemplo, uma VM A3 tem quatro núcleos). |
| Número máximo de conjuntos de disponibilidade | 10 | O número máximo de conjuntos de disponibilidade que podem ser criadas nesta localização. |
| Define o número máximo de dimensionamento de máquinas virtuais | 100 | O número máximo de conjuntos de dimensionamento de máquinas virtuais que podem ser criadas nesta localização. |
| Capacidade máxima (em GB) de disco gerido standard | 2048 | A capacidade máxima de discos geridos padrão que podem ser criadas nesta localização. |
| Capacidade máxima (em GB) de um disco gerido premium | 2048 | A capacidade máxima do premium geridos os discos que podem ser criados nesta localização. |

## <a name="storage-quota-types"></a>Tipos de quota de armazenamento 

| **Item** | **Default value** (Valor predefinido) | **Descrição** |
| --- | --- | --- |
| Capacidade máxima (GB) |2048 |Capacidade de armazenamento total (incluindo os blobs e todos os instantâneos de assiociated tabelas, filas) que pode ser utilizada por uma subscrição nesta localização. |
| Número total de contas de armazenamento |20 |O número máximo de contas de armazenamento que uma subscrição pode criar nesta localização. |

> [!NOTE]  
> Pode demorar até duas horas antes de uma quota de armazenamento é imposta.


## <a name="network-quota-types"></a>Tipos de quota de rede

| **Item** | **Default value** (Valor predefinido) | **Descrição** |
| --- | --- | --- |
| IPs públicos máximo |50 |O número máximo de IPs públicos que uma subscrição pode criar nesta localização. |
| Redes virtuais máximos |50 |O número máximo de redes virtuais que uma subscrição pode criar nesta localização. |
| Gateways de rede virtual máximo |1 |O número máximo de gateways de rede virtual (Gateways de VPN) que uma subscrição pode criar nesta localização. |
| Ligações de rede máxima |2 |O número máximo de ligações de rede (ponto a ponto ou site a site) que uma subscrição pode criar em todos os gateways de rede virtual nesta localização. |
| Balanceadores de carga máxima |50 |O número máximo de balanceadores de carga que uma subscrição pode criar nesta localização. |
| NICs máximos |100 |O número máximo de interfaces de rede que uma subscrição pode criar nesta localização. |
| Grupos de segurança de rede máxima |50 |O número máximo de grupos de segurança de rede que uma subscrição pode criar nesta localização. |

## <a name="view-an-existing-quota"></a>Ver uma quota existente

Existem duas formas diferentes para ver uma quota existente:

### <a name="plans"></a>Planos

1.  No painel de navegação esquerdo do portal do administrador, selecione **planos**.
2.  Selecione o plano que gostaria de ver os detalhes para, ao clicar no respetivo nome.
3.  No painel do que se abre, selecione **serviços e quotas**.
4.  Selecione a quota de que pretende ver ao clicar no **nome** coluna.

    [![Quotas](media/azure-stack-quota-types/quotas1sm.png "ver quotas")](media/azure-stack-quota-types/quotas1.png#lightbox)

### <a name="resource-providers"></a>Fornecedores de recursos

1. Encontrar no dashboard do portal de administração do padrão, o **fornecedores de recursos** mosaico.
2. Selecione o serviço com a quota de que pretende ver, assim **computação**, **rede**, ou **armazenamento**.
3. Selecione **Quotas**e, em seguida, selecione a quota de que pretende visualizar.

## <a name="edit-a-quota"></a>Editar uma quota

Existem duas formas diferentes de editar uma quota:

### <a name="edit-a-plan"></a>Editar um plano

1.  No painel de navegação esquerdo do portal do administrador, selecione **planos**.
2.  Selecione o plano para o qual quiser para editar uma quota, ao clicar no respetivo nome.
3.  No painel do que se abre, selecione **serviços e quotas**.
4.  Selecione a quota de que pretende editar clicando-lo na **nome** coluna.
    [![Quotas](media/azure-stack-quota-types/quotas1sm.png "ver quotas")](media/azure-stack-quota-types/quotas1.png#lightbox)

5.  No painel do que se abre, selecione **editar na computação**, **editar na rede**, ou **editar no armazenamento**.
    ![Quotas](media/azure-stack-quota-types/quotas3.png "ver quotas")    

Em alternativa, pode seguir este procedimento para editar uma quota:

1. No dashboard do padrão do portal do administrador, encontrar o **fornecedores de recursos** mosaico.
2. Selecione o serviço com a quota de que pretende modificar, como **computação**, **rede**, ou **armazenamento**.
3. Em seguida, selecione **Quotas**e, em seguida, selecione a quota de que pretende alterar.
4. Sobre o **quotas de armazenamento definida**, **computação definir quotas**, ou **quotas de rede de conjunto** painel (dependendo do tipo de quota que escolheu para editar), edite os valores e, em seguida, selecione **Guardar**.

### <a name="edit-original-configuration"></a>Editar a configuração original
  
Pode optar por editar a configuração original de uma quota em vez de [utilizar um plano de suplemento](create-add-on-plan.md). Ao editar uma quota, a nova configuração automaticamente aplica-se globalmente a todos os planos que utilizam esse quota e todas as assinaturas existentes que utilizam esses planos. A edição de uma quota é diferente de quando utiliza um plano de suplemento para fornecer uma quota modificada, o que um utilizador optar por subscrever. 

Os novos valores para a quota aplicam-se globalmente a todos os planos que utilizam a quota modificada e a todas as subscrições existentes que utilizam esses planos. 

## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais sobre os planos, ofertas e quotas.](azure-stack-plan-offer-quota-overview.md)
- [Crie quotas durante a criação de um plano.](azure-stack-create-plan.md)
