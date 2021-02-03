---
title: Novidades em Azure Sentinel
description: Este artigo descreve novas funcionalidades no Azure Sentinel dos últimos meses.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 0eb3b8feda09d931654d1781ea37737d68a1b377
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526641"
---
# <a name="whats-new-in-azure-sentinel"></a>Novidades em Azure Sentinel

Este artigo lista funcionalidades recentes adicionadas ao Azure Sentinel, e novas funcionalidades em serviços relacionados que proporcionam uma experiência de utilizador melhorada no Azure Sentinel.

Para obter informações sobre funcionalidades anteriores entregues, consulte os [nossos blogs da Tech Community](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

As funcionalidades notadas estão atualmente em PREVIEW. Os [Termos Complementares de Pré-visualização do Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais aplicáveis às funcionalidades do Azure que estão em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.


> [!TIP]
> As nossas equipas de caça ameaçadoras em toda a Microsoft contribuem com consultas, playbooks, livros e cadernos para a [Comunidade Azure Sentinel](https://github.com/Azure/Azure-Sentinel), incluindo consultas de [caça específicas](https://github.com/Azure/Azure-Sentinel) que as suas equipas podem adaptar e usar. 
>
> Também pode contribuir! Junte-se a nós na [comunidade Azure Sentinel Threat Hunters GitHub](https://github.com/Azure/Azure-Sentinel/wiki).
> 

## <a name="january-2021"></a>Janeiro de 2021

- [Módulo Az.SecurityInsights PowerShell (pré-visualização pública)](#azsecurityinsights-powershell-module-public-preview)
- [Conector de base de dados SQL](#sql-database-connector)
- [Melhores comentários de incidentes](#improved-incident-comments)
- [Clusters dedicados log analytics](#dedicated-log-analytics-clusters)
- [Aplicativos lógicos geridos identidades](#logic-apps-managed-identities)
- [Melhor afinação de regras com os gráficos de pré-visualização da regra de análise](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="azsecurityinsights-powershell-module-public-preview"></a>Módulo Az.SecurityInsights PowerShell (pré-visualização pública)

O Azure Sentinel suporta agora o novo módulo [Az.SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) PowerShell.

O módulo **Az.SecurityInsights** suporta casos comuns de utilização do Azure Sentinel, como interagir com incidentes para mudar estátuas, gravidade, proprietário, e assim por diante, adicionando comentários e rótulos a incidentes, e criando marcadores.

Embora recomendemos a utilização de modelos [Azure Resource Manager (ARM)](/azure/azure-resource-manager/templates/) para o seu pipeline CI/CD, o módulo **Az.SecurityInsights** é útil para tarefas pós-implantação e é direcionado para a automatização SOC.  Por exemplo, a sua automatização SOC pode incluir passos para configurar conectores de dados, criar regras de análise ou adicionar ações de automação às regras de análise.

Para obter mais informações, incluindo uma lista completa e descrição dos cmdlets disponíveis, descrições de parâmetros e exemplos, consulte a [documentação Az.SecurityInsights PowerShell](/powershell/module/az.securityinsights/).

### <a name="sql-database-connector"></a>Conector de base de dados SQL

O Azure Sentinel fornece agora um conector de base de dados Azure SQL, que transmite os registos de auditoria e diagnóstico das suas bases de dados no Azure Sentinel e monitoriza continuamente a atividade em todos os seus casos.

O Azure SQL é um motor de base de dados totalmente gerido, Plataforma-as-a-Service (PaaS) que lida com a maioria das funções de gestão de bases de dados, tais como upgrade, patching, backups e monitorização, sem o envolvimento do utilizador.

Para obter mais informações, consulte [os diagnósticos de diagnóstico e registos de auditoria da base de dados Connect Azure SQL](connect-azure-sql-logs.md).

### <a name="improved-incident-comments"></a>Melhores comentários de incidentes

Os analistas usam comentários de incidentes para colaborar em incidentes, documentando processos e passos manualmente ou como parte de um livro de jogadas. 

A nossa melhor experiência de comentário de incidentes permite-lhe formatar os seus comentários e editar ou eliminar comentários existentes.

Para obter mais informações, consulte [criar automaticamente incidentes a partir de alertas de segurança da Microsoft.](create-incidents-from-alerts.md)
### <a name="dedicated-log-analytics-clusters"></a>Clusters dedicados log analytics

O Azure Sentinel agora suporta clusters dedicados do Log Analytics como uma opção de implementação. Recomendamos considerar um cluster dedicado se você:

- **Ingerir mais de 1 Tb por dia** no seu espaço de trabalho Azure Sentinel
- **Tenha vários espaços de trabalho do Azure Sentinel** na sua inscrição no Azure

Os clusters dedicados permitem-lhe utilizar funcionalidades como chaves geridas pelo cliente, lockbox, encriptação dupla e consultas de espaço de trabalho cruzada mais rápidas quando tem vários espaços de trabalho no mesmo cluster.

Para obter mais informações, consulte [os registos do Azure Monitor sobre agrupamentos dedicados](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters).

### <a name="logic-apps-managed-identities"></a>Aplicativos lógicos geridos identidades

O Azure Sentinel suporta agora identidades geridas para o conector Azure Sentinel Logic Apps, permitindo-lhe conceder permissões diretamente a um livro de jogadas específico para operar no Azure Sentinel em vez de criar identidades extra.

- **Sem uma identidade gerida,** o conector Logic Apps requer uma identidade separada com um papel rbac Azure Sentinel para funcionar no Azure Sentinel. A identidade separada pode ser um utilizador AZure AD ou um diretor de serviço, como uma aplicação registada Azure AD.

- **Ligar o suporte de identidade gerido na sua Aplicação Lógica** regista a App Lógica com AZure AD e fornece um ID de objeto. Utilize o ID do objeto em Azure Sentinel para atribuir a App Lógica com um papel RBAC Azure no seu espaço de trabalho Azure Sentinel. 

Para obter mais informações, consulte:

- [Autenticação com Identidade Gerida em Apps LógicaS Azure](/azure/logic-apps/create-managed-service-identity)
- [Documentação do conector Azure Sentinel Logic Apps](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Melhor afinação de regras com os gráficos de pré-visualização da regra de análise (visualização pública)

O Azure Sentinel ajuda-o agora a afinar melhor as suas regras de análise, ajudando-o a aumentar a sua precisão e a diminuir o ruído.

Depois de editar uma regra de análise no separador **lógica de regras definida,** encontre a área **de simulação de Resultados** à direita. 

Selecione **Teste com dados atuais** para que o Azure Sentinel execute uma simulação das últimas 50 corridas da sua regra de análise. Um gráfico é gerado para mostrar o número médio de alertas que a regra teria gerado, com base nos dados brutos do evento avaliados. 

Para obter mais informações, consulte [Definir a lógica de consulta de regras e configurar as definições](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings).

## <a name="december-2020"></a>Dezembro de 2020

- [80 novas consultas de caça embuti](#80-new-built-in-hunting-queries)
- [Melhorias do agente Log Analytics](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 novas consultas de caça embuti
 
As consultas de caça incorporadas de Azure Sentinel capacitam os analistas da SOC a reduzir as lacunas na cobertura de deteção atual e a inflamar novas pistas de caça.

Esta atualização para o Azure Sentinel inclui novas consultas de caça que fornecem cobertura em toda a matriz-quadro MITRE ATT&CK:

- **Coleção**
- **Comando e Controlo**
- **Acesso Credencial**
- **Descoberta**
- **Execução**
- **Exfiltração**
- **Impacto**
- **Acesso Inicial**
- **Persistência**
- **Escalada de Privilégios**

As consultas de caça adicionadas são projetadas para ajudá-lo a encontrar atividade suspeita no seu ambiente. Embora possam devolver atividades legítimas e atividades potencialmente maliciosas, podem ser úteis para orientar a sua caça. 

Se depois de executar estas consultas, está confiante com os resultados, pode querer convertê-los em regras de análise ou adicionar resultados de caça a incidentes existentes ou novos.

Todas as consultas adicionais estão disponíveis através da página de Caça Sentinela Azure. Para mais informações, consulte [Hunt para ameaças com Azure Sentinel.](hunting.md)

### <a name="log-analytics-agent-improvements"></a>Melhorias do agente Log Analytics

Os utilizadores do Azure Sentinel beneficiam das seguintes melhorias do agente Log Analytics:

- **Suporte para mais sistemas operativos**, incluindo CentOS 8, RedHat 8 e SUSE Linux 15.
- **Apoio para Python 3** além de Python 2

O Azure Sentinel utiliza o agente Log Analytics para enviar eventos para o seu espaço de trabalho, incluindo eventos de Segurança do Windows, eventos Syslog, registos CEF e muito mais.

> [!NOTE]
> O agente Log Analytics é por vezes referido como o Agente OMS ou o Agente de Monitorização da Microsoft (MMA). 
> 

Para obter mais informações, consulte a [documentação do Log Analytics](/azure/azure-monitor/platform/log-analytics-agent) e as notas de lançamento do [agente Log Analytics](https://github.com/microsoft/OMS-Agent-for-Linux/releases).
## <a name="november-2020"></a>Novembro de 2020

- [Monitorize os seus Playbooks de Aplicações Lógicas em Azure Sentinel](#monitor-your-logic-apps-playbooks-in-azure-sentinel)
- [Conector Microsoft 365 Defender (pré-visualização pública)](#microsoft-365-defender-connector-public-preview)
### <a name="monitor-your-logic-apps-playbooks-in-azure-sentinel"></a>Monitorize os seus Playbooks de Aplicações Lógicas em Azure Sentinel

O Azure Sentinel integra-se agora com [o Azure Log Apps](/azure/logic-apps/), um serviço em nuvem que o ajuda a agendar, automatizar e orquestrar tarefas, processos de negócio e fluxos de trabalho.

Utilize uma App Azure Logic em Azure Sentinel como um livro de jogadas, que pode ser automaticamente invocado quando um incidente é criado, ou quando triagem e trabalho com incidentes. 

Para fornecer informações sobre a saúde, desempenho e uso dos seus livros de jogadas, incluindo qualquer que adicione com Azure Logic Apps, adicionámos um [Livro Azure](/azure/azure-monitor/platform/workbooks-overview) chamado **Playbooks health monitoring**. 

Utilize o manual de monitorização de **saúde playbooks** para monitorizar a saúde dos seus livros de jogadas ou procure anomalias na quantidade de execuções bem sucedidas ou falhadas. 

O **livro de monitorização de saúde playbooks** já está disponível na galeria Azure Sentinel Templates:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Livro de monitorização de saúde de livros de reprodução de livros de amostra":::

Para obter mais informações, consulte:

- [Documentação de Apps lógicas](/azure/logic-apps/monitor-logic-apps-log-analytics#set-up-azure-monitor-logs)

- [Documentação do Azure Monitor](/azure/azure-monitor/platform/activity-log#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Conector Microsoft 365 Defender (pré-visualização pública)
 
O conector Microsoft 365 Defender para OZure Sentinel permite-lhe transmitir registos de caça avançados (um tipo de dados de eventos brutos) do Microsoft 365 Defender para o Azure Sentinel. 

Com a integração do [Microsoft Defender for Endpoint (MDATP)](/windows/security/threat-protection/) no guarda-chuva de segurança [Microsoft 365 Defender,](/microsoft-365/security/mtp/microsoft-threat-protection) pode agora recolher o seu Microsoft Defender para eventos de caça avançados do Endpoint utilizando o conector Microsoft 365 Defender e transmiti-los diretamente para novas tabelas construídas de propósito no seu espaço de trabalho Azure Sentinel. 

As tabelas Azure Sentinel são construídas sobre o mesmo esquema que é usado no portal Microsoft 365 Defender, e fornecem-lhe acesso completo ao conjunto completo de registos de caça avançados. 

Para obter mais informações, consulte [os dados do Microsoft 365 Defender ao Azure Sentinel](connect-microsoft-365-defender.md).

> [!NOTE]
> O Microsoft 365 Defender era anteriormente conhecido como Microsoft Threat Protection ou MTP. O Microsoft Defender for Endpoint era anteriormente conhecido como Microsoft Defender Advanced Threat Protection ou MDATP.
> 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[A bordo Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Obter visibilidade sobre os alertas](quickstart-get-visibility.md)
