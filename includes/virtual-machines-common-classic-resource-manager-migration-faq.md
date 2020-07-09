---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: tanmaygore
ms.service: virtual-machines
ms.topic: include
ms.date: 02/06/2020
ms.author: tagore
ms.custom: include file
ms.openlocfilehash: fa97935c10b2a5df70c9babf031a889a9c1e9f34
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83837875"
---
## <a name="what-is-the-time-required-for-migration"></a>Qual é o tempo necessário para a migração?

O planeamento e execução da migração depende muito da complexidade da arquitetura e pode demorar alguns meses.  

## <a name="what-is-the-definition-of-a-new-customer-on-iaas-vms-classic"></a>Qual é a definição de um novo cliente em IaaS VMs (clássico)?

Os clientes que não tinham IaaS VMs (clássico) nas suas assinaturas no mês de fevereiro de 2020 (um mês antes do início da depreciação) são considerados novos clientes. 

## <a name="what-is-the-definition-of-an-existing-customer-on-iaas-virtual-machines-classic"></a>Qual é a definição de cliente existente em IaaS Virtual Machines (clássico)?

Os clientes que tinham ativado ou parado mas que atribuíram VMs IaaS (Classic) nas suas subscrições no mês de fevereiro de 2020, são considerados como um cliente existente. Só estes clientes têm até 1 de março de 2023 para migrar os seus VMs de Azure Service Manager para Azure Resource Manager. 

## <a name="why-am-i-getting-an-error-stating-newclassicvmcreationnotallowedforsubscription"></a>Porque estou a receber um erro a dizer "NewClassicVMCreationNotAllowedForSubscription"?

Como parte do processo de aposentação, a IaaS VM (clássico) já não está disponível para novos clientes. Identificámo-lo como novos clientes e, por isso, a sua operação não foi autorizada. Recomendamos vivamente a utilização de [Máquinas Virtuais Azure utilizando o ARM](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell). Se não puder utilizar VMs Azure utilizando ARM, contacte o suporte para a lista de assinaturas.

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Este plano de migração afeta algum dos meus serviços ou aplicações existentes que são executados em máquinas virtuais do Azure? 

Só a 1 de março de 2023 para os IaaS VMs (clássico). Os IaaS VMs (clássico) são serviços totalmente suportados em disponibilidade geral. Pode continuar a utilizar estes recursos para expandir a sua utilização no Microsoft Azure. No dia 1 de março de 2023, estes VMs serão totalmente reformados e quaisquer VMs ativos ou atribuídos serão interrompidos & acordos. Não haverá impacto para outros recursos clássicos como Cloud Services (Classic), Storage Accounts (Classic), etc.   

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>O que acontece às minhas VMs se não pretender migrá-las no futuro próximo? 

No dia 1 de março de 2023, os IaaS VMs (Classic) serão totalmente aposentados e quaisquer VMs ativos ou atribuídos serão interrompidos & acordos. Para prevenir o impacto do negócio, recomumam-se muito para começar a planear a sua migração hoje e completá-la antes de 1 de março de 2023. Não estamos a depreciar as APIs clássicas existentes, serviços em nuvem e modelo de recursos. Queremos facilitar a migração, tendo em conta as funcionalidades avançadas que o modelo de implementação Resource Manager disponibiliza. Recomendamos que comece a planear migrar estes recursos para o Azure Resource Manager. 

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>O que significa este plano de migração para as minhas ferramentas existentes? 

Atualizar as ferramentas para o modelo de implementação Resource Manager é uma das principais alterações a considerar nos seus planos de migração.

## <a name="how-long-will-the-management-plane-downtime-be"></a>Durante quanto tempo vai estar indisponível o plano de gestão? 

Depende do número de recursos que estão a ser migrados. Para implementações mais pequenas (algumas dezenas de VMs), a migração completa deve demorar menos de uma hora. Para implementações em grande escala (centenas de VMs), pode demorar algumas horas.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Depois de os recursos migrados forem considerados no Resource Manager, posso reverter a migração? 

Pode abortar a migração desde que os recursos estejam no estado preparado. A reversão não é suportada depois de os recursos terem sido migrados com êxito através da consolidação.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>Posso reverter a migração se a operação de consolidação falhar? 

Não pode abortar a migração se a operação de consolidação falhar. Todas as operações de migração, incluindo a operação de consolidação, são idempotent. Por isso, recomendamos que repita a operação após um curto período de tempo. Se ainda enfrentar um erro, crie um bilhete de apoio.

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Se tiver de utilizar IaaS no Resource Manager, tenho de comprar outro circuito do ExpressRoute? 

Não. Recentemente, ativámos a [passagem dos circuitos do ExpressRoute do modelo de implementação clássica para o modelo do Resource Manager](../articles/expressroute/expressroute-move.md). Se já tiver um circuito ExpressRoute, não precisa de comprar um novo.

## <a name="what-if-i-had-configured-role-based-access-control-policies-for-my-classic-iaas-resources"></a>O que acontece se tiver configurado políticas de Controlo de Aceso Baseado em Funções nos meus recursos de IaaS clássicos? 

Durante a migração, os recursos passam da implementação clássica para Resource Manager. Assim, recomendamos que planeie as atualizações das políticas de RBAC que têm de ocorrer após a migração.

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Apoiei os meus VMs clássicos num cofre. Posso migrar as VMs do modo clássico para o modo Resource Manager e protegê-las num cofre dos Serviços de Recuperação?

Quando se move um VM do modo classic para o gestor de recursos, as cópias de segurança tomadas antes da migração não migrarão para o recém-migrado Resource Manager VM. No entanto, se quiser manter os seus backups de VMs clássicos, siga estes passos antes da migração. 

1. No cofre dos Serviços de Recuperação, vá ao separador **Itens Protegidos** e selecione o VM. 
2. Clique em Parar Proteção. Deixe a opção *Eliminar dados de cópia de segurança associados***desmarcada**.

> [!NOTE]
> Serão cobrados custos de instância de backup até reter dados. As cópias de reserva serão podadas de acordo com o intervalo de retenção. No entanto, a última cópia de cópia de segurança é sempre mantida até eliminar explicitamente os dados de backup. É aconselhável verificar o seu alcance de retenção da máquina Virtual e acionar "Delete Backup Data" no item protegido no cofre uma vez que o intervalo de retenção esteja terminado. 
>
>

Para migrar a máquina virtual para o modo Gestor de Recursos, 

1. Elimine a extensão de cópia de segurança/instantâneo da VM.
2. Migre a máquina virtual do modo clássico para o modo do Resource Manager. Certifique-se de que as informações de armazenamento e rede correspondentes à máquina virtual também são migradas para o modo do Resource Manager.

Além disso, se pretender fazer o backup do VM migrado, aceda à lâmina de gestão da Máquina Virtual para [ativar](../articles/backup/quick-backup-vm-portal.md#enable-backup-on-a-vm)a cópia de segurança .

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>Posso verificar se a minha subscrição ou os meus recursos podem ser migrados? 

Sim. Na opção de migração suportada por plataforma, o primeiro passo na preparação da migração é verificar se os recursos podem ser migrados. Caso a operação de verificação falhe, recebe mensagens com todos os motivos pelos quais a migração não pode ser concluída.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>O que acontece se me deparar com um erro de quota ao preparar os recursos de IaaS para a migração? 

Recomendamos que aborte a migração e apresente um pedido de suporte para aumentar as quotas na região para a qual está a migrar as VMs. Quando o pedido de aumento de quota for aprovado, pode começar a executar os passos da migração novamente.

## <a name="how-do-i-report-an-issue"></a>Como posso comunicar problemas? 

Publique os seus problemas e perguntas sobre migração para o nosso [Microsoft Q&Uma página de perguntas para VM,](https://docs.microsoft.com/answers/topics/azure-virtual-machines.html)com a palavra-chave ClassicIaaSMigration. Recomendamos que publique todas as suas perguntas neste fórum. Se tiver um contrato de suporte, também pode apresentar um pedido de suporte

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>E se não gostar dos nomes dos recursos que a plataforma escolheu durante a migração? 

Todos os recursos para os quais indique explicitamente nomes no modelo de implementação clássica são mantidos durante a migração. Em alguns casos, são criados recursos novos. Por exemplo, é criada uma interface de rede para cada VM. Atualmente, não suportamos a capacidade para controlar os nomes destes recursos novos criados durante a migração. Registe os seus votos para esta funcionalidade no [fórum de comentários do Azure](https://feedback.azure.com).

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>Posso migrar circuitos do ExpressRoute utilizados em várias subscrições com ligações de autorização? 

Os circuitos do ExpressRoute que utilizem ligações de autorização em várias subscrições não podem ser migrados automaticamente sem tempo de inatividade. Temos orientações que mostram como utilizar passos manuais para migrá-los. Veja [Migrate ExpressRoute circuits and associated virtual networks from the classic to the Resource Manager deployment model](../articles/expressroute/expressroute-migration-classic-resource-manager.md) (Migrar circuitos do ExpressRoute e as máquinas virtuais associadas do modelo de implementação clássica para Resource Manager) para obter os passos e mais informações.

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>Recebi a mensagem *"A VM está a reportar o estado geral do agente como Não Pronto. Por conseguinte, o VM não pode ser migrado. Certifique-se de que o Agente VM está a reportar o estado global do agente como Ready"* ou *"VM contém extensão cujo Estado não está a ser reportado a partir do VM. Portanto, este VM não pode ser migrado."*

Esta mensagem é recebida quando a VM não tem conectividade de saída à Internet. O agente da VM utiliza a conectividade de saída para comunicar com a conta de Armazenamento do Azure para atualizar o estado do agente a cada cinco minutos.
