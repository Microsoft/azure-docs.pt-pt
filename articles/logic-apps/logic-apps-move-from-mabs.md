---
title: Mova as aplicações dos serviços BizTalk para o Azure Logic Apps | Documentos da Microsoft
description: Migrar de serviços BizTalk do Azure (MABS) para o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: f813cb5d8d5c442fc17f126c3a2ff6de7b0bdde1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61323111"
---
# <a name="migrate-from-biztalk-services-to-azure-logic-apps"></a>Migrar dos serviços BizTalk para o Azure Logic Apps

Microsoft Azure BizTalk Services (MABS) está desativando. Para mover as suas soluções de integração do MABS para [do Azure Logic Apps](../logic-apps/logic-apps-overview.md), siga as orientações neste artigo. 

## <a name="introduction"></a>Introdução

Os serviços BizTalk consiste em dois subservices:

* Ligações híbridas dos serviços BizTalk do Microsoft
* Integração baseada em pontes EAI e EDI

[Ligações híbridas do serviço de aplicações Azure](../app-service/app-service-hybrid-connections.md) substitui as ligações híbridas do BizTalk Services. Ligações híbridas do Azure está disponível, com o serviço de aplicações do Azure, através do portal do Azure. Este serviço fornece um Gestor de ligações híbridas para que possa gerir as ligações híbridas existentes dos serviços BizTalk e também novas ligações híbridas que criar no portal. 

[O Logic Apps](../logic-apps/logic-apps-overview.md) substitui integração baseada em pontes EAI e EDI com todas as mesmas capacidades em serviços do BizTalk e muito mais. Este serviço fornece funcionalidades baseado no consumo de fluxo de trabalho e orquestração para que possa criar, rápida e facilmente soluções de integração complexos através de um browser ou com o Visual Studio de escala da cloud.

Esta tabela mapeia funcionalidades dos serviços BizTalk para aplicações lógicas.

| Serviços BizTalk   | Aplicações Lógicas            | Objetivo                      |
| ------------------ | --------------------- | ---------------------------- |
| Conector          | Conector             | Enviar e receber dados   |
| Bridge             | Aplicação Lógica             | Processador de pipeline           |
| Validar o estágio     | Ação de validação de XML | Validar um documento XML com um esquema | 
| Enriqueça fase       | Data Tokens           | Promover propriedades em mensagens ou para decisões de encaminhamento |
| Transformar fase    | Transformar a ação      | Converter mensagens XML de um formato para outro |
| Descodificar fase       | Ação de descodificação de ficheiro simples | Converter de arquivo simples em XML |
| Codificar o estágio       | Ação de codificação de ficheiro simples | Converter a partir de XML a um ficheiro simples |
| Inspetor de mensagem  | As funções do Azure ou aplicações API | Executar código personalizado em suas integrações |
| Ação de rota       | Condição ou comutador | Encaminhar mensagens para um dos conectores do especificado |
|||| 

## <a name="biztalk-services-artifacts"></a>Artefactos de serviços BizTalk

Os serviços BizTalk tem vários tipos de artefactos. 

## <a name="connectors"></a>Conectores

Conectores de serviços do BizTalk ajudam pontes enviar e receber dados, incluindo pontes bidirecionais que permitem interações de solicitação/resposta com base em HTTP. O Logic Apps utiliza a mesma terminologia e tem 180 conectores que servem o mesmo objetivo ao ligar a uma vasta gama de tecnologias e serviços. Por exemplo, os conectores estão disponíveis para a cloud de SaaS e PaaS serviços, tais como o OneDrive, Office 365, Dynamics CRM e muito mais, bem como em sistemas no local através do Gateway de dados no local, que substitui o BizTalk Adapter Service para os serviços BizTalk. Origens dos serviços BizTalk estão limitadas a subscrição de FTP, SFTP e fila do Service Bus ou tópico.

![](media/logic-apps-move-from-mabs/sources.png)

Por predefinição, cada ponte tem um ponto de final HTTP, que está configurado com o endereço de tempo de execução e as propriedades de endereço relativo para a ponte. Para atingir os mesmos resultados com o Logic Apps, utilize o [solicitação e resposta](../connectors/connectors-native-reqres.md) ações.

## <a name="xml-processing-and-bridges"></a>Processamento XML e pontes

Nos serviços do BizTalk, uma ponte é semelhante a um pipeline de processamento. Uma ponte pode levar os dados recebidos a partir de um conector, algumas trabalhar com os dados e enviar os resultados para outro sistema. O Logic Apps faz o mesmo que suporta os mesmos padrões de interação com base no pipeline, como os serviços BizTalk e também fornecendo outros padrões de integração. O [Bridge de solicitação-resposta XML](https://msdn.microsoft.com/library/azure/hh689781.aspx) nos serviços do BizTalk é conhecido como um pipeline VETER que consiste em fases que realizar estas tarefas:

* (V) validar
* (E) enriquecer
* (T) transformação
* (E) enriquecer
* (R) rota

Esta imagem mostra como o processamento é dividido entre pedido e resposta, que fornece controlo sobre o pedido e os caminhos de resposta separadamente, por exemplo, ao utilizar diferentes é mapeada para cada caminho:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Além disso, um XML ponte unidirecional adiciona as fases de Decodificação e codificar no início e fim do processamento. A ponte de pass-through contém uma fase de Enrich única.

### <a name="message-processing-decoding-and-encoding"></a>Processamento de mensagens, descodificar e codificar

Nos serviços do BizTalk, pode receber diferentes tipos de mensagens XML e determinar o esquema correspondente para a mensagem recebida. Esse trabalho é feito no *tipos de mensagem* estágio do pipeline de processamento de receção. A fase de Decodificação, em seguida, utiliza o tipo de mensagem detetado para decodificar a mensagem com o esquema fornecido. Se o esquema é um esquema de ficheiro simples, neste estágio converte o entrada ficheiro simples para XML. 

O Logic Apps fornece recursos semelhantes. Recebe um ficheiro simples através de diferentes protocolos com os acionadores de conector diferentes (sistema de ficheiros, FTP, HTTP e assim por diante) e utilizar o [descodificação de ficheiro simples](../logic-apps/logic-apps-enterprise-integration-flatfile.md) ação para converter os dados de entrada em XML. Pode mover seus esquemas existentes do ficheiro simples diretamente para o Logic Apps sem quaisquer alterações e, em seguida, carregue esquemas para a sua conta de integração.

### <a name="validation"></a>Validação

Depois dos dados de entrada são convertidos para XML (ou se o XML foi o formato de mensagem recebido), a validação é executada para determinar se a mensagem respeita seu esquema XSD. Para efetuar esta tarefa no Logic Apps, utilize o [validação de XML](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) ação. Pode utilizar os mesmos esquemas dos serviços BizTalk sem quaisquer alterações.

### <a name="transform-messages"></a>Transforme as mensagens

Nos serviços do BizTalk, o estágio de transformação converte um formato de mensagens baseada em XML para outro. Este trabalho é feito através da aplicação de um mapa, com o mapeador de pontos com base em TRFM. No Logic Apps, o processo é semelhante. A ação de transformação executa um mapa da sua conta de integração. A principal diferença é que o maps no Logic Apps estão no formato XSLT. XSLT inclui a capacidade de reutilizar o XSLT existente já tem, incluindo mapas criados do BizTalk Server, que contêm functoids. 

### <a name="routing-rules"></a>Regras de encaminhamento

Os serviços BizTalk toma uma decisão de encaminhamento em qual ponto final ou o conector para enviar mensagens de entrada ou de dados. A capacidade de selecionar a partir de pontos finais previamente configurados é possível utilizar a opção de filtro de encaminhamento:

![](media/logic-apps-move-from-mabs/route-filter.png)

Os serviços BizTalk, se existirem duas únicas opções, a utilizar um *condição* é a melhor forma para a conversão de filtros de encaminhamento dos serviços BizTalk. Se existirem mais de duas, em seguida, utilizar um **mudar**.

O Logic Apps fornece capacidades de lógica sofisticado e fluxo de controle avançado e roteamento com [instruções condicionais](../logic-apps/logic-apps-control-flow-conditional-statement.md) e [declarações de comutador](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Enriquecer

No processamento de serviços do BizTalk, o estágio de Enrich Adiciona propriedades para o contexto de mensagem associado com os dados recebidos. Por exemplo, a promover uma propriedade para utilizar para o encaminhamento de uma pesquisa de base de dados ou ao extrair um valor utilizando uma expressão XPath. O Logic Apps fornece acesso a todos os resultados de dados contextuais do anterior ações, tornando muito simples replicar o mesmo comportamento. Por exemplo, utilizando o `Get Row` ação de ligação de SQL, devolver dados de uma base de dados do SQL Server e utilizar os dados numa ação de decisão para o encaminhamento. Da mesma forma, as propriedades de entrada do Service Bus em fila de espera mensagens por um acionador são endereçável, bem como XPath usando a expressão de linguagem de definição de fluxo de trabalho de xpath.

### <a name="run-custom-code"></a>Executar código personalizado

Serviços BizTalk permitem-lhe [executar código personalizado](https://msdn.microsoft.com/library/azure/dn232389.aspx) que é carregado em seus próprios assemblies. Esta funcionalidade é implementada pela [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector) interface. Cada fase na ponte inclui duas propriedades (no Inspetor de introduzir e no Inspetor de saída) que fornecem o tipo de .NET que criou, que implementa essa interface. Código personalizado permite-lhe efetuar o processamento mais complexo nos dados e permite-lhe reutilizar o código existente em assemblies que executam a lógica de negócios comum. 

O Logic Apps fornece duas formas principais de executar código personalizado: As funções do Azure e aplicações API. As funções do Azure podem ser criadas e chamadas a partir de aplicações lógicas. Ver [adicionar e executar código personalizado para o logic apps através das funções do Azure](../logic-apps/logic-apps-azure-functions.md). Utilize as aplicações API, parte do serviço de aplicações do Azure, para criar seus próprios acionadores e ações. Saiba mais sobre [criar uma API personalizada para utilizar com o Logic Apps](../logic-apps/logic-apps-create-api-app.md). 

Se tiver o código personalizado em assemblies que chamar dos serviços BizTalk, pode mover este código para as funções do Azure ou criar APIs personalizadas com aplicações API, consoante o que esteja implementando. Por exemplo, se tiver o código que encapsula outro serviço para o qual o Logic Apps não tem um conector, em seguida, criar uma aplicação API e utilizar as ações que a aplicação API fornece na sua aplicação lógica. Se tiver funções auxiliares ou bibliotecas, em seguida, as funções do Azure é provavelmente a melhor opção.

### <a name="edi-processing-and-trading-partner-management"></a>Processamento e gestão de parceiros comerciais de EDI

Os serviços BizTalk e o Logic Apps incluem o processamento de EDI e B2B com suporte para AS2 (declaração de aplicabilidade 2), X12 e EDIFACT. Nos serviços do BizTalk, sua criar bridges EDI e criar ou gerir parceiros e contratos no portal de controle e gerenciamento de dedicado comerciais.
No Logic Apps, obter essa funcionalidade por meio da [Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md). Fornece o EIP [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e as ações B2B para processamento de EDI e B2B. Também é usar uma conta de integração para criar e gerir [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) e [contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md). Depois de criar uma conta de integração, pode associar uma ou mais aplicações de lógica para a conta. Em seguida, pode utilizar ações B2B para aceder a informações de parceiro comercial da sua aplicação lógica. São fornecidas as seguintes ações:

* Codificação AS2.
* Descodificação de AS2
* Codificação de X12
* Descodificação de X12
* A codificação EDIFACT
* A descodificação EDIFACT

Ao contrário dos serviços BizTalk, essas ações estão desacopladas dos protocolos de transporte. Portanto, quando cria as logic apps, tem mais flexibilidade na qual conectores que utilizar para enviar e receber dados. Por exemplo, pode receber X12 os ficheiros como anexos de e-mail e o processo, em seguida, estes ficheiros numa aplicação lógica. 

## <a name="manage-and-monitor"></a>Gerir e monitorizar

Nos serviços do BizTalk, um portal dedicado fornecidas capacidades de controlo para monitorizar e resolver problemas. O Logic Apps fornece mais avançados de controlo e recursos por meio de monitoramento a [portal do Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md)e inclui uma aplicação móvel para manter um olho em coisas quando estiver em qualquer lugar.

## <a name="high-availability"></a>Elevada disponibilidade

Para elevada disponibilidade (HA) dos serviços BizTalk, pode partilhar a carga de processamento através de mais de uma instância numa região específica. O Logic Apps fornece HA na região e sem custos adicionais. 

Nos serviços do BizTalk, recuperação após desastre de fora da região para processamento B2B requer um processo de cópia de segurança e restauro. Para a continuidade do negócio, o Logic Apps fornece entre regiões. o ativo/passivo [capacidade de DR](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md), que permite que sincronize dados B2B em todas as contas de integração em regiões diferentes.

## <a name="next-steps"></a>Passos Seguintes

* [O que é o Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md) ou começar rapidamente com um [modelo compilado previamente](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* [Ver todos os conectores disponíveis](../connectors/apis-list.md) que pode utilizar no logic apps