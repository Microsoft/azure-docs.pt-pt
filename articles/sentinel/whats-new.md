---
title: Novidades em Azure Sentinel
description: Este artigo descreve novas funcionalidades no Azure Sentinel dos últimos meses.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 7f9a8cb54458999d8f20a258bc36241dfdbd0de8
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376040"
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

## <a name="april-2021"></a>abril de 2021

- [Cronologia do incidente (pré-visualização pública)](#incident-timeline-public-preview)

### <a name="incident-timeline-public-preview"></a>Cronologia do incidente (pré-visualização pública)

O primeiro separador de uma página de detalhes de incidentes é agora o **Timeline**, que mostra uma cronologia de alertas e marcadores no incidente. A cronologia de um incidente pode ajudá-lo a entender melhor o incidente e reconstruir a linha do tempo da atividade do atacante através dos alertas e marcadores relacionados.

- Selecione um item na linha do tempo para ver os seus detalhes, sem deixar o contexto do incidente
- Filtrar o conteúdo da linha do tempo apenas para mostrar alertas ou marcadores, ou itens de uma gravidade específica ou tática MITRE.
- Pode selecionar o link **de ID de alerta do Sistema** para visualizar todo o registo ou a ligação **Eventos** para ver os eventos relacionados na área **de Logs.**

Por exemplo:

:::image type="content" source="media/tutorial-investigate-cases/incident-timeline.png" alt-text="Separador de linha do tempo incidente":::

Para mais informações, consulte [Tutorial: Investigue incidentes com Azure Sentinel.](tutorial-investigate-cases.md)

## <a name="march-2021"></a>março de 2021

- [Desemocione os livros para atualizar automaticamente enquanto está no modo de visualização](#set-workbooks-to-automatically-refresh-while-in-view-mode)
- [Novas deteções para Azure Firewall](#new-detections-for-azure-firewall)
- [Regras de automação e livros de jogadas desencadeados por incidentes](#automation-rules-and-incident-triggered-playbooks) (incluindo documentação totalmente nova do livro de jogadas)
- [Novos enriquecimentos de alerta: mapeamento de entidades melhoradas e detalhes personalizados](#new-alert-enrichments-enhanced-entity-mapping-and-custom-details)
- [Imprima os seus livros de Azure Sentinel ou guarde como PDF](#print-your-azure-sentinel-workbooks-or-save-as-pdf)
- [Filtros de incidentes e preferências de classificação agora guardadas na sua sessão (visualização pública)](#incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview)
- [Integração de incidentes microsoft 365 Defender (visualização pública)](#microsoft-365-defender-incident-integration-public-preview)
- [Novos conectores de serviço da Microsoft que utilizam a Política Azure](#new-microsoft-service-connectors-using-azure-policy)

### <a name="set-workbooks-to-automatically-refresh-while-in-view-mode"></a>Desemocione os livros para atualizar automaticamente enquanto está no modo de visualização

Os utilizadores do Azure Sentinel podem agora utilizar a nova capacidade do [Azure Monitor](https://techcommunity.microsoft.com/t5/azure-monitor/azure-workbooks-set-it-to-auto-refresh/ba-p/2228555) para atualizar automaticamente os dados do livro durante uma sessão de visualização.

Em cada modelo de livro ou livro, selecione :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false"::: **'Renovar' automático** para mostrar as opções de intervalo. Selecione a opção que pretende utilizar para a sessão de visualização atual e selecione **Aplicar**.

- Os intervalos de atualização suportados variam entre **5 minutos** e **1 dia**.
- Por predefinição, a atualização automática é desligada. Para otimizar o desempenho, a atualização automática também é desligada cada vez que fecha um livro e não é executada em segundo plano. Volte a refrescar-se automaticamente da próxima vez que abrir o livro.
- A atualização automática é interrompida enquanto edita um livro de trabalho, e os intervalos de atualização automática são reiniciados cada vez que muda para visualizar o modo de edição.

    Os intervalos também são reiniciados se atualizar manualmente o livro, selecionando o :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false"::: botão **Refresh.**

Para obter mais informações, consulte [Tutorial: Visualize e monitorize os seus dados](tutorial-monitor-your-data.md) e a documentação do [Azure Monitor](../azure-monitor/visualize/workbooks-overview.md).

### <a name="new-detections-for-azure-firewall"></a>Novas deteções para Azure Firewall

Várias deteções fora da caixa para a Azure Firewall foram adicionadas à área [de Analytics](import-threat-intelligence.md#analytics-puts-your-threat-indicators-to-work-detecting-potential-threats) em Azure Sentinel. Estas novas deteções permitem que as equipas de segurança recebam alertas se as máquinas da rede interna tentarem consultar ou ligar nomes de domínios da Internet ou endereços IP que estejam associados a IOCs conhecidos, tal como definido na consulta da regra de deteção.

As novas deteções incluem:

- [Farol da Rede Solorigate](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
- [Domínios e haléios de gálio conhecidos](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
- [IP de IRIDIUM conhecido](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
- [Domínios conhecidos do grupo de fósforo/IP](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
- [Domínios THALLIUM incluídos na tomada de DCU](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
- [Haxixe maldoc relacionado com zinco conhecido](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)
- [Domínios conhecidos do grupo STRONTIUM](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
- [NOBELIUM - IOCs de domínio e IP - março de 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)


As deteções de Firewalls Azure são continuamente adicionadas à galeria de modelos incorporada. Para obter as deteções mais recentes para Azure Firewall, em **modelos de regras,** filtre as **Fontes de Dados** por **Azure Firewall**:

:::image type="content" source="media/whats-new/new-detections-analytics-efficiency-workbook.jpg" alt-text="Novas deteções no livro de eficiência da Analytics":::

Para obter mais informações, consulte [novas deteções para Azure Firewall em Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-network-security/new-detections-for-azure-firewall-in-azure-sentinel/ba-p/2244958).

### <a name="automation-rules-and-incident-triggered-playbooks"></a>Regras de automação e livros de jogadas desencadeados por incidentes

As regras de automação são um novo conceito no Azure Sentinel, permitindo-lhe gerir centralmente a automatização do tratamento de incidentes. Além de permitir atribuir playbooks a incidentes (não apenas a alertas como antes), as regras de automação também permitem automatizar respostas para várias regras de análise ao mesmo tempo, marcar, atribuir ou fechar incidentes sem necessidade de playbooks, e controlar a ordem de ações que são executadas. As regras de automação irão simplificar o uso da automatização no Azure Sentinel e permitir-lhe-ão simplificar fluxos de trabalho complexos para os seus processos de orquestração de incidentes.

Saiba mais com esta [explicação completa das regras de automação.](automate-incident-handling-with-automation-rules.md)

Como mencionado acima, os playbooks podem agora ser ativados com o gatilho do incidente, além do gatilho de alerta. O gatilho do incidente fornece aos seus playbooks um conjunto maior de entradas para trabalhar (uma vez que o incidente inclui todos os dados de alerta e entidade também), dando-lhe ainda mais potência e flexibilidade nos seus fluxos de trabalho de resposta. Os livros de jogadas desencadeados por incidentes são ativados por serem chamados das regras de automação.

Saiba mais sobre [as capacidades melhoradas dos playbooks](automate-responses-with-playbooks.md)e como [criar um fluxo de trabalho de resposta](tutorial-respond-threats-playbook.md) utilizando livros de jogadas juntamente com as regras de automação.

### <a name="new-alert-enrichments-enhanced-entity-mapping-and-custom-details"></a>Novos enriquecimentos de alerta: mapeamento de entidades melhoradas e detalhes personalizados

Enriqueça os seus alertas de duas novas formas de torná-los mais utilizáveis e mais informativos.

Comece por levar o mapeamento da sua entidade para o próximo nível. Agora é possível mapear quase 20 tipos de entidades, desde utilizadores, anfitriões e endereços IP, até ficheiros e processos, até caixas de correio, recursos Azure e dispositivos IoT. Também pode utilizar vários identificadores para cada entidade, para fortalecer a sua identificação única. Isto dá-lhe um conjunto de dados muito mais rico nos seus incidentes, proporcionando uma correlação mais ampla e uma investigação mais poderosa. [Aprenda a nova forma de mapear entidades](map-data-fields-to-entities.md) nos seus alertas.

[Leia mais sobre as entidades](entities-in-azure-sentinel.md) e veja a [lista completa das entidades disponíveis e dos seus identificadores.](entities-reference.md)

Dê um impulso ainda maior às suas capacidades de investigação e resposta, personalizando os seus alertas para os detalhes da superfície dos seus eventos crus. Traga visibilidade de conteúdo de eventos para os seus incidentes, dando-lhe cada vez mais poder e flexibilidade na resposta e investigação de ameaças à segurança. [Saiba como apresentar detalhes personalizados](surface-custom-details-in-alerts.md) nos seus alertas.



### <a name="print-your-azure-sentinel-workbooks-or-save-as-pdf"></a>Imprima os seus livros de Azure Sentinel ou guarde como PDF

Agora pode imprimir livros de trabalho do Azure Sentinel, que também lhe permite exportar para PDFs e economizar localmente ou partilhar.

No seu livro, selecione o menu de opções > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Imprimir o conteúdo**. Em seguida, selecione a sua impressora ou **selecione Guardar o PDF** conforme necessário.

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="Imprima o seu livro ou guarde como PDF.":::

Para mais informações, consulte [Tutorial: Visualize e monitorize os seus dados.](tutorial-monitor-your-data.md)

### <a name="incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview"></a>Filtros de incidentes e preferências de classificação agora guardadas na sua sessão (visualização pública)

Agora, os filtros e a triagem de incidentes são guardados durante toda a sua sessão do Azure Sentinel, mesmo navegando para outras áreas do produto.
Enquanto ainda estiver na mesma sessão, navegar de volta à área [de Incidentes](tutorial-investigate-cases.md) em Azure Sentinel mostra os seus filtros e a triagem tal como o deixou.

> [!NOTE]
> Os filtros e a triagem de incidentes não são guardados depois de deixar o Azure Sentinel ou refrescar o seu navegador.

### <a name="microsoft-365-defender-incident-integration-public-preview"></a>Integração de incidentes microsoft 365 Defender (visualização pública)

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
- [Insights da UEBA na página da entidade (visualização pública)](#ueba-insights-in-the-entity-page-public-preview)
- [Melhoria da pesquisa de incidentes (visualização pública)](#improved-incident-search-public-preview)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>Livro de certificação do modelo de maturidade da cibersegurança (CMMC)

O Azure Sentinel CMMC Workbook fornece um mecanismo para visualizar consultas de registo alinhadas com os controlos cmmc em todo o portfólio da Microsoft, incluindo ofertas de segurança da Microsoft, Office 365, Teams, Intune, Windows Virtual Desktop e muito mais.

O livro da CMMC permite que arquitetos de segurança, engenheiros, analistas de operações de segurança, gestores e profissionais de TI ganhem visibilidade de consciência da situação para a postura de segurança das cargas de trabalho em nuvem. Existem também recomendações para selecionar, projetar, implementar e configurar ofertas da Microsoft para o alinhamento com os respetivos requisitos e práticas da CMMC.

Mesmo que não seja obrigado a cumprir com a CMMC, o manual da CMMC é útil na construção de Centros de Operações de Segurança, no desenvolvimento de alertas, na visualização de ameaças e na sensibilização para a situação das cargas de trabalho.

Aceda ao livro de trabalhos da CMMC na área dos **livros de trabalho** Azure Sentinel. Selecione **o modelo** e, em seguida, procure **cmMC**.

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="Alternar o guia de livros da CMMC de vez em quando" lightbox="media/whats-new/cmmc-guide-toggle.gif":::


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

### <a name="ueba-insights-in-the-entity-page-public-preview"></a>Insights da UEBA na página da entidade (visualização pública)

As páginas de detalhes da entidade Azure Sentinel fornecem um [painel insights,](identify-threats-with-entity-behavior-analytics.md#entity-insights)que exibe insights comportamentais sobre a entidade e ajuda a identificar rapidamente anomalias e ameaças à segurança.

Se tiver [a UEBA ativada,](ueba-enrichments.md)e tiver selecionado um prazo de pelo menos quatro dias, este painel de insights incluirá também as seguintes novas secções para insights da UEBA:

|Section  |Descrição  |
|---------|---------|
|**UEBA Insights**     | Resume as atividades anómalas do utilizador: <br>- Em locais geográficos, dispositivos e ambientes<br>- Através de horizontes de tempo e frequência, em comparação com a história do próprio utilizador <br>- Comparado com o comportamento dos pares <br>- Comparado com o comportamento da organização     |
|**Pares de utilizadores baseados na adesão ao Grupo de Segurança**     |   Lista os pares do utilizador com base na adesão do Azure AD Security Groups, fornecendo às equipas de operações de segurança uma lista de outros utilizadores que partilham permissões semelhantes.  |
|**Permissões de acesso ao utilizador para subscrição do Azure**     |     Mostra as permissões de acesso do utilizador às subscrições Azure acessíveis diretamente, ou através de grupos AD /principais de serviço Azure.   |
|**Indicadores de ameaça relacionados com o utilizador**     |  Lista uma coleção de ameaças conhecidas relativas a endereços IP representados nas atividades do utilizador. As ameaças são listadas por tipo de ameaça e família, e são enriquecidas pelo serviço de inteligência de ameaça da Microsoft.       |
|     |         |

### <a name="improved-incident-search-public-preview"></a>Melhoria da pesquisa de incidentes (visualização pública)

Melhorámos a experiência de pesquisa de incidentes do Azure Sentinel, permitindo-lhe navegar mais rapidamente através de incidentes enquanto investiga uma ameaça específica.

Ao procurar incidentes em Azure Sentinel, pode agora pesquisar pelos seguintes detalhes do incidente:

- ID
- Título
- Produto
- Proprietário
- Etiqueta

## <a name="january-2021"></a>Janeiro de 2021

- [Assistente de regras de análise: Experiência de edição de consulta melhorada (visualização pública)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [Módulo Az.SecurityInsights PowerShell (pré-visualização pública)](#azsecurityinsights-powershell-module-public-preview)
- [Conector de base de dados SQL](#sql-database-connector)
- [Conector Dinâmico 365 (Visualização pública)](#dynamics-365-connector-public-preview)
- [Melhores comentários de incidentes](#improved-incident-comments)
- [Clusters dedicados log analytics](#dedicated-log-analytics-clusters)
- [Aplicativos lógicos geridos identidades](#logic-apps-managed-identities)
- [Melhor afinação de regras com os gráficos de pré-visualização da regra de análise](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Assistente de regras de análise: Experiência de edição de consulta melhorada (visualização pública)

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

### <a name="dynamics-365-connector-public-preview"></a>Conector Dinâmico 365 (Visualização pública)

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
- **Escalamento de Privilégios**

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
