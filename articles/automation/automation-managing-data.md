---
title: Segurança de dados da Azure Automation
description: Este artigo ajuda-o a aprender como a Azure Automation protege a sua privacidade e protege os seus dados.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/08/2021
ms.topic: conceptual
ms.openlocfilehash: 40405607e7f7198f190f621121022537ac3b3171
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98046044"
---
# <a name="management-of-azure-automation-data"></a>Gestão dos dados da Automatização do Azure

Este artigo contém vários tópicos que explicam como os dados são protegidos e protegidos num ambiente de Automação Azure.

## <a name="tls-12-enforcement-for-azure-automation"></a>Aplicação TLS 1.2 para a Azure Automation

Para garantir a segurança dos dados em trânsito para a Azure Automation, encorajamo-lo a configurar a utilização da Segurança da Camada de Transporte (TLS) 1.2. Segue-se uma lista de métodos ou clientes que comunicam através do HTTPS ao serviço de Automação:

* Chamadas webhook

* Trabalhadores de runbook híbridos, que inclui máquinas geridas por Gestão de Atualização e Rastreio e Inventário de Alterações.

* Nódes DSC

Versões mais antigas da camada de tomadas TLS/Secure Sockets (SSL) foram consideradas vulneráveis e, embora ainda atualmente trabalhem para permitir retrocompatibilidade, não são **recomendadas**. Não recomendamos que o seu agente utilize apenas o TLS 1.2 a menos que seja absolutamente necessário, pois pode quebrar funcionalidades de segurança de nível da plataforma que lhe permitam detetar e tirar partido de protocolos mais recentes e mais seguros à medida que estes ficam disponíveis, como o TLS 1.3.

Para obter informações sobre o suporte TLS 1.2 com o agente Log Analytics para Windows e Linux, que é uma dependência para o papel de Trabalhador de Runbook Híbrido, consulte [a visão geral do agente Log Analytics - TLS 1.2](..//azure-monitor/platform/log-analytics-agent.md#tls-12-protocol).

### <a name="platform-specific-guidance"></a>Orientação específica da plataforma

|Plataforma/Idioma | Suporte | Mais Informações |
| --- | --- | --- |
|Linux | As distribuições linux tendem a contar com [o suporte OpenSSL](https://www.openssl.org) para suporte TLS 1.2.  | Verifique o [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) para confirmar que a sua versão do OpenSSL está suportada.|
| Windows 8.0 - 10 | Suportado e ativado por defeito. | Para confirmar que ainda está a utilizar as [definições predefinições](/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 - 2016 | Suportado e ativado por defeito. | Para confirmar que ainda está a utilizar as [definições predefinidos](/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 e Windows Server 2008 R2 SP1 | Suportado, mas não ativado por defeito. | Consulte a página [de definições de registo de segurança da camada de transporte (TLS)](/windows-server/security/tls/tls-registry-settings) para obter mais detalhes sobre como ativar.  |

## <a name="data-retention"></a>Retenção de dados

Quando elimina um recurso na Azure Automation, é retido por vários dias para efeitos de auditoria antes da remoção permanente. Não pode ver ou usar o recurso durante este tempo. Esta política aplica-se também aos recursos que pertencem a uma conta de Automação eliminada.

O quadro que se segue resume a política de retenção de diferentes recursos.

| Dados | Política |
|:--- |:--- |
| Contas |Uma conta é permanentemente removida 30 dias após o utilizador a eliminar. |
| Elementos |Um ativo é permanentemente removido 30 dias após um utilizador o eliminar, ou 30 dias após um utilizador apagar uma conta que detém o ativo. Os ativos incluem variáveis, horários, credenciais, certificados, pacotes Python 2 e conexões. |
| Nó de DSC |Um nó DSC é removido permanentemente 30 dias após não ter sido registado numa conta de Automação utilizando o portal Azure ou o cmdlet [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode) no Windows PowerShell. Um nó também é removido permanentemente 30 dias após um utilizador apagar a conta que detém o nó. |
| Tarefas |Um trabalho é eliminado e permanentemente removido 30 dias após a modificação, por exemplo, após o fim do trabalho, é interrompido ou suspenso. |
| Módulos |Um módulo é removido permanentemente 30 dias após um utilizador o eliminar, ou 30 dias após um utilizador apagar a conta que detém o módulo. |
| Configurações de nó/ficheiros MOF |Uma configuração de nó antigo é removida permanentemente 30 dias após a geração de uma nova configuração do nó. |
| Relatórios de Nó |Um relatório de nó é permanentemente removido 90 dias após a criação de um novo relatório para esse nó. |
| Runbooks |Um livro de aplicação é removido permanentemente 30 dias após um utilizador apagar o recurso, ou 30 dias após um utilizador apagar a conta que detém o recurso. |

A política de retenção aplica-se a todos os utilizadores e atualmente não pode ser personalizada. No entanto, se necessitar de manter os dados por um período mais longo, pode [encaminhar os dados de trabalho da Azure Automation para os registos do Azure Monitor](automation-manage-send-joblogs-log-analytics.md).

## <a name="data-backup"></a>Backup de dados

Quando elimina uma conta Desmômes em Azure, todos os objetos da conta são eliminados. Os objetos incluem runbooks, módulos, configurações, configurações, empregos e ativos. Não podem ser recuperados depois de a conta ser apagada. Pode utilizar as seguintes informações para fazer o back up do conteúdo da sua conta Automation antes de a eliminar.

### <a name="runbooks"></a>Runbooks

Pode exportar os seus livros para scripts utilizando o portal Azure ou o cmdlet [Get-AzureAutomationRunbookDefinition](/powershell/module/servicemanagement/azure.service/get-azureautomationrunbookdefinition) no Windows PowerShell. Pode importar estes ficheiros de script para outra conta de Automação, como discutido na [Manage runbooks in Azure Automation](manage-runbooks.md).

### <a name="integration-modules"></a>Módulos de integração

Não é possível exportar módulos de integração da Azure Automation. Deve disponibilizá-los fora da conta Automation.

### <a name="assets"></a>Elementos

Não é possível exportar ativos da Azure Automation: certificados, conexões, credenciais, horários e variáveis. Em vez disso, pode utilizar o portal Azure e os cmdlets Azure para observar os detalhes destes ativos. Em seguida, use estes detalhes para criar quaisquer ativos que sejam usados por runbooks que você importa para outra conta de Automação.

Não é possível recuperar os valores para variáveis encriptadas ou os campos de senha de credenciais usando cmdlets. Se não conhece estes valores, pode recuperá-los num livro de bordo. Para obter valores variáveis, consulte [ativos variáveis na Azure Automation](shared-resources/variables.md). Para saber mais sobre a recuperação de valores credenciais, consulte [os ativos da Credential na Azure Automation.](shared-resources/credentials.md)

### <a name="dsc-configurations"></a>Configurações de DSC

Pode exportar as suas configurações DSC para ficheiros de scripts utilizando o portal Azure ou o cmdlet [Export-AzAutomationDscConfiguration](/powershell/module/az.automation/export-azautomationdscconfiguration) no Windows PowerShell. Pode importar e utilizar estas configurações noutra conta de Automação.

## <a name="geo-replication-in-azure-automation"></a>Geo-replicação na Azure Automation

A geo-replicação é padrão nas contas Azure Automation. Você escolhe uma região primária ao configurar a sua conta. O serviço de geo-replicação da Automação interna atribui automaticamente uma região secundária à conta. O serviço, em seguida, continuamente confirma os dados da conta da região primária para a região secundária. A lista completa das regiões primárias e secundárias pode ser encontrada na [continuidade do Negócios e na recuperação de desastres (BCDR): Regiões Emparelhadas Azure](../best-practices-availability-paired-regions.md).

A cópia de segurança criada pelo serviço de geo-replicação automation é uma cópia completa dos ativos, configurações e similares da Automação. Esta cópia de segurança pode ser usada se a região primária descer e perder dados. No caso improvável de os dados de uma região primária se perderem, a Microsoft tenta recuperá-lo. Se a empresa não conseguir recuperar os dados primários, utiliza falha automática e informa-o da situação através da sua assinatura Azure.

O serviço de geo-replicação automation não é acessível diretamente a clientes externos se houver uma falha regional. Se pretender manter a configuração da Automatização e os livros de execução durante as falhas regionais:

1. Selecione uma região secundária para emparelhar com a região geográfica da sua conta de Automação primária.

2. Criar uma conta Automation na região secundária.

3. Na conta principal, exporte os seus livros como ficheiros de script.

4. Importe os livros para a sua conta de Automação na região secundária.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre ativos seguros na Azure Automation, consulte [encriptação de ativos seguros na Azure Automation](automation-secure-asset-encryption.md).

* Para saber mais sobre a geo-replicação, consulte [criar e utilizar a geo-replicação ativa.](../azure-sql/database/active-geo-replication-overview.md)
