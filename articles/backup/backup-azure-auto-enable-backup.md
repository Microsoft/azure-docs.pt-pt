---
title: Ativar automaticamente a cópia de segurança no momento da criação da VM com o Azure Policy
description: Um artigo que descreve como usar a Política Azure para permitir automaticamente a cópia de segurança para todos os VMs criados num determinado âmbito
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1d423371788f87155328e8f5c9334e47349a68e8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77584273"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Ativar automaticamente a cópia de segurança no momento da criação da VM com o Azure Policy

Uma das principais responsabilidades de um Administrador de Backup ou Compliance numa organização é garantir que todas as máquinas críticas de negócio sejam apoiadas com a retenção adequada.

Hoje em dia, o Azure Backup fornece uma política incorporada (usando a Política Azure) que pode ser atribuída a **todos os VMs Azure em um local especificado dentro de um grupo de subscrição ou recursos**. Quando esta política é atribuída a um determinado âmbito, todos os novos VMs criados nesse âmbito são automaticamente configurados para cópia de segurança para um **cofre existente no mesmo local e subscrição**. O utilizador pode especificar o cofre e a política de retenção à qual os VMs de retenção devem ser associados.

## <a name="supported-scenarios"></a>Cenários Suportados

* Atualmente, a política incorporada é apoiada apenas para os VMs Azure. Os utilizadores devem ter o cuidado de garantir que a política de retenção especificada durante a atribuição é uma política de retenção em VM. Consulte [este](https://docs.microsoft.com/azure/backup/backup-azure-policy-supported-skus) documento para ver todos os SKUs VM apoiados por esta política.

* A apólice pode ser atribuída a um único local e subscrição de cada vez. Para permitir a cópia de segurança dos VMs em locais e subscrições, é necessário criar várias instâncias da atribuição de políticas, uma para cada combinação de localização e subscrição.

* O cofre especificado e os VM configurados para cópia de segurança podem estar em diferentes grupos de recursos.

* O âmbito do Grupo de Gestão não é atualmente suportado.

* A política incorporada não está atualmente disponível nas nuvens nacionais.

## <a name="using-the-built-in-policy"></a>Usando a política incorporada

Para atribuir a apólice ao âmbito exigido, siga os passos abaixo:

1. Inscreva-se no Portal Azure e navegue para o **Painel de Política.**
2. Selecione **Definições** no menu esquerdo para obter uma lista de todas as políticas incorporadas em todos os Recursos Azure.
3. Filtrar a lista para **categoria=backup**. Verá a lista filtrada para uma única política chamada "Configurar backup em VMs de um local para um Cofre Central existente no mesmo local".
![Painel de Política](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Clique no nome da apólice. Será redirecionado para a definição detalhada para esta política.
![Lâmina de definição de política](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Clique no botão **Atribuir** na parte superior da lâmina. Isto redireciona-o para a lâmina **''Atribuir'.**
6. Em **Basics**, clique nos três pontos junto ao campo **Âmbito.** Isto abre uma lâmina de contexto certa onde pode selecionar a subscrição para a política a aplicar. Também pode selecionar opcionalmente um grupo de recursos, de modo a que a política seja aplicada apenas para VMs num determinado grupo de recursos.
![Básicos de atribuição de políticas](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. No separador **Parâmetros,** escolha uma localização a partir do drop-down e selecione a política de cofre e backup à qual os VMs no âmbito devem estar associados.
![Parâmetros de atribuição de políticas](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Certifique-se de que **o Efeito** está definido para implementarifNotExists.
9. Navegar para **Revisão+criar** e clicar **Criar.**

> [!NOTE]
>
> A Política Azure também pode ser utilizada em VMs existentes, utilizando [a reparação](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources).

> [!NOTE]
>
> Recomenda-se que esta política não seja atribuída a mais de 200 VMs de cada vez. Se a apólice for atribuída a mais de 200 VMs, pode resultar no disparo de backup algumas horas mais tarde do que o especificado no horário.

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais sobre a Política Azure](https://docs.microsoft.com/azure/governance/policy/overview)
