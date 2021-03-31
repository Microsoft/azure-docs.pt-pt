---
title: Ativar automaticamente a cópia de segurança no momento da criação da VM com o Azure Policy
description: Um artigo que descreve como usar a Política Azure para permitir automaticamente a cópia de segurança para todos os VMs criados num determinado âmbito
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: dfa4364eeaa9f5b60af3f5d6a19aaeb188d4f65e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101707307"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Ativar automaticamente a cópia de segurança no momento da criação da VM com o Azure Policy

Uma das principais responsabilidades de um Administrador de Backup ou Compliance numa organização é garantir que todas as máquinas críticas de negócio sejam apoiadas com a retenção adequada.

Hoje em dia, o Azure Backup fornece uma variedade de políticas incorporadas (utilizando a [Política Azure](../governance/policy/overview.md)) para ajudá-lo automaticamente a garantir que as suas máquinas virtuais Azure estão configuradas para cópias de segurança. Dependendo da forma como as suas equipas de backup e recursos são organizados, pode utilizar qualquer uma das políticas abaixo:

## <a name="policy-1---configure-backup-on-vms-without-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>Política 1 - Configurar backup em VMs sem uma etiqueta dada a um cofre de serviços de recuperação existente no mesmo local

Se a sua organização tiver uma equipa de backup central que gere backups em todas as equipas de aplicação, pode usar esta política para configurar o backup para um cofre dos Serviços de Recuperação central existente na mesma subscrição e localização que os VMs que estão sendo governados. Pode optar por **excluir** VMs que contenham uma determinada etiqueta, do âmbito desta política.

## <a name="policy-2---preview-configure-backup-on-vms-with-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>Política 2 - [Pré-visualização] Configurar backup em VMs com uma etiqueta dada a um cofre de serviços de recuperação existente no mesmo local
Esta política funciona da mesma forma que a Política 1 acima, sendo que a única diferença é que pode utilizar esta política para **incluir** VMs que contenham uma determinada etiqueta, no âmbito desta política. 

## <a name="policy-3---preview-configure-backup-on-vms-without-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>Política 3 - [Pré-visualização] Configurar backup em VMs sem uma etiqueta dada para um novo cofre de serviços de recuperação com uma política padrão
Se organiza aplicações em grupos de recursos dedicados e quer que sejam apoiadas pelo mesmo cofre, esta política permite-lhe gerir automaticamente esta ação. Pode optar por **excluir** VMs que contenham uma determinada etiqueta, do âmbito desta política.

## <a name="policy-4---preview-configure-backup-on-vms-with-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>Política 4 - [Pré-visualização] Configurar backup em VMs com uma etiqueta dada para um novo cofre de serviços de recuperação com uma política padrão
Esta política funciona da mesma forma que a Política 3 acima, sendo que a única diferença é que pode utilizar esta política para **incluir** VMs que contenham uma determinada etiqueta, no âmbito desta política. 

Além do acima, o Azure Backup também fornece uma política [apenas de auditoria](../governance/policy/concepts/effects.md#audit) - **Azure Backup deve ser ativado para máquinas virtuais**. Esta política identifica quais as máquinas virtuais que não têm backup ativado, mas não configura automaticamente cópias de segurança para estes VMs. Isto é útil quando se procura apenas avaliar a conformidade geral dos VMs, mas não pretende agir imediatamente.

## <a name="supported-scenarios"></a>Cenários Suportados

* Atualmente, a política incorporada é apoiada apenas para os VMs Azure. Os utilizadores devem ter o cuidado de garantir que a política de retenção especificada durante a atribuição é uma política de retenção em VM. Consulte [este](./backup-azure-policy-supported-skus.md) documento para ver todos os SKUs VM apoiados por esta política.

* As políticas 1 e 2 podem ser atribuídas a um único local e subscrição de cada vez. Para permitir a cópia de segurança dos VMs em locais e subscrições, é necessário criar várias instâncias da atribuição de políticas, uma para cada combinação de localização e subscrição.

* Para as políticas 1 e 2, o âmbito do grupo de gestão não é atualmente suportado.

* Para as políticas 1 e 2, o cofre especificado e os VMs configurados para backup podem estar sob diferentes grupos de recursos.

* As políticas 1, 2, 3 e 4 não estão atualmente disponíveis nas nuvens nacionais.

* As políticas 3 e 4 podem ser atribuídas a uma única subscrição de cada vez (ou a um grupo de recursos dentro de uma subscrição).

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="using-the-built-in-policies"></a>Usando as políticas incorporadas

Os passos abaixo descrevem o processo de ponta a ponta de atribuição da Política 1: Configurar a cópia de **segurança em VMs sem uma etiqueta dada a um cofre de serviços de recuperação existente no mesmo local** para um determinado âmbito. Instruções semelhantes serão aplicadas para as outras políticas. Uma vez atribuído, qualquer novo VM criado no âmbito é automaticamente configurado para cópia de segurança.

1. Inscreva-se no portal Azure e navegue para o **Painel de Política.**
2. Selecione **Definições** no menu esquerdo para obter uma lista de todas as políticas incorporadas em todos os Recursos Azure.
3. Filtrar a lista para **Categoria=Backup** e selecionar a política denominada "Configurar a cópia de segurança em VMs de uma localização para um Cofre central existente no mesmo local".
![Painel de Política](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Selecione o nome da apólice. Será redirecionado para a definição detalhada para esta apólice.
![Painel de definição de política](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Selecione o botão **Atribuir** na parte superior do painel. Isto redireciona-o para o painel **de Política de Atribuição.**
6. Em **Basics**, selecione os três pontos junto ao campo **Scope.** Isto abre um painel de contexto certo onde pode selecionar a subscrição para a política a aplicar. Também pode selecionar opcionalmente um grupo de recursos, de modo a que a política seja aplicada apenas para VMs num determinado grupo de recursos.
![Básicos de atribuição de políticas](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. No separador **Parâmetros,** escolha uma localização a partir do drop-down e selecione a política de cofre e backup à qual os VMs no âmbito devem estar associados. Também pode optar por especificar um nome de etiqueta e uma matriz de valores de etiqueta. Um VM que contenha qualquer um dos valores especificados para a etiqueta dada será excluído do âmbito da atribuição de políticas.
![Parâmetros de atribuição de políticas](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Certifique-se de que **o Efeito** está definido para implementarifNotExists.
9. Navegar para **Rever+criar** e selecionar **Criar.**

> [!NOTE]
>
> A Política Azure também pode ser utilizada em VMs existentes, utilizando [a reparação](../governance/policy/how-to/remediate-resources.md).

> [!NOTE]
>
> Recomenda-se que esta política não seja atribuída a mais de 200 VMs de cada vez. Se a apólice for atribuída a mais de 200 VMs, pode resultar no desencadeamento da cópia de segurança algumas horas mais tarde do que o especificado no calendário.

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais sobre a Política Azure](../governance/policy/overview.md)