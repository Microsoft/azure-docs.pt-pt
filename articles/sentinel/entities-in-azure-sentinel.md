---
title: Utilize entidades para classificar e analisar dados em Azure Sentinel | Microsoft Docs
description: Atribua classificações de entidades (utilizadores, hostnames, endereços IP) a itens de dados em Azure Sentinel, e use-as para comparar, analisar e correlacionar dados de várias fontes.
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
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 43da1af7a3001d7f8e000a878948428a3d63aa4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102456301"
---
# <a name="classify-and-analyze-data-using-entities-in-azure-sentinel"></a>Classificar e analisar dados usando entidades no Azure Sentinel

## <a name="what-are-entities"></a>O que são entidades?

Quando os alertas são enviados ou gerados pelo Azure Sentinel, contêm dados que o Sentinel pode reconhecer e classificar em categorias como **entidades.** Quando o Azure Sentinel compreende que tipo de entidade um determinado item de dados representa, sabe as perguntas certas a fazer sobre o mesmo, e pode então comparar insights sobre esse item em toda a gama de fontes de dados, e facilmente rastreá-lo e encaminhá-lo ao longo de toda a experiência Sentinel - análise, investigação, remediação, caça, e assim por diante. Alguns exemplos comuns de entidades são utilizadores, anfitriões, ficheiros, processos, endereços IP e URLs.

### <a name="entity-identifiers"></a>Identificadores de entidades

O Azure Sentinel suporta uma grande variedade de tipos de entidades. Cada tipo tem os seus próprios atributos únicos, incluindo alguns que podem ser usados para identificar uma determinada entidade. Estes atributos são representados como campos na entidade, e são **chamados identificadores**. Consulte a lista completa de entidades apoiadas e seus identificadores abaixo.

#### <a name="strong-and-weak-identifiers"></a>Identificadores fortes e fracos

Como referido logo acima, para cada tipo de entidade existem campos, ou conjuntos de campos, que podem identificá-lo. Estes campos ou conjuntos de campos podem ser referidos como **identificadores fortes** se puderem identificar uma entidade sem qualquer ambiguidade, ou como **identificadores fracos** se puderem identificar uma entidade em algumas circunstâncias, mas não estão garantidos para identificar uma entidade de forma única em todos os casos. Em muitos casos, porém, uma seleção de identificadores fracos pode ser combinada para produzir um identificador forte.

Por exemplo, as contas de utilizador podem ser identificadas como entidades de **conta** em mais do que uma forma: utilizando um único **identificador forte** como o identificador numérico de uma conta Azure (o campo **GUID),** ou o seu valor **principal de utilizador (UPN)** ou, em alternativa, utilizando uma combinação de **identificadores fracos** como o seu **nome** e campos **NTDomain.** Diferentes fontes de dados podem identificar o mesmo utilizador de diferentes formas. Sempre que a Azure Sentinel encontra duas entidades que pode reconhecer como a mesma entidade com base nos seus identificadores, funde as duas entidades numa única entidade, para que possa ser manuseada de forma adequada e consistente.

Se, no entanto, um dos seus fornecedores de recursos criar um alerta no qual uma entidade não esteja suficientemente identificada - por exemplo, utilizando apenas um **identificador fraco** como um nome de utilizador sem o contexto de nome de domínio - então a entidade utilizadora não pode ser fundida com outros casos da mesma conta de utilizador. Esses outros casos seriam identificados como uma entidade separada, e essas duas entidades permaneceriam separadas em vez de unificadas.

De forma a minimizar o risco de isso acontecer, deve verificar se todos os seus fornecedores de alerta identificam adequadamente as entidades nos alertas que produzem. Além disso, sincronizar as entidades de conta de utilizador com o Azure Ative Directory pode criar um diretório unificador, que poderá fundir entidades de conta de utilizador.

#### <a name="supported-entities"></a>Entidades apoiadas

Os seguintes tipos de entidades estão atualmente identificados no Azure Sentinel:

- Conta de utilizador
- Anfitrião
- Endereço IP
- Software maligno
- Ficheiro
- Processo
- Aplicação em nuvem
- Nome de domínio
- Recurso do Azure
- Hash de ficheiro
- Chave do registo
- Valor de registo
- Grupo de segurança
- URL
- Dispositivo IoT
- Mailbox
- Aglomerado de correio
- Mensagem de correio
- Correio de submissão

Pode ver os identificadores destas entidades e outras informações relevantes na referência das [entidades.](entities-reference.md)

## <a name="entity-mapping"></a>Mapeamento de entidades

Como é que o Azure Sentinel reconhece um dado num alerta como identificando uma entidade?

Vamos ver como o processamento de dados é feito em Azure Sentinel. Os dados são ingeridos de várias fontes através de [conectores](connect-data-sources.md), seja de serviço ao serviço, baseado em agentes, ou utilizando um serviço de syslog e um reencaminhador de registo. Os dados são armazenados em tabelas no seu espaço de trabalho Log Analytics. Estas tabelas são então consultadas em intervalos regulares pelas regras de análise que definiu e ativou. Uma das muitas ações tomadas por estas regras de análise é o mapeamento de campos de dados nas tabelas para entidades reconhecidas pelo Azure Sentinel. De acordo com os mapeamentos que define nas suas regras de análise, o Azure Sentinel irá retirar os campos dos resultados devolvidos pela sua consulta, reconhecê-los pelos identificadores especificados para cada tipo de entidade, e aplicar-lhes o tipo de entidade identificado por esses identificadores.

Qual é o objetivo de tudo isto?

Quando o Azure Sentinel é capaz de identificar entidades em alertas de diferentes tipos de fontes de dados, e especialmente se puder fazê-lo usando identificadores fortes comuns a cada fonte de dados ou a um terceiro esquema, pode então facilmente correlacionar-se entre todos estes alertas e fontes de dados. Estas correlações ajudam a construir uma rica loja de informações e insights sobre as entidades, dando-lhe uma base sólida para as suas operações de segurança.

Saiba como [mapear campos de dados para entidades.](map-data-fields-to-entities.md)

Saiba [quais os identificadores que identificam fortemente uma entidade.](entities-reference.md)

## <a name="entity-pages"></a>Páginas de entidade

Quando encontra qualquer entidade (atualmente limitada a utilizadores e anfitriões) numa pesquisa, um alerta ou uma investigação, pode selecionar a entidade e ser levado para uma página de **entidade,** uma folha de dados cheia de informações úteis sobre essa entidade. Os tipos de informação que irá encontrar nesta página incluem factos básicos sobre a entidade, uma cronologia de eventos notáveis relacionados com esta entidade e insights sobre o comportamento da entidade.

As páginas de entidades consistem em três partes:

- O painel do lado esquerdo contém as informações de identificação da entidade, recolhidas a partir de fontes de dados como Azure Ative Directory, Azure Monitor, Azure Security Center e Microsoft Defender.

- O painel central mostra uma cronologia gráfica e textual de eventos notáveis relacionados com a entidade, tais como alertas, marcadores e atividades. As atividades são agregações de eventos notáveis da Log Analytics. As consultas que detetam essas atividades são desenvolvidas por equipas de investigação de segurança da Microsoft.

- O painel do lado direito apresenta insights comportamentais sobre a entidade. Estes conhecimentos ajudam a identificar rapidamente anomalias e ameaças à segurança. Os insights são desenvolvidos pelas equipas de pesquisa de segurança da Microsoft, e são baseados em modelos de deteção de anomalias.

### <a name="the-timeline"></a>A linha do tempo

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="Linha do tempo das páginas da entidade":::

A linha do tempo é uma parte importante da contribuição da página da entidade para a análise de comportamentos em Azure Sentinel. Apresenta uma história sobre eventos relacionados com entidades, ajudando-o a compreender a atividade da entidade dentro de um prazo específico.

Pode escolher o intervalo de **tempo** entre várias opções predefinidas (como *as últimas 24 horas),* ou defini-lo para qualquer período de tempo definido sob medida. Além disso, pode definir filtros que limitam a informação na linha do tempo a tipos específicos de eventos ou alertas.

Os seguintes tipos de itens estão incluídos na linha do tempo:

- Alertas - quaisquer alertas em que a entidade seja definida como uma **entidade mapeada**. Tenha em atenção que se a sua organização criou [alertas personalizados utilizando regras de análise,](./tutorial-detect-threats-custom.md)deve certificar-se de que o mapeamento da entidade das regras é feito corretamente.

- Marcadores - quaisquer marcadores que incluam a entidade específica mostrada na página.

- Atividades - agregação de eventos notáveis relacionados com a entidade.

### <a name="entity-insights"></a>Insights de Entidade

Os conhecimentos das entidades são consultas definidas pelos investigadores de segurança da Microsoft para ajudar os seus analistas a investigar de forma mais eficiente e eficaz. Os insights são apresentados como parte da página da entidade, e fornecem informações de segurança valiosas sobre anfitriões e utilizadores, sob a forma de dados e gráficos tabulares. Ter a informação aqui significa que não tem de desviar para o Log Analytics. Os insights incluem dados relativos a insiná-ins, adições de grupo, eventos anómalos e muito mais, e incluem algoritmos de ML avançados para detetar comportamentos anómalos.

Os insights baseiam-se nas seguintes fontes de dados:

- Syslog (Linux)
- SecurityEvent (Janelas)
- AuditLogs (Azure AD)
- SigninLogs (Azure AD)
- OfficeActivity (Office 365)
- BehaviorAnalytics (Azure Sentinel UEBA)
- Batimento cardíaco (agente monitor Azure)
- CommonSecurityLog (Sentinela Azure)

### <a name="how-to-use-entity-pages"></a>Como usar páginas de entidades

As páginas da entidade são projetadas para fazer parte de múltiplos cenários de utilização, e podem ser acedidas a partir da gestão de incidentes, do gráfico de investigação, dos marcadores ou diretamente da página de pesquisa da entidade sob análise de comportamento da **Entidade** no menu principal do Azure Sentinel.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Casos de utilização de página de entidade":::

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a trabalhar com entidades no Azure Sentinel. Para obter orientações práticas sobre a implementação e para utilizar os insights que obteve, consulte os seguintes artigos:

- [Ativar a análise de comportamento da entidade](./enable-entity-behavior-analytics.md) em Azure Sentinel.
- [Caça por ameaças à segurança.](./hunting.md)
