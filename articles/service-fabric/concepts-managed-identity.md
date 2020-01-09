---
title: Visão geral da identidade gerenciada Service Fabric
description: Este artigo é uma visão geral da identidade gerenciada e seus aplicativos para o Azure Service Fabric.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: dc7dafa59596537456accde66e878c06f9e5ca23
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75528152"
---
# <a name="managed-identity-for-service-fabric-application-preview"></a>Identidade gerenciada para o aplicativo Service Fabric (versão prévia)

Um desafio comum inerente à criação de aplicações na cloud passa pela gestão das credenciais que estão no seu código para a autenticação nos serviços cloud. Manter as credenciais seguras é uma tarefa importante, pois elas nunca aparecem em estações de trabalho do desenvolvedor e não são verificadas no controle do código-fonte. O recurso de identidade gerenciada para recursos do Azure no Azure Active Directory (Azure AD) resolve esse problema. Oferece aos serviços do Azure uma identidade gerida automaticamente no Azure AD. Pode utilizar essa identidade para autenticar em qualquer serviço que suporte a autenticação do Azure AD, incluindo o Key Vault, sem ser necessário ter credenciais no seu código.

O recurso de identidade gerenciada para recursos do Azure é gratuito com o Azure AD para assinaturas do Azure. Não há nenhum custo adicional.

> [!NOTE]
> Identidade gerenciada para recursos do Azure é o novo nome para o serviço anteriormente conhecido como Identidade de Serviço Gerenciada (MSI).

## <a name="terminology"></a>Terminologia

Os termos a seguir são usados em todo o conjunto de documentação de identidade gerenciada para recursos do Azure:

- **ID do cliente** – um identificador exclusivo gerado pelo Azure AD que está vinculado a um aplicativo e uma entidade de serviço durante seu provisionamento inicial (também veja a [ID do aplicativo](/azure/active-directory/develop/developer-glossary#application-id-client-id)).

- **ID da entidade de segurança** – a ID de objeto do objeto de entidade de serviço para sua identidade gerenciada que é usada para conceder acesso baseado em função a um recurso do Azure.

- **Entidade de serviço** -um objeto Azure Active Directory, que representa a projeção de um aplicativo AAD em um determinado locatário (também consulte a [entidade de serviço](../active-directory/develop/developer-glossary.md#service-principal-object).)


## <a name="about-managed-identities-in-azure"></a>Sobre identidades gerenciadas no Azure

- [Tipos de identidade gerenciada (MI) no Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work)

- [Como funciona a identidade gerenciada atribuída pelo sistema no Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-system-assigned-managed-identity-works-with-an-azure-vm)

- [Como funciona a MI (identidade gerenciada) definida pelo usuário no Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-user-assigned-managed-identity-works-with-an-azure-vm)


## <a name="supported-scenarios-for-service-fabric-applications"></a>Cenários com suporte para aplicativos Service Fabric

As identidades gerenciadas para Service Fabric só têm suporte em clusters de Service Fabric implantados no Azure e somente para aplicativos implantados como recursos do Azure; um aplicativo que não é implantado como um recurso do Azure não pode ser atribuído a uma identidade. Falando conceitualmente, o suporte para identidades gerenciadas no Cluster Service Fabric do Azure consiste em duas fases:

1. Atribuir uma ou mais identidades gerenciadas ao recurso de aplicativo; um aplicativo pode receber uma única identidade atribuída pelo sistema e/ou até 32 identidades atribuídas pelo usuário, respectivamente.

2. Na definição do aplicativo, mapeie uma das identidades atribuídas ao aplicativo para qualquer serviço individual que compõe o aplicativo.

A identidade atribuída pelo sistema de um aplicativo é exclusiva para esse aplicativo; uma identidade atribuída pelo usuário é um recurso autônomo, que pode ser atribuído a vários aplicativos. Dentro de um aplicativo, uma única identidade (se atribuído pelo sistema ou atribuído pelo usuário) pode ser atribuída a vários serviços do aplicativo, mas cada serviço pode ser atribuído apenas a uma identidade. Por fim, um serviço deve ser atribuído explicitamente a uma identidade para ter acesso a esse recurso. Na verdade, o mapeamento das identidades de um aplicativo para seus serviços constituintes permite um isolamento no aplicativo-um serviço pode usar apenas a identidade mapeada para ele (e nenhum, se não tiver sido explicitamente atribuído a ele).  

A lista de cenários com suporte para a versão de visualização é a seguinte:

   - Implantar um novo aplicativo com um ou mais serviços e uma ou mais identidades atribuídas

   - Atribua uma ou mais identidades gerenciadas a um aplicativo existente a fim de acessar os recursos do Azure; o aplicativo deve ter sido implantado como um recurso do Azure em si


Os cenários a seguir não têm suporte ou não são recomendados; Observe que essas ações podem não ser bloqueadas, mas podem levar a interrupções em seus aplicativos:

   - Remover ou alterar as identidades atribuídas a um aplicativo; Se você precisar fazer alterações, envie implantações separadas para primeiro adicionar uma nova atribuição de identidade e, em seguida, para remover uma anteriormente atribuída. A remoção de uma identidade de um aplicativo existente pode ter efeitos indesejáveis, incluindo deixar seu aplicativo em um estado que não seja atualizável. É seguro excluir o aplicativo completamente se a remoção de uma identidade for necessária; Observação isso excluirá a identidade atribuída pelo sistema (se for definida) associada ao aplicativo e removerá todas as associações com as identidades atribuídas ao usuário atribuídas ao aplicativo.

   - O suporte ao it para identidades gerenciadas não está integrado no momento ao [o azureservicetokenprovider](../key-vault/service-to-service-authentication.md); a integração será alcançada até o final do período de visualização do recurso de identidade gerenciada.

>
> [!NOTE]
>
> Este recurso está em visualização; como tal, ele pode estar sujeito a alterações frequentes e pode não ser adequado para implantações de produção.

## <a name="next-steps"></a>Passos seguintes
* [Implantar um novo cluster de Service Fabric do Azure com suporte de identidade gerenciada](./configure-new-azure-service-fabric-enable-managed-identity.md) 
* [Habilitar o suporte de identidade gerenciada em um cluster existente do Azure Service Fabric](./configure-existing-cluster-enable-managed-identity-token-service.md)
* [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo usuário](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Aproveite a identidade gerenciada de um aplicativo Service Fabric do código de serviço](./how-to-managed-identity-service-fabric-app-code.md)
* [Conceder a um aplicativo de Service Fabric do Azure acesso a outros recursos do Azure](./how-to-grant-access-other-resources.md)
* [Declarando e usando os segredos do aplicativo como KeyVaultReferences](./service-fabric-keyvault-references.md) 
