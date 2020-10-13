---
title: Ativar automaticamente a cópia de segurança no momento da criação da VM com o Azure Policy
description: Um artigo que descreve como usar a Política Azure para permitir automaticamente a cópia de segurança para todos os VMs criados num determinado âmbito
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: de4923000bc842203535e03727fd532c67a8f517
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88826077"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Ativar automaticamente a cópia de segurança no momento da criação da VM com o Azure Policy

Uma das principais responsabilidades de um Administrador de Backup ou Compliance numa organização é garantir que todas as máquinas críticas de negócio sejam apoiadas com a retenção adequada.

Hoje em dia, o Azure Backup fornece uma política incorporada (usando a Política Azure) que pode ser atribuída a **todos os VMs Azure em um local especificado dentro de um grupo de subscrição ou recursos**. Quando esta política é atribuída a um determinado âmbito, todos os novos VMs criados nesse âmbito são automaticamente configurados para cópia de segurança para um **cofre existente no mesmo local e subscrição**. O utilizador pode especificar o cofre e a política de retenção à qual os VMs apoiados devem estar associados.

## <a name="supported-scenarios"></a>Cenários Suportados

* Atualmente, a política incorporada é apoiada apenas para os VMs Azure. Os utilizadores devem ter o cuidado de garantir que a política de retenção especificada durante a atribuição é uma política de retenção em VM. Consulte [este](./backup-azure-policy-supported-skus.md) documento para ver todos os SKUs VM apoiados por esta política.

* A apólice pode ser atribuída a um único local e subscrição de cada vez. Para permitir a cópia de segurança dos VMs em locais e subscrições, é necessário criar várias instâncias da atribuição de políticas, uma para cada combinação de localização e subscrição.

* O cofre especificado e os VM configurados para cópia de segurança podem estar em diferentes grupos de recursos.

* O âmbito do Grupo de Gestão não é atualmente suportado.

* A política incorporada não está atualmente disponível nas nuvens nacionais.

## <a name="using-the-built-in-policy"></a>Usando a política incorporada

Para atribuir a apólice ao âmbito exigido, siga os passos abaixo:

1. Inscreva-se no portal Azure e navegue para o **Painel de Política.**
1. Selecione **Definições** no menu esquerdo para obter uma lista de todas as políticas incorporadas em todos os Recursos Azure.
1. Filtrar a lista para **categoria=backup**. Verá a lista filtrada para uma única política chamada "Configurar a cópia de segurança em VMs de um local para um Cofre Central existente no mesmo local".
![Painel de Política](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
1. Selecione o nome da apólice. Será redirecionado para a definição detalhada para esta apólice.
![Painel de definição de política](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
1. Selecione o botão **Atribuir** na parte superior do painel. Isto redireciona-o para o painel **de Política de Atribuição.**
1. Em **Basics**, selecione os três pontos junto ao campo **Scope.** Isto abre um painel de contexto certo onde pode selecionar a subscrição para a política a aplicar. Também pode selecionar opcionalmente um grupo de recursos, de modo a que a política seja aplicada apenas para VMs num determinado grupo de recursos.
![Básicos de atribuição de políticas](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
1. No separador **Parâmetros,** escolha uma localização a partir do drop-down e selecione a política de cofre e backup à qual os VMs no âmbito devem estar associados.
![Parâmetros de atribuição de políticas](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
1. Certifique-se de que **o Efeito** está definido para implementarifNotExists.
1. Navegar para **Rever+criar** e selecionar **Criar.**

> [!NOTE]
>
> A Política Azure também pode ser utilizada em VMs existentes, utilizando [a reparação](../governance/policy/how-to/remediate-resources.md).

> [!NOTE]
>
> Recomenda-se que esta política não seja atribuída a mais de 200 VMs de cada vez. Se a apólice for atribuída a mais de 200 VMs, pode resultar no desencadeamento da cópia de segurança algumas horas mais tarde do que o especificado no calendário.

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais sobre a Política Azure](../governance/policy/overview.md)
