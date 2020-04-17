---
title: Identidades geridas para Azure
description: Saiba usar identidades geridas para Azure com Tecido de Serviço.
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: a26f188ed2f5e18bdf775cd1fb21001495ffdc89
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461451"
---
# <a name="using-managed-identities-for-azure-with-service-fabric"></a>Utilização de identidades geridas para Azure com tecido de serviço

Um desafio comum ao construir aplicações em nuvem é como gerir de forma segura as credenciais do seu código para autenticar vários serviços sem as guardar localmente numa estação de trabalho de desenvolvedores ou no controlo de fontes. *Identidades geridas para o Azure* resolvem este problema por todos os seus recursos no Azure Ative Directory (Azure AD), fornecendo-lhes identidades geridas automaticamente dentro da AD Azure. Pode utilizar a identidade de um serviço para autenticar qualquer serviço que suporte a autenticação Azure AD, incluindo o Key Vault, sem quaisquer credenciais armazenadas no seu código.

*As identidades geridas para os recursos Azure* são gratuitas com a Azure AD para subscrições azure. Não há custos adicionais.

> [!NOTE]
> *Identidades geridas para o Azure* é o novo nome para o serviço anteriormente conhecido como Identidade de Serviço Gerido (MSI).

## <a name="concepts"></a>Conceitos

As identidades geridas para o Azure baseiam-se em vários conceitos-chave:

- **ID do cliente** - um identificador único gerado pela Azure AD que está ligado a uma aplicação e ao principal de serviço durante o seu fornecimento inicial (consulte também o ID da [aplicação](/azure/active-directory/develop/developer-glossary#application-id-client-id).)

- **ID principal** - o objeto de identificação do objeto principal do serviço para a sua Identidade Gerida que é usado para conceder acesso baseado em papéis a um recurso Azure.

- **Serviço Principal** - um objeto de Diretório Ativo Azure, que representa a projeção de uma aplicação AAD em um determinado inquilino (ver [também diretor](../active-directory/develop/developer-glossary.md#service-principal-object)de serviço .)

Existem dois tipos de identidades geridas:

- Uma **identidade gerida atribuída** pelo Sistema é ativada diretamente numa instância de serviço Azure.  O ciclo de vida de uma identidade atribuída ao sistema é exclusivo da instância de serviço Azure em que está ativado.
- Uma **identidade gerida atribuída pelo utilizador**, que é criada como um recurso do Azure autónomo. A identidade pode ser atribuída a uma ou mais instâncias de serviço Azure e é gerida separadamente dos ciclos de vida desses casos.

Para compreender melhor a diferença entre os tipos de identidade [geridos, veja como funcionam as identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)

## <a name="supported-scenarios-for-service-fabric-applications"></a>Cenários suportados para aplicações de Tecido de Serviço

As identidades geridas para o Tecido de Serviço são suportadas apenas em clusters de Tecidos de Serviço implantados no Azure, e apenas para aplicações implementadas como recursos Azure; uma aplicação que não seja implementada como recurso Azure não pode ser atribuída uma identidade. Conceptualmente falando, o apoio a identidades geridas num cluster azure service fabric consiste em duas fases:

1. Atribuir uma ou mais identidades geridas ao recurso de aplicação; Pode ser atribuída uma aplicação com uma única identidade atribuída ao sistema e/ou até 32 identidades atribuídas ao utilizador, respectivamente.

2. Dentro da definição da aplicação, mapeie uma das identidades atribuídas à aplicação a qualquer serviço individual que coma a aplicação.

A identidade atribuída pelo sistema de uma aplicação é única a essa aplicação; uma identidade atribuída ao utilizador é um recurso autónomo, que pode ser atribuído a várias aplicações. Dentro de uma aplicação, uma única identidade (atribuída pelo sistema ou atribuída ao utilizador) pode ser atribuída a vários serviços da aplicação, mas cada serviço individual só pode ser atribuído a uma identidade. Por último, deve ser atribuído um serviço a uma identidade explicitamente para ter acesso a esta funcionalidade. Com efeito, o mapeamento das identidades de uma aplicação aos seus serviços constituintes permite o isolamento na aplicação — um serviço só pode utilizar a identidade mapeada para a mesma.  

Atualmente, os seguintes cenários são suportados para esta funcionalidade:

- Implementar uma nova aplicação com um ou mais serviços e uma ou mais identidades atribuídas

- Atribuir uma ou mais identidades geridas a uma aplicação existente (implantada pelo Azure) a fim de aceder aos recursos do Azure

Os seguintes cenários não são suportados ou não são recomendados; Note que estas ações podem não ser bloqueadas, mas podem levar a interrupções nas suas aplicações:

- Remover ou alterar as identidades atribuídas a um pedido; se tiver de fazer alterações, submeta-se a uma nova atribuição de identidade e, em seguida, a remover uma anteriormente atribuída. A remoção de uma identidade de uma aplicação existente pode ter efeitos indesejáveis, incluindo deixar a sua aplicação num estado que não é atualizável. É seguro suprimir completamente o pedido se for necessária a remoção de uma identidade; Note que eliminará a identidade atribuída pelo sistema (se tal for definida) associada à aplicação, e removerá quaisquer associações com as identidades atribuídas ao utilizador atribuídas à aplicação.

- O suporte do Serviço Tecido para identidades geridas não está integrado neste momento no [AzureServiceTokenProvider](../key-vault/general/service-to-service-authentication.md).

## <a name="next-steps"></a>Passos seguintes

- [Implementar um novo cluster azure service fabric com suporte de identidade gerido](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [Ativar suporte de identidade gerido num cluster de tecido de serviço Azure existente](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [Implementar uma aplicação Azure Service Fabric com uma identidade gerida atribuída pelo sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [Implementar uma aplicação Azure Service Fabric com uma identidade gerida atribuída pelo utilizador](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [Alavancar a identidade gerida de uma aplicação Service Fabric a partir do código de serviço](./how-to-managed-identity-service-fabric-app-code.md)
- [Conceder um acesso à aplicação Azure Service Fabric a outros recursos azure](./how-to-grant-access-other-resources.md)
- [Declarar e usar segredos de aplicação como KeyVaultReferences](./service-fabric-keyvault-references.md)
