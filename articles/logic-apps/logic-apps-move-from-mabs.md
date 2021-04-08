---
title: Migrar aplicativos dos Serviços BizTalk para Azure Logic Apps
description: Como mover as suas apps e soluções dos Serviços Microsoft Azure BizTalk (MABS) para Azure Logic Apps
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: 6c07ab4b18c017bd29723d2640129b8e67374e3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023657"
---
# <a name="migrate-your-apps-and-solutions-from-biztalk-services-to-azure-logic-apps"></a>Migrar as suas apps e soluções dos Serviços BizTalk para a Azure Logic Apps

A Microsoft Azure BizTalk Services (MABS) está a reformar-se. Para mover as suas soluções de integração MABS para [Azure Logic Apps,](../logic-apps/logic-apps-overview.md)siga as orientações neste artigo. 

## <a name="introduction"></a>Introdução

A BizTalk Services é constituída por dois subserviços:

* Ligações híbridas de serviços Microsoft BizTalk
* Integração baseada em pontes EAI e EDI

[A azure App Service Hybrid Connections](../app-service/app-service-hybrid-connections.md) substitui as ligações híbridas dos serviços BizTalk. A Azure Hybrid Connections está disponível com o Azure App Service através do portal Azure. Este serviço fornece um Hybrid Connection Manager para que possa gerir as ligações híbridas bizTalk e também novas ligações híbridas que cria no portal. 

[A Logic Apps](../logic-apps/logic-apps-overview.md) substitui a integração baseada em pontes EAI e EDI com todas as mesmas capacidades nos Serviços BizTalk e muito mais. Este serviço fornece funcionalidades de fluxo de trabalho e orquestração baseados em nuvem para que você construa soluções de integração rápidas e facilmente complexas através de um navegador ou com o Visual Studio.

Esta tabela mapeia as capacidades dos Serviços BizTalk para as Aplicações Lógicas.

| Serviços BizTalk   | Logic Apps            | Objetivo                      |
| ------------------ | --------------------- | ---------------------------- |
| Conector          | Conector             | Enviar e receber dados   |
| Bridge             | Aplicação Lógica             | Processador de pipeline           |
| Fase de validação     | Ação de validação de XML | Validar um documento XML contra um esquema | 
| Estágio de enriquecimento       | Fichas de Dados           | Promover propriedades em mensagens ou para decisões de encaminhamento |
| Transformar palco    | Transformar ação      | Converter mensagens XML de um formato para outro |
| Estágio de descodificar       | Ação de descodificar ficheiros planos | Converter de ficheiro plano para XML |
| Fase de codificação       | Ação de codificação de ficheiros planos | Converter de XML para ficheiro plano |
| Inspetor de mensagens  | Funções Azure ou Apps API | Executar código personalizado nas suas integrações |
| Ação de Rota       | Condição ou Comutação | Encaminhar mensagens para um dos conectores especificados |
|||| 

## <a name="biztalk-services-artifacts"></a>Artefactos dos Serviços BizTalk

A BizTalk Services tem vários tipos de artefactos. 

## <a name="connectors"></a>Conectores

Os conectores bizTalk Services ajudam as pontes a enviar e receber dados, incluindo pontes bidirecionais que permitem interações de pedido/resposta baseadas em HTTP. A Logic Apps usa a mesma terminologia e tem centenas de conectores que servem o mesmo propósito ligando-se a uma vasta gama de tecnologias e serviços. Por exemplo, os conectores estão disponíveis para serviços cloud SaaS e PaaS, tais como OneDrive, Office365, Dynamics CRM, e muito mais, além de sistemas no local através do Gateway de Dados On-Premis, que substitui o Serviço de Adaptação BizTalk para serviços BizTalk. As fontes nos Serviços BizTalk estão limitadas à ftp, SFTP e à fila de autocarros de serviço ou à subscrição de tópicos.

![Diagrama que mostra o fluxo de Serviços BizTalk.](media/logic-apps-move-from-mabs/sources.png)

Por predefinição, cada ponte tem um ponto final HTTP, que está configurado com o Endereço de Tempo de Execução e as propriedades do Endereço Relativo para a ponte. Para obter os mesmos resultados com as Aplicações Lógicas, utilize as ações [de Pedido e Resposta.](../connectors/connectors-native-reqres.md)

## <a name="xml-processing-and-bridges"></a>Processamento e pontes XML

Nos Serviços BizTalk, uma ponte é análoga a um gasoduto de processamento. Uma ponte pode pegar nos dados recebidos de um conector, fazer algum trabalho com os dados, e enviar os resultados para outro sistema. A Logic Apps faz o mesmo suportando os mesmos padrões de interação baseados em pipeline que os Serviços BizTalk e fornecendo outros padrões de integração. A [Ponte Request-Reply XML](/previous-versions/azure/hh689781(v=azure.100)) nos Serviços BizTalk é conhecida como um oleoduto VETER, que consiste em etapas que executam estas tarefas:

* (V) Validar
* (E) Enriquecer
* (T) Transformar
* (E) Enriquecer
* (R) Rota

Esta imagem mostra como o processamento é dividido entre pedido e resposta, o que proporciona controlo sobre o pedido e os caminhos de resposta separadamente, por exemplo, utilizando diferentes mapas para cada caminho:

![Screenshot que mostra como o processamento é dividido entre pedido e resposta.](media/logic-apps-move-from-mabs/xml-request-reply.png)

Além disso, uma ponte de One-Way XML adiciona fases de Decode e Encode no início e no fim do processamento. A ponte Pass-Through contém um único estágio de Enriquecimento.

### <a name="message-processing-decoding-and-encoding"></a>Processamento de mensagens, descodição e codificação

Nos Serviços BizTalk, pode receber diferentes tipos de mensagens XML e determinar o esquema correspondente para a mensagem recebida. Este trabalho é realizado na fase *'Tipos de mensagem'* do gasoduto de processamento de receção. A fase Decode utiliza então o tipo de mensagem detetada para descodificar a mensagem utilizando o esquema fornecido. Se o esquema for um esquema de ficheiro plano, este estágio converte o ficheiro plano de entrada em XML. 

As Aplicações Lógicas fornecem capacidades semelhantes. Recebe um ficheiro plano sobre diferentes protocolos utilizando os diferentes gatilhos do conector (Sistema de Ficheiros, FTP, HTTP e assim por diante), e utiliza a ação [de descodificar ficheiros planos](../logic-apps/logic-apps-enterprise-integration-flatfile.md) para converter os dados de entrada em XML. Pode mover os seus esquemas de ficheiros planos existentes diretamente para as Aplicações Lógicas sem alterações e, em seguida, fazer o upload de esquemas para a sua Conta de Integração.

### <a name="validation"></a>Validação

Após a conversão dos dados de entrada para XML (ou se XML foi o formato de mensagem recebido), a validação corre para determinar se a mensagem adere ao seu esquema XSD. Para executar esta tarefa em Aplicações Lógicas, utilize a ação [de validação XML.](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) Pode utilizar os mesmos esquemas da BizTalk Services sem alterações.

### <a name="transform-messages"></a>Transformar mensagens

Nos Serviços BizTalk, o estágio Transform converte um formato de mensagem baseado em XML para outro. Este trabalho é feito aplicando um mapa, utilizando o mapper baseado em TRFM. Nas Aplicações Lógicas, o processo é semelhante. A ação Transform executa um mapa a partir da sua Conta de Integração. A principal diferença é que os mapas em Aplicações Lógicas estão no formato XSLT. O XSLT inclui a capacidade de reutilizar o XSLT existente que já tem, incluindo mapas criados para o BizTalk Server que contêm functoids. 

### <a name="routing-rules"></a>Regras de encaminhamento

A BizTalk Services toma uma decisão de encaminhamento sobre qual ponto final ou conector para enviar mensagens ou dados de entrada. A capacidade de selecionar a partir de pontos finais pré-configurados é possível utilizando a opção do filtro de encaminhamento:

![Screenshot que mostra a opção do filtro de encaminhamento.](media/logic-apps-move-from-mabs/route-filter.png)

Nos Serviços BizTalk, se existirem apenas duas opções, usar uma *condição* é a melhor maneira de converter filtros de encaminhamento nos Serviços BizTalk. Se houver mais de dois, use um **interruptor**.

As Aplicações Lógicas fornecem capacidades lógicas sofisticadas e fluxo de controlo avançado e encaminhamento com [declarações condicionais](../logic-apps/logic-apps-control-flow-conditional-statement.md) e [declarações de switch](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Enriquecer

No processamento dos Serviços BizTalk, o estágio Enrich adiciona propriedades ao contexto de mensagem associado aos dados recebidos. Por exemplo, promover uma propriedade a usar para encaminhamento a partir de uma pesquisa de base de dados, ou extraindo um valor usando uma expressão XPath. A Logic Apps fornece acesso a todas as saídas de dados contextuais das ações anteriores, tornando-a simples de replicar o mesmo comportamento. Por exemplo, utilizando a ação `Get Row` de ligação SQL, devolve dados de uma base de dados do SQL Server e utiliza os dados numa ação de Decisão para encaminhamento. Da mesma forma, as propriedades em serviço de entrada Despachadas mensagens por um gatilho são endereçadas, bem como XPath usando a expressão de linguagem de definição de fluxo de trabalho xpath.

### <a name="run-custom-code"></a>Executar código personalizado

A BizTalk Services permite-lhe [executar código personalizado](/previous-versions/azure/dn232389(v=azure.100)) que é carregado nas suas próprias assembléias. Esta funcionalidade é implementada pela interface [IMessageInspector.]() Cada etapa da ponte inclui duas propriedades (Inspetor On Enter, e On Exit Inspetor) que fornecem o tipo .NET que criou que implementa esta interface. O código personalizado permite-lhe realizar um processamento mais complexo em dados e permite-lhe reutilizar o código existente em conjuntos que executam lógicas de negócio comuns. 

As Aplicações Lógicas fornecem duas formas primárias de executar código personalizado: Funções Azure e Aplicações API. As Funções Azure podem ser criadas e chamadas a partir de aplicações lógicas. Consulte [adicionar e executar código personalizado para aplicações lógicas através de Funções Azure](../logic-apps/logic-apps-azure-functions.md). Utilize aplicativos API, parte do Azure App Service, para criar os seus próprios gatilhos e ações. Saiba mais sobre [a criação de uma API personalizada para usar com as Aplicações Lógicas.](../logic-apps/logic-apps-create-api-app.md) 

Se tiver um código personalizado nos conjuntos que chama a partir dos Serviços BizTalk, pode mover este código para Azure Functions ou criar APIs personalizados com apps API, dependendo do que está a implementar. Por exemplo, se tiver um código que embrulhe outro serviço para o qual as Aplicações Lógicas não têm um conector, então crie uma App API e use as ações que a sua app API fornece dentro da sua aplicação lógica. Se tiver funções ou bibliotecas auxiliares, então as Funções Azure são provavelmente as mais adequadas.

### <a name="edi-processing-and-trading-partner-management"></a>Gestão de parceiros de processamento e negociação de EDI

Os Serviços BizTalk e aplicações lógicas incluem o processamento EDI e B2B com suporte para AS2 (Declaração de Aplicabilidade 2), X12 e EDIFACT. Nos Serviços BizTalk, crie pontes EDI e crie ou gere parceiros e acordos comerciais no portal dedicado de Tracking and Management.
Nas Aplicações Lógicas, obtém esta funcionalidade através do [Pack de Integração Empresarial (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md). O EIP fornece ações [de Conta de Integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e B2B para o processamento de EDI e B2B. Também utiliza uma Conta de Integração para criar e gerir [parceiros](../logic-apps/logic-apps-enterprise-integration-partners.md) e [acordos comerciais.](../logic-apps/logic-apps-enterprise-integration-agreements.md) Depois de criar uma Conta de Integração, pode ligar uma ou mais aplicações lógicas à conta. Em seguida, pode utilizar ações B2B para aceder a informações de parceiros de negociação a partir da sua aplicação lógica. São fornecidas as seguintes ações:

* Código AS2
* Descodificar AS2
* Codificação X12
* Descodificar X12
* Codificação EDIFACT
* Descodificar o EDIFACT

Ao contrário dos Serviços BizTalk, estas ações são dissociadas dos protocolos de transporte. Assim, quando cria as suas aplicações lógicas, tem mais flexibilidade nos conectores que utiliza para enviar e receber dados. Por exemplo, pode receber ficheiros X12 como anexos de e-mail e, em seguida, processar estes ficheiros numa aplicação lógica. 

## <a name="manage-and-monitor"></a>Gerir e monitorizar

Nos Serviços BizTalk, um portal dedicado forneceu capacidades de rastreio para monitorizar e resolver problemas. A Logic Apps fornece capacidades de rastreio e monitorização mais ricas para [monitorizar aplicações lógicas no portal Azure](../logic-apps/monitor-logic-apps.md)– e inclui uma aplicação móvel para manter um olho nas coisas quando está em movimento.

## <a name="high-availability"></a>Elevada disponibilidade

Para uma elevada disponibilidade (HA) nos Serviços BizTalk, pode partilhar a carga de processamento utilizando mais de um caso numa região específica. A Logic Apps fornece HA na região sem custos adicionais. 

Nos Serviços BizTalk, a recuperação de desastres fora da região para o processamento de B2B requer um processo de backup e restauro. Para a continuidade do negócio, a Logic Apps fornece [capacidade de DR](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)ativa/passiva de região, que permite sincronizar dados B2B através de contas de integração em diferentes regiões.

## <a name="next-steps"></a>Passos seguintes

* [O que é o Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md) ou começar rapidamente com um [modelo compilado previamente](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* [Ver todos os conectores disponíveis](../connectors/apis-list.md) que pode usar em aplicações lógicas