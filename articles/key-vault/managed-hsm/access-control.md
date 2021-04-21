---
title: Controlo de acesso HSM gerido a Azure
description: Gerir permissões de acesso para Azure Managed HSM e chaves. Cobre o modelo de autenticação e autorização para OMS Gerido e como proteger os seus HSMs.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: bea1ccf0777c6325bc86c15e0f88304c465d89c9
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750289"
---
# <a name="managed-hsm-access-control"></a>Controlo de acesso do HSM Gerido

> [!NOTE]
> O fornecedor de recursos Key Vault suporta dois tipos de recursos: **cofres** e **HSMs geridos.** O controlo de acesso descrito neste artigo aplica-se apenas aos **HSMs geridos**. Para saber mais sobre o controlo de acesso para o HSM gerido, consulte [Fornecer acesso a chaves, certificados e segredos key Vault com um controlo de acesso baseado em funções Azure](../general/rbac-guide.md).

Azure Key Vault Managed HSM é um serviço de nuvem que protege as chaves de encriptação. Uma vez que estes dados são sensíveis e críticos de negócio, é necessário garantir o acesso aos seus HSMs geridos, permitindo apenas que aplicações e utilizadores autorizados acedam aos mesmos. Este artigo fornece uma visão geral do modelo de controlo de acesso gerido do HSM. Explica a autenticação e a autorização e descreve como garantir o acesso aos seus HSMs geridos.

## <a name="access-control-model"></a>Modo de controlo de acesso

O acesso a um HSM gerido é controlado através de duas interfaces: o **plano de gestão** e o **plano de dados.** O avião de gestão é onde gere o próprio HSM. As operações neste plano incluem a criação e eliminação de HSMs geridos e a recuperação de propriedades geridas de HSM. O plano de dados é onde trabalha com os dados armazenados num HSM gerido, que são chaves de encriptação apoiadas pelo HSM. Pode adicionar, eliminar, modificar e utilizar chaves para executar operações criptográficas, gerir atribuições de funções para controlar o acesso às teclas, criar uma cópia de segurança completa do HSM, restaurar a cópia de segurança completa e gerir o domínio de segurança a partir da interface do plano de dados.

Para aceder a um HSM gerido em qualquer um dos planos, todos os chamadores devem ter a autenticação e autorização adequadas. A autenticação estabelece a identidade do chamador. A autorização determina quais as operações que o chamador pode executar. Um chamador pode ser qualquer um dos [principais de segurança definidos](../../role-based-access-control/overview.md#security-principal) no Azure Ative Directory - utilizador, grupo, principal de serviço ou identidade gerida.

Ambos os aviões usam o Azure Ative Directory para autenticação. Para autorização, utilizam diferentes sistemas da seguinte forma
- O avião de gestão usa o controlo de acesso baseado em funções Azure -- Azure RBAC -- um sistema de autorização construído em Azure Azure Resource Manager 
- O plano de dados utiliza um RBAC de nível HSM gerido (Gerido HSM local RBAC) -- um sistema de autorização implementado e aplicado ao nível de HSM gerido.

Quando um HSM gerido é criado, o solicitador também fornece uma lista de administradores de planos de dados (todos os princípios de segurança são [suportados).](../../role-based-access-control/overview.md#security-principal) Apenas estes administradores podem aceder ao plano de dados gerido do HSM para executar operações-chave e gerir as atribuições de funções de data plane (C RBAC local gerido pelo HSM).

O modelo de permissão para ambos os aviões usa a mesma sintaxe, mas são aplicadas a diferentes níveis e as atribuições de papéis usam diferentes âmbitos. O avião de gestão Azure RBAC é imposto pelo Azure Resource Manager enquanto o plano de dados Gerido HSM local RBAC é aplicado pela própria HSM gerida.

> [!IMPORTANT]
> Conceder a um avião de gestão principal de segurança o acesso a um HSM gerido não lhes dá qualquer acesso a um plano de dados para aceder a chaves ou atribuições de funções de avião de dados Geridos HSM local RBAC). Este isolamento é por design para evitar a expansão inadvertida de privilégios que afetam o acesso às chaves armazenadas no HSM Gerido.

Por exemplo, um administrador de subscrição (uma vez que têm permissão de "Contribuinte" para todos os recursos da subscrição) pode eliminar um HSM gerido na sua subscrição, mas se não tiverem acesso a um plano de dados especificamente concedido através do CSE local gerido do HSM, não podem aceder a chaves ou gerir a atribuição de funções no HSM gerido para conceder a si mesmos ou a outros acesso ao plano de dados.

## <a name="azure-active-directory-authentication"></a>Autenticação do Azure Active Directory

Quando cria um HSM gerido numa subscrição Azure, está automaticamente associado ao inquilino do Azure Ative Directory da subscrição. Todos os chamadores em ambos os aviões devem estar registados neste inquilino e autenticar para aceder ao HSM gerido.

A aplicação autentica-se com o Azure Ative Directory antes de ligar para qualquer um dos aviões. A aplicação pode utilizar qualquer [método de autenticação suportado](../../active-directory/develop/authentication-vs-authorization.md) com base no tipo de aplicação. A aplicação adquire um símbolo para um recurso no avião para ter acesso. O recurso é um ponto final na gestão ou plano de dados, baseado no ambiente Azure. A aplicação utiliza o token e envia um pedido de API REST para o ponto final gerido do HSM. Para saber mais, reveja todo o [fluxo de autenticação.](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

A utilização de um único mecanismo de autenticação para ambos os aviões tem vários benefícios:

- As organizações podem controlar o acesso central a todos os HSMs geridos na sua organização.
- Se um utilizador sair, perde instantaneamente o acesso a todos os HSMs geridos na organização.
- As organizações podem personalizar a autenticação utilizando as opções no Azure Ative Directory, tais como para permitir a autenticação de vários fatores para uma segurança adicional.

## <a name="resource-endpoints"></a>Pontos finais de recursos

Os diretores de segurança acedem aos aviões através de pontos finais. Os controlos de acesso dos dois aviões funcionam de forma independente. Para conceder a uma aplicação acesso a chaves de utilização num HSM gerido, você concede acesso a um plano de dados utilizando o C RBAC local gerido do HSM. Para conceder a um utilizador acesso a recurso Gerido HSM para criar, ler, excluir, mover os HSMs geridos e editar outras propriedades e tags que utiliza Azure RBAC.

A tabela seguinte mostra os pontos finais para os aviões de gestão e dados.

| Avião de acesso &nbsp; | Pontos finais de acesso | Operações | Mecanismo de controlo de acesso |
| --- | --- | --- | --- |
| Plano de gestão | **Global:**<br> management.azure.com:443<br> | Criar, ler, atualizar, excluir e mover HSMs geridos<br>Definir tags HSM geridas | RBAC do Azure |
| Plano de dados | **Global:**<br> &lt;hsm-nome &gt; .managedhsm.azure.net:443<br> | **Chaves:** desencriptar, encriptar,<br> desembrulhar, embrulhar, verificar, assinar, obter, listar, atualizar, criar, importar, eliminar, limpar, restaurar, purgar<br/><br/> **Gestão de funções de plano de dados (Managed HSM local RBAC)**_: <br/> <br/> listar definições de funções, atribuir funções, eliminar atribuições de funções, definir funções personalizadas_*Backup/restaurar **: backup, restaurar, verificar o estado de backup/restaurar operações <br/> <br/>** Domínio de segurança**: descarregar e carregar domínio de segurança | Gerido HSM local RBAC |
|||||

## <a name="management-plane-and-azure-rbac"></a>Avião de gestão e Azure RBAC

No plano de gestão, você usa Azure RBAC para autorizar as operações que um chamador pode executar. No modelo Azure RBAC, cada subscrição do Azure tem um exemplo de Azure Ative Directory. Você concede acesso a utilizadores, grupos e aplicações deste diretório. O acesso é concedido para gerir recursos na subscrição Azure que utilizam o modelo de implementação do Gestor de Recursos Azure. Para conceder acesso, utilize o [portal Azure](https://portal.azure.com/), o [Azure CLI,](/cli/azure/install-classic-cli) [a Azure PowerShell,](/powershell/azureps-cmdlets-docs)ou o [Azure Resource Manager REST APIs](/rest/api/authorization/roleassignments).

Cria um cofre chave num grupo de recursos e gere o acesso utilizando o Azure Ative Directory. Você concede aos utilizadores ou grupos a capacidade de gerir os cofres chave em um grupo de recursos. Você concede o acesso a um nível de âmbito específico, atribuindo as funções apropriadas de Azure. Para conceder acesso a um utilizador para gerir cofres-chave, atribui uma função predefinida `key vault Contributor` ao utilizador num âmbito específico. Os seguintes níveis de âmbito podem ser atribuídos a uma função Azure:

- **Grupo de gestão**: Uma função Azure atribuída ao nível da subscrição aplica-se a todas as subscrições desse grupo de gestão.
- **Subscrição**: Uma função Azure atribuída ao nível de subscrição aplica-se a todos os grupos de recursos e recursos dentro dessa subscrição.
- **Grupo de recursos**: Uma função Azure atribuída ao nível do grupo de recursos aplica-se a todos os recursos desse grupo de recursos.
- **Recurso específico**: Uma função Azure atribuída a um recurso específico aplica-se a esse recurso. Neste caso, o recurso é um cofre-chave específico.

Há vários papéis predefinidos. Se um papel predefinido não se adequa às suas necessidades, pode definir o seu próprio papel. Para mais informações, consulte [Azure RBAC: Funções incorporadas](../../role-based-access-control/built-in-roles.md).

## <a name="data-plane-and-managed-hsm-local-rbac"></a>Plano de dados e GERIDO HSM local RBAC

Concede-se um acesso principal de segurança para executar operações-chave específicas, atribuindo uma função. Para cada atribuição de funções, é necessário especificar uma função e um âmbito sobre o qual essa atribuição se aplica. Para o HSM local gerido, estão disponíveis dois âmbitos.

- **"/" ou "/chaves":** âmbito de nível HSM. Os diretores de segurança atribuídos a um papel neste âmbito podem executar as operações definidas na função para todos os objetos (teclas) no HSM gerido.
- **"/Teclas/ &lt; nome de &gt; chave":** Âmbito de nível da chave. Os diretores de segurança atribuídos a um papel neste âmbito podem desempenhar as operações definidas nesta função apenas para todas as versões da chave especificada.

## <a name="next-steps"></a>Passos seguintes

- Para um tutorial de iniciação para um administrador, veja [o que é O HSM gerido?](overview.md)
- Para um tutorial de gestão de funções, consulte [o CSC local gerido do HSM](role-management.md)
- Para obter mais informações sobre o registo de utilização para registos geridos de HSM, consulte [a sessão de registo de HSM gerida](logging.md)