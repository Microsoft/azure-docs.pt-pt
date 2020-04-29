---
title: Migrar aplicativos dos Serviços BizTalk para aplicações lógicas azure
description: Como mover as suas apps e soluções dos Serviços Microsoft Azure BizTalk (MABS) para Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: 97399635399c12022006ac95e60c5828bf2a9dc5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76905428"
---
# <a name="migrate-your-apps-and-solutions-from-biztalk-services-to-azure-logic-apps"></a>Migrar as suas apps e soluções dos Serviços BizTalk para aplicações lógicas azure

O Microsoft Azure BizTalk Services (MABS) está a reformar-se. Para mover as suas soluções de integração MABS para [As Aplicações Lógicas Azure,](../logic-apps/logic-apps-overview.md)siga as orientações deste artigo. 

## <a name="introduction"></a>Introdução

A BizTalk Services é constituída por dois subserviços:

* Ligações híbridas da Microsoft BizTalk
* Integração baseada em pontes EAI e EDI

[Ligações híbridas do Serviço de Aplicações Azure](../app-service/app-service-hybrid-connections.md) substituem as Ligações Híbridas bizTalk Services. A Azure Hybrid Connections está disponível com o Azure App Service através do portal Azure. Este serviço fornece um Hybrid Connection Manager para que possa gerir as conexões híbridas bizTalk Services existentes e também novas ligações híbridas que cria no portal. 

[Logic Apps](../logic-apps/logic-apps-overview.md) substitui a integração baseada em pontes EAI e EDI por todas as mesmas capacidades nos Serviços BizTalk e muito mais. Este serviço fornece recursos de fluxo de trabalho e orquestração baseados em nuvem para que possa construir soluções de integração complexas de forma rápida e fácil através de um navegador ou com O Estúdio Visual.

Esta tabela mapeia as capacidades dos Serviços BizTalk para aplicações lógicas.

| Serviços BizTalk   | Aplicações Lógicas            | Objetivo                      |
| ------------------ | --------------------- | ---------------------------- |
| Conector          | Conector             | Enviar e receber dados   |
| Bridge             | Aplicação Lógica             | Processador de gasoduto           |
| Fase de validação     | Ação de validação XML | Validar um documento XML contra um esquema | 
| Estágio enriquecedor       | Tokens de dados           | Promover propriedades em mensagens ou para decisões de encaminhamento |
| Transformar palco    | Transformar a ação      | Converter mensagens XML de um formato para outro |
| Descodificar estágio       | Ação de descodificação de ficheiro plano | Converter de ficheiro plano para XML |
| Fase de codificação       | Ação de código de arquivo plano | Converter de XML para ficheiro plano |
| Inspetor de Mensagens  | Funções Azure ou Aplicações API | Execute código personalizado nas suas integrações |
| Ação de Rota       | Condição ou Interruptor | Encaminha as mensagens para um dos conectores especificados |
|||| 

## <a name="biztalk-services-artifacts"></a>Artefactos dos Serviços BizTalk

A BizTalk Services tem vários tipos de artefactos. 

## <a name="connectors"></a>Conectores

Os conectores bizTalk services ajudam as pontes a enviar e a receber dados, incluindo pontes bidirecionais que permitem interações de pedido/resposta baseadas em HTTP. As Aplicações Lógicas usam a mesma terminologia e têm centenas de conectores que servem o mesmo propósito ligando-se a uma vasta gama de tecnologias e serviços. Por exemplo, os conectores estão disponíveis para serviços SaaS e PaaS em nuvem, tais como OneDrive, Office365, Dynamics CRM, e muito mais, além de sistemas no local através do Gateway de Dados On-Premises, que substitui o Serviço adaptador BizTalk para os Serviços BizTalk. As fontes nos Serviços BizTalk estão limitadas a FTP, SFTP e Service Bus Queue ou assinatura Tópico.

![](media/logic-apps-move-from-mabs/sources.png)

Por padrão, cada ponte tem um ponto final HTTP, que é configurado com o Endereço De Tempo de Execução e as propriedades relativas para a ponte. Para obter os mesmos resultados com Apps Lógicas, utilize as ações [de Pedido e Resposta.](../connectors/connectors-native-reqres.md)

## <a name="xml-processing-and-bridges"></a>Processamento e pontes XML

Nos Serviços BizTalk, uma ponte é análoga a um oleoduto de processamento. Uma ponte pode pegar os dados recebidos de um conector, fazer algum trabalho com os dados e enviar os resultados para outro sistema. As Aplicações Lógicas fazem o mesmo suportando os mesmos padrões de interação baseados em pipeline que os Serviços BizTalk e fornecendo também outros padrões de integração. A [Ponte XML](https://msdn.microsoft.com/library/azure/hh689781.aspx) de Resposta a Pedidos nos Serviços BizTalk é conhecida como um gasoduto VETER, que consiste em fases que executam estas tarefas:

* (V) Validar
* E Enriquecer
* (T) Transformar
* E Enriquecer
* R Rota

Esta imagem mostra como o processamento é dividido entre pedido e resposta, o que fornece controlo sobre o pedido e os caminhos de resposta separadamente, por exemplo, utilizando mapas diferentes para cada caminho:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Além disso, uma ponte XML One-Way adiciona estágios Decode e Encode no início e fim do processamento. A ponte Pass-Through contém um único estágio de Enrich.

### <a name="message-processing-decoding-and-encoding"></a>Processamento de mensagens, descodificação e codificação

Nos Serviços BizTalk, pode receber diferentes tipos de mensagens XML e determinar o esquema correspondente para a mensagem recebida. Este trabalho é realizado na fase dos Tipos de *Mensagens* do gasoduto de processamento de receção. A fase Descodificação utiliza então o tipo de mensagem detetado para descodificar a mensagem utilizando o esquema fornecido. Se o esquema for um esquema de ficheiro plano, esta fase converte o ficheiro plano de entrada para XML. 

As Aplicações Lógicas fornecem capacidades semelhantes. Recebe um ficheiro plano sobre diferentes protocolos utilizando os diferentes gatilhos do conector (Sistema de Ficheiros, FTP, HTTP, e assim por diante), e utiliza a ação [Flat File Decode](../logic-apps/logic-apps-enterprise-integration-flatfile.md) para converter os dados recebidos em XML. Pode mover os seus ficheiros planos existentes diretamente para Aplicações Lógicas sem alterações e, em seguida, fazer o upload de schemas para a sua Conta de Integração.

### <a name="validation"></a>Validação

Depois de os dados recebidos forem convertidos para XML (ou se o XML foi o formato de mensagem recebido), a validação corre para determinar se a mensagem adere ao seu esquema XSD. Para realizar esta tarefa em Aplicações Lógicas, utilize a ação de [validação XML.](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) Pode utilizar os mesmos esquemas dos Serviços BizTalk sem alterações.

### <a name="transform-messages"></a>Transformar mensagens

Nos Serviços BizTalk, o palco Transform converte um formato de mensagem baseado em XML para outro. Este trabalho é feito aplicando um mapa, utilizando o mapper baseado em TRFM. Nas Aplicações Lógicas, o processo é semelhante. A ação Transform executa um mapa da sua Conta de Integração. A principal diferença é que os mapas em Aplicações Lógicas estão no formato XSLT. O XSLT inclui a capacidade de reutilizar o XSLT existente que já tem, incluindo mapas criados para o BizTalk Server que contêm functoides. 

### <a name="routing-rules"></a>Regras de encaminhamento

A BizTalk Services toma uma decisão de encaminhamento sobre qual ponto final ou conector para enviar mensagens ou dados de entrada. A capacidade de selecionar a partir de pontos finais pré-configurados é possível utilizando a opção de filtro de encaminhamento:

![](media/logic-apps-move-from-mabs/route-filter.png)

Nos Serviços BizTalk, se houver apenas duas opções, usar uma *condição* é a melhor maneira de converter filtros de encaminhamento nos Serviços BizTalk. Se houver mais de dois, use um **interruptor**.

As Aplicações Lógicas fornecem capacidades lógicas sofisticadas, além de fluxo de controlo avançado e encaminhamento com [declarações condicionais](../logic-apps/logic-apps-control-flow-conditional-statement.md) e [declarações de switch](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Enriquecer

No processamento de Serviços BizTalk, o palco Enrich adiciona propriedades ao contexto de mensagem associado aos dados recebidos. Por exemplo, promover uma propriedade para utilizar para o encaminhamento de uma pesquisa de base de dados, ou extraindo um valor usando uma expressão XPath. As Aplicações Lógicas fornecem acesso a todas as saídas contextuais de dados de ações anteriores, tornando simples replicar o mesmo comportamento. Por exemplo, `Get Row` utilizando a ação de ligação SQL, devolve dados de uma base de dados do SQL Server e utiliza os dados numa ação de decisão para o encaminhamento. Da mesma forma, as propriedades em mensagens de serviço de entrada por um gatilho são endereçadas, bem como XPath usando a expressão de linguagem de definição de fluxo de trabalho xpath.

### <a name="run-custom-code"></a>Executar código personalizado

A BizTalk Services permite-lhe [executar código personalizado](https://msdn.microsoft.com/library/azure/dn232389.aspx) que é carregado nas suas próprias assembleias. Esta funcionalidade é implementada pela interface [iMessageInspector.](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector) Cada etapa da ponte inclui duas propriedades (Inspetor de Entrada e Inspetor de Saída) que fornecem o tipo .NET que criou que implementa esta interface. O código personalizado permite-lhe realizar um processamento mais complexo de dados e permite-lhe reutilizar o código existente em conjuntos que executam a lógica de negócio comum. 

As Aplicações Lógicas fornecem duas formas primárias de executar código personalizado: Funções Azure e Aplicações API. As Funções Azure podem ser criadas e chamadas a partir de aplicações lógicas. Consulte [adicionar e executar código personalizado para aplicações lógicas através de Funções Azure](../logic-apps/logic-apps-azure-functions.md). Utilize aplicações API, parte do Serviço de Aplicações Azure, para criar os seus próprios gatilhos e ações. Saiba mais sobre a criação de [uma API personalizada para usar com Aplicações Lógicas.](../logic-apps/logic-apps-create-api-app.md) 

Se tiver um código personalizado em conjuntos que ligue para os Serviços BizTalk, pode mover este código para funções Azure ou criar APIs personalizados com Aplicações API, dependendo do que está a implementar. Por exemplo, se tiver um código que envolva outro serviço para o qual as Aplicações Lógicas não têm um conector, então crie uma App API e utilize as ações que a sua aplicação API fornece dentro da sua aplicação lógica. Se tiver funções ou bibliotecas de ajudantes, então as Funções Azure são provavelmente as melhores.

### <a name="edi-processing-and-trading-partner-management"></a>Gestão de parceiros de processamento e negociação edi

Os Serviços BizTalk e aplicações lógicas incluem processamento EDI e B2B com suporte para AS2 (Declaração de Aplicabilidade 2), X12 e EDIFACT. Nos Serviços BizTalk, a sua criação de pontes EDI e a criação ou gestão de parceiros comerciais e acordos no portal dedicado de Rastreio e Gestão.
Em Aplicações Lógicas, obtém-se esta funcionalidade através do [Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md). O EIP fornece ações de [Conta de Integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e B2B para o processamento EDI e B2B. Também utiliza uma Conta de Integração para criar e gerir parceiros e [acordos](../logic-apps/logic-apps-enterprise-integration-agreements.md) [comerciais.](../logic-apps/logic-apps-enterprise-integration-partners.md) Depois de criar uma Conta de Integração, pode ligar uma ou mais aplicações lógicas à conta. Em seguida, pode utilizar ações B2B para aceder a informações de parceiros de negociação a partir da sua aplicação lógica. São fornecidas as seguintes ações:

* AS2 Encode
* Descodificação AS2
* X12 Codificar
* X12 Decode
* EDIFACT Encode
* Descodificação EDIFACT

Ao contrário dos Serviços BizTalk, estas ações são dissociadas dos protocolos de transporte. Assim, quando cria as suas aplicações lógicas, tem mais flexibilidade em que conectores que utiliza para enviar e receber dados. Por exemplo, pode receber ficheiros X12 como anexos a partir de e-mail e, em seguida, processar estes ficheiros numa aplicação lógica. 

## <a name="manage-and-monitor"></a>Gerir e monitorizar

Na BizTalk Services, um portal dedicado forneceu capacidades de rastreamento para monitorizar e resolver problemas. A Logic Apps fornece capacidades de rastreio e monitorização mais ricas para [monitorizar aplicações lógicas no portal Azure](../logic-apps/monitor-logic-apps.md), e inclui uma aplicação móvel para manter um olho nas coisas quando está em movimento.

## <a name="high-availability"></a>Elevada disponibilidade

Para uma elevada disponibilidade (HA) nos Serviços BizTalk, pode partilhar a carga de processamento utilizando mais de um caso numa região específica. As Aplicações Lógicas fornecem HA na região sem custos adicionais. 

Nos Serviços BizTalk, a recuperação de desastres fora da região para o processamento de B2B requer um processo de backup e restauro. Para a continuidade do negócio, as Aplicações Lógicas fornecem capacidade de [DR](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)ativa/passiva inter-região, o que permite sincronizar dados B2B em contas de integração em diferentes regiões.

## <a name="next-steps"></a>Passos seguintes

* [O que é o Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md) ou começar rapidamente com um [modelo compilado previamente](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* [Veja todos os conectores disponíveis](../connectors/apis-list.md) que pode usar em aplicações lógicas