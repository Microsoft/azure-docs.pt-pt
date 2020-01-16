---
title: Segurança do Azure Key Vault | Microsoft Docs
description: Gerencie permissões de acesso para Azure Key Vault, chaves e segredos. Aborda o modelo de autenticação e autorização para Key Vault e como proteger o cofre de chaves.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 74dac926ea67b9f6a31993a72dc6331aa48155b7
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981559"
---
# <a name="azure-key-vault-security"></a>Segurança de Azure Key Vault

Você precisa proteger chaves de criptografia e segredos como certificados, cadeias de conexão e senhas na nuvem para que você esteja usando Azure Key Vault. Como você está armazenando dados confidenciais e críticos para os negócios, você precisa tomar medidas para maximizar a segurança de seus cofres e os dados armazenados neles. Este artigo abordará alguns dos conceitos que você deve considerar ao projetar sua segurança de Azure Key Vault.

## <a name="identity-and-access-management"></a>Gestão de identidades e acesso

Quando você cria um cofre de chaves em uma assinatura do Azure, ele é automaticamente associado ao locatário do Azure AD da assinatura. Qualquer pessoa que tentar gerenciar ou recuperar conteúdo de um cofre deve ser autenticada pelo Azure AD.

- A autenticação estabelece a identidade do chamador.
- A autorização determina quais operações o chamador pode executar. A autorização no Key Vault usa uma combinação de RBAC ( [controle de acesso baseado em função](../role-based-access-control/overview.md) ) e políticas de acesso de Azure Key Vault.

### <a name="access-model-overview"></a>Visão geral do modelo de acesso

O acesso aos cofres ocorre por meio de duas interfaces ou planos. Esses planos são o plano de gerenciamento e o plano de dados.

- O *plano de gerenciamento* é onde você gerencia Key Vault ele mesmo e é a interface usada para criar e excluir cofres. Você também pode ler as propriedades do cofre de chaves e gerenciar políticas de acesso.
- O *plano de dados* permite que você trabalhe com os dados armazenados em um cofre de chaves. Você pode adicionar, excluir e modificar chaves, segredos e certificados.

Para acessar um cofre de chaves em qualquer plano, todos os chamadores (usuários ou aplicativos) devem ser autenticados e autorizados. Ambos os planos usam Azure Active Directory (AD do Azure) para autenticação. Para autorização, o plano de gerenciamento usa o RBAC (controle de acesso baseado em função) e o plano de dados usa uma política de acesso Key Vault.

O modelo de um único mecanismo de autenticação para ambos os planos tem vários benefícios:

- As organizações podem controlar o acesso centralmente a todos os cofres de chaves em sua organização.
- Se um usuário deixar, ele perderá instantaneamente o acesso a todos os cofres de chaves na organização.
- As organizações podem personalizar a autenticação usando as opções no Azure AD, como para habilitar a autenticação multifator para aumentar a segurança

### <a name="managing-administrative-access-to-key-vault"></a>Gerenciando o acesso administrativo ao Key Vault

Ao criar um cofre de chaves em um grupo de recursos, você gerencia o acesso usando o Azure AD. Você concede aos usuários ou grupos a capacidade de gerenciar os cofres de chaves em um grupo de recursos. Você pode conceder acesso em um nível de escopo específico atribuindo as funções RBAC apropriadas. Para conceder acesso a um usuário para gerenciar cofres de chaves, atribua uma função de `key vault Contributor` predefinida para o usuário em um escopo específico. Os seguintes níveis de escopos podem ser atribuídos a uma função de RBAC:

- **Assinatura**: uma função RBAC atribuída no nível de assinatura se aplica a todos os grupos de recursos e recursos dentro dessa assinatura.
- **Grupo de recursos**: uma função RBAC atribuída no nível do grupo de recursos se aplica a todos os recursos nesse grupo de recursos.
- **Recurso específico**: uma função RBAC atribuída a um recurso específico se aplica a esse recurso. Nesse caso, o recurso é um cofre de chaves específico.

Há várias funções predefinidas. Se uma função predefinida não atender às suas necessidades, você poderá definir sua própria função. Para obter mais informações, consulte [RBAC: funções internas](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Se um usuário tiver `Contributor` permissões para um plano de gerenciamento do cofre de chaves, o usuário poderá conceder a si mesmo o acesso ao plano de dados definindo uma política de acesso de Key Vault. Você deve controlar rigidamente quem tem `Contributor` acesso de função para seus cofres de chaves. Certifique-se de que apenas pessoas autorizadas possam acessar e gerenciar seus cofres de chaves, chaves, segredos e certificados.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Controlando o acesso a dados de Key Vault

Key Vault políticas de acesso concedem permissões separadamente a chaves, segredos ou certificado. Você pode conceder a um usuário acesso apenas a chaves e não a segredos. Permissões de acesso para chaves, segredos e certificados são gerenciadas no nível do cofre.

> [!IMPORTANT]
> Key Vault políticas de acesso não dão suporte a permissões granulares em nível de objeto, como uma chave, um segredo ou um certificado específico. Quando um usuário recebe permissão para criar e excluir chaves, ele pode executar essas operações em todas as chaves nesse cofre de chaves.

Para definir políticas de acesso para um cofre de chaves, use o [portal do Azure](https://portal.azure.com/), o [CLI do Azure](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs)ou as [APIs REST de gerenciamento de Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx).

Você pode restringir o acesso ao plano de dados usando [pontos de extremidade de serviço de rede virtual para Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Você pode configurar [firewalls e regras de rede virtual](key-vault-network-security.md) para uma camada adicional de segurança.

## <a name="network-access"></a>Acesso à rede

Você pode reduzir a exposição de seus cofres especificando quais endereços IP têm acesso a eles. Os pontos de extremidade de serviço de rede virtual para Azure Key Vault permitem restringir o acesso a uma rede virtual especificada. Os pontos de extremidade também permitem restringir o acesso a uma lista de intervalos de endereços IPv4 (protocolo IP versão 4). Qualquer usuário que se conecta ao cofre de chaves de fora dessas fontes tem o acesso negado.

Depois que as regras de firewall estiverem em vigor, os usuários só poderão ler dados de Key Vault quando suas solicitações se originarem de redes virtuais permitidas ou intervalos de endereços IPv4. Isso também se aplica ao acesso a Key Vault do portal do Azure. Embora os usuários possam navegar para um cofre de chaves do portal do Azure, eles podem não ser capazes de listar chaves, segredos ou certificados se o computador cliente não estiver na lista de permissões. Isso também afeta o seletor de Key Vault por outros serviços do Azure. Os usuários podem ver a lista de cofres de chaves, mas não as chaves de lista, se as regras de firewall impedirem seu computador cliente.

Para obter mais informações sobre Azure Key Vault endereço de rede revisar [pontos de extremidade de serviço de rede virtual para Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)

## <a name="monitoring"></a>Monitorização

Key Vault log salva informações sobre as atividades executadas em seu cofre. Logs de Key Vault:

- Todas as solicitações da API REST autenticadas, incluindo solicitações com falha
  - Operações no próprio cofre de chaves. Essas operações incluem criação, exclusão, definição de políticas de acesso e atualização de atributos do Key Vault, como marcas.
  - Operações em chaves e segredos no cofre de chaves, incluindo:
    - Criando, modificando ou excluindo essas chaves ou segredos.
    - Assinatura, verificação, criptografia, descriptografia, encapsulamento e desencapsulamento de chaves, obtenção de segredos e listagem de chaves e segredos (e suas versões).
- Pedidos não autenticados que resultam numa resposta 401. Exemplos são solicitações que não têm um token de portador, malformados ou expirados, ou que têm um token inválido.

As informações de log podem ser acessadas em 10 minutos após a operação do cofre de chaves. Cabe a você gerenciar seus logs em sua conta de armazenamento.

- Utilize métodos padrão de controlo de acesso do Azure para proteger os seus registos, restringindo o seu acesso.
- Elimine os registos que já não pretende manter na sua conta de armazenamento.

Para obter recomendações sobre o gerenciamento seguro de contas de armazenamento, examine o [Guia de segurança do armazenamento do Azure](../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>Próximos Passos

- [Pontos de extremidade de serviço de rede virtual para Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
- [RBAC: funções internas](../role-based-access-control/built-in-roles.md)
- [pontos de extremidade de serviço de rede virtual para Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
