---
title: Identidades geridas para Azure
description: Saiba usar identidades geridas para Azure com Tecido de Serviço.
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 06ebcfdf3d6a3815908752153acb09437d745d15
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986755"
---
# <a name="using-managed-identities-for-azure-with-service-fabric-preview"></a>Utilização de identidades geridas para Azure com tecido de serviço (Pré-visualização)

Um desafio comum ao construir aplicações em nuvem é como gerir de forma segura as credenciais do seu código para autenticar vários serviços sem as guardar localmente numa estação de trabalho de desenvolvedores ou no controlo de fontes. *Identidades geridas para o Azure* resolvem este problema por todos os seus recursos no Azure Ative Directory (Azure AD), fornecendo-lhes identidades geridas automaticamente dentro da AD Azure. Pode utilizar a identidade de um serviço para autenticar qualquer serviço que suporte a autenticação Azure AD, incluindo o Key Vault, sem quaisquer credenciais armazenadas no seu código.

*As identidades geridas para os recursos Azure* são gratuitas com a Azure AD para subscrições azure. Não há custos adicionais.

> [!NOTE]
> *Identidades geridas para o Azure* é o novo nome para o serviço anteriormente conhecido como Identidade de Serviço Gerido (MSI).

## <a name="concepts"></a>Conceitos

As identidades geridas para o Azure baseiam-se em vários conceitos-chave:

- **ID do cliente** – um identificador exclusivo gerado pelo Azure AD que está vinculado a um aplicativo e uma entidade de serviço durante seu provisionamento inicial (também veja a [ID do aplicativo](/azure/active-directory/develop/developer-glossary#application-id-client-id)).

- **ID da entidade de segurança** – a ID de objeto do objeto de entidade de serviço para sua identidade gerenciada que é usada para conceder acesso baseado em função a um recurso do Azure.

- **Entidade de serviço** -um objeto Azure Active Directory, que representa a projeção de um aplicativo AAD em um determinado locatário (também consulte a [entidade de serviço](../active-directory/develop/developer-glossary.md#service-principal-object).)

Existem dois tipos de identidades geridas:

- Uma **identidade gerida atribuída** pelo Sistema é ativada diretamente numa instância de serviço Azure.  O ciclo de vida de uma identidade atribuída ao sistema é exclusivo da instância de serviço Azure em que está ativado.
- Uma **identidade gerida atribuída pelo utilizador**, que é criada como um recurso do Azure autónomo. A identidade pode ser atribuída a uma ou mais instâncias de serviço Azure e é gerida separadamente dos ciclos de vida desses casos.

Para compreender melhor a diferença entre os tipos de identidade [geridos, veja como funcionam as identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)

## <a name="supported-scenarios-for-service-fabric-applications"></a>Cenários com suporte para aplicativos Service Fabric

As identidades geridas para o Tecido de Serviço são suportadas apenas em clusters de Tecidos de Serviço implantados no Azure, e apenas para aplicações implementadas como recursos Azure; uma aplicação que não seja implementada como recurso Azure não pode ser atribuída uma identidade. Conceptualmente falando, o apoio a identidades geridas num cluster azure service fabric consiste em duas fases:

1. Atribuir uma ou mais identidades gerenciadas ao recurso de aplicativo; um aplicativo pode receber uma única identidade atribuída pelo sistema e/ou até 32 identidades atribuídas pelo usuário, respectivamente.

2. Na definição do aplicativo, mapeie uma das identidades atribuídas ao aplicativo para qualquer serviço individual que compõe o aplicativo.

A identidade atribuída pelo sistema de um aplicativo é exclusiva para esse aplicativo; uma identidade atribuída pelo usuário é um recurso autônomo, que pode ser atribuído a vários aplicativos. Dentro de um aplicativo, uma única identidade (se atribuído pelo sistema ou atribuído pelo usuário) pode ser atribuída a vários serviços do aplicativo, mas cada serviço pode ser atribuído apenas a uma identidade. Por fim, um serviço deve ser atribuído explicitamente a uma identidade para ter acesso a esse recurso. Com efeito, o mapeamento das identidades de uma aplicação aos seus serviços constituintes permite o isolamento na aplicação — um serviço só pode utilizar a identidade mapeada para a mesma.  

Atualmente, os seguintes cenários são suportados para esta funcionalidade de pré-visualização:

- Implementar uma nova aplicação com um ou mais serviços e uma ou mais identidades atribuídas

- Atribuir uma ou mais identidades geridas a uma aplicação existente (implantada pelo Azure) a fim de aceder aos recursos do Azure

Os cenários a seguir não têm suporte ou não são recomendados; Observe que essas ações podem não ser bloqueadas, mas podem levar a interrupções em seus aplicativos:

- Remover ou alterar as identidades atribuídas a um aplicativo; Se você precisar fazer alterações, envie implantações separadas para primeiro adicionar uma nova atribuição de identidade e, em seguida, para remover uma anteriormente atribuída. A remoção de uma identidade de um aplicativo existente pode ter efeitos indesejáveis, incluindo deixar seu aplicativo em um estado que não seja atualizável. É seguro excluir o aplicativo completamente se a remoção de uma identidade for necessária; Observação isso excluirá a identidade atribuída pelo sistema (se for definida) associada ao aplicativo e removerá todas as associações com as identidades atribuídas ao usuário atribuídas ao aplicativo.

- O suporte do Serviço Tecido para identidades geridas não está integrado neste momento no [AzureServiceTokenProvider;](../key-vault/service-to-service-authentication.md) a integração será alcançada até ao final do período de pré-visualização para a funcionalidade de identidade gerida.

>
> [!NOTE]
>
> Esta funcionalidade está em pré-visualização. Pode estar sujeito a alterações frequentes e não adequado para implantações de produção.

## <a name="next-steps"></a>Passos seguintes

- [Implantar um novo cluster de Service Fabric do Azure com suporte de identidade gerenciada](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [Habilitar o suporte de identidade gerenciada em um cluster existente do Azure Service Fabric](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo usuário](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [Aproveite a identidade gerenciada de um aplicativo Service Fabric do código de serviço](./how-to-managed-identity-service-fabric-app-code.md)
- [Conceder a um aplicativo de Service Fabric do Azure acesso a outros recursos do Azure](./how-to-grant-access-other-resources.md)
- [Declarando e usando os segredos do aplicativo como KeyVaultReferences](./service-fabric-keyvault-references.md)
