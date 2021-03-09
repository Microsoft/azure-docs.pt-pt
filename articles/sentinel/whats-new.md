---
title: Novidades em Azure Sentinel
description: Este artigo descreve novas funcionalidades no Azure Sentinel dos últimos meses.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: 66294e3c59c2252042e30864dd220683106cd574
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485389"
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

## <a name="march-2021"></a>março de 2021

- [Integração de incidentes microsoft 365 Defender](#microsoft-365-defender-incident-integration) (pré-visualização pública)
- [Novos conectores de serviço da Microsoft que utilizam a Política Azure](#new-microsoft-service-connectors-using-azure-policy)

### <a name="microsoft-365-defender-incident-integration"></a>Integração de incidentes do Microsoft 365 Defender

A integração de incidentes microsoft [365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) do Azure Sentinel permite transmitir todos os incidentes M365D em Azure Sentinel e mantê-los sincronizados entre ambos os portais. Incidentes de M365D (anteriormente conhecido como Microsoft Threat Protection ou MTP) incluem todos os alertas, entidades e informações relevantes associados, fornecendo-lhe contexto suficiente para realizar triagem e investigação preliminar em Azure Sentinel. Uma vez em Sentinel, os Incidentes permanecerão bi-direccionalmente sincronizados com m365D, permitindo-lhe tirar partido dos benefícios de ambos os portais na sua investigação de incidentes.

Usar o Azure Sentinel e o Microsoft 365 Defender juntos dá-lhe o melhor dos dois mundos. Obtém-se a amplitude de perspicácia que um SIEM lhe dá em todo o âmbito de recursos de informação da sua organização, bem como a profundidade do poder de investigação personalizado e personalizado que um XDR fornece para proteger os seus recursos Microsoft 365, ambos coordenados e sincronizados para uma operação SOC sem costura.

Para mais informações, consulte [a integração do Microsoft 365 Defender com o Azure Sentinel](microsoft-365-defender-sentinel-integration.md).

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Novos conectores de serviço da Microsoft que utilizam a Política Azure

[A Azure Policy](../governance/policy/overview.md) é um serviço Azure que lhe permite usar políticas para impor e controlar as propriedades de um recurso. A utilização de políticas garante que os recursos se mantenham conformes com os seus padrões de governação de TI.

Entre as propriedades de recursos que podem ser controladas por políticas estão a criação e tratamento de diagnósticos e registos de auditoria. O Azure Sentinel usa agora a Azure Policy para permitir a aplicação de um conjunto comum de definições de registos de diagnóstico a todos os recursos (atuais e futuros) de um tipo específico cujos registos pretende ingerir no Azure Sentinel. Graças à Política Azure, já não terá de definir os registos de diagnóstico de configurações de recursos.

Os conectores baseados em políticas Azure estão agora disponíveis para os seguintes serviços Azure:
- [Cofre de Chave Azure](connect-azure-key-vault.md) (pré-visualização pública)
- [Serviço Azure Kubernetes](connect-azure-kubernetes-service.md) (pré-visualização pública)
- Bases de dados/servidores Azure SQL (GA)

Os clientes ainda poderão enviar os registos manualmente para instâncias específicas e não terão de usar o motor de política.

## <a name="february-2021"></a>Fevereiro de 2021

- [Livro de certificação do modelo de maturidade da cibersegurança (CMMC)](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [Conectores de dados de terceiros](#third-party-data-connectors)
- [Insights da UEBA na página da entidade](#ueba-insights-in-the-entity-page)
- [Pesquisa melhorada de incidentes](#improved-incident-search)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>Livro de certificação do modelo de maturidade da cibersegurança (CMMC)

O Azure Sentinel CMMC Workbook fornece um mecanismo para visualizar consultas de registo alinhadas com os controlos cmmc em todo o portfólio da Microsoft, incluindo ofertas de segurança da Microsoft, Office 365, Teams, Intune, Windows Virtual Desktop e muito mais.

O livro da CMMC permite que arquitetos de segurança, engenheiros, analistas de operações de segurança, gestores e profissionais de TI ganhem visibilidade de consciência da situação para a postura de segurança das cargas de trabalho em nuvem. Existem também recomendações para selecionar, projetar, implementar e configurar ofertas da Microsoft para o alinhamento com os respetivos requisitos e práticas da CMMC.

Mesmo que não seja obrigado a cumprir com a CMMC, o manual da CMMC é útil na construção de Centros de Operações de Segurança, no desenvolvimento de alertas, na visualização de ameaças e na sensibilização para a situação das cargas de trabalho.

Aceda ao livro de trabalhos da CMMC na área dos **livros de trabalho** Azure Sentinel. Selecione **o modelo** e, em seguida, procure **cmMC**.

Para obter mais informações, consulte:

- [Azure Sentinel Cybersecurity Maturity Model Certification (CMMC) Workbook](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [Tutorial: Visualizar e monitorizar os seus dados](tutorial-monitor-your-data.md)


### <a name="third-party-data-connectors"></a>Conectores de dados de terceiros

A nossa coleção de integrações de terceiros continua a crescer, com trinta conectores adicionados nos últimos dois meses. Aqui está uma lista:

- [Defesa de Phishing Agari e Proteção de Marcas](connect-agari-phishing-defense.md)
- [Eventos de Segurança Akamai](connect-akamai-security-events.md)
- [Alsid para Diretório Ativo](connect-alsid-active-directory.md)
- [Servidor Apache HTTP](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Blackberry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco Firepower eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Sistema de Computação Unificada cisco (UCS)](connect-cisco-ucs.md)
- [Inspetor da Empresa ESET](connect-data-sources.md)
- [Centro de Gestão de Segurança ESET](connect-data-sources.md)
- [Google Workspace (anteriormente G Suite)](connect-google-workspace.md)
- [Porta de entrada da WAF Imperva](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [Registos DNS NXLog](connect-nxlog-dns.md)
- [Auditoria NXLog Linux](connect-nxlog-linuxaudit.md)
- [Plataforma Onapsis](connect-data-sources.md)
- [Ponto de revisão sobre segurança de e-mail de pedido (POD)](connect-proofpoint-pod.md)
- [Base de Conhecimento de Gestão de Vulnerabilidades qualys](connect-data-sources.md)
- [Salesforce Service Cloud](connect-salesforce-service-cloud.md)
- [SonicWall Firewall](connect-data-sources.md)
- [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Proteção Symantec Endpoint](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Tendência Micro XDR](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page"></a>Insights da UEBA na página da entidade

As páginas de detalhes da entidade Azure Sentinel fornecem um [painel insights,](identify-threats-with-entity-behavior-analytics.md#entity-insights)que exibe insights comportamentais sobre a entidade e ajuda a identificar rapidamente anomalias e ameaças à segurança.

Se tiver [a UEBA ativada,](ueba-enrichments.md)e tiver selecionado um prazo de pelo menos quatro dias, este painel de insights incluirá também as seguintes novas secções para insights da UEBA:

|Section  |Descrição  |
|---------|---------|
|**UEBA Insights**     | Resume as atividades anómalas do utilizador: <br>- Em locais geográficos, dispositivos e ambientes<br>- Através de horizontes de tempo e frequência, em comparação com a história do próprio utilizador <br>- Comparado com o comportamento dos pares <br>- Comparado com o comportamento da organização     |
|**Pares de utilizadores baseados na adesão ao Grupo de Segurança**     |   Lista os pares do utilizador com base na adesão do Azure AD Security Groups, fornecendo às equipas de operações de segurança uma lista de outros utilizadores que partilham permissões semelhantes.  |
|**Permissões de acesso ao utilizador para subscrição do Azure**     |     Mostra as permissões de acesso do utilizador às subscrições Azure acessíveis diretamente, ou através de grupos AD /principais de serviço Azure.   |
|**Indicadores de ameaça relacionados com o utilizador**     |  Lista uma coleção de ameaças conhecidas relativas a endereços IP representados nas atividades do utilizador. As ameaças são listadas por tipo de ameaça e família, e são enriquecidas pelo serviço de inteligência de ameaça da Microsoft.       |
|     |         |

### <a name="improved-incident-search"></a>Pesquisa melhorada de incidentes

Melhorámos a experiência de pesquisa de incidentes do Azure Sentinel, permitindo-lhe navegar mais rapidamente através de incidentes enquanto investiga uma ameaça específica.

Ao procurar incidentes em Azure Sentinel, pode agora pesquisar pelos seguintes detalhes do incidente:

- ID
- Título
- Produto
- Proprietário
- Etiqueta

## <a name="january-2021"></a>Janeiro de 2021

- [Assistente de regras de análise: Experiência de edição de consulta melhorada (pré-visualização pública)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [Módulo Az.SecurityInsights PowerShell (pré-visualização pública)](#azsecurityinsights-powershell-module-public-preview)
- [Conector de base de dados SQL](#sql-database-connector)
- [Conector Dinâmico 365](#dynamics-365-connector)
- [Melhores comentários de incidentes](#improved-incident-comments)
- [Clusters dedicados log analytics](#dedicated-log-analytics-clusters)
- [Aplicativos lógicos geridos identidades](#logic-apps-managed-identities)
- [Melhor afinação de regras com os gráficos de pré-visualização da regra de análise](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Assistente de regras de análise: Experiência de edição de consulta melhorada (pré-visualização pública)

O assistente de regras de análise Azure Sentinel Agendado agora fornece as seguintes melhorias para consultas de escrita e edição:

-   Uma janela de edição expansível, proporcionando-lhe mais espaço de ecrã para visualizar a sua consulta.
-   Palavra-chave realçando no seu código de consulta.
-   Suporte autocompleto expandido.
-   Validações de consultas em tempo real. Os erros na sua consulta mostram agora como um bloco vermelho na barra de deslocação e como um ponto vermelho no nome do separador **de lógica de regra definida.** Além disso, uma consulta com erros não pode ser guardada.

Para obter mais informações, consulte [Tutorial: Crie regras de análise personalizadas para detetar ameaças.](tutorial-detect-threats-custom.md)
### <a name="azsecurityinsights-powershell-module-public-preview"></a>Módulo Az.SecurityInsights PowerShell (pré-visualização pública)

O Azure Sentinel suporta agora o novo módulo [Az.SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) PowerShell.

O módulo **Az.SecurityInsights** suporta casos comuns de utilização do Azure Sentinel, como interagir com incidentes para mudar estátuas, gravidade, proprietário, e assim por diante, adicionando comentários e rótulos a incidentes, e criando marcadores.

Embora recomendemos a utilização de modelos [Azure Resource Manager (ARM)](../azure-resource-manager/templates/index.yml) para o seu pipeline CI/CD, o módulo **Az.SecurityInsights** é útil para tarefas pós-implantação e é direcionado para a automatização SOC.  Por exemplo, a sua automatização SOC pode incluir passos para configurar conectores de dados, criar regras de análise ou adicionar ações de automação às regras de análise.

Para obter mais informações, incluindo uma lista completa e descrição dos cmdlets disponíveis, descrições de parâmetros e exemplos, consulte a [documentação Az.SecurityInsights PowerShell](/powershell/module/az.securityinsights/).

### <a name="sql-database-connector"></a>Conector de base de dados SQL

O Azure Sentinel fornece agora um conector de base de dados Azure SQL, que transmite os registos de auditoria e diagnóstico das suas bases de dados no Azure Sentinel e monitoriza continuamente a atividade em todos os seus casos.

O Azure SQL é um motor de base de dados totalmente gerido, Plataforma-as-a-Service (PaaS) que lida com a maioria das funções de gestão de bases de dados, tais como upgrade, patching, backups e monitorização, sem o envolvimento do utilizador.

Para obter mais informações, consulte [os diagnósticos de diagnóstico e registos de auditoria da base de dados Connect Azure SQL](connect-azure-sql-logs.md).

### <a name="dynamics-365-connector"></a>Conector Dinâmico 365

O Azure Sentinel fornece agora um conector para o Microsoft Dynamics 365, que permite recolher o utilizador, administração e suporte de registos de atividades da Dynamics 365 no Azure Sentinel. Pode utilizar estes dados para ajudá-lo a auditar a totalidade das ações de processamento de dados que estão a decorrer e analisá-los para possíveis falhas de segurança.

Para obter mais informações, consulte os registos de atividade da [Connect Dynamics 365 ao Azure Sentinel](connect-dynamics-365.md).

### <a name="improved-incident-comments"></a>Melhores comentários de incidentes

Os analistas usam comentários de incidentes para colaborar em incidentes, documentando processos e passos manualmente ou como parte de um livro de jogadas. 

A nossa melhor experiência de comentário de incidentes permite-lhe formatar os seus comentários e editar ou eliminar comentários existentes.

Para obter mais informações, consulte [criar automaticamente incidentes a partir de alertas de segurança da Microsoft.](create-incidents-from-alerts.md)
### <a name="dedicated-log-analytics-clusters"></a>Clusters dedicados log analytics

O Azure Sentinel agora suporta clusters dedicados do Log Analytics como uma opção de implementação. Recomendamos considerar um cluster dedicado se você:

- **Ingerir mais de 1 Tb por dia** no seu espaço de trabalho Azure Sentinel
- **Tenha vários espaços de trabalho do Azure Sentinel** na sua inscrição no Azure

Os clusters dedicados permitem-lhe utilizar funcionalidades como chaves geridas pelo cliente, lockbox, encriptação dupla e consultas de espaço de trabalho cruzada mais rápidas quando tem vários espaços de trabalho no mesmo cluster.

Para obter mais informações, consulte [os registos do Azure Monitor sobre agrupamentos dedicados](../azure-monitor/logs/logs-dedicated-clusters.md).

### <a name="logic-apps-managed-identities"></a>Aplicativos lógicos geridos identidades

O Azure Sentinel suporta agora identidades geridas para o conector Azure Sentinel Logic Apps, permitindo-lhe conceder permissões diretamente a um livro de jogadas específico para operar no Azure Sentinel em vez de criar identidades extra.

- **Sem uma identidade gerida,** o conector Logic Apps requer uma identidade separada com um papel rbac Azure Sentinel para funcionar no Azure Sentinel. A identidade separada pode ser um utilizador AZure AD ou um diretor de serviço, como uma aplicação registada Azure AD.

- **Ligar o suporte de identidade gerido na sua Aplicação Lógica** regista a App Lógica com AZure AD e fornece um ID de objeto. Utilize o ID do objeto em Azure Sentinel para atribuir a App Lógica com um papel RBAC Azure no seu espaço de trabalho Azure Sentinel. 

Para obter mais informações, consulte:

- [Autenticação com Identidade Gerida em Apps LógicaS Azure](../logic-apps/create-managed-service-identity.md)
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
- **Deteção**
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

Para obter mais informações, consulte a [documentação do Log Analytics](../azure-monitor/agents/log-analytics-agent.md) e as notas de lançamento do [agente Log Analytics](https://github.com/microsoft/OMS-Agent-for-Linux/releases).
## <a name="november-2020"></a>Novembro de 2020

- [Monitorize a saúde dos seus Playbooks em Azure Sentinel](#monitor-your-playbooks-health-in-azure-sentinel)
- [Conector Microsoft 365 Defender (pré-visualização pública)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>Monitorize a saúde dos seus Playbooks em Azure Sentinel

Os playbooks do Azure Sentinel são baseados em fluxos de trabalho construídos em [Azure Log Apps](../logic-apps/index.yml), um serviço de nuvem que o ajuda a agendar, automatizar e orquestrar tarefas, processos de negócios e fluxos de trabalho. Os playbooks podem ser automaticamente invocados quando um incidente é criado, ou quando triagem e trabalho com incidentes. 

Para fornecer informações sobre a saúde, desempenho e uso dos seus livros, adicionámos um [livro](../azure-monitor/visualize/workbooks-overview.md) chamado **Playbooks health monitoring**. 

Utilize o manual de monitorização de **saúde playbooks** para monitorizar a saúde dos seus livros de jogadas ou procure anomalias na quantidade de execuções bem sucedidas ou falhadas. 

O **livro de monitorização de saúde playbooks** já está disponível na galeria Azure Sentinel Templates:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Livro de monitorização de saúde de livros de reprodução de livros de amostra":::

Para obter mais informações, consulte:

- [Documentação de Apps lógicas](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Documentação do Azure Monitor](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

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
