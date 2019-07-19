---
title: Mover aplicativos dos serviços BizTalk para o aplicativo lógico do Azure | Microsoft Docs
description: Migrar dos serviços BizTalk do Azure (MABS) para os aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: dfc0aa4fa7c70ae91f25f97671b15dacfe991594
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273186"
---
# <a name="migrate-from-biztalk-services-to-azure-logic-apps"></a>Migrar dos serviços BizTalk para o aplicativo lógico do Azure

Serviços BizTalk do Microsoft Azure (MABS) está sendo desativado. Para mover suas soluções de integração do MABS para os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md), siga as orientações neste artigo. 

## <a name="introduction"></a>Introdução

Os serviços BizTalk consistem em dois subserviços:

* Conexões Híbridas de serviços BizTalk da Microsoft
* Integração baseada em ponte EAI e EDI

O [serviço de Azure App conexões híbridas](../app-service/app-service-hybrid-connections.md) substitui conexões híbridas de serviços BizTalk. O Conexões Híbridas do Azure está disponível com o serviço Azure App por meio do portal do Azure. Esse serviço fornece um Gerenciador de Conexões Híbridas para que você possa gerenciar conexões híbridas de serviços BizTalk existentes e também novas conexões híbridas criadas no Portal. 

Os [aplicativos lógicos](../logic-apps/logic-apps-overview.md) substituem a integração baseada em ponte EAI e EDI com todos os mesmos recursos nos serviços BizTalk e muito mais. Esse serviço fornece fluxo de trabalho baseado em consumo em escala de nuvem e recursos de orquestração para você criar rapidamente e facilmente soluções de integração complexas por meio de um navegador ou com o Visual Studio.

Esta tabela mapeia os recursos dos serviços BizTalk para aplicativos lógicos.

| Serviços BizTalk   | Aplicações Lógicas            | Objetivo                      |
| ------------------ | --------------------- | ---------------------------- |
| Conector          | Conector             | Enviar e receber dados   |
| Bridge             | Aplicação Lógica             | Processador de pipeline           |
| Estágio de validação     | Ação de validação de XML | Validar um documento XML em um esquema | 
| Estágio enriquecer       | Tokens de dados           | Promover Propriedades em mensagens ou para decisões de roteamento |
| Estágio de transformação    | Ação de transformação      | Converter mensagens XML de um formato para outro |
| Estágio de decodificação       | Ação de decodificação de arquivo simples | Converter de arquivo simples em XML |
| Estágio de codificação       | Ação de codificação de arquivo simples | Converter de XML em arquivo simples |
| Inspetor de mensagem  | Azure Functions ou aplicativos de API | Executar código personalizado em suas integrações |
| Ação de rota       | Condição ou comutador | Rotear mensagens para um dos conectores especificados |
|||| 

## <a name="biztalk-services-artifacts"></a>Artefatos dos serviços BizTalk

Os serviços BizTalk têm vários tipos de artefatos. 

## <a name="connectors"></a>Conectores

Os conectores dos serviços BizTalk ajudam as pontes a enviar e receber dados, incluindo pontes bidirecionais que habilitam interações de solicitação/resposta baseadas em HTTP. Os aplicativos lógicos usam a mesma terminologia e têm centenas de conectores que têm a mesma finalidade conectando-se a uma ampla gama de tecnologias e serviços. Por exemplo, os conectores estão disponíveis para serviços de PaaS e SaaS de nuvem, como OneDrive, Office365, Dynamics CRM e mais, além de sistemas locais por meio do gateway de dados local, que substitui o serviço de adaptador do BizTalk para serviços BizTalk. As fontes nos serviços BizTalk são limitadas ao FTP, SFTP e à fila do barramento de serviço ou à assinatura de tópico.

![](media/logic-apps-move-from-mabs/sources.png)

Por padrão, cada ponte tem um ponto de extremidade HTTP, que é configurado com o endereço de tempo de execução e as propriedades de endereço relativo para a ponte. Para obter os mesmos resultados com aplicativos lógicos, use as ações de [solicitação e resposta](../connectors/connectors-native-reqres.md) .

## <a name="xml-processing-and-bridges"></a>Processamento e pontes XML

Nos serviços BizTalk, uma ponte é análoga a um pipeline de processamento. Uma ponte pode receber dados recebidos de um conector, fazer algumas trabalhar com os dados e enviar os resultados para outro sistema. Os aplicativos lógicos são os mesmos oferecendo suporte aos mesmos padrões de interação baseados em pipeline que os serviços BizTalk e também fornecem outros padrões de integração. A [ponte de solicitação-resposta XML](https://msdn.microsoft.com/library/azure/hh689781.aspx) nos serviços BizTalk é conhecida como um pipeline VETER, que consiste em estágios que executam essas tarefas:

* (V) validar
* (E) enriquecer
* (T) transformar
* (E) enriquecer
* (R) rota

Esta imagem mostra como o processamento é dividido entre a solicitação e a resposta, que fornece controle sobre a solicitação e os caminhos de resposta separadamente, por exemplo, usando mapas diferentes para cada caminho:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Além disso, uma ponte unidirecional XML adiciona estágios de decodificação e codificação no início e no final do processamento. A ponte de passagem contém um único estágio de enriquecimento.

### <a name="message-processing-decoding-and-encoding"></a>Processamento de mensagens, decodificação e codificação

Nos serviços BizTalk, você pode receber diferentes tipos de mensagens XML e determinar o esquema de correspondência para a mensagem recebida. Esse trabalho é executado no estágio *tipos de mensagem* do pipeline de processamento de recebimento. Em seguida, o estágio de decodificação usa o tipo de mensagem detectada para decodificar a mensagem usando o esquema fornecido. Se o esquema for um esquema de arquivo simples, esse estágio converterá o arquivo simples de entrada em XML. 

Os aplicativos lógicos fornecem recursos semelhantes. Você recebe um arquivo simples em protocolos diferentes usando gatilhos de conector diferentes (sistema de arquivos, FTP, HTTP e assim por diante) e usa a ação de decodificação de [arquivo simples](../logic-apps/logic-apps-enterprise-integration-flatfile.md) para converter os dados de entrada em XML. Você pode mover os esquemas de arquivo simples existentes diretamente para os aplicativos lógicos sem nenhuma alteração e, em seguida, carregar esquemas para sua conta de integração.

### <a name="validation"></a>Validação

Depois que os dados de entrada são convertidos em XML (ou se XML foi o formato de mensagem recebido), a validação é executada para determinar se a mensagem está de acordo com o esquema XSD. Para executar essa tarefa em aplicativos lógicos, use a ação [validação de XML](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) . Você pode usar os mesmos esquemas dos serviços BizTalk sem nenhuma alteração.

### <a name="transform-messages"></a>Transformar mensagens

Nos serviços BizTalk, o estágio transformar converte um formato de mensagem baseado em XML em outro. Esse trabalho é feito aplicando-se um mapa, usando o mapeador baseado em TRFM. Em aplicativos lógicos, o processo é semelhante. A ação de transformação executa um mapa de sua conta de integração. A principal diferença é que os mapas em aplicativos lógicos estão no formato XSLT. O XSLT inclui a capacidade de reutilizar o XSLT existente que você já tem, incluindo mapas criados para BizTalk Server que contêm functoids. 

### <a name="routing-rules"></a>Regras de encaminhamento

Os serviços BizTalk fazem uma decisão de roteamento sobre a qual ponto de extremidade ou conector enviar mensagens ou dados de entrada. A capacidade de selecionar pontos de extremidade pré-configurados é possível usando a opção de filtro de roteamento:

![](media/logic-apps-move-from-mabs/route-filter.png)

Nos serviços BizTalk, se houver apenas duas opções, o uso de uma *condição* é a melhor maneira de converter filtros de roteamento nos serviços BizTalk. Se houver mais de duas, use uma **opção**.

Os aplicativos lógicos fornecem recursos de lógica sofisticados, além de fluxo de controle avançado e roteamento com [instruções condicionais](../logic-apps/logic-apps-control-flow-conditional-statement.md) e [instruções switch](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Aprimora

No processamento dos serviços BizTalk, o estágio enriquecer adiciona propriedades ao contexto da mensagem associado aos dados recebidos. Por exemplo, promover uma propriedade a ser usada para roteamento de uma pesquisa de banco de dados ou extrair um valor usando uma expressão XPath. Os aplicativos lógicos fornecem acesso a todas as saídas de dados contextuais de ações anteriores, tornando simples replicar o mesmo comportamento. Por exemplo, usando a `Get Row` ação de conexão do SQL, você retorna dados de um SQL Server um banco de dados e usa-os em uma ação de decisão para roteamento. Da mesma forma, as propriedades nas mensagens enfileiradas do barramento de serviço de entrada por um gatilho são endereçáveis, bem como XPath usando a expressão de linguagem de definição de fluxo de trabalho XPath.

### <a name="run-custom-code"></a>Executar código personalizado

Os serviços BizTalk permitem que você [execute código personalizado](https://msdn.microsoft.com/library/azure/dn232389.aspx) que é carregado em seus próprios assemblies. Essa funcionalidade é implementada pela interface [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector) . Cada estágio na ponte inclui duas propriedades (no Inspetor de entrada e no Inspetor de saída) que fornecem o tipo .NET criado que implementa essa interface. O código personalizado permite que você execute processamento mais complexo nos dados e permite reutilizar o código existente em assemblies que executam lógica de negócios comum. 

Os aplicativos lógicos fornecem duas maneiras principais de executar código personalizado: Azure Functions e aplicativos de API. Azure Functions pode ser criado e chamado de aplicativos lógicos. Consulte [Adicionar e executar código personalizado para aplicativos lógicos por meio de Azure Functions](../logic-apps/logic-apps-azure-functions.md). Use aplicativos de API, parte do serviço Azure App, para criar seus próprios gatilhos e ações. Saiba mais sobre como [criar uma API personalizada para usar com aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md). 

Se você tiver código personalizado em assemblies que você chama dos serviços BizTalk, você pode mover esse código para Azure Functions ou criar APIs personalizadas com aplicativos de API, dependendo do que você está implementando. Por exemplo, se você tiver um código que encapsula outro serviço para o qual os aplicativos lógicos não têm um conector, crie um aplicativo de API e use as ações que seu aplicativo de API fornece dentro de seu aplicativo lógico. Se você tiver funções ou bibliotecas auxiliares, Azure Functions provavelmente será a melhor opção.

### <a name="edi-processing-and-trading-partner-management"></a>Processamento de EDI e gerenciamento de parceiros comerciais

Os serviços BizTalk e os aplicativos lógicos incluem processamento de EDI e B2B com suporte para AS2 (Applicability Statement 2), X12 e EDIFACT. Nos serviços BizTalk, crie pontes EDI e crie ou gerencie parceiros e contratos comerciais no portal de gerenciamento e acompanhamento dedicado.
Em aplicativos lógicos, você obtém essa funcionalidade por meio do [Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md). O EIP fornece a [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e as ações B2B para o processamento de EDI e B2B. Você também usa uma conta de integração para criar e gerenciar [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) e [contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md). Depois de criar uma conta de integração, você pode vincular um ou mais aplicativos lógicos à conta. Você pode usar ações B2B para acessar informações de parceiros comerciais de seu aplicativo lógico. As seguintes ações são fornecidas:

* Codificação AS2
* Decodificação AS2
* Codificação X12
* Decodificação X12
* Codificação EDIFACT
* Decodificação EDIFACT

Ao contrário dos serviços BizTalk, essas ações são dissociadas dos protocolos de transporte. Portanto, ao criar seus aplicativos lógicos, você tem mais flexibilidade em quais conectores você usa para enviar e receber dados. Por exemplo, você pode receber arquivos X12 como anexos de email e, em seguida, processar esses arquivos em um aplicativo lógico. 

## <a name="manage-and-monitor"></a>Gerir e monitorizar

Nos serviços BizTalk, um portal dedicado fornecia recursos de controle para monitorar e solucionar problemas. Os aplicativos lógicos fornecem recursos mais avançados de rastreamento e monitoramento por meio do [portal do Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md)e inclui um aplicativo móvel para ficar atento às coisas quando você está em movimento.

## <a name="high-availability"></a>Elevada disponibilidade

Para HA (alta disponibilidade) nos serviços BizTalk, você pode compartilhar a carga de processamento usando mais de uma instância em uma região específica. Os aplicativos lógicos fornecem HA em região sem custo adicional. 

Nos serviços BizTalk, a recuperação de desastres fora de região para processamento B2B requer um processo de backup e restauração. Para a continuidade dos negócios, os aplicativos lógicos fornecem o [recurso de recuperação](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)de desastres ativo/passivo entre regiões, que permite sincronizar dados B2B entre contas de integração em regiões diferentes.

## <a name="next-steps"></a>Passos Seguintes

* [O que são aplicativos lógicos?](../logic-apps/logic-apps-overview.md)
* [Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md) ou começar rapidamente com um [modelo compilado previamente](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* [Exibir todos os conectores disponíveis](../connectors/apis-list.md) que você pode usar em aplicativos lógicos