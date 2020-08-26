---
title: Segurança Azure Key Vault / Microsoft Docs
description: Gerencie permissões de acesso para Azure Key Vault, chaves e segredos. Cobre o modelo de autenticação e autorização do Key Vault e como proteger o cofre da chave.
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
ms.openlocfilehash: edef64c215f96b34cd0efbff4521a7d5adb98885
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870583"
---
# <a name="azure-key-vault-security"></a>Segurança do Azure Key Vault

Você precisa proteger chaves de encriptação e segredos como certificados, cadeias de conexão e senhas na nuvem para que você esteja usando Azure Key Vault. Uma vez que está a armazenar dados críticos sensíveis e empresariais, precisa de tomar medidas para maximizar a segurança dos seus cofres e os dados armazenados nos mesmos. Este artigo cobrirá alguns dos conceitos que deve considerar ao conceber a sua segurança Azure Key Vault.

## <a name="identity-and-access-management"></a>Gestão de identidades e acessos

Quando cria um cofre chave numa subscrição do Azure, está automaticamente associado ao inquilino AD AD da subscrição. Qualquer pessoa que tente gerir ou recuperar conteúdo de um cofre deve ser autenticada pela Azure AD.

- A autenticação estabelece a identidade do chamador.
- A autorização determina quais as operações que o chamador pode efetuar. A autorização em Key Vault utiliza uma combinação de políticas de [acesso baseadas em fun](../../role-based-access-control/overview.md) (RBAC) e Azure Key Vault.

### <a name="access-model-overview"></a>Visão geral do modelo de acesso

O acesso aos cofres ocorre através de duas interfaces ou aviões. Estes aviões são o avião de gestão e o avião de dados.

- O *avião de gestão* é onde gere o Key Vault em si e é a interface usada para criar e apagar cofres. Também pode ler as principais propriedades do cofre e gerir as políticas de acesso.
- O *plano de dados* permite-lhe trabalhar com os dados armazenados num cofre chave. Pode adicionar, excluir e modificar chaves, segredos e certificados.

Para aceder a um cofre chave em qualquer um dos planos, todos os chamadores (utilizadores ou aplicações) devem ser autenticados e autorizados. Ambos os aviões utilizam o Azure Ative Directory (Azure AD) para autenticação. Para autorização, o plano de gestão utiliza o controlo de acesso baseado em funções (RBAC) e o plano de dados utiliza uma política de acesso ao Cofre-Chave.

O modelo de um único mecanismo de autenticação para ambos os aviões tem vários benefícios:

- As organizações podem controlar o acesso central a todos os cofres chave da sua organização.
- Se um utilizador sair, perdem instantaneamente o acesso a todos os cofres chave da organização.
- As organizações podem personalizar a autenticação utilizando as opções em Azure AD, tais como para permitir a autenticação de vários fatores para uma segurança adicional.

### <a name="managing-administrative-access-to-key-vault"></a>Gerir o acesso administrativo ao Cofre-Chave

Quando cria um cofre chave num grupo de recursos, gere o acesso utilizando o Azure AD. Você concede aos utilizadores ou grupos a capacidade de gerir os cofres chave em um grupo de recursos. Pode conceder acesso a um nível de âmbito específico, atribuindo as funções Azure apropriadas. Para conceder acesso a um utilizador para gerir cofres-chave, atribui uma função predefinida `key vault Contributor` ao utilizador num âmbito específico. Os seguintes níveis de âmbito podem ser atribuídos a uma função Azure:

- **Subscrição**: Uma função Azure atribuída ao nível de subscrição aplica-se a todos os grupos de recursos e recursos dentro dessa subscrição.
- **Grupo de recursos**: Uma função Azure atribuída ao nível do grupo de recursos aplica-se a todos os recursos desse grupo de recursos.
- **Recurso específico**: Uma função Azure atribuída a um recurso específico aplica-se a esse recurso. Neste caso, o recurso é um cofre-chave específico.

Há vários papéis predefinidos. Se um papel predefinido não se adequa às suas necessidades, pode definir o seu próprio papel. Para mais informações, consulte [o RBAC: Funções incorporadas.](../../role-based-access-control/built-in-roles.md)

> [!IMPORTANT]
> Se um utilizador tiver `Contributor` permissões para um avião de gestão de cofre chave, o utilizador pode conceder-se acesso ao plano de dados definindo uma política de acesso ao Cofre de Chaves. Deve controlar bem quem tem `Contributor` acesso aos seus cofres chave. Certifique-se de que apenas pessoas autorizadas podem aceder e gerir os seus cofres chave, chaves, segredos e certificados.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Controlo do acesso aos dados do Cofre-Chave

As políticas de acesso ao Cofre chave concedem permissões separadamente a chaves, segredos ou certificados. Pode conceder acesso a um utilizador apenas às chaves e não aos segredos. As permissões de acesso para chaves, segredos e certificados são geridas ao nível do cofre.

> [!IMPORTANT]
> As políticas de acesso ao Cofre não suportam permissões granulares ao nível de objetos como uma chave específica, segredo ou certificado. Quando um utilizador tem permissão para criar e eliminar chaves, pode efetuar essas operações em todas as teclas do cofre.

Para definir políticas de acesso para um cofre de chaves, utilize o [portal Azure,](https://portal.azure.com/)o [Azure CLI,](/cli/azure/install-azure-cli?view=azure-cli-latest) [o Azure PowerShell](/powershell/azure/)ou as [APIs de Gestão de Cofres de Chaves](/rest/api/keyvault/).

Pode restringir o acesso a um plano de dados utilizando [pontos finais de serviço de rede virtual para a Azure Key Vault](overview-vnet-service-endpoints.md)). Pode configurar [firewalls e regras de rede virtuais](network-security.md) para uma camada adicional de segurança.

## <a name="network-access"></a>Acesso à rede

Pode reduzir a exposição dos seus cofres especificando quais endereços IP têm acesso aos mesmos. Os pontos finais de serviço de rede virtual para Azure Key Vault permitem-lhe restringir o acesso a uma rede virtual especificada. Os pontos finais também permitem restringir o acesso a uma lista de intervalos de endereços IPv4 (versão 4 do protocolo de internet). Qualquer utilizador que se conecte ao cofre de fora dessas fontes é negado acesso.

Após as regras de firewall estarem em vigor, os utilizadores só podem ler dados do Key Vault quando os seus pedidos são originários de redes virtuais permitidas ou de intervalos de endereços IPv4. Isto também se aplica ao acesso ao Cofre de Chaves a partir do portal Azure. Embora os utilizadores possam navegar para um cofre chave a partir do portal Azure, podem não ser capazes de listar chaves, segredos ou certificados se a sua máquina cliente não estiver na lista permitida. Isto também afeta o Key Vault Picker por outros serviços Azure. Os utilizadores podem ser capazes de ver a lista de cofres chave, mas não listar chaves, se as regras de firewall impedirem a sua máquina de clientes.

Para mais informações sobre o endereço da rede Azure Key Vault, reveja [os pontos finais do serviço de rede virtual para Azure Key Vault ( Azure Key Vault)](overview-vnet-service-endpoints.md)

## <a name="monitoring"></a>Monitorização

O registo do Key Vault guarda informações sobre as atividades realizadas no seu cofre. Registos do Cofre de Chaves:

- Todos os pedidos autenticados da API DO REST, incluindo pedidos falhados
  - Operações no cofre da chave em si. Estas operações incluem criação, eliminação, definição de políticas de acesso e atualização de atributos chave do cofre, tais como tags.
  - Operações com chaves e segredos no cofre, incluindo:
    - Criar, modificar ou apagar estas chaves ou segredos.
    - Assinar, verificar, encriptar, desencriptar, embrulhar e desembrulhar chaves, obter segredos e listar chaves e segredos (e as suas versões).
- Pedidos não autenticados que resultam numa resposta 401. Exemplos são pedidos que não têm um símbolo ao portador, que são mal formados ou expirados, ou que têm um token inválido.

A informação de registo pode ser acedida no prazo de 10 minutos após a operação do cofre da chave. Cabe-lhe a si gerir os seus registos na sua conta de armazenamento.

- Utilize métodos padrão de controlo de acesso do Azure para proteger os seus registos, restringindo o seu acesso.
- Elimine os registos que já não pretende manter na sua conta de armazenamento.

Para recomendação sobre gestão segura de contas de armazenamento, reveja o [guia de segurança do Azure Storage](../../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>Passos Seguintes

- [Pontos finais de serviço de rede virtual para Azure Key Vault](overview-vnet-service-endpoints.md)
- [RBAC: Funções incorporadas](../../role-based-access-control/built-in-roles.md)

