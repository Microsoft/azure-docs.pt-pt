---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: singhkays
ms.service: virtual-machines
ms.topic: include
ms.date: 05/18/2018
ms.author: kasing
ms.custom: include file
ms.openlocfilehash: a7a3c6edbbeca96a90f8003fda1b92fc8bf99fec
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021205"
---
## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Este plano de migração afeta algum dos meus serviços ou aplicações existentes que são executados em máquinas virtuais do Azure? 

Não. As VMs (clássicas) são serviços totalmente suportados na disponibilidade geral. Pode continuar a utilizar estes recursos para expandir a sua utilização no Microsoft Azure.

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>O que acontece às minhas VMs se não pretender migrá-las no futuro próximo? 

Não vamos descontinuar as APIs clássicas nem o modelo de recursos atuais. Queremos facilitar a migração, tendo em conta as funcionalidades avançadas que o modelo de implementação Resource Manager disponibiliza. Recomendamos vivamente que reveja [alguns dos avanços](../articles/azure-resource-manager/management/deployment-models.md) que fazem parte do IaaS no Resource Manager.

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>O que significa este plano de migração para as minhas ferramentas existentes? 

Atualizar as ferramentas para o modelo de implementação Resource Manager é uma das principais alterações a considerar nos seus planos de migração.

## <a name="how-long-will-the-management-plane-downtime-be"></a>Durante quanto tempo vai estar indisponível o plano de gestão? 

Depende do número de recursos que estão a ser migrados. Para implementações mais pequenas (algumas dezenas de VMs), a migração completa deve demorar menos de uma hora. Para implementações em grande escala (centenas de VMs), pode demorar algumas horas.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Depois de os recursos migrados forem considerados no Resource Manager, posso reverter a migração? 

Pode abortar a migração desde que os recursos estejam no estado preparado. A reversão não é suportada depois de os recursos terem sido migrados com êxito através da consolidação.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>Posso reverter a migração se a operação de consolidação falhar? 

Não pode abortar a migração se a operação de consolidação falhar. Todas as operações de migração, incluindo a operação de consolidação, são idempotent. Por isso, recomendamos que repita a operação após um curto período de tempo. Se continuar a deparar-se com um erro, crie um pedido de suporte ou escreva uma mensagem no fórum com a etiqueta ClassicIaaSMigration no nosso [fórum de VMs](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Se tiver de utilizar IaaS no Resource Manager, tenho de comprar outro circuito do ExpressRoute? 

Não. Recentemente, ativámos a [passagem dos circuitos do ExpressRoute do modelo de implementação clássica para o modelo do Resource Manager](../articles/expressroute/expressroute-move.md). Se já tiver um circuito ExpressRoute, não precisa de comprar um novo.

## <a name="what-if-i-had-configured-role-based-access-control-policies-for-my-classic-iaas-resources"></a>O que acontece se tiver configurado políticas de Controlo de Aceso Baseado em Funções nos meus recursos de IaaS clássicos? 

Durante a migração, os recursos passam da implementação clássica para Resource Manager. Assim, recomendamos que planeie as atualizações das políticas de RBAC que têm de ocorrer após a migração.

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Fiz backup de minhas VMs clássicas em um cofre. Posso migrar as VMs do modo clássico para o modo Resource Manager e protegê-las num cofre dos Serviços de Recuperação?

<a name="vault">Quando</a> você move uma VM do modo clássico para o Gerenciador de recursos, os backups feitos antes da migração não serão migrados para a VM do Gerenciador de recursos recém migrado. No entanto, se você quiser manter seus backups de VMs clássicas, siga estas etapas antes da migração. 

1. No cofre dos serviços de recuperação, vá para a guia **itens protegidos** e selecione a VM. 
2. Clique em Parar Proteção. Deixe a opção *Eliminar dados de cópia de segurança associados***desmarcada**.

> [!NOTE]
> Você será cobrado pelo custo da instância de backup até manter os dados. As cópias de backup serão removidas de acordo com o período de retenção. No entanto, a última cópia de backup sempre é mantida até que você exclua explicitamente os dados de backup. É aconselhável verificar o período de retenção da máquina virtual e disparar "excluir dados de backup" no item protegido no cofre depois que o período de retenção terminar. 
>
>

Para migrar a máquina virtual para o modo do Gerenciador de recursos, 

1. Elimine a extensão de cópia de segurança/instantâneo da VM.
2. Migre a máquina virtual do modo clássico para o modo do Resource Manager. Certifique-se de que as informações de armazenamento e rede correspondentes à máquina virtual também são migradas para o modo do Resource Manager.

Além disso, se você quiser fazer backup da VM migrada, vá para a folha gerenciamento de máquinas virtuais para [habilitar o backup](../articles/backup/quick-backup-vm-portal.md#enable-backup-on-a-vm).

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>Posso verificar se a minha subscrição ou os meus recursos podem ser migrados? 

Sim. Na opção de migração suportada por plataforma, o primeiro passo na preparação da migração é verificar se os recursos podem ser migrados. Caso a operação de verificação falhe, recebe mensagens com todos os motivos pelos quais a migração não pode ser concluída.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>O que acontece se me deparar com um erro de quota ao preparar os recursos de IaaS para a migração? 

Recomendamos que aborte a migração e apresente um pedido de suporte para aumentar as quotas na região para a qual está a migrar as VMs. Quando o pedido de aumento de quota for aprovado, pode começar a executar os passos da migração novamente.

## <a name="how-do-i-report-an-issue"></a>Como posso comunicar problemas? 

Publique os seus problemas e as suas perguntas sobre a migração no nosso [fórum de VMs](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows), com a palavra-chave ClassicIaaSMigration. Recomendamos que publique todas as suas perguntas neste fórum. Se tiver um contrato de suporte, também pode apresentar um pedido de suporte

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>E se não gostar dos nomes dos recursos que a plataforma escolheu durante a migração? 

Todos os recursos para os quais indique explicitamente nomes no modelo de implementação clássica são mantidos durante a migração. Em alguns casos, são criados recursos novos. Por exemplo, é criada uma interface de rede para cada VM. Atualmente, não suportamos a capacidade para controlar os nomes destes recursos novos criados durante a migração. Registe os seus votos para esta funcionalidade no [fórum de comentários do Azure](https://feedback.azure.com).

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>Posso migrar circuitos do ExpressRoute utilizados em várias subscrições com ligações de autorização? 

Os circuitos do ExpressRoute que utilizem ligações de autorização em várias subscrições não podem ser migrados automaticamente sem tempo de inatividade. Temos orientações que mostram como utilizar passos manuais para migrá-los. Veja [Migrate ExpressRoute circuits and associated virtual networks from the classic to the Resource Manager deployment model](../articles/expressroute/expressroute-migration-classic-resource-manager.md) (Migrar circuitos do ExpressRoute e as máquinas virtuais associadas do modelo de implementação clássica para Resource Manager) para obter os passos e mais informações.

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>Recebi a mensagem *"a VM está relatando o status geral do agente como não pronto. Portanto, a VM não pode ser migrada. Verifique se o agente de VM está relatando o status geral do agente como pronto "* ou *" VM contém a extensão cujo status não está sendo relatado da VM. Portanto, essa VM não pode ser migrada ".*

Esta mensagem é recebida quando a VM não tem conectividade de saída à Internet. O agente da VM utiliza a conectividade de saída para comunicar com a conta de Armazenamento do Azure para atualizar o estado do agente a cada cinco minutos.
