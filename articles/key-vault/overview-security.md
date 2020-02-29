---
title: Segurança do Cofre de Chaves Azure  Microsoft Docs
description: Gerencie permissões de acesso para cofre de chaves Azure, chaves e segredos. Cobre o modelo de autenticação e autorização para o Cofre chave e como proteger o cofre da chave.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 3cc5cb68f430ac8e5070b9c8c4a1aa0c28639311
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197781"
---
# <a name="azure-key-vault-security"></a>Segurança do cofre de chaves Azure

Você precisa proteger chaves de encriptação e segredos como certificados, cordas de ligação e senhas na nuvem para que você esteja usando Azure Key Vault. Uma vez que está a armazenar dados críticos sensíveis e empresariais, precisa de tomar medidas para maximizar a segurança dos seus cofres e os dados armazenados nos mesmos. Este artigo cobrirá alguns dos conceitos que deve considerar ao conceber a sua segurança do Cofre de Chaves Azure.

## <a name="identity-and-access-management"></a>Gestão de identidades e acessos

Quando se cria um cofre chave numa subscrição Azure, está automaticamente associado ao inquilino da AD Azure da subscrição. Qualquer pessoa que tente gerir ou recuperar conteúdo de um cofre deve ser autenticada pela Azure AD.

- A autenticação estabelece a identidade do chamador.
- A autorização determina quais as operações que o chamador pode realizar. A autorização no Key Vault utiliza uma combinação de controlo de [acesso baseado em Funções](../role-based-access-control/overview.md) (RBAC) e políticas de acesso azure key vault.

### <a name="access-model-overview"></a>Visão geral do modelo de acesso

O acesso a cofres ocorre através de duas interfaces ou aviões. Estes aviões são o avião de gestão e o avião de dados.

- O plano de *gestão* é onde você gere o próprio Key Vault e é a interface usada para criar e apagar cofres. Você também pode ler propriedades chave do cofre e gerir políticas de acesso.
- O plano de *dados* permite-lhe trabalhar com os dados armazenados num cofre chave. Pode adicionar, eliminar e modificar chaves, segredos e certificados.

Para aceder a um cofre chave em qualquer um dos planos, todos os chamadores (utilizadores ou aplicações) devem ser autenticados e autorizados. Ambos os aviões utilizam o Azure Ative Directory (Azure AD) para autenticação. Para autorização, o plano de gestão utiliza controlo de acesso baseado em funções (RBAC) e o plano de dados utiliza uma política de acesso ao Cofre chave.

O modelo de um único mecanismo de autenticação a ambos os aviões tem vários benefícios:

- As organizações podem controlar o acesso central a todos os cofres chave da sua organização.
- Se um utilizador sair, perde instantaneamente o acesso a todos os cofres chave da organização.
- As organizações podem personalizar a autenticação utilizando as opções em AD Azure, de modo a permitir a autenticação de vários fatores para uma maior segurança

### <a name="managing-administrative-access-to-key-vault"></a>Gerir o acesso administrativo ao Cofre Chave

Quando se cria um cofre chave num grupo de recursos, gere-se o acesso utilizando o Azure AD. Você concede aos utilizadores ou grupos a capacidade de gerir os cofres chave em um grupo de recursos. Pode conceder acesso a um nível específico de âmbito, atribuindo as funções rbac adequadas. Para conceder acesso a um utilizador para gerir cofres chave, atribui um papel `key vault Contributor` predefinido ao utilizador num âmbito específico. Os seguintes níveis de âmbito podem ser atribuídos a uma função RBAC:

- **Subscrição**: Uma função RBAC atribuída ao nível de subscrição aplica-se a todos os grupos e recursos de recursos dentro dessa subscrição.
- **Grupo de recursos**: Uma função RBAC atribuída ao nível do grupo de recursos aplica-se a todos os recursos desse grupo de recursos.
- **Recurso específico**: Uma função RBAC atribuída a um recurso específico aplica-se a esse recurso. Neste caso, o recurso é um cofre específico.

Há vários papéis predefinidos. Se um papel predefinido não se adequa às suas necessidades, pode definir o seu próprio papel. Para mais informações, consulte [As funções RBAC: Incorporadas.](../role-based-access-control/built-in-roles.md)

> [!IMPORTANT]
> Se um utilizador tiver `Contributor` permissões para um avião de gestão de cofres chave, o utilizador pode conceder-se acesso ao plano de dados definindo uma política de acesso ao Cofre chave. Devias controlar bem quem tem acesso `Contributor` papel aos teus cofres chave. Certifique-se de que apenas pessoas autorizadas podem aceder e gerir os seus cofres chave, chaves, segredos e certificados.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Controlar o acesso aos dados do Cofre chave

As políticas de acesso ao Cofre Chave concedem permissões separadamente a chaves, segredos ou certificados. Só pode conceder acesso a um utilizador a chaves e não a segredos. Permissões de acesso a chaves, segredos e certificados são geridos ao nível do cofre.

> [!IMPORTANT]
> As políticas de acesso ao Cofre Chave não suportam permissões granulares, ao nível do objeto, como uma chave específica, segredo ou certificado. Quando um utilizador tem permissão para criar e apagar chaves, pode efetuar essas operações em todas as teclas do cofre da chave.

Para definir as políticas de acesso para um cofre chave, utilize o [portal Azure,](https://portal.azure.com/)o [Azure CLI,](../cli-install-nodejs.md) [o Azure PowerShell](/powershell/azureps-cmdlets-docs)ou o [Key Vault Management REST APIs](https://msdn.microsoft.com/library/azure/mt620024.aspx).

Pode restringir o acesso ao avião de dados utilizando [pontos finais](key-vault-overview-vnet-service-endpoints.md)de serviço de rede virtual para o Cofre de Chaves Azure . Pode configurar [firewalls e regras de rede virtuais](key-vault-network-security.md) para uma camada adicional de segurança.

## <a name="network-access"></a>Acesso à rede

Pode reduzir a exposição dos seus cofres especificando quais os endereços IP que têm acesso aos mesmos. Os pontos finais do serviço de rede virtual para o Cofre de Chaves Azure permitem-lhe restringir o acesso a uma rede virtual especificada. Os pontos finais também permitem restringir o acesso a uma lista de gamas de endereços IPv4 (versão 4 do protocolo de internet). Qualquer utilizador que ligue ao seu cofre chave de fora dessas fontes é negado acesso.

Após as regras de firewall estarem em vigor, os utilizadores só podem ler dados do Key Vault quando os seus pedidos têm origem em redes virtuais permitidas ou em intervalos de endereços IPv4. Isto também se aplica ao acesso ao Cofre chave do portal Azure. Embora os utilizadores possam navegar para um cofre chave a partir do portal Azure, eles podem não ser capazes de listar chaves, segredos ou certificados se a sua máquina cliente não estiver na lista permitida. Isto também afeta o Key Vault Picker por outros serviços Azure. Os utilizadores podem ser capazes de ver a lista de cofres chave, mas não listar chaves, se as regras de firewall impedirem a sua máquina de clientes.

Para mais informações sobre a revisão de endereços de rede Azure Key Vault, o [serviço de rede virtual para o Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)

## <a name="monitoring"></a>Monitorização

A exploração de key vault guarda informações sobre as atividades realizadas no seu cofre. Registos do cofre da chave:

- Todos os pedidos autenticados da API REST, incluindo pedidos falhados
  - Operações no cofre em si. Estas operações incluem criação, eliminação, definição de políticas de acesso e atualização de atributos chave do cofre, tais como tags.
  - Operações sobre chaves e segredos no cofre da chave, incluindo:
    - Criar, modificar ou apagar estas chaves ou segredos.
    - Assinar, verificar, encriptar, desencriptar, embrulhar e desembrulhar chaves, obter segredos e listar chaves e segredos (e as suas versões).
- Pedidos não autenticados que resultam numa resposta 401. Exemplos são pedidos que não têm um símbolo portador, que são mal formados ou expirados, ou que têm um token inválido.

A informação de registo pode ser acedida no prazo de 10 minutos após a operação do cofre da chave. Cabe-lhe a si gerir os seus registos na sua conta de armazenamento.

- Utilize métodos padrão de controlo de acesso do Azure para proteger os seus registos, restringindo o seu acesso.
- Elimine os registos que já não pretende manter na sua conta de armazenamento.

Para recomendação sobre gestão segura das contas de armazenamento, reveja o guia de [segurança do Armazenamento Azure](../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>Passos Seguintes

- [Pontos finais de serviço de rede virtual para cofre chave Azure](key-vault-overview-vnet-service-endpoints.md)
- [RBAC: Funções incorporadas](../role-based-access-control/built-in-roles.md)
- [pontos finais de serviço de rede virtual para O Cofre chave Azure](key-vault-overview-vnet-service-endpoints.md)
