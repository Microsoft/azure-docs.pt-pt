---
title: Identidades geridas para Azure
description: Saiba como utilizar identidades geridas para Azure com Tecido de Serviço.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 28c992792d4572a43e12f5d32855f8411b0f4c6f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574756"
---
# <a name="using-managed-identities-for-azure-with-service-fabric"></a>Utilização de identidades geridas para Azure com Tecido de Serviço

Um desafio comum ao construir aplicações em nuvem é como gerir de forma segura as credenciais no seu código para autenticar vários serviços sem as guardar localmente numa estação de trabalho do desenvolvedor ou no controlo de fontes. *Identidades geridas para a Azure* resolvem este problema para todos os seus recursos no Azure Ative Directory (Azure AD), fornecendo-lhes identidades geridas automaticamente dentro do Azure AD. Pode utilizar a identidade de um serviço para autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Key Vault, sem quaisquer credenciais armazenadas no seu código.

*As identidades geridas para os recursos Azure* são gratuitas com Azure AD para subscrições Azure. Não há custos adicionais.

> [!NOTE]
> *Identidades geridas para Azure* é o novo nome para o serviço anteriormente conhecido como Identidade de Serviço Gerido (MSI).

## <a name="concepts"></a>Conceitos

As identidades geridas para a Azure baseiam-se em vários conceitos-chave:

- **ID do cliente** - um identificador único gerado pela Azure AD que está ligado a um principal de aplicação e serviço durante o seu fornecimento inicial (ver também [iD de aplicação](../active-directory/develop/developer-glossary.md#application-id-client-id).)

- **ID principal** - o iD do objeto principal do serviço para a sua Identidade Gerida que é usado para conceder acesso baseado em funções a um recurso Azure.

- **Diretor de Serviço** - um objeto Azure Ative Directory, que representa a projeção de um pedido de AAD num dado inquilino (ver também [o principal do serviço](../active-directory/develop/developer-glossary.md#service-principal-object).)

Existem dois tipos de identidades geridas:

- Uma **identidade gerida atribuída pelo sistema** é ativada diretamente numa instância de serviço Azure.  O ciclo de vida de uma identidade atribuída ao sistema é exclusivo da instância de serviço do Azure em que está ativado.
- Uma **identidade gerida atribuída pelo utilizador**, que é criada como um recurso do Azure autónomo. A identidade pode ser atribuída a uma ou mais instâncias de serviço Azure e é gerida separadamente dos ciclos de vida desses casos.

Para compreender melhor a diferença entre tipos de identidade [geridos, veja como funcionam as identidades geridas para os recursos Azure?](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)

## <a name="supported-scenarios-for-service-fabric-applications"></a>Cenários suportados para aplicações de Tecido de Serviço

As identidades geridas para o Tecido de Serviço são suportadas apenas em clusters de tecido de serviço implantados pela Azure, e apenas para aplicações implementadas como recursos Azure; uma aplicação que não seja implantada como recurso Azure não pode ser atribuída uma identidade. Conceptualmente falando, o apoio a identidades geridas num cluster de tecido de serviço Azure consiste em duas fases:

1. Atribuir uma ou mais identidades geridas ao recurso de aplicação; uma aplicação pode ser atribuída a uma única identidade atribuída ao sistema e/ou até 32 identidades atribuídas pelo utilizador, respectivamente.

2. Dentro da definição da aplicação, mapear uma das identidades atribuídas à aplicação a qualquer serviço individual que inclua a aplicação.

A identidade atribuída ao sistema de uma aplicação é exclusiva dessa aplicação; uma identidade atribuída ao utilizador é um recurso autónomo, que pode ser atribuído a múltiplas aplicações. Dentro de uma aplicação, uma única identidade (atribuída ao sistema ou atribuída ao utilizador) pode ser atribuída a múltiplos serviços da aplicação, mas cada serviço individual só pode ser atribuído a uma identidade. Por último, deve ser atribuído um serviço a uma identidade que tenha acesso a esta funcionalidade. Com efeito, o mapeamento das identidades de uma aplicação aos seus serviços constitutivo permite o isolamento na aplicação — um serviço só pode utilizar a identidade mapeada para o mesmo.  

Atualmente, os seguintes cenários são suportados para esta funcionalidade:

- Implementar uma nova aplicação com um ou mais serviços e uma ou mais identidades atribuídas

- Atribuir uma ou mais identidades geridas a uma aplicação existente (Azure-implementada) para aceder aos recursos da Azure

Os seguintes cenários não são suportados ou não recomendados; note que estas ações podem não estar bloqueadas, mas podem levar a interrupções nas suas aplicações:

- Remover ou alterar as identidades atribuídas a uma aplicação; se tiver de estois, submeter implementações separadas para primeiro adicionar uma nova atribuição de identidade e, em seguida, remover uma já atribuída. A remoção de uma identidade de uma aplicação existente pode ter efeitos indesejáveis, incluindo deixar a sua aplicação num estado que não seja atualizável. É seguro suprimir completamente o pedido se for necessária a remoção de uma identidade; note que isto eliminará a identidade atribuída ao sistema (se assim for definida) associada à aplicação, e removerá quaisquer associações com as identidades atribuídas ao utilizador atribuídas à aplicação.

- O suporte do Tecido de Serviço para identidades geridas não está integrado neste momento no [AzureServiceTokenProvider](../key-vault/general/service-to-service-authentication.md).

## <a name="next-steps"></a>Passos seguintes

- [Implementar um novo cluster de tecidos de serviço Azure com suporte de identidade gerido](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [Permitir o suporte de identidade gerido num cluster de tecido de serviço Azure existente](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [Implementar uma aplicação Azure Service Fabric com uma identidade gerida atribuída ao sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [Implementar uma aplicação Azure Service Fabric com uma identidade gerida atribuída pelo utilizador](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [Alavancar a identidade gerida de uma aplicação de Tecido de Serviço a partir do código de serviço](./how-to-managed-identity-service-fabric-app-code.md)
- [Conceder a uma aplicação Azure Service Fabric acesso a outros recursos da Azure](./how-to-grant-access-other-resources.md)
- [Declarar e usar segredos de aplicações como KeyVaultReferences](./service-fabric-keyvault-references.md)
