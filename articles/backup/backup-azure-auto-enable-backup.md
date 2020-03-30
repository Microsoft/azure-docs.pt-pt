---
title: Ativar automaticamente a cópia de segurança no momento da criação da VM com o Azure Policy
description: Um artigo que descreve como usar a Política Azure para ativar automaticamente a cópia de segurança para todos os VMs criados num dado âmbito
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1d423371788f87155328e8f5c9334e47349a68e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584273"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Ativar automaticamente a cópia de segurança no momento da criação da VM com o Azure Policy

Uma das principais responsabilidades de um Backup ou Compliance Admin numa organização é garantir que todas as máquinas críticas ao negócio sejam apoiadas com a retenção adequada.

Hoje, a Azure Backup fornece uma política incorporada (utilizando a Política Azure) que pode ser atribuída a **todos os VMs Azure num local especificado dentro**de um grupo de subscrição ou recursos. Quando esta política é atribuída a um dado âmbito, todos os novos VMs criados nesse âmbito são automaticamente configurados para backup para um **cofre existente no mesmo local e subscrição**. O utilizador pode especificar o cofre e a política de retenção a que devem ser associados os VMs de apoio.

## <a name="supported-scenarios"></a>Cenários Suportados

* Atualmente, a política incorporada é apoiada apenas para os VMs Azure. Os utilizadores devem ter o cuidado de garantir que a política de retenção especificada durante a atribuição é uma política de retenção vm. Consulte [este](https://docs.microsoft.com/azure/backup/backup-azure-policy-supported-skus) documento para ver todas as VM SKUs apoiadas por esta política.

* A apólice pode ser atribuída a um único local e subscrição de cada vez. Para permitir o backup de VMs em locais e subscrições, é necessário criar várias instâncias da atribuição de políticas, uma para cada combinação de localização e subscrição.

* O cofre especificado e os VMs configurados para cópia de segurança podem estar em diferentes grupos de recursos.

* O âmbito do Grupo de Gestão não é atualmente suportado.

* A política incorporada não está atualmente disponível nas nuvens nacionais.

## <a name="using-the-built-in-policy"></a>Usando a política incorporada

Para atribuir a apólice ao âmbito necessário, siga os passos abaixo:

1. Inscreva-se no Portal Azure e navegue para o Painel **político.**
2. Selecione **Definições** no menu esquerdo para obter uma lista de todas as políticas incorporadas em todos os Recursos Azure.
3. Filtre a lista para **Categoria=Cópia de Segurança**. Verá a lista filtrada para uma única política chamada "Configure backup em VMs de um local para um Cofre Central existente no mesmo local".
![Painel de Política](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Clique no nome da apólice. Será redirecionado para a definição detalhada desta política.
![Lâmina de Definição de Política](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Clique no botão **Atribuir** na parte superior da lâmina. Isto redireciona-o para a lâmina política de **atribuição.**
6. Em **Basics,** clique nos três pontos junto ao campo **Scope.** Isto abre uma lâmina de contexto certa onde pode selecionar a subscrição para a política a aplicar. Também pode selecionar opcionalmente um grupo de recursos, para que a política seja aplicada apenas a VMs num determinado grupo de recursos.
![Fundamentos de Atribuição de Políticas](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. No separador **Parâmetros,** escolha um local a partir da queda e selecione a política de abóbada e de backup a que os VMs no âmbito devem ser associados.
![Parâmetros de atribuição de políticas](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Certifique-se de que o **Efeito** está definido para implementar IfNotExists.
9. Navegue para **Rever+criar** e clique **em Criar**.

> [!NOTE]
>
> A Política Azure também pode ser utilizada em VMs existentes, utilizando [a reparação.](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources)

> [!NOTE]
>
> Recomenda-se que esta política não seja atribuída a mais de 200 VMs de cada vez. Se a apólice for atribuída a mais de 200 VMs, pode resultar no disparo do backup algumas horas mais tarde do que o especificado pelo calendário.

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais sobre a Política De Azure](https://docs.microsoft.com/azure/governance/policy/overview)
