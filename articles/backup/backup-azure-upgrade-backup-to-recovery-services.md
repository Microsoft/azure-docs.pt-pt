---
title: Atualizar um cofre de cópia de segurança para um cofre dos serviços de recuperação
description: Instruções e informações de suporte para atualizar o seu Cofre de cópia de segurança do Azure para um cofre dos serviços de recuperação.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 1/4/2018
ms.author: sogup
ms.openlocfilehash: b4ecebc6bef7f49a23455c7a85f25680df087a95
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530761"
---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Atualizar um cofre de cópia de segurança para um cofre dos serviços de recuperação

Este artigo explica como atualizar um cofre de cópia de segurança para um cofre dos serviços de recuperação. O processo de atualização não tem impacto na quaisquer tarefas de cópia de segurança está em execução, e não existem dados de cópia de segurança se perder. Os principais motivos para atualizar um cofre de cópia de segurança para um cofre dos serviços de recuperação:
- Todas as funcionalidades de um cofre de cópia de segurança são mantidas num cofre dos serviços de recuperação.
- Os cofres dos serviços de recuperação têm mais recursos do que os cofres de cópia de segurança, incluindo: melhor monitorização de segurança, integrada, restauros mais rápidos e restauros ao nível do item.
- Gerir itens de cópia de segurança a partir de um portal aprimorado e simplificado.
- Novos recursos aplicam-se apenas aos cofres de serviços de recuperação.

## <a name="impact-to-operations-during-upgrade"></a>Impacto para as operações durante a atualização

Ao atualizar um cofre de cópia de segurança para um cofre dos serviços de recuperação, não há nenhum impacto para as suas operações de plano de dados. Todas as tarefas de cópia de segurança continuam como normal e quaisquer tarefas de restauro do Active Directory continuam sem interrupções. Durante a atualização, operações de gestão de incorrer num curto período de indisponibilidade e não é possível proteger itens novos ou criar tarefas de cópias de segurança de ad hoc. Tarefas de restauro de VMs de IaaS não são executados durante a atualização. A atualização do cofre demora menos de uma hora a concluir. Depois de concluído, um cofre dos serviços de recuperação substitui o Cofre de cópia de segurança.

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>Alterações para a sua ferramenta após a atualização e automatização

Ao preparar a infraestrutura para a atualização do cofre, tem de atualizar o seu automatização existente ou as ferramentas para garantir que continuam a funcionar após a atualização.
Consulte as referências de cmdlets do PowerShell para o [modelo de implementação do Resource Manager](backup-client-automation.md).


## <a name="before-you-upgrade"></a>Antes da atualização

Verifique os seguintes problemas antes de atualizar os cofres de cópia de segurança para cofres de serviços de recuperação.

- **Versão de agente mínima**: Para atualizar o seu Cofre, certifique-se de que o agente dos serviços de recuperação do Azure (MARS) da Microsoft é, pelo menos, versão 2.0.9083.0. Se o agente de MARS é mais antigo que 2.0.9083.0, atualize o agente antes de iniciar o processo de atualização.
- **Modelo de faturação com base na instância**: Os cofres dos serviços de recuperação só suportam o modelo de faturação com base na instância. Se tiver um cofre de cópia de segurança que está a utilizar o modelo de faturação baseada no armazenamento mais antigo, converta o modelo de faturação durante a atualização.
- **Não existem operações de configuração de cópia de segurança em curso**: Durante a atualização, o acesso ao plano de gestão é restrito. Conclua todas as ações de plano de gestão e, em seguida, iniciar a atualização.

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>Utilizar scripts do PowerShell para atualizar os cofres

Pode utilizar scripts do PowerShell para atualizar os cofres de cópia de segurança para cofres dos serviços de recuperação. Verifique se tem os componentes necessários do PowerShell para acionar a atualização do cofre. Scripts do PowerShell para os cofres de cópia de segurança não funcionam para cofres dos serviços de recuperação. Prepare o ambiente para atualizar os cofres:

1. Instale ou Atualize [Windows Management Framework (WMF) para a versão 5](https://www.microsoft.com/download/details.aspx?id=50395) ou superior.
2. [Instalar o Azure PowerShell MSI](https://github.com/Azure/azure-powershell/releases/download/v3.8.0-April2017/azure-powershell.3.8.0.msi).
3. Transfira o [script do PowerShell](https://aka.ms/vaultupgradescript2) para atualizar os cofres.

### <a name="run-the-powershell-script"></a>Executar o script do PowerShell

Utilize o seguinte script para atualizar os cofres. O seguinte script de exemplo tem explicações dos parâmetros.

RecoveryServicesVaultUpgrade-1.0.2.ps1 **-SubscriptionID** `<subscriptionID>` **-VaultName** `<vaultname>` **-Location** `<location>` **-ResourceType** `BackupVault` **-TargetResourceGroupName** `<rgname>`

**SubscriptionID** -o número de ID de subscrição do cofre que está a ser atualizado.<br/>
**VaultName** -o nome do Cofre de cópia de segurança que está a ser atualizado.<br/>
**Localização** -localização do cofre a ser atualizado.<br/>
**ResourceType** -utilizar BackupVault.<br/>
**TargetResourceGroupName** - uma vez que estiver a atualizar o cofre para uma implementação baseada no Resource Manager, especifique um grupo de recursos. Pode utilizar um grupo de recursos existente ou criar um, fornecendo um novo nome. Se errar o nome de um grupo de recursos, pode criar um novo grupo de recursos. Para saber mais sobre grupos de recursos, leia este [descrição geral sobre grupos de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups).

>[!NOTE]
> Os nomes de grupo de recursos têm restrições. Certifique-se de que siga as orientações; falhas ao fazer isso podem causar atualizações do cofre falhar.
>
>**Azure US Government** os clientes têm de definir o ambiente para "AzureUSGovernment" ao executar o script.
>**O Azure na China** os clientes têm de definir o ambiente para "AzureChinaCloud" ao executar o script.

O fragmento de código seguinte é um exemplo de como deve ser qual o comando do PowerShell:

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

Também pode executar o script sem quaisquer parâmetros e é-lhe perguntado para fornecer entradas para todos os parâmetros necessários.

O script do PowerShell pede-lhe para introduzir as suas credenciais. Introduza as suas credenciais duas vezes: uma vez para a conta do Gestor de serviço e uma segunda vez para a conta de Gestor de recursos.

### <a name="pre-requisites-checking"></a>A verificação de pré-requisitos
Depois de introduzir as credenciais do Azure, Azure verifica se o seu ambiente cumpre os seguintes pré-requisitos:

- **Versão de agente mínima** -atualizar os cofres de cópia de segurança para cofres dos serviços de recuperação requer que o agente de MARS ser, pelo menos, versão 2.0.9083.0. Se tiver registados para um cofre de cópia de segurança com um agente anteriores ao 2.0.9083.0 de itens, a verificação de pré-requisitos falha. Se falhar a verificação de pré-requisitos, atualize o agente e tente atualizar novamente o cofre. Pode baixar a versão mais recente do agente do [ https://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe ](https://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).
- **Tarefas de configuração em curso**: Se alguém está a configurar as tarefas de um cofre de cópia de segurança definido para ser atualizado ou registrar-se um item, a verificação de pré-requisitos falha. Concluir a configuração, ou concluir a registar o item e, em seguida, inicie o processo de atualização do cofre.
- **Modelo de faturação baseada no armazenamento**: Os cofres dos serviços de recuperação suportam o modelo de faturação com base na instância. Se executar a atualização do cofre num cofre de cópia de segurança que utiliza o modelo de faturação baseada no armazenamento, lhe for pedido para atualizar o modelo de faturação, juntamente com o cofre. Caso contrário, pode atualizar o modelo de faturação em primeiro lugar, e, em seguida, executar a atualização do cofre.
- Identifica um grupo de recursos para o Cofre dos serviços de recuperação. Para tirar partido das funcionalidades de implementação do Resource Manager, tem de colocar um cofre dos serviços de recuperação num grupo de recursos. Se não sabe qual grupo de recursos para utilizar, indique um nome e o processo de atualização cria o grupo de recursos para. O processo de atualização também associa o Cofre novo grupo de recursos.

Quando o processo de atualização concluir a verificação de pré-requisitos, o processo solicita que iniciar a atualização do cofre. Depois de confirmar, o processo de atualização normalmente, demora cerca de 15 a 20 minutos a concluir, consoante o tamanho do seu cofre. Se tiver um cofre de grandes dimensões, a atualização pode demorar até 90 minutos.

## <a name="managing-your-recovery-services-vaults"></a>Gerir os cofres de serviços de recuperação

Os ecrãs seguintes mostram um novo cofre de serviços de recuperação, atualizado a partir do Cofre de cópia de segurança, no portal do Azure. A primeira tela mostra o dashboard do cofre que exibe a entidades-chave do cofre.

![exemplo do Cofre de serviços de recuperação atualizado a partir de um cofre de cópia de segurança](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

O segundo ecrã mostra a ajuda de ligações disponíveis para ajudá-lo a começar a utilizar o Cofre dos serviços de recuperação.

![ligações de ajuda, no painel início rápido](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Passos pós-atualização
Cofre dos Recovery Services suporta a especificação de informações de fuso horário na política de cópia de segurança. Após a atualização com êxito do cofre, vá para políticas de cópia de segurança no menu de definições do cofre e atualizar as informações de fuso horário para cada uma das políticas configuradas no cofre. Este ecrã já mostra o tempo de agenda de cópia de segurança especificado como por fuso horário local utilizado quando criou a política.

## <a name="enhanced-security"></a>Segurança avançada

Quando um cofre de cópia de segurança é atualizado para um cofre dos serviços de recuperação, as definições de segurança para esse cofre automaticamente são ativadas. Quando as definições de segurança estão em determinadas operações como a eliminar as cópias de segurança ou alterar uma frase de acesso necessitar de um [multi-factor Authentication](../active-directory/authentication/multi-factor-authentication.md) PIN. Para obter mais informações sobre a segurança avançada, consulte o artigo [recursos de segurança para proteger cópias de segurança híbridas](backup-azure-security-feature.md).

Quando a segurança avançada estiver ativada, os dados são mantidos até 14 dias após as informações de ponto de recuperação foi eliminadas do cofre. Os clientes são faturados para o armazenamento destes dados de segurança. Retenção de dados de segurança se aplica a pontos de recuperação direcionados para o agente de cópia de segurança do Azure, Azure Backup Server e System Center Data Protection Manager.

## <a name="gather-data-on-your-vault"></a>Recolher dados do seu Cofre

Depois de atualizar para um cofre dos serviços de recuperação, configure relatórios para o Azure Backup (para VMs de IaaS e serviços de recuperação do Azure (MARS) da Microsoft) e utilizar o Power BI para aceder aos relatórios. Para obter informações adicionais sobre a recolha de dados, consulte o artigo [configurar a cópia de segurança do Azure comunica](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="does-the-upgrade-plan-affect-my-ongoing-backups"></a>O plano de atualização afeta a minha cópias de segurança em curso?
Não. As cópias de segurança em curso continuam sem interrupções durante e após a atualização.

### <a name="if-i-dont-plan-on-upgrading-soon-what-happens-to-my-vaults"></a>Se não pretender atualizar em breve, o que acontece aos meus cofres?
Uma vez que todos os novos recursos aplicam-se apenas a cofres dos serviços de recuperação, recomendamos vivamente que Atualize os cofres. A partir de 1 de Setembro de 2017, a Microsoft começará o cofres de cópia de segurança-atualizar de automático para cofres dos serviços de recuperação. Depois de Novembro 30,2017, já não pode criar cofres de cópia de segurança com o PowerShell. O Cofre pode ser atualizado automaticamente sempre que entre. A Microsoft recomenda a que atualizar o seu Cofre logo que possível.

### <a name="what-does-this-upgrade-mean-for-my-existing-tooling"></a>O que isto significa atualização para minhas ferramentas existentes?
Atualize a sua ferramenta para o modelo de implementação do Resource Manager. Serviços de recuperação cofres que foram criados para utilizam no modelo de implementação do Resource Manager. Planejamento para o modelo de implementação do Resource Manager e gestão de contas para a diferença nos seus cofres são importante.

### <a name="during-the-upgrade-is-there-much-downtime"></a>Durante a atualização, há muito tempo de inatividade?
Depende do número de recursos que estão a ser atualizado. Para implementações mais pequenas (algumas dezenas de instâncias protegidas), a atualização completa deve demorar menos de 20 minutos. Para implementações maiores, deve demorar um máximo de uma hora.

### <a name="can-i-roll-back-after-upgrading"></a>Posso reverter após a atualização?
Não. Reversão não é suportada depois dos recursos foram atualizados com êxito.

### <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-upgrade"></a>Pode validar a minha subscrição ou recursos para ver se eles são capazes de atualização?
Sim. A primeira etapa na atualização valida que os recursos são capazes de atualização. No caso de falha de validação de pré-requisitos, recebe mensagens com todos os motivos pelos quais que a atualização não pode ser concluída.

### <a name="can-i-upgrade-my-csp-based-backup-vault"></a>Posso atualizar o meu Cofre de cópia de segurança baseada no CSP?
Não. Atualmente, não é possível atualizar os cofres de cópia de segurança com base CSP. Vamos adicionar suporte para a atualização de cofres de cópia de segurança baseada em CSP nas versões seguintes.

### <a name="can-i-view-my-classic-vault-post-upgrade"></a>Pode ver meu cofre clássico depois da atualização?
Não. Não é possível ver ou gerir o seu Cofre clássico depois da atualização. Só poderá utilizar o novo portal do Azure para todas as ações de gestão no cofre.

### <a name="my-upgrade-failed-but-the-machine-that-held-the-agent-requiring-updating-doesnt-exist-anymore-what-do-i-do-in-such-a-case"></a>Falha ao atualizar o meu, mas a máquina que mantidos o agente que requerem a atualização, não existe mais. O que fazer nesse caso?
Se precisar de utilizar o arquivo, as cópias de segurança desta máquina para retenção de longa duração, em seguida, não será capazes de atualizar o cofre. Em versões futuras, adicionaremos suporte para atualizar um cofre dos.
Se não for preciso armazenar as cópias de segurança desta máquina deixa de poder, em seguida,. anular o registo nesta máquina a partir do cofre e repita a atualização.

### <a name="why-cant-i-see-the-jobs-information-for-my-resources-after-upgrade"></a>Por que motivo não vejo as informações de tarefas para os meus recursos após a atualização?
Monitorização de cópias de segurança (agente MARS e IaaS) é uma nova funcionalidade que obtém quando atualizar o seu Cofre de cópia de segurança para cofre dos serviços de recuperação. As informações de monitorização demora até 12 horas para sincronizar com o serviço.

### <a name="how-do-i-report-an-issue"></a>Como posso comunicar problemas?
Se qualquer parte da atualização do cofre falhar, tenha em atenção que o OperationId listados no erro. Microsoft Support proativamente funcionará para resolver o problema. Pode contactar o suporte ou envie um e-mail para rsvaultupgrade@service.microsoft.com com o ID de subscrição, nome do cofre e OperationId. Vamos tentar resolver o problema mais depressa possível. Não repita a operação, a menos que explicitamente instruído para fazê-lo pela Microsoft.


## <a name="next-steps"></a>Passos Seguintes
Utilize o seguinte artigo para:</br>
[Fazer uma cópia de segurança de uma VM de IaaS](backup-azure-arm-vms-prepare.md)</br>
[Fazer uma cópia de segurança de um servidor de cópia de segurança do Azure](backup-azure-microsoft-azure-backup.md)</br>
[Cópia de segurança um servidor Windows](backup-configure-vault.md).
