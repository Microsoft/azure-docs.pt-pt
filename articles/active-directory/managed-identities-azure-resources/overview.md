---
title: Identidades geridas para os recursos do Azure
description: Descrição geral das identidades geridas para os recursos do Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5a9f83a8f6e8d5e83f30d30da2b1c4c0f722dc3
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063043"
---
# <a name="what-is-managed-identities-for-azure-resources"></a>O que são as identidades geridas para os recursos do Azure?

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Um desafio comum inerente à criação de aplicações na cloud passa pela gestão das credenciais que estão no seu código para a autenticação nos serviços cloud. Manter essas credenciais protegidas é uma tarefa importante. Idealmente, nunca aparecem nas estações de trabalho dos programadores nem são verificadas no controlo de origem. O Azure Key Vault oferece uma forma de armazenar credenciais, segredos e outras chaves em segurança, mas o código tem de se autenticar no Key Vault para poder obtê-los. 

A funcionalidade de identidades geridas para recursos do Azure no Active Directory (Azure AD) resolve este problema. Oferece aos serviços do Azure uma identidade gerida automaticamente no Azure AD. Pode utilizar essa identidade para autenticar em qualquer serviço que suporte a autenticação do Azure AD, incluindo o Key Vault, sem ser necessário ter credenciais no seu código.

A funcionalidade de identidades geridas para recursos do Azure é gratuita com o Azure AD para subscrições do Azure. Não há custos adicionais.

> [!NOTE]
> Identidades geridas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como Identidade de Serviço Gerida (MSI).

## <a name="terminology"></a>Terminologia

Os termos a seguir são usados em todo o conjunto de documentação de identidades gerenciadas para recursos do Azure:

- **ID do cliente** – um identificador exclusivo gerado pelo Azure AD que está vinculado a um aplicativo e uma entidade de serviço durante seu provisionamento inicial.
- **ID da entidade de segurança** – a ID de objeto do objeto de entidade de serviço para sua identidade gerenciada que é usada para conceder acesso baseado em função a um recurso do Azure.
- **IMDS (serviço de metadados de instância) do Azure** – um ponto de extremidade REST acessível a todas as VMs de IaaS criadas por meio do Azure Resource Manager. O ponto de extremidade está disponível em um endereço IP não roteável e conhecido (169.254.169.254) que pode ser acessado somente de dentro da VM.

## Como funcionam as identidades gerenciadas dos recursos do Azure?<a name="how-does-it-work"></a>

Existem dois tipos de identidades geridas:

- Uma **identidade gerida atribuída pelo sistema**, que é ativada diretamente numa instância de um serviço do Azure. Quando ativada, o Azure cria uma identidade para a instância no inquilino do Azure AD no qual a subscrição da instância confia. Assim que a identidade for criada, as credenciais são aprovisionadas na instância. O ciclo de vida das identidades atribuídas pelo sistema está diretamente ligado à instância do serviço do Azure nas quais estão ativadas. Se a instância for eliminada, o Azure limpa automaticamente as credenciais e a identidade no Azure AD.
- Uma **identidade gerida atribuída pelo utilizador**, que é criada como um recurso do Azure autónomo. Através de um processo de criação, o Azure cria uma identidade no inquilino do Azure AD no qual a subscrição que está a ser utilizada confia. Depois de criada, a identidade pode ser atribuída a uma ou mais instâncias do serviço do Azure. O ciclo de vida das identidades atribuídas pelo utilizador é gerido separadamente do ciclo de vida das instâncias do serviço do Azure ao qual estão atribuídas.

Internamente, identidades gerenciadas são entidades de serviço de um tipo especial, que são bloqueadas para serem usadas somente com recursos do Azure. Quando a identidade gerenciada é excluída, a entidade de serviço correspondente é automaticamente removida. 

O seu código pode utilizar uma identidade gerida para pedir tokens de acesso para serviços que suportem a autenticação do Azure AD. O Azure encarrega-se da implementação das credenciais que a instância do serviço utiliza. 

O diagrama seguinte mostra como é que as identidades de serviço geridas funcionam com as máquinas virtuais (VMs) do Azure:

![Identidades de Serviço Geridas e VMs do Azure](media/overview/msi-vm-vmextension-imds-example.png)

|  Propriedade    | Identidade gerenciada atribuída pelo sistema | Identidade gerenciada atribuída pelo usuário |
|------|----------------------------------|--------------------------------|
| Criação |  Criado como parte de um recurso do Azure (por exemplo, uma máquina virtual do Azure ou um serviço de Azure App) | Criado como um recurso autônomo do Azure |
| Ciclo de vida | Ciclo de vida compartilhado com o recurso do Azure com o qual a identidade gerenciada é criada. <br/> Quando o recurso pai é excluído, a identidade gerenciada também é excluída. | Ciclo de vida independente. <br/> Deve ser excluído explicitamente. |
| Compartilhamento entre recursos do Azure | Não pode ser compartilhado. <br/> Ele só pode ser associado a um único recurso do Azure. | Pode ser compartilhado <br/> A mesma identidade gerenciada atribuída pelo usuário pode ser associada a mais de um recurso do Azure. |
| Casos de utilização comuns | Cargas de trabalho contidas em um único recurso do Azure <br/> Cargas de trabalho para as quais você precisa de identidades independentes. <br/> Por exemplo, um aplicativo que é executado em uma única máquina virtual | Cargas de trabalho que são executadas em vários recursos e que podem compartilhar uma única identidade. <br/> Cargas de trabalho que precisam de autorização prévia para um recurso seguro como parte de um fluxo de provisionamento. <br/> Cargas de trabalho em que os recursos são reciclados com frequência, mas as permissões devem permanecer consistentes. <br/> Por exemplo, uma carga de trabalho em que várias máquinas virtuais precisam acessar o mesmo recurso | 

### <a name="how-a-system-assigned-managed-identity-works-with-an-azure-vm"></a>Como funcionam as identidades geridas atribuídas pelo sistema com uma VM do Azure

1. O Azure Resource Manager recebe um pedido para ativar a identidade gerida atribuída pelo sistema numa VM.

2. O Azure Resource Manager cria um principal de serviço no Azure AD para a identidade da VM. O principal de serviço é criado no inquilino do Azure AD no qual a subscrição confia.

3. Azure Resource Manager configura a identidade na VM atualizando o ponto de extremidade de identidade do serviço de metadados da instância do Azure com a ID do cliente e o certificado da entidade de serviço.

4. Quando a VM tiver uma identidade, utilize as informações do principal de serviço para lhe conceder acesso aos recursos do Azure. Para chamar o Azure Resource Manager, utilize o controlo de acesso baseado em funções (RBAC) no Azure AD para atribuir a função adequada ao principal de serviço da VM. Para chamar o Key Vault, conceda ao seu código acesso ao segredo ou à chave específica no Key Vault.

5. Seu código em execução na VM pode solicitar um token do ponto de extremidade do serviço de metadados da instância do Azure, acessível somente de dentro da VM: `http://169.254.169.254/metadata/identity/oauth2/token`
    - O parâmetro do recurso especifica o serviço para o qual o token é enviado. Para autenticar no Azure Resource Manager, utilize `resource=https://management.azure.com/`.
    - O parâmetro de versão da API especifica a versão do IMDS; utilize api-version=2018-02-01 ou superior.

6. É feita uma chamada para o Azure AD a pedir uma token de acesso (conforme especificado no passo 5) através da utilização do ID de cliente e do certificado configurados no passo 3. O Azure AD devolve um token de acesso JSON Web Token (JWT).

7. O código envia o token de acesso numa chamada para um serviço que suporte a autenticação do Azure AD.

### <a name="how-a-user-assigned-managed-identity-works-with-an-azure-vm"></a>Como funcionam as identidades geridas atribuídas pelo utilizador com uma VM do Azure

1. O Azure Resource Manager recebe um pedido para criar uma identidade gerida atribuída pelo utilizador.

2. O Azure Resource Manager cria um principal de serviço no Azure AD para a identidade gerida atribuída pelo utilizador. O principal de serviço é criado no inquilino do Azure AD no qual a subscrição confia.

3. Azure Resource Manager recebe uma solicitação para configurar a identidade gerenciada atribuída pelo usuário em uma VM e atualiza o ponto de extremidade de identidade do serviço de metadados de instância do Azure com a ID do cliente e o certificado da entidade de serviço de identidade gerenciada atribuída pelo usuário.

4. Quando a identidade gerida atribuída pelo utilizador estiver criada, utilize as informações do principal de serviço para lhe conceder acesso aos recursos do Azure. Para chamar o Azure Resource Manager, utilize RBAC no Azure AD para atribuir a função adequada ao principal de serviço da identidade atribuída pelo utilizador. Para chamar o Key Vault, conceda ao seu código acesso ao segredo ou à chave específica no Key Vault.

   > [!Note]
   > Também pode concluir este passo antes do passo 3.

5. Seu código em execução na VM pode solicitar um token do ponto de extremidade de identidade do serviço de metadados da instância do Azure, acessível somente de dentro da VM: `http://169.254.169.254/metadata/identity/oauth2/token`
    - O parâmetro do recurso especifica o serviço para o qual o token é enviado. Para autenticar no Azure Resource Manager, utilize `resource=https://management.azure.com/`.
    - O parâmetro de ID de cliente especifica a identidade para a qual o token é pedido. Este valor é necessário para eliminar ambiguidades se uma única VM tiver mais de uma identidade atribuída pelo utilizador.
    - O parâmetro da versão da API especifica a versão do Azure Instance Metadata Service. Utilize `api-version=2018-02-01` ou superior.

6. É feita uma chamada para o Azure AD a pedir uma token de acesso (conforme especificado no passo 5) através da utilização do ID de cliente e do certificado configurados no passo 3. O Azure AD devolve um token de acesso JSON Web Token (JWT).
7. O código envia o token de acesso numa chamada para um serviço que suporte a autenticação do Azure AD.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Como posso utilizar as identidades geridas para os recursos do Azure?

Para saber como pode utilizar as identidades geridas para aceder a diferentes recursos do Azure, experimente os seguintes tutoriais.

> [!NOTE]
> Confira o curso [implementando identidades gerenciadas para Microsoft Azure de recursos](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing) para obter mais informações sobre identidades gerenciadas, incluindo instruções detalhadas em vídeo sobre vários cenários com suporte.

Saiba como utilizar uma identidade gerida com uma VM do Windows:

* [Aceder ao Azure Data Lake Store](tutorial-windows-vm-access-datalake.md)
* [Aceder ao Azure Resource Manager](tutorial-windows-vm-access-arm.md)
* [Aceder ao SQL do Azure](tutorial-windows-vm-access-sql.md)
* [Aceder ao Armazenamento do Azure com uma chave de acesso](tutorial-windows-vm-access-storage.md)
* [Access Azure Storage by using shared access signatures](tutorial-windows-vm-access-storage-sas.md) (Aceder ao Armazenamento do Azure com assinaturas de acesso partilhado)
* [Aceder a um recurso não Azure com o Azure Key Vault](tutorial-windows-vm-access-nonaad.md)

Saiba como utilizar uma identidade gerida com uma VM do Linux:

* [Acessar o registro de contêiner do Azure](../../container-registry/container-registry-authentication-managed-identity.md)
* [Aceder ao Azure Data Lake Store](tutorial-linux-vm-access-datalake.md)
* [Aceder ao Azure Resource Manager](tutorial-linux-vm-access-arm.md)
* [Aceder ao Armazenamento do Azure com uma chave de acesso](tutorial-linux-vm-access-storage.md)
* [Access Azure Storage by using shared access signatures](tutorial-linux-vm-access-storage-sas.md) (Aceder ao Armazenamento do Azure com assinaturas de acesso partilhado)
* [Aceder a um recurso não Azure com o Azure Key Vault](tutorial-linux-vm-access-nonaad.md)

Saiba como utilizar uma identidade gerida com outros serviços do Azure:

* [App Service do Azure](/azure/app-service/overview-managed-identity)
* [Gestão de API do Azure](../../api-management/api-management-howto-use-managed-service-identity.md)
* [Azure Container Instances](../../container-instances/container-instances-managed-identity.md)
* [Tarefas do registro de contêiner do Azure](../../container-registry/container-registry-tasks-authentication-managed-identity.md)
* [Azure Event Hubs](../../event-hubs/event-hubs-managed-service-identity.md)
* [Funções do Azure](/azure/app-service/overview-managed-identity)
* [Serviço Kubernetes do Azure](/azure/aks/use-managed-identity)
* [Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
* [Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md)


## Que serviços do Azure suportam a funcionalidade?<a name="which-azure-services-support-managed-identity"></a>

As identidades geridas para recurso do Azure podem ser utilizadas para autenticação em serviços que suportem a autenticação do Azure AD. Para obter uma lista dos serviços do Azure que suportam a funcionalidade de identidades geridas para recursos do Azure, veja [Serviços que suportam as identidades geridas para recursos do Azure](services-support-msi.md).

## <a name="next-steps"></a>Passos seguintes

Comece a utilizar a funcionalidade de identidades geridas para recursos do Azure com os inícios rápidos seguintes:

* [Utilizar uma identidade gerida atribuída pelo sistema de VM do Windows para aceder ao Resource Manager](tutorial-windows-vm-access-arm.md)
* [Utilizar uma identidade gerida atribuída pelo sistema de VM do Linux para aceder ao Resource Manager](tutorial-linux-vm-access-arm.md)
