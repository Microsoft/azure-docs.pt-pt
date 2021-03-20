---
title: Configurar uma identidade de laboratório em Azure DevTest Labs
description: Aprenda a configurar uma identidade de laboratório em Azure DevTest.
ms.topic: article
ms.date: 08/20/2020
ms.openlocfilehash: a652eb5751f9b723911a1c1baaaaf9860febc5b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88719683"
---
# <a name="configure-a-lab-identity"></a>Configure uma identidade de laboratório

Um desafio comum inerente à criação de aplicações na cloud passa pela gestão das credenciais que estão no seu código para a autenticação nos serviços cloud. Manter essas credenciais protegidas é uma tarefa importante. Idealmente, nunca aparecem nas estações de trabalho dos programadores nem são verificadas no controlo de origem. O Azure Key Vault fornece uma forma de armazenar credenciais, segredos e outras chaves de forma segura, mas o seu código deve autenticar-se no Key Vault para as recuperar. 

As identidades geridas para recursos Azure apresentam-se no Azure Ative Directory (Azure AD) para resolver este problema. Oferece aos serviços do Azure uma identidade gerida automaticamente no Azure AD. Pode utilizar essa identidade para autenticar em qualquer serviço que suporte a autenticação do Azure AD, incluindo o Key Vault, sem ser necessário ter credenciais no seu código. Saiba mais sobre [identidades geridas no Azure.](../active-directory/managed-identities-azure-resources/overview.md) 

Existem dois tipos de identidades geridas: 

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema  

Uma **identidade gerida atribuída pelo sistema** é ativada diretamente numa instância de serviço   Azure. Quando ativada, o Azure cria uma identidade para a instância no inquilino do Azure AD no qual a subscrição da instância confia. Assim que a identidade for criada, as credenciais são aprovisionadas na instância. O ciclo de vida de uma identidade atribuída ao sistema está diretamente ligado à instância de serviço do Azure em que está ativada. Se a instância for eliminada, o Azure limpa automaticamente as credenciais e a identidade no Azure AD. 

### <a name="scenarios-for-using-labs-system-assigned-identity"></a>Cenários para usar o sistema de laboratório atribuído identidade  

Todos os Laboratórios DevTest são criados com uma identidade atribuída ao sistema que permanece válida para a vida do laboratório. A identidade atribuída ao sistema é utilizada para os seguintes fins:  

- Todas as implementações baseadas em [Recursos Azure](devtest-lab-create-environment-from-arm.md) usadas para girar multi-VM e/ou plataforma como um ambiente de serviço será executado usando o sistema do laboratório identidade atribuída  
- A encriptação do disco para discos de laboratório usando uma chave gerida pelo cliente é suportada através da identidade atribuída pelo sistema do laboratório. Ao fornecer acesso explícito à identidade do laboratório para aceder ao seu conjunto de encriptação de discos, o laboratório pode encriptar todos os discos de máquinas virtuais em seu nome. Saiba mais sobre [como ativar a encriptação do disco](encrypt-disks-customer-managed-keys.md) para os seus discos de laboratório utilizando uma chave gerida pelo cliente.  

### <a name="configure-identity"></a>Identidade configure

Esta secção demonstra como configurar a política de identidade do laboratório.

> [!NOTE]
> Para os laboratórios criados antes de 8/10/2020, a identidade atribuída ao sistema será definida para Off. Como dono de laboratório, pode ligá-lo, caso pretenda usar laboratórios para os fins listados na secção anterior.  
>
> Para novos laboratórios criados após 8/10/2020, a identidade atribuída ao sistema do laboratório está definida para On por padrão e o proprietário do laboratório não será capaz de desativar isto para o ciclo de vida do laboratório.  

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure por **Laboratórios DevTest.**
1. Da lista de laboratórios, selecione o laboratório que quiser.
1. Selecione **Configuração e políticas**  ->  **Identidade (Pré-visualização)**. 

> [!div class="mx-imgBorder"]
> ![Identidade configure](./media/configure-lab-identity/configure-identity.png)

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador  

Uma identidade gerida atribuída ao utilizador é criada como um recurso autónomo do Azure. Através de um processo de criação, o Azure cria uma identidade no inquilino do Azure AD no qual a subscrição que está a ser utilizada confia. Depois de criada, a identidade pode ser atribuída a uma ou mais instâncias do serviço do Azure. O ciclo de vida de uma identidade atribuída ao utilizador é gerido separadamente do ciclo de vida das instâncias de serviço Azure às quais é atribuído. 

A DevTest Labs suporta identidades atribuídas pelo utilizador tanto para máquinas virtuais como para ambientes baseados em Recursos Azure.  Para obter mais informações, consulte os seguintes tópicos:

- [Adicione uma identidade atribuída ao utilizador para implementar ambientes de gestor de recursos do laboratório Azure](use-managed-identities-environments.md)
- [Adicione identidades atribuídas ao utilizador para implementar máquinas virtuais de laboratório](enable-managed-identities-lab-vms.md)

## <a name="next-steps"></a>Passos seguintes

[Revisão Configurar gestão de custos](devtest-lab-configure-cost-management.md)