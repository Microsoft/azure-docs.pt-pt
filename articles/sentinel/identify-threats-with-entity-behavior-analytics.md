---
title: Identifique ameaças avançadas com a Análise de Comportamento de Utilizador e Entidade (UEBA) no Azure Sentinel Microsoft Docs
description: Crie linhas de base comportamentais para entidades (utilizadores, nomes de anfitriões, endereços IP) e use-as para detetar comportamentos anómalos e identificar ameaças persistentes avançadas de zero dias (APT).
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/19/2020
ms.author: yelevin
ms.openlocfilehash: ad0486c9d2eb6c651b507f4b0a44f4a6fc2b018f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100665"
---
# <a name="identify-advanced-threats-with-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Identificar ameaças avançadas com a Análise de Comportamento de Utilizador e Entidade (UEBA) em Azure Sentinel

## <a name="what-is-user-and-entity-behavior-analytics-ueba"></a>O que é Analítica de Comportamento de Utilizador e Entidade (UEBA)?

### <a name="the-concept"></a>O conceito

Identificar ameaças dentro da sua organização e o seu impacto potencial - seja uma entidade comprometida ou um infiltrado malicioso - sempre foi um processo demorado e de mão de obra intensiva. Peneirar através de alertas, ligar os pontos e caçar ativos soma-se a quantidades massivas de tempo e esforço gastos com retornos mínimos, e a possibilidade de ameaças sofisticadas simplesmente evitar a descoberta. Ameaças particularmente evasivas como ameaças de dia zero, alvo e ameaças persistentes avançadas podem ser as mais perigosas para a sua organização, tornando a sua deteção ainda mais crítica.

A capacidade da UEBA em Azure Sentinel elimina a penosa das cargas de trabalho dos seus analistas e a incerteza dos seus esforços, e fornece inteligência de alta fidelidade e ação, para que possam concentrar-se na investigação e remediação.

Como o Azure Sentinel recolhe registos e alertas de todas as suas fontes de dados conectadas, analisa-os e constrói perfis comportamentais de base das entidades da sua organização (utilizadores, anfitriões, endereços IP, aplicações, etc.) através do horizonte de grupo de tempo e pares. Utilizando uma variedade de técnicas e capacidades de aprendizagem automática, o Sentinel pode então identificar atividade anómala e ajudá-lo a determinar se um ativo foi comprometido. Não só isso, mas também pode descobrir a sensibilidade relativa de determinados ativos, identificar grupos de ativos pares e avaliar o impacto potencial de qualquer ativo comprometido (o seu "raio de explosão"). Armado com esta informação, pode priorizar a sua investigação e tratamento de incidentes. 

### <a name="architecture-overview"></a>Descrição geral da arquitetura

:::image type="content" source="media/identify-threats-with-entity-behavior-analytics/entity-behavior-analytics-architecture.png" alt-text="Arquitetura de análise de comportamento de entidade":::

### <a name="security-driven-analytics"></a>Análises orientadas para a segurança

Inspirado no paradigma da Gartner para soluções UEBA, o Azure Sentinel oferece uma abordagem "externa", baseada em três quadros de referência:

- **Casos de utilização:** Priorizando vetores e cenários de ataque relevantes com base em pesquisas de segurança alinhadas com o MITRE ATT&quadro CK de táticas, técnicas e sub-técnicas que colocam várias entidades como vítimas, autores ou pontos de rotação na cadeia de morte; O Azure Sentinel foca-se especificamente nos registos mais valiosos que cada fonte de dados pode fornecer.

- **Fontes de dados:** Embora, em primeiro lugar e principalmente, suporte a fontes de dados do Azure, o Azure Sentinel seleciona cuidadosamente fontes de dados de terceiros para fornecer dados que correspondam aos nossos cenários de ameaça.

- **Analítica:** Utilizando vários algoritmos de machine learning (ML), Azure Sentinel identifica atividades anómalas e apresenta evidências claras e concisas sob a forma de enriquecimentos contextuais, alguns exemplos dos quais aparecem abaixo.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/behavior-analytics-top-down.png" alt-text="Arquitetura de análise de comportamento de entidade" indica uma anomalia identificada:
- em locais geográficos, dispositivos e ambientes.
- através de horizontes de tempo e frequência (em comparação com a própria história do utilizador).
- em comparação com o comportamento dos pares.
- em comparação com o comportamento da organização.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/context.png" alt-text="Arquitetura de análise de comportamento de entidade":::


### <a name="scoring"></a>Classificação

Cada atividade é pontuada com "Resultado Prioritário de Investigação" – que determina a probabilidade de um utilizador específico realizar uma atividade específica, com base na aprendizagem comportamental do utilizador e dos seus pares. As atividades identificadas como as mais anormais recebem as pontuações mais altas (numa escala de 0-10).

Veja como a análise de comportamento é usada na [Microsoft Cloud App Security](https://techcommunity.microsoft.com/t5/microsoft-security-and/prioritize-user-investigations-in-cloud-app-security/ba-p/700136) para um exemplo de como isto funciona.

## <a name="entities-in-azure-sentinel"></a>Entidades em Azure Sentinel

### <a name="entity-identifiers"></a>Identificadores de entidades

Quando os alertas são enviados para o Azure Sentinel, incluem elementos de dados que o Azure Sentinel identifica e classifica como entidades, tais como contas de utilizador, anfitriões, endereços IP e outros. Por vezes, esta identificação pode ser um desafio, caso o alerta não contenha informação suficiente sobre a entidade.

Por exemplo, as contas de utilizador podem ser identificadas de uma forma: utilizando o identificador numérico de uma conta AZure AD (GUID), ou o seu nome principal de utilizador (UPN) ou, em alternativa, utilizando uma combinação do seu nome de utilizador e do seu nome de domínio NT. Diferentes fontes de dados podem identificar o mesmo utilizador de diferentes formas. Assim, sempre que possível, a Azure Sentinel funde esses identificadores numa única entidade, para que possa ser devidamente identificado.

Pode acontecer, no entanto, que um dos seus fornecedores de recursos cria um alerta no qual uma entidade não está suficientemente identificada - por exemplo, um nome de utilizador sem o contexto do nome de domínio. Nesse caso, a entidade utilizadora não pode ser fundida com outros casos da mesma conta de utilizador, que seriam identificados como uma entidade separada, e essas duas entidades permaneceriam separadas em vez de unificadas.

De forma a minimizar o risco de isso acontecer, deve verificar se todos os seus fornecedores de alerta identificam adequadamente as entidades nos alertas que produzem. Além disso, sincronizar as entidades de conta de utilizador com o Azure Ative Directory pode criar um diretório unificador, que poderá fundir entidades de conta de utilizador.

Os seguintes tipos de entidades estão atualmente identificados no Azure Sentinel:

- Conta de utilizador (Conta)
- Anfitrião
- Endereço IP (IP)
- Software maligno
- Ficheiro
- Processo
- Aplicação em nuvem (CloudApplication)
- Nome de domínio (DNS)
- Recurso do Azure
- Arquivo (FileHash)
- Chave do registo
- Valor de registo
- Grupo de segurança
- URL
- Dispositivo IoT
- Mailbox
- Aglomerado de correio
- Mensagem de correio
- Correio de submissão

### <a name="entity-pages"></a>Páginas de entidade

Quando encontra qualquer entidade (atualmente limitada a utilizadores e anfitriões) numa pesquisa, um alerta ou uma investigação, pode selecionar a entidade e ser levado para uma página de **entidade,** uma folha de dados cheia de informações úteis sobre essa entidade. Os tipos de informação que irá encontrar nesta página incluem factos básicos sobre a entidade, uma cronologia de eventos notáveis relacionados com esta entidade e insights sobre o comportamento da entidade.
 
As páginas de entidades consistem em três partes:
- O painel do lado esquerdo contém as informações de identificação da entidade, recolhidas a partir de fontes de dados como Azure Ative Directory, Azure Monitor, Azure Security Center e Microsoft Defender.

- O painel central mostra uma cronologia gráfica e textual de eventos notáveis relacionados com a entidade, tais como alertas, marcadores e atividades. As atividades são agregações de eventos notáveis da Log Analytics. As consultas que detetam essas atividades são desenvolvidas por equipas de investigação de segurança da Microsoft.

- O painel do lado direito apresenta insights comportamentais sobre a entidade. Estes conhecimentos ajudam a identificar rapidamente anomalias e ameaças à segurança. Os insights são desenvolvidos pelas equipas de pesquisa de segurança da Microsoft, e são baseados em modelos de deteção de anomalias.

### <a name="the-timeline"></a>A linha do tempo

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="Arquitetura de análise de comportamento de entidade":::

A linha do tempo é uma parte importante da contribuição da página da entidade para a análise de comportamentos em Azure Sentinel. Apresenta uma história sobre eventos relacionados com entidades, ajudando-o a compreender a atividade da entidade dentro de um prazo específico.

Pode escolher o intervalo de **tempo** entre várias opções predefinidas (como *as últimas 24 horas),* ou defini-lo para qualquer período de tempo definido sob medida. Além disso, pode definir filtros que limitam a informação na linha do tempo a tipos específicos de eventos ou alertas.

Os seguintes tipos de itens estão incluídos na linha do tempo:

- Alertas - quaisquer alertas em que a entidade seja definida como uma **entidade mapeada** . Tenha em atenção que se a sua organização criou [alertas personalizados utilizando regras de análise,](./tutorial-detect-threats-custom.md)deve certificar-se de que o mapeamento da entidade das regras é feito corretamente.

- Marcadores - quaisquer marcadores que incluam a entidade específica mostrada na página.

- Atividades - agregação de eventos notáveis relacionados com a entidade. 
 
### <a name="entity-insights"></a>Insights de Entidade
 
Os conhecimentos das entidades são consultas definidas pelos investigadores de segurança da Microsoft para ajudar os seus analistas a investigar de forma mais eficiente e eficaz. Os insights são apresentados como parte da página da entidade, e fornecem informações de segurança valiosas sobre anfitriões e utilizadores, sob a forma de dados e gráficos tabulares. Ter a informação aqui significa que não tem de desviar para o Log Analytics. Os insights incluem dados relativos a Ins-Ins, Adições de Grupo, Eventos Anómalos e muito mais, e incluem algoritmos ML avançados para detetar comportamentos anómalos. Os insights baseiam-se nos seguintes tipos de dados:
- Syslog
- SecurityEvent
- Registos de Auditoria
- Registos de inscrição
- Atividade de Escritório
- BehaviorAnalytics (UEBA) 
 
### <a name="how-to-use-entity-pages"></a>Como usar páginas de entidades

As páginas da entidade são projetadas para fazer parte de múltiplos cenários de utilização, e podem ser acedidas a partir da gestão de incidentes, do gráfico de investigação, dos marcadores ou diretamente da página de pesquisa da entidade sob análise de comportamento da **Entidade** no menu principal do Azure Sentinel.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Arquitetura de análise de comportamento de entidade":::


## <a name="data-schema"></a>Esquema de dados

### <a name="behavior-analytics-table"></a>Tabela de análise de comportamento

| Campo                     | Descrição                                                         |
|---------------------------|---------------------------------------------------------------------|
| TenantId                  | número de ID único do inquilino                                      |
| SourceRecordId            | número de ID único do evento EBA                                   |
| TimeGenerated             | timetamp da ocorrência da atividade                              |
| Tempo Processado             | timetamp do processamento da atividade pelo motor EBA            |
| Dispositivo de atividade              | categoria de alto nível da atividade                                 |
| ActionType                | nome normalizado da atividade                                     |
| Nome de Utilizador                  | nome de utilizador do utilizador que iniciou a atividade                    |
| UserPrincipalName         | nome de utilizador completo do utilizador que iniciou a atividade               |
| Fonte de Eventos               | fonte de dados que forneceu o evento original                        |
| SourceIPAddress           | Endereço IP a partir do qual a atividade foi iniciada                        |
| Localização SourceIP          | país a partir do qual a atividade foi iniciada, enriquecida a partir do endereço IP |
| FonteDevice              | nome de anfitrião do dispositivo que iniciou a atividade                  |
| DestinoIPAddress      | Endereço IP do alvo da atividade                            |
| DestinationIPLocação     | país do alvo da atividade, enriquecido a partir do endereço IP     |
| DestinoDevice         | nome do dispositivo-alvo                                           |
| **UtilizadoresInsights**         | enriquecimentos contextuais dos utilizadores envolvidos                            |
| **DispositivosInsights**       | enriquecimentos contextuais de dispositivos envolvidos                          |
| **AtividadeInsights**      | análise contextual da atividade com base no nosso perfil              |
| **InvestigaçãoPrioridade** | pontuação de anomalia, entre 0-10 (0=benigno, 10=altamente anómalo)         |
|

### <a name="querying-behavior-analytics-data"></a>Consulta de dados de análise de comportamento

Usando [o KQL,](https://docs.microsoft.com/azure/data-explorer/kusto/query/)podemos consultar a Tabela de Análise Comportamental.

Por exemplo – se quisermos encontrar todos os casos de um utilizador que não tenha assinado num recurso Azure, onde foi a primeira tentativa do utilizador de se ligar a partir de um determinado país, e as ligações desse país são incomuns mesmo para os pares do utilizador, podemos utilizar a seguinte consulta:

```Kusto
BehaviorAnalytics
| where ActivityType == "FailedLogOn"
| where FirstTimeUserConnectedFromCountry == True
| where CountryUncommonlyConnectedFromAmongPeers == True
```

### <a name="user-peers-metadata---table-and-notebook"></a>Metadados de pares de utilizadores - tabela e caderno

Os metadados dos pares dos utilizadores fornecem um contexto importante na deteção de ameaças, na investigação de um incidente e na procura de uma ameaça potencial. Os analistas de segurança podem observar as atividades normais dos pares de um utilizador para determinar se as atividades do utilizador são incomuns em comparação com as dos seus pares.

O Azure Sentinel calcula e classifica os pares de um utilizador, com base na adesão do grupo de segurança Azure AD do utilizador, lista de correio eletrónico, etc, e armazena os pares classificados 1-20 na tabela **UserPeerAnalytics.** A imagem abaixo mostra o esquema da tabela UserPeerAnalytics, e exibe os oito melhores pares do utilizador Kendall Collins. O Azure Sentinel utiliza o algoritmo *de frequência-reverso de frequência* (TF-IDF) para normalizar a pesagem para calcular a classificação: quanto menor o grupo, maior o peso. 

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-peers-metadata.png" alt-text="Arquitetura de análise de comportamento de entidade":::

Pode utilizar o [caderno Jupyter](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) fornecido no repositório Azure Sentinel GitHub para visualizar os metadados dos pares do utilizador. Para obter instruções detalhadas sobre como utilizar o caderno, consulte o caderno [de metadados de segurança do utilizador.](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb)

### <a name="permission-analytics---table-and-notebook"></a>Análise de permissão - mesa e caderno

A análise de permissão ajuda a determinar o impacto potencial do comprometimento de um ativo organizacional por um intruso. Este impacto também é conhecido como o "raio de explosão" do ativo. Os analistas de segurança podem usar esta informação para priorizar investigações e tratamento de incidentes.

O Azure Sentinel determina os direitos de acesso diretos e transitórios detidos por um determinado utilizador aos recursos da Azure, avaliando as subscrições do Azure que o utilizador pode aceder diretamente ou através de grupos ou principais serviços. Esta informação, bem como a lista completa da adesão ao grupo de segurança Azure AD do utilizador, é então armazenada na tabela **UserAccessAnalytics.** A imagem abaixo mostra uma linha de amostra na tabela UserAccessAnalytics, para o utilizador Alex Johnson. **A entidade-fonte** é a conta principal do utilizador ou serviço, e **a entidade-alvo** é o recurso a que a entidade-fonte tem acesso. Os valores do **nível** de acesso e **do tipo** de acesso dependem do modelo de controlo de acesso da entidade-alvo. Pode ver que a Alex tem acesso ao Azure Subscription *Contoso Hotels Tenant.* O modelo de controlo de acesso da subscrição é o RBAC.   

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-access-analytics.png" alt-text="Arquitetura de análise de comportamento de entidade":::

Pode utilizar o [portátil Jupyter](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) (o mesmo caderno acima mencionado) do repositório Azure Sentinel GitHub para visualizar os dados de análise de permissão. Para obter instruções detalhadas sobre como utilizar o caderno, consulte o caderno [de metadados de segurança do utilizador.](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb)

### <a name="hunting-queries-and-exploration-queries"></a>Consultas de caça e consultas de exploração

Azure Sentinel fornece fora da caixa um conjunto de consultas de caça, consultas de exploração e um livro, baseado na tabela BehaviorAnalytics. Estas ferramentas apresentam dados enriquecidos, focados em casos de uso específico, que indicam comportamento anómalo. 

Saiba mais sobre [a caça e o gráfico de investigação](./hunting.md) em Azure Sentinel.

## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu sobre as capacidades de análise de comportamento da entidade de Azure Sentinel. Para obter orientações práticas sobre a implementação e para utilizar os insights que obteve, consulte os seguintes artigos:

- [Ativar a análise de comportamento da entidade](./enable-entity-behavior-analytics.md) em Azure Sentinel.
- [Caça por ameaças à segurança.](./hunting.md)