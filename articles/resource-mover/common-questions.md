---
title: Perguntas comuns sobre Azure Resource Mover?
description: Obtenha respostas a perguntas comuns sobre Azure Resource Mover
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: raynew
ms.openlocfilehash: 18de210d817ee309ac4970ecea6b62f6ef03ef02
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530408"
---
# <a name="common-questions"></a>Perguntas comuns

Este artigo responde a perguntas comuns sobre [a Azure Resource Mover](overview.md).

## <a name="general"></a>Geral

### <a name="is-resource-mover-generally-available"></a>A Resource Mover está geralmente disponível?

A Resource Mover está atualmente em pré-visualização pública. As cargas de trabalho de produção são suportadas.



## <a name="moving-across-regions"></a>Movendo-se através de regiões

### <a name="can-i-move-resources-across-any-regions"></a>Posso mover recursos em qualquer região?

Atualmente, pode mover recursos de qualquer região pública de origem para qualquer região pública alvo, dependendo dos [tipos de recursos disponíveis nessa região.](https://azure.microsoft.com/global-infrastructure/services/) A movimentação de recursos nas regiões do Governo de Azure não é atualmente apoiada.

### <a name="what-resources-can-i-move-across-regions-using-resource-mover"></a>Que recursos posso deslocar-me através de regiões utilizando o Resource Mover?

Utilizando o Resource Mover, pode atualmente mover os seguintes recursos através das regiões:

- VMs Azure e discos associados
- NICs
- Conjuntos de disponibilidade 
- Redes virtuais do Azure 
- Endereços IP públicos
- Grupos de segurança de rede (NSGs)
- Equilibradores de carga interna e pública 
- Bases de dados Azure SQL e piscinas elásticas


### <a name="can-i-move-resources-across-subscriptions-when-i-move-them-across-regions"></a>Posso mover recursos através de assinaturas quando os deslocar através de regiões?

Pode alterar a subscrição depois de transferir recursos para a região de destino. [Saiba mais](../azure-resource-manager/management/move-resource-group-and-subscription.md) sobre a mudança de recursos para uma subscrição diferente. 

### <a name="where-is-the-metadata-for-moving-across-regions-stored"></a>Onde estão os metadados para se deslocarem através das regiões armazenadas?

Está guardado numa base de [dados da Azure Cosmos,](../cosmos-db/database-encryption-at-rest.md) e no [armazenamento de blob Azure,](../storage/common/storage-service-encryption.md)numa subscrição da Microsoft. Atualmente, os metadados estão armazenados no Leste dos EUA 2 e norte da Europa. Vamos alargar esta cobertura a outras regiões. Isto não o impede de mover recursos em nenhuma região pública.

### <a name="is-the-collected-metadata-encrypted"></a>Os metadados recolhidos estão encriptados?

Sim, tanto em trânsito como em repouso.
- Durante o trânsito, os metadados são enviados de forma segura para o serviço de Transporte de Recursos através da internet utilizando HTTPS.
- No armazenamento, os metadados são encriptados.

### <a name="how-is-managed-identity-used-in-resource-mover"></a>Como é utilizada a identidade gerida no Resource Mover?

[A identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) (anteriormente conhecida como Identidade de Serviço Gerido (MIS)) fornece serviços Azure com uma identidade gerida automaticamente em Azure AD.
- A Resource Mover utiliza identidade gerida para que possa aceder a subscrições do Azure para mover recursos através das regiões.
- Uma coleção de movimentos precisa de uma identidade atribuída ao sistema, com acesso à subscrição que contém recursos que está a mover.

- Se mover recursos através de regiões do portal, este processo acontece automaticamente.
- Se mover recursos usando o PowerShell, executar cmdlets para atribuir uma identidade atribuída ao sistema à coleção e, em seguida, atribuir uma função com as permissões de subscrição corretas ao principal identidade. 

### <a name="what-managed-identity-permissions-does-resource-mover-need"></a>Que permissões de identidade geridas precisa o Resource Mover?

A Azure Resource Mover geriu a identidade precisa pelo menos destas permissões: 

- Permissão para escrever/criar recursos na subscrição do utilizador, disponível com a função *Contribuinte.* 
- Permissão para criar atribuições de papéis. Normalmente disponível com as funções *de Administrador de* Acesso ao *Utilizador* ou com uma função personalizada que tem a *microsoft.Autorizações/atribuições de funções/escrever permissão* atribuída. Esta permissão não é necessária se a identidade gerida do recurso de partilha de dados já tiver acesso à loja de dados Azure. 
 
Quando adiciona recursos no hub de Resource Mover no portal, as permissões são manuseadas automaticamente desde que o utilizador tenha as permissões acima descritas. Se adicionar recursos com o PowerShell, atribui permissões manualmente.

> [!IMPORTANT]
> Recomendamos vivamente que não modifique ou remova atribuições de funções de identidade. 

### <a name="what-should-i-do-if-i-dont-have-permissions-to-assign-role-identity"></a>O que devo fazer se não tiver permissões para atribuir identidade de papel?

**Causa possível** | **Recomendação**
--- | ---
Não é administrador *de contribuinte* e de acesso ao *utilizador* (ou *Proprietário)* quando adiciona um recurso pela primeira vez. | Utilize uma conta com *permissões de Contribuinte* e *User Access (ou* *Proprietário)* para a subscrição.
A identidade gerida pela Resource Mover não tem o papel necessário. | Adicione as funções de "Contribuinte" e "Administrador de Acesso ao Utilizador".
A identidade gerida pela Resource Mover foi reposta a *Nenhuma*. | Reenable uma identidade atribuída ao sistema na recolha de movimentos > **Identidade**. Em alternativa, adicione novamente o recurso em **Recursos Adicionais,** o que faz a mesma coisa.  
A assinatura foi transferida para outro inquilino. | Desative e, em seguida, ative a identidade gerida para a recolha do movimento.


## <a name="next-steps"></a>Passos seguintes

[Saiba mais](about-move-process.md) sobre os componentes do Resource Mover e o processo de mudança.
