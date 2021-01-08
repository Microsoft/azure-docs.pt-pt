---
title: Conectores do Azure Logic Apps
description: Automatizar fluxos de trabalho com conectores para apps Azure Logic, tais como incorporados, geridos, no local, conta de integração, ISE e conectores empresariais
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: article
ms.date: 01/07/2021
ms.openlocfilehash: c2b89450c0e474f5030f8812e888890f1fedde7e
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019640"
---
# <a name="connectors-for-azure-logic-apps"></a>Conectores do Azure Logic Apps

Os conectores proporcionam um acesso rápido a partir do Azure Logic Apps a eventos, dados e ações noutros serviços, aplicações, sistemas, protocolos e plataformas. Ao utilizar conectores nas suas aplicações lógicas, expande as capacidades das aplicações na cloud e locais para executarem tarefas com os dados que cria e já tem.

Enquanto a Logic Apps oferece [centenas de conectores](/connectors), este artigo descreve os conectores *populares e mais utilizados* que são usados com sucesso por milhares de apps e milhões de execuções para o processamento de dados e informações. Para encontrar a lista completa de conectores e as informações de referência de cada conector, tais como gatilhos, ações e limites, consulte as páginas de referência do conector sob [a visão geral dos Conectores](/connectors). Além disso, saiba mais sobre [gatilhos e ações,](#triggers-actions) [modelo de preços de Aplicações Lógicas](../logic-apps/logic-apps-pricing.md)e detalhes de preços de Apps [Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps/)

> [!TIP]
> Para se integrar com um serviço ou API que não tenha conector, pode ligar diretamente para o serviço através de um protocolo como HTTP, ou criar um [conector personalizado.](#custom)

## <a name="connector-types"></a>Tipos de conector

Os conectores estão disponíveis como gatilhos e ações incorporados ou como conectores geridos.

<a name="built-in"></a>

* [**Incorporado :**](#built-ins)Desencadeadores e ações incorporadas são executados de forma nativa em Azure Logic Apps para que não exijam a criação de uma ligação antes de as utilizar e ajudá-lo a executar estas tarefas para as suas aplicações lógicas:

  * Corra em horários personalizados e avançados.

  * Organize e controle o fluxo de trabalho da sua aplicação lógica, por exemplo, loops e condições, e também para trabalhar com variáveis e operações de dados.

  * Comunicar com outros pontos finais.

  * Receber e responder aos pedidos.

  * Call Azure funções, Azure API Apps (Web Apps), as suas próprias APIs geridas e publicadas com a Azure API Management, e aplicações lógicas aninhadas que podem receber pedidos.

<a name="managed-connectors"></a>

* [**Conectores geridos**](#managed-api-connectors): Implantados e geridos pela Microsoft, estes conectores fornecem gatilhos e ações para aceder a serviços na nuvem, sistemas no local, ou ambos, incluindo Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint, entre outros. Alguns conectores suportam especificamente cenários de comunicação business-to-business (B2B) e requerem uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) que está ligada à sua aplicação lógica. Antes de utilizar certos conectores, poderá ter de criar primeiro ligações, que são geridas por Azure Logic Apps.

  Por exemplo, se estiver a utilizar o Microsoft BizTalk Server, as suas aplicações lógicas podem ligar-se e comunicar com o seu Servidor BizTalk utilizando o [conector do Servidor BizTalk no local](#on-premises-connectors). Em seguida, pode estender ou executar operações semelhantes ao BizTalk nas suas aplicações lógicas utilizando os [conectores](#integration-account-connectors)de conta de integração .

  Os conectores são classificados como Standard ou Enterprise. [Os conectores empresariais](#enterprise-connectors) fornecem acesso a sistemas empresariais como o SAP, o IBM MQ e o IBM 3270 por um custo adicional. Para determinar se um conector é Standard ou Enterprise, consulte os detalhes técnicos na página de referência de cada conector no panorama geral dos [Conectores](/connectors).

  Também pode identificar conectores utilizando estas categorias, embora alguns conectores possam existir em várias categorias. Por exemplo, o SAP é um conector Enterprise e um conector no local:

  | Categoria | Descrição |
  |----------|-------------|
  | [**Conectores geridos**](#managed-api-connectors) | Crie aplicações lógicas que utilizem serviços como Azure Blob Storage, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online, entre muitos outros. |
  | [**Conectores no local**](#on-premises-connectors) | Depois de instalar e configurar o [gateway de dados no local,][gateway-doc]estes conectores ajudam as suas aplicações lógicas a aceder a sistemas no local como SQL Server, SharePoint Server, Oracle DB, partilhas de ficheiros, entre outros. |
  | [**Conectores de conta de integração**](#integration-account-connectors) | Disponíveis quando cria e paga por uma conta de integração, estes conectores transformam e validam XML, codificam e descodificam ficheiros planos e processam mensagens business-to-business (B2B) com protocolos AS2, EDIFACT e X12. |
  |||

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment-ise"></a>Conecte-se a partir de um ambiente de serviço de integração (ISE)

Para aplicações lógicas que necessitem de acesso direto aos recursos numa rede virtual Azure, pode criar um ambiente de serviço de integração dedicado [(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) onde pode construir, implementar e executar as suas aplicações lógicas em recursos dedicados. No Logic App Designer, quando navega nos conectores que pretende utilizar para aplicações lógicas num ISE, aparece uma etiqueta **CORE** em gatilhos e ações incorporados, enquanto a etiqueta **ISE** aparece em alguns conectores.

> [!NOTE]
> As aplicações lógicas que funcionam num ISE e os seus conectores, independentemente do local onde esses conectores funcionam, seguem um plano de preços fixos em relação ao plano de preços baseado no consumo. Para obter mais informações, consulte [o modelo de preços de Apps Lógicas](../logic-apps/logic-apps-pricing.md) e detalhes de preços de Apps [Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps/)

| Etiqueta | Exemplo | Descrição |
|-------|---------|-------------|
| **CORE** | ![Conector CORE exemplo](./media/apis-list/example-core-connector.png) | Os gatilhos incorporados e as ações com esta etiqueta funcionam no mesmo ISE que as suas aplicações lógicas. |
| **ISE** | ![Conector ISE exemplo](./media/apis-list/example-ise-connector.png) | Os conectores geridos com esta etiqueta funcionam no mesmo ISE que as suas aplicações lógicas. Se tiver um sistema no local ligado a uma rede virtual Azure, um ISE permite que as suas aplicações lógicas acedam diretamente a esse sistema sem o [portal de dados no local.](../logic-apps/logic-apps-gateway-connection.md) Em vez disso, pode utilizar o conector **ISE** do sistema, se disponível, uma ação HTTP ou um [conector personalizado.](#custom) Para sistemas no local que não tenham conectores **ISE,** utilize o portal de dados no local. Para rever os conectores ISE disponíveis, consulte [os conectores ISE](#ise-connectors). |
| Sem rótulo | ![Exemplo de conector multi-inquilino](./media/apis-list/example-multi-tenant-connector.png) | Todos os outros conectores sem a etiqueta **CORE** ou **ISE,** que pode continuar a utilizar, funcionam no serviço global de Aplicações Lógicas multi-arrendatários. |
|||

<a name="built-ins"></a>

## <a name="built-in"></a>Incorporado

A Logic Apps fornece gatilhos e ações incorporados para que possa criar fluxos de trabalho baseados em horários, ajudar as suas aplicações lógicas a comunicar com outras apps e serviços, controlar o fluxo de trabalho através das suas aplicações lógicas e gerir ou manipular dados.

| Nome | Descrição |
|------|-------------|
| [![Horário do conector incorporado ][schedule-icon]<br> ][schedule-doc] | - Executar uma aplicação lógica numa recorrência especificada, que vai desde horários básicos a horários avançados com o gatilho [ **de Recorrência**][schedule-recurrence-doc]. <br>- Executar uma aplicação lógica que precisa de lidar com dados em pedaços contínuos com o gatilho [ **da janela deslizante**][schedule-sliding-window-doc]. <br>- Faça uma pausa na sua aplicação lógica durante uma duração especificada com a ação [ **Delay**][schedule-delay-doc]. <br>- Faça uma pausa na sua aplicação lógica até à data e hora especificadas com o [ **Atraso até** à ação][schedule-delay-until-doc]. |
| [![Lote de conector incorporado ][batch-icon]<br> **lote**][batch-doc] | - Processar mensagens em lotes com o gatilho **de mensagens Batch.** <br>- Ligue para aplicações lógicas que tenham gatilhos de lote existentes com as **mensagens Enviar para ação de lote.** |
| [![HTTP Conector incorporado ][http-icon]<br> **HTTP**][http-doc] | Chamar pontos finais HTTP ou HTTPS com acionadores e ações para HTTP. Outros gatilhos e ações incorporadas http-in incluem [HTTP + Conector incorporado swagger][http-swagger-doc] e HTTP + [Webhook][http-webhook-doc]. |
| [![Pedido de pedido de ][http-request-icon]<br>  conector incorporado][http-request-doc] | - Tornar a sua aplicação lógica callable a partir de outras aplicações ou serviços, desencadear eventos de recursos de Event Grid ou desencadear respostas aos alertas do Azure Security Center com o gatilho **do Pedido.** <br>- Enviar respostas para uma app ou serviço com a ação **Resposta.** |
| [![Gestão AZURE API Gestão incorporada ][azure-api-management-icon]<br> **Azure API <br> Management**][azure-api-management-doc] | Chame acionadores e ações definidos pelas suas próprias APIs que gere e publica com a Gestão de API do Azure. |
| [![Azure App Services serviços de conector ][azure-app-services-icon]<br> **<br> incorporadoS Azure App Services**][azure-app-services-doc] | Ligue para as Aplicações API do Azure ou as Aplicações Web alojadas no Serviço de Aplicações do Azure. Os gatilhos e ações definidos por estas aplicações aparecem como quaisquer outros gatilhos e ações de primeira classe quando o Swagger está incluído. |
| [![Azure Logic Apps incorporado connector ][azure-logic-apps-icon]<br> **Azure Logic <br> Apps**][nested-logic-app-doc] | Ligue para outras aplicações lógicas que começam com o gatilho **Do Pedido.** |
|||

### <a name="run-code-from-logic-apps"></a>Executar código a partir de aplicativos lógicos

A Logic Apps fornece ações incorporadas para executar o seu próprio código no fluxo de trabalho da sua aplicação lógica:

| Nome | Descrição |
|------|-------------|
| [![Funções Azure funções de conector incorporado ][azure-functions-icon]<br> **Funções Azure**][azure-functions-doc] | Ligue para as funções Azure que executam os snippets de código personalizado (C# ou Node.js) das suas aplicações lógicas. |
| [![Código inline código inline código ][inline-code-icon]<br> **inline Código Inline Código Código Inline Código**][inline-code-doc] | Adicione e execute os snippets de código JavaScript das suas aplicações lógicas. |
|||

### <a name="control-workflow"></a>Fluxo de trabalho de controlo

A Logic Apps fornece ações integradas para estruturar e controlar as ações no fluxo de trabalho da sua aplicação lógica:

| Nome | Descrição |
|------|-------------|
| [![Condição de ação incorporada ][condition-icon]<br> ][condition-doc] | Avaliar uma condição e executar diferentes ações com base no facto de a condição ser verdadeira ou falsa. |
| [![Para cada ação incorporada ][for-each-icon]<br> **para cada**][for-each-doc] | Execute as mesmas ações em cada item de uma matriz. |
| [![Âmbito de ação incorporado ][scope-icon]<br> **Âmbito**][scope-doc] | Ações de grupo em *âmbitos*, que obtêm o seu próprio estatuto após as ações no âmbito de execução. |
| [![Switch switch de ação incorporado ][switch-icon]<br> **Switch**][switch-doc] | Ações de grupo em *casos*, que são atribuídos valores únicos, exceto para o caso padrão. Executar apenas aquele caso cujo valor atribuído corresponde ao resultado de uma expressão, objeto ou token. Se não existirem fósforos, execute o caso predefinido. |
| [![Terminar a ação incorporada ][terminate-icon]<br> **Termina**][terminate-doc] | Pare um fluxo de trabalho de aplicações lógicas ativamente funcionando. |
| [![Até a ação incorporada ][until-icon]<br> **até**][until-doc] | Repita as ações até que a condição especificada seja verdadeira ou algum estado tenha mudado. |
|||

### <a name="manage-or-manipulate-data"></a>Gerir ou manipular dados

A Logic Apps fornece ações integradas para trabalhar com as saídas de dados e os seus formatos:

| Nome | Descrição |
|------|-------------|
| [![Operações de dados incorporadas em ][data-operations-icon]<br> **operações de dados**][data-operations-doc] | Realizar operações com dados: <p>- **Compor**: Criar uma única saída a partir de múltiplas entradas com vários tipos. <br>- **Criar tabela CSV**: Criar uma tabela de valor separado em vírgula (CSV) a partir de uma matriz com objetos JSON. <br>- **Criar tabela HTML**: Criar uma tabela HTML a partir de uma matriz com objetos JSON. <br>- **Matriz de filtro**: Crie uma matriz a partir de itens em outra matriz que satisfaça os seus critérios. <br>- **Junte-se:** Crie uma cadeia de todos os itens numa matriz e separe esses itens com olimdidor especificado. <br>- **Parse JSON**: Crie fichas fáceis de utilizar a partir de propriedades e seus valores em conteúdo JSON para que possa utilizar essas propriedades no seu fluxo de trabalho. <br>- **Selecione:** Crie uma matriz com objetos JSON transformando itens ou valores noutra matriz e mapeando esses itens para propriedades especificadas. |
| ![Data hora incorporada ação][date-time-icon]<br>**Hora da data** | Executar operações com tempotando: <p>- **Adicione ao tempo**: Adicione o número especificado de unidades a uma estampada de tempo. <br>- **Verso horário**: Converta um relógio de tempo do fuso horário de origem para o fuso horário alvo. <br>- **Tempo atual**: Devolva a atual placa de tempo como uma corda. <br>- **Obtenha a hora futura**: Devolva a hora atual mais as unidades de tempo especificadas. <br>- **Passar a tempo**: Devolva o tempo de tempo atual menos as unidades de tempo especificadas. <br>- **Subtrair do tempo**: Subtrair um número de unidades de tempo de um relógio. |
| [![Variáveis de ação ][variables-icon]<br> **incorporadas**][variables-doc] | Realizar operações com variáveis: <p>- **Apêndice à variável de matriz**: Insira um valor como o último item numa matriz armazenada por uma variável. <br>- **Apêndice à variável de corda**: Insira um valor como o último caracter numa corda armazenada por uma variável. <br>- **Variável de decremento**: Diminua uma variável por um valor constante. <br>- **Variável incremento**: Aumente uma variável por um valor constante. <br>- **Inicializar variável**: Criar uma variável e declarar o seu tipo de dados e valor inicial. <br>- **Variável definida**: Atribua um valor diferente a uma variável existente. |
|||

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>Conectores geridos

A Logic Apps fornece estes conectores Standard populares para automatizar tarefas, processos e fluxos de trabalho com estes serviços ou sistemas:

| Nome | Descrição |
|------|-------------|
| [![Azure Service Bus geriu o conector ][azure-service-bus-icon]<br> **Azure Service Bus**][azure-service-bus-doc] | Faça a gestão de mensagens assíncronas, sessões e subscrições de tópicos com o conector mais usado em Logic Apps. |
| [![SQL Server gerido conector ][sql-server-icon]<br> **SQL Server**][sql-server-doc] | Ligue ao seu SQL Server nas instalações ou numa Base de Dados Azure SQL na nuvem para que possa gerir registos, executar procedimentos armazenados ou realizar consultas. |
| [![Azure Blob Storage gerido conector ][azure-blob-storage-icon]<br> **Azure Blob <br> Storage**][azure-blob-storage-doc] | Ligue-se à sua conta de armazenamento para criar e gerir conteúdos de blob. |
| [![Office 365 Outlook gerido conector ][office-365-outlook-icon]<br> **Office 365 <br> Outlook**][office-365-outlook-doc] | Conecte-se à sua conta de e-mail de trabalho ou escola para que possa criar e gerir e-mails, tarefas, eventos de calendário e reuniões, contactos, pedidos e muito mais. |
| [![Conector gerido ][sftp-ssh-icon]<br> **SFTP-SSH SFTP-SSH**][sftp-ssh-doc] | Ligue-se a servidores SFTP aos quais pode aceder a partir da Internet com o SSH para poder trabalhar com os ficheiros e as pastas. |
| [![SharePoint Contacto Gerido Online ][sharepoint-online-icon]<br> **SharePoint <br> Online**][sharepoint-online-doc] | Ligue-se ao SharePoint Online para poder gerir ficheiros, anexos, pastas e mais. |
| [![Azure Queues geriu o conector ][azure-queues-icon]<br> **Azure <br> Queues**][azure-queues-doc] | Ligue-se à sua conta de Armazenamento Azure para que possa criar e gerir filas e mensagens. |
| [![Conector gerido ][ftp-icon]<br> **FTP FTP**][ftp-doc] | Ligue-se aos servidores FTP que pode aceder a partir da internet para que possa trabalhar com os seus ficheiros e pastas. |
| [![Sistema de ficheiros gerido sistema de ][file-system-icon]<br> **conector <br> de ficheiros gerido**][file-system-doc] | Ligue-se à partilha de ficheiros no local para que possa criar e gerir ficheiros. |
| [![Azure Event Hubs geriu o conector ][azure-event-hubs-icon]<br> **Azure Event Hubs**][azure-event-hubs-doc] | Consuma e publique eventos através de um Hub de Eventos. Por exemplo, utilize o Hubs de Eventos para obter resultados da sua aplicação lógica e, em seguida, envie esses resultados para um fornecedor de análises em tempo real. |
| [![Azure Event Grid gerido connector ][azure-event-grid-icon]<br> **Azure Event** <br> **Grid**][azure-event-grid-doc] | Monitorize eventos publicados por uma Grade de Eventos, por exemplo, quando os recursos do Azure ou recursos de terceiros mudam. |
| [![Salesforce gerido conector ][salesforce-icon]<br> **Salesforce**][salesforce-doc] | Ligue-se à sua conta Salesforce para que possa criar e gerir itens como registos, empregos, objetos e muito mais. |
|||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Conectores no local

Antes de criar uma ligação a um sistema no local, tem primeiro de [descarregar, instalar e configurar um gateway de dados no local.][gateway-doc] Este gateway fornece um canal de comunicação seguro sem ter que configurar a infraestrutura de rede necessária. 

Aqui estão *alguns* conectores Standard comumente usados que as Aplicações Lógicas fornecem para aceder a dados e recursos em sistemas no local. Para a lista de conectores no local, consulte [fontes de dados suportadas](../logic-apps/logic-apps-gateway-connection.md#supported-connections).

:::row:::
    :::column:::
        [![Servidor bizTalk Servidor ][biztalk-server-icon]<br> **bizTalk** <br>][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![Sistema de ficheiros do conector do sistema ][file-system-icon]<br> **de <br> ficheiros de ficheiros** do sistema de ficheiros][file-system-doc]
    :::column-end:::
    :::column:::
        [![Conector DB2 ][ibm-db2-icon]<br> **IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Conector informe ][ibm-informix-icon]<br> **IBM** <br> **Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Conector ][mysql-icon]<br> **MySQL MySQl**][mysql-doc]
    :::column-end:::
    :::column:::
        [![Conector ][oracle-db-icon]<br> **Oráculo** DB Oracle DB][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![Postgresql conector ][postgre-sql-icon]<br> **postgresql**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![Servidor do conector do SharePoint ][sharepoint-server-icon]<br> **<br> SharePoint do SharePoint**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![][sql-server-icon]<br>**<br> Servidor SQL** Server][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Conector ][teradata-icon]<br> **Teradata Teradata**][teradata-doc]
    :::column-end:::
    :::column:::
        
    :::column-end:::
    :::column:::
        
    :::column-end:::
:::row-end:::

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Conectores de conta de integração

A Logic Apps fornece conectores Standard para construir soluções business-to-business (B2B) com as suas aplicações lógicas quando cria e paga por uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), que está disponível através do Enterprise Integration Pack (EIP) em Azure. Com esta conta, pode criar e armazenar artefactos B2B como parceiros comerciais, acordos, mapas, esquemas, certificados, e assim por diante. Para utilizar estes artefactos, associe as suas aplicações lógicas à sua conta de integração. Se utilizar atualmente o BizTalk Server, estes conectores podem já parecer familiares.

:::row:::
    :::column:::
        [![Ação de descodição ][as2-icon]<br> **AS2 <br> descodante AS2**][as2-doc]
    :::column-end:::
    :::column:::
        [![Ação de codificação ][as2-icon]<br> **AS2 AS2 <br>**][as2-doc]
    :::column-end:::
    :::column:::
        [![Ação de descodição ][edifact-icon]<br> **do EDIFACT EDIFACT <br>**][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![Ação de codificação ][edifact-icon]<br> **EDIFACT EDIFACT <br> codificação**][edifact-encode-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Descodição de ficheiro plano ][flat-file-decode-icon]<br> **<br> descodimento de ficheiro plano**][flat-file-decode-doc]
    :::column-end:::
    :::column:::
        [![Codificação de arquivo plano codificação ][flat-file-encode-icon]<br> **de arquivo plano <br> codificação de ficheiros**][flat-file-encode-doc]
    :::column-end:::
    :::column:::
        [![Conta de integração ][integration-account-icon]<br> **Conta Conta <br> Integração**][integration-account-doc]
    :::column-end:::
    :::column:::
        [![Líquido transforma ação ][liquid-icon]<br> **Liquid** <br> **transforma**][json-liquid-transform-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![X12 descodição de ação ][x12-icon]<br> **X12 <br>**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![Codificação X12 de codificação de ação ][x12-icon]<br> **X12 <br>**][x12-encode-doc]
    :::column-end:::
    :::column:::
        [![XML transforma ação ][xml-transform-icon]<br> **XML** <br> **transforma**][xml-transform-doc]
    :::column-end:::
    :::column:::
        [![Validação XML de validação de ação de validação ][xml-validate-icon]<br> **XML <br>**][xml-validate-doc]
    :::column-end:::
:::row-end:::

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Conectores empresariais

A Logic Apps fornece estes conectores Enterprise para aceder a sistemas empresariais, tais como SAP e IBM MQ:

:::row:::
    :::column:::
        [![Conector IBM 3270 ][ibm-3270-icon]<br> **IBM 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Conector MQ ][ibm-mq-icon]<br> **IBM MQ**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![Conector ][sap-icon]<br> **SAP**][sap-connector-doc]
    :::column-end:::
    :::column:::
        
    :::column-end:::
:::row-end:::

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>Conectores ISE

Para aplicações lógicas que cria e execute num ambiente de serviço de integração dedicado [(ISE),](#integration-service-environment)o Logic App Designer identifica gatilhos e ações incorporadas que funcionam no seu ISE utilizando a etiqueta **CORE.** Os conectores geridos que funcionam num ise exibem a etiqueta **ISE,** enquanto os conectores que funcionam no serviço global de Aplicações Lógicas multi-arrendatários não exibem nenhuma das etiquetas. Esta lista mostra os conectores que atualmente têm versões ISE:

:::row:::
    :::column:::
        [![Conector AS2 ISE ][as2-icon]<br> **AS2**][as2-doc]
    :::column-end:::
    :::column:::
        [![Azure Automation ISE connector ][azure-automation-icon]<br> **Azure <br> Automation**][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![Azure Blob Storage ISE connector ][azure-blob-storage-icon]<br> **Azure Blob <br> Storage**][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![Conector Azure Cosmos DB ISE ][azure-cosmos-db-icon]<br> **Azure Cosmos <br> DB**][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Event Hubs ISE connector ][azure-event-hubs-icon]<br> **Azure Event <br> Hubs**][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![Azure Event Grid ISE connector ][azure-event-grid-icon]<br> **Azure Event <br> Grid**][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![Armazenamento de ficheiros Azure ISE connector ][azure-file-storage-icon]<br> **Azure File <br> Storage**][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![Cofre ISE chave Azure ][azure-key-vault-icon]<br> **<br>**][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Registos de monitores Azure ISE ][azure-monitor-logs-icon]<br> **<br> Registos monitores Azure Monitor**][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![Ônibus ise de ônibus ][azure-service-bus-icon]<br> **Azure <br>**][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![Azure Synapse Analytics ISE conector ][azure-sql-data-warehouse-icon]<br> **Azure SQL Data <br> Warehouse**][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![Armazenamento de mesa Azure ISE connector ][azure-table-storage-icon]<br> **Azure Table <br> Storage**][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Filas Azure ISE connector ][azure-queues-icon]<br> **Azure <br> Queues**][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![][edifact-icon]<br>**Conector EDIFACT** ISE EDIFACT][edifact-doc]
    :::column-end:::
    :::column:::
        [![Sistema de ficheiros ise do sistema de ][file-system-icon]<br> **<br> ficheiros**][file-system-doc]
    :::column-end:::
    :::column:::
        [![Conector FTP ISE ][ftp-icon]<br> **FTP**][ftp-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Conector IBM 3270 ISE ][ibm-3270-icon]<br> **IBM 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Conector DB2 ISE ][ibm-db2-icon]<br> **IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Conector MQ ISE ][ibm-mq-icon]<br> **IBM MQ**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![SAP conector SAP ISE ][sap-icon]<br> ][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Conector ISE SFTP-SSH ][sftp-ssh-icon]<br> **SFTP-SSH**][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![SMTP ISE conector ][smtp-icon]<br> **SMTP SMTP**][smtp-doc]
    :::column-end:::
    :::column:::
        [![][sql-server-icon]<br>**<br> Servidor SQL** Server ISE][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Conector X12 ISE ][x12-icon]<br> **X12**][x12-doc]
    :::column-end:::
:::row-end:::

Para obter mais informações, veja estes tópicos:

* [Acesso aos recursos de rede virtual Azure a partir de Apps Azure Logic](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Modelo de preços de Aplicações Lógicas](../logic-apps/logic-apps-pricing.md)
* [Ligue-se a redes virtuais Azure a partir de Apps Azure Logic](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>Gatilhos e tipos de ação

Os conectores podem fornecer *gatilhos,* *ações,* ou ambos. Um *gatilho* é o primeiro passo em qualquer aplicação lógica, especificando normalmente o evento que dispara o gatilho e começa a executar a sua aplicação lógica. Por exemplo, o conector FTP tem um gatilho que inicia a sua aplicação lógica "quando um ficheiro é adicionado ou modificado". Alguns gatilhos verificam regularmente o evento ou dados especificados e depois disparam quando detetam o evento ou dados especificados. Outros gatilhos esperam, mas disparam instantaneamente quando um evento específico acontece ou quando novos dados estão disponíveis. Os gatilhos também transmitem todos os dados necessários para a sua aplicação lógica. A sua aplicação lógica pode ler e usar esses dados em todo o fluxo de trabalho. Por exemplo, o conector do Office 365 Outlook tem um gatilho, "Quando um novo e-mail chega", que pode passar o conteúdo desse e-mail para o fluxo de trabalho da sua aplicação lógica.

Depois de um gatilho, a Azure Logic Apps cria uma instância da sua aplicação lógica e começa a executar as *ações* no fluxo de trabalho da sua aplicação lógica. As ações são os passos que seguem o gatilho e executam tarefas no fluxo de trabalho da sua aplicação lógica. Por exemplo, pode criar uma aplicação lógica que obtém dados do cliente a partir de uma base de dados SQL e processar esses dados em ações posteriores.

Aqui estão os tipos gerais de gatilhos que a Azure Logic Apps fornece:

* *Trigger de recorrência*: Este gatilho funciona com um horário especificado e não está fortemente associado a um determinado serviço ou sistema.

* *Gatilho de sondagens*: Este gatilho sonda regularmente um serviço ou sistema específico com base no horário especificado, verificando novos dados ou se um evento específico aconteceu. Se novos dados estiverem disponíveis ou o evento específico acontecer, o gatilho cria e executa uma nova instância da sua aplicação lógica, que pode agora usar os dados que são passados como entrada.

* *Acionar o gatilho*: Este gatilho espera e ouve novos dados ou para que um evento aconteça. Quando novos dados estão disponíveis ou quando o evento acontece, o gatilho cria e executa uma nova instância da sua aplicação lógica, que pode agora usar os dados que são passados como entrada.

<a name="connections"></a>

## <a name="connector-configuration"></a>Configuração do conector

Os gatilhos e ações de cada conector fornecem as suas próprias propriedades para que possa configurar. Muitos conectores também exigem que crie primeiro uma *ligação* ao serviço ou sistema alvo e forneça credenciais de autenticação ou outros detalhes de configuração antes de poder usar um gatilho ou ação na sua aplicação lógica. Por exemplo, antes de poder aceder e trabalhar com a sua conta de e-mail office 365 Outlook, tem de autorizar uma ligação a essa conta.

Para conectores que utilizam OAuth Azure Ative Directory (Azure AD), criar uma ligação significa iniciar sessão no serviço, como o Office 365, Salesforce ou GitHub, onde o seu token de acesso é [encriptado](../security/fundamentals/encryption-overview.md) e armazenado de forma segura numa loja secreta Azure. Outros conectores, como FTP e SQL, requerem uma ligação que tenha detalhes de configuração, tais como o endereço do servidor, nome de utilizador e senha. Estes detalhes de configuração de ligação também são encriptados e armazenados de forma segura. Saiba mais sobre [encriptação em Azure.](../security/fundamentals/encryption-overview.md)

As ligações podem aceder ao serviço ou sistema alvo enquanto esse serviço ou sistema permitir. Para serviços que utilizam ligações Azure AD OAuth, como Office 365 e Dynamics, a Azure Logic Apps atualiza o acesso a tokens indefinidamente. Outros serviços podem ter limites em quanto tempo as Azure Logic Apps podem usar um token sem refrescante. Geralmente, algumas ações invalidam todos os tokens de acesso, como alterar a sua palavra-passe.

<a name="recurrence-behavior"></a>

## <a name="recurrence-behavior"></a>Comportamento de recorrência

O comportamento de gatilhos incorporados recorrentes que funcionam de forma nativa em Azure Logic Apps, como o [gatilho Recurrence,](../connectors/connectors-native-recurrence.md)difere do comportamento para gatilhos baseados em conexão recorrentes onde é necessário criar uma ligação primeiro, como o gatilho do conector SQL.

No entanto, para ambos os tipos de gatilhos, se uma recorrência não especificar uma data e hora de início específicas, a primeira recorrência corre imediatamente quando guarda ou implementa a aplicação lógica, apesar da configuração de recorrência do gatilho. Para evitar este comportamento, forneça uma data de início e hora para quando quiser que a primeira recorrência seja executada.

<a name="recurrence-built-in"></a>

### <a name="recurrence-for-built-in-triggers"></a>Recorrência para gatilhos incorporados

Os gatilhos incorporados recorrentes honram o horário que definiu, incluindo qualquer fuso horário que especifique. No entanto, se uma recorrência não especificar quaisquer outras opções avançadas de agendamento, tais como tempos específicos para executar recorrências futuras, essas recorrências são baseadas na última execução do gatilho. Como resultado, os tempos de início para essas recorrências podem derivar devido a fatores como a latência durante as chamadas de armazenamento. Além disso, se não selecionar um fuso horário, o horário de verão (DST) pode afetar quando os gatilhos funcionam, por exemplo, deslocando a hora de início uma hora para a frente quando o DST começa e uma hora para trás quando o DST termina.

Para se certificar de que a sua aplicação lógica funciona na hora de início especificada e não perde uma recorrência, especialmente quando a frequência é em dias ou mais, experimente estas soluções:

* Certifique-se de que seleciona um fuso horário para que a sua aplicação lógica seja executado na hora de início especificada. Caso contrário, o DST pode afetar quando os gatilhos funcionam, por exemplo, deslocando a hora de início uma hora para a frente quando o DST começa e uma hora para trás quando o DST termina.

  Ao agendar postos de trabalho, a Logic Apps coloca a mensagem para o processamento na fila e especifica quando essa mensagem fica disponível, com base no tempo UTC quando o último trabalho foi executado e na hora utc quando o próximo trabalho está programado para ser executado. Ao especificar um fuso horário, o tempo UTC para a sua aplicação lógica também muda para contrariar a mudança de tempo sazonal. No entanto, algumas janelas de tempo podem causar problemas quando a hora muda. Para obter mais informações e exemplos, consulte [Recorrência para horário de verão e horário normal](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time).

* Utilize o gatilho de Recorrência e forneça uma data e hora de início para a recorrência mais os horários específicos para quando executar recorrências subsequentes utilizando as propriedades denominadas **Nestas horas** e **nestas atas**, que estão disponíveis apenas para as frequências **dia** e **semana.**

* Utilize o [gatilho da janela deslizante](../connectors/connectors-native-sliding-window.md), em vez do gatilho de recorrência.

<a name="recurrence-connection-based"></a>

### <a name="recurrence-for-connection-based-triggers"></a>Recorrência para gatilhos baseados em ligação

Em gatilhos baseados em ligação recorrente, tais como SQL ou SFTP-SSH, o horário não é o único controlador que controla a execução, e o fuso horário apenas determina a hora de início inicial. As execuções subsequentes dependem do calendário de recorrência, da última execução do *gatilho, e* de outros fatores que podem causar tempos de fuga ou produzir comportamentos inesperados, por exemplo:

* Se o gatilho acede a um servidor que tem mais dados, que o gatilho tenta imediatamente obter.

* Quaisquer falhas ou retrações que o gatilho incorre.

* Latência durante as chamadas de armazenamento.

* Não mantendo o horário especificado quando o horário de verão (DST) começar e terminar.

* Outros fatores que podem afetar quando a próxima hora de execução acontece.

Para resolver ou contornar estes problemas, experimente estas soluções:

* Para garantir que o tempo de recorrência não muda quando o DST entra em vigor, ajuste manualmente a recorrência para que a sua aplicação lógica continue a funcionar no momento esperado. Caso contrário, a hora de início muda uma hora para a frente quando o DST começa e uma hora para trás quando o DST termina.

* Utilize o gatilho de Recorrência para que possa especificar um fuso horário, uma data e hora de início, *além* dos horários específicos para executar as recorrências subsequentes utilizando as propriedades denominadas **Nestas horas** e **nestes minutos**, que estão disponíveis apenas para as frequências **dia** e **semana.** No entanto, algumas janelas de tempo ainda podem causar problemas quando a hora muda. Para obter mais informações e exemplos, consulte [Recorrência para horário de verão e horário normal](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time).

* Para evitar recorrências perdidas, utilize o [gatilho da janela deslizante](../connectors/connectors-native-sliding-window.md), em vez do gatilho de Recorrência.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>APIs e conectores personalizados

Para chamar APIs que executam código personalizado ou não estão disponíveis como conectores, você pode estender a plataforma De Aplicações Lógicas [criando aplicações API personalizadas](../logic-apps/logic-apps-create-api-app.md). Também pode [criar conectores personalizados](../logic-apps/custom-connector-overview.md) para *quaisquer* APIs baseadas em REST ou SOAP, que disponibilizam essas APIs a qualquer aplicação lógica na sua subscrição Azure. Para tornar públicas as aplicações ou conectores API personalizados para qualquer pessoa utilizar no Azure, pode [submeter conectores para certificação microsoft.](/connectors/custom-connectors/submit-certification)

> [!NOTE]
> As aplicações lógicas que implementa e funciona num [ambiente de serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) podem aceder diretamente aos recursos numa rede virtual Azure. Se tiver conectores personalizados que requerem o gateway de dados no local, e criou esses conectores fora de um ISE, as aplicações lógicas num ISE também podem usar esses conectores.
>
> Os conectores personalizados criados dentro de um ISE não funcionam com o portal de dados no local. No entanto, estes conectores podem aceder diretamente a fontes de dados no local que estão ligadas a uma rede virtual Azure que hospeda o ISE. Assim, as aplicações lógicas num ISE provavelmente não precisam da porta de dados quando comunicam com esses recursos.
>
> Para obter mais informações sobre a criação de ISEs, consulte [as redes virtuais Connect to Azure da Azure Logic Apps.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="block-connections"></a>

## <a name="block-creating-connections"></a>Bloquear a criação de ligações

Se a sua organização não permitir a ligação a recursos específicos utilizando os seus conectores em Azure Logic Apps, pode [bloquear a capacidade de criar essas ligações](../logic-apps/block-connections-connectors.md) para conectores específicos em fluxos de trabalho de aplicações lógicas utilizando a [Azure Policy.](../governance/policy/overview.md) Para obter mais informações, consulte [as ligações do Bloco criadas por conectores específicos em Azure Logic Apps](../logic-apps/block-connections-connectors.md).

## <a name="get-ready-for-deployment"></a>Prepare-se para a implantação

Embora crie ligações a partir de uma aplicação lógica, as ligações são recursos Azure separados com as suas próprias definições de recursos. Para rever estas definições de recursos de conexão, [baixe a sua aplicação lógica do Azure para o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md), que é a forma mais fácil de criar um modelo de aplicação lógica parametrizada válido que esteja maioritariamente pronto para ser implantado.

## <a name="next-steps"></a>Próximos passos

* Ver a [lista completa do conector](/connectors)
* [Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Criar conectores personalizados para aplicações lógicas](/connectors/custom-connectors/)
* [Criar APIs personalizadas para aplicações lógicas](../logic-apps/logic-apps-create-api-app.md)

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-devops-icon]: ./media/apis-list/azure-devops.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[twilio-icon]: ./media/apis-list/twilio.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Ligar a origens de dados no local a partir de aplicações lógicas com gateway de dados no local"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Crie uma instância de serviço de Gestão API da Azure para gerir e publicar as suas APIs"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "Integrar aplicações lógicas com Aplicações API do Serviço de Aplicações"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrar aplicativos lógicos com Funções Azure"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Processar mensagens em grupos, ou como lotes"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Avaliar uma condição e executar diferentes ações baseadas em se a condição é verdadeira ou falsa"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Execute as mesmas ações em cada item em uma matriz"
[http-doc]: ./connectors-native-http.md "Ligue para pontos finais HTTP ou HTTPS a partir das suas aplicações lógicas"
[http-request-doc]: ./connectors-native-reqres.md "Receba pedidos HTTP nas suas apps lógicas"
[http-response-doc]: ./connectors-native-reqres.md "Responda aos pedidos HTTP das suas aplicações lógicas"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Ligue para os pontos finais do REST a partir das suas aplicações lógicas"
[http-webhook-doc]: ./connectors-native-webhook.md "Aguarde eventos específicos de pontos finais HTTP ou HTTPS"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "Adicione e execute snippets de código JavaScript das suas aplicações lógicas"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integre aplicações lógicas com fluxos de trabalho aninhados"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Selecione e filtre matrizes com a ação Consultar"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Executar aplicativos de lógica baseados em um horário"
[schedule-delay-doc]: ./connectors-native-delay.md "Atrasar a execução da próxima ação"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Atrasar a execução da próxima ação"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Executar aplicativos de lógica em um horário recorrente"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Executar aplicativos lógicos que precisam de lidar com dados em pedaços contíguos"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organize ações em grupos, que obtêm o seu próprio estatuto após as ações em grupo terminar em execução"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organize ações em casos, que são atribuídos valores únicos. Executar apenas o caso cujo valor corresponde ao resultado de uma expressão, objeto ou token. Se não existirem fósforos, executar o caso padrão"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Pare ou cancele um fluxo de trabalho ativamente em execução para a sua aplicação lógica"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Repita as ações até que a condição especificada seja verdadeira ou algum estado tenha mudado"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Realizar operações de dados como filtrar matrizes ou criar tabelas CSV e HTML"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Executar operações com variáveis, tais como inicializar, definir, incrementar, decrementar e anexar a cadeia ou matriz variável"

<!--Managed connector doc links-->
[azure-automation-doc]: /connectors/azureautomation/ "Crie e gere empregos de automação para a sua infraestrutura de nuvem e instalações"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Gerir ficheiros no seu contentor de blobs com o conector de armazenamento de blobs do Azure"
[azure-cosmos-db-doc]: /connectors/documentdb/ "Ligue-se à Azure Cosmos DB para que possa aceder a documentos e procedimentos armazenados"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Monitorize eventos publicados por uma Grade de Eventos, por exemplo, quando os recursos do Azure ou recursos de terceiros mudam"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Conecte-se aos Azure Event Hubs para que possa receber e enviar eventos entre aplicações lógicas e Centros de Eventos"
[azure-file-storage-doc]: /connectors/azurefile/ "Conecte-se à sua conta de Armazenamento Azure para que possa criar, atualizar, obter e eliminar ficheiros"
[azure-key-vault-doc]: /connectors/keyvault/ "Ligue-se ao seu Cofre de Chaves Azure para que possa gerir os seus segredos e chaves"
[azure-monitor-logs-doc]: /connectors/azuremonitorlogs/ "Executar consultas contra registos do Monitor Azure em espaços de trabalho do Log Analytics e componentes de Insights de Aplicação"
[azure-queues-doc]: /connectors/azurequeues/ "Conecte-se à sua conta de Armazenamento Azure para que possa criar e gerir filas e mensagens"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Envie mensagens de Service Bus Queues and Topics e receba mensagens de Filas e Assinaturas de Autocarros de Serviço"
[azure-sql-data-warehouse-doc]: /connectors/sqldw/ "Ligue-se ao Azure Synapse Analytics para que possa ver os seus dados"
[azure-table-storage-doc]: /connectors/azuretables/ "Conecte-se à sua conta de Armazenamento Azure para que possa criar, atualizar e consultar tabelas e muito mais"
[biztalk-server-doc]: /connectors/biztalk/ "Conecte-se ao seu Servidor BizTalk para que possa executar aplicações baseadas no BizTalk lado a lado com as Aplicações Azure Logic"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Ligue a um sistema de ficheiros no local"
[ftp-doc]: ./connectors-create-api-ftp.md "Ligue a um servidor FTP / FTPS e para tarefas de FTP, como carregar, obter, eliminar ficheiros, entre outras"
[github-doc]: ./connectors-create-api-github.md "Ligue o GitHub e controle problemas"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Conecta-se ao Calendário do Google e pode gerir calendário"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Ligue-se ao Google Sheets para que possa modificar as suas folhas"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Conecta-se às Tarefas do Google para que possa gerir as suas tarefas"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Ligue-se a 3270 aplicações em mainframes da IBM"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Ligue-se ao IBM DB2 na nuvem ou no local. Atualizar uma linha, obter uma mesa, e muito mais"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Ligue-se ao Informix na nuvem ou no local. Leia uma linha, liste as tabelas, e muito mais"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Ligue-se à IBM MQ no local ou em Azure para enviar e receber mensagens"
[instagram-doc]: ./connectors-create-api-instagram.md "Ligue-se ao Instagram. Desencadear ou agir em eventos"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Ligue ao Mandrill para comunicação"
[mysql-doc]: /connectors/mysql/ "Ligue-se à base de dados MySQL no local para que possa ler e escrever dados"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Conecte-se à sua conta de trabalho ou escola para que possa enviar e receber e-mails, gerir o seu calendário e contactos, entre outros"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Conecte-se ao seu Microsoft OneDrive pessoal para que possa carregar, eliminar, listar ficheiros e muito mais"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Conecte-se ao seu negócio Microsoft OneDrive para que possa carregar, eliminar, listar os seus ficheiros e muito mais"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Ligue-se a uma base de dados Oráculo para que possa adicionar, inserir, eliminar linhas e muito mais"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Conecte-se à sua caixa de correio do Outlook para que possa gerir o seu email, calendários, contactos e muito mais"
[postgre-sql-doc]: /connectors/postgresql/ "Ligue-se à sua base de dados PostgreSQL para que possa ler dados a partir de tabelas"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Ligue-se à sua conta Salesforce. Gerir contas, leads, oportunidades e muito mais"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Ligue a um sistema SAP no local"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Ligue-se a SendGrid. Enviar e-mail e gerir listas de destinatários"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Ligue-se à sua conta SFTP utilizando o SSH. Carregar, obter, apagar ficheiros e muito mais"
[sharepoint-server-doc]: ./connectors-create-api-sharepoint.md "Ligue-se ao servidor SharePoint no local. Gerir documentos, listar itens e muito mais"
[sharepoint-online-doc]: ./connectors-create-api-sharepoint.md "Ligue-se ao SharePoint Online. Gerir documentos, listar itens e muito mais"
[slack-doc]: ./connectors-create-api-slack.md "Ligue-se a Slack e publique mensagens nos canais Slack"
[smtp-doc]: ./connectors-create-api-smtp.md "Conecte-se a um servidor SMTP e envie e-mail com anexos"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Ligue ao SparkPost para comunicação"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Ligue-se à Base de Dados Azure SQL ou ao SQL Server. Criar, atualizar, obter e apagar entradas numa tabela de base de dados SQL"
[teradata-doc]: /connectors/teradata/ "Ligue-se à sua base de dados Teradata para ler dados a partir de tabelas"
[twilio-doc]: ./connectors-create-api-twilio.md "Ligue-se a Twilio. Enviar e receber mensagens, obter números disponíveis, gerir números de telefone de entrada, e muito mais"
[youtube-doc]: ./connectors-create-api-youtube.md "Ligue-se ao YouTube. Gerencie os seus vídeos e canais"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Codificar e descodificar mensagens que utilizam o protocolo AS2"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Codificar e descodificar mensagens que utilizam o protocolo EDIFACT"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Descodificar mensagens que utilizam o protocolo EDIFACT"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Codificar mensagens que utilizam o protocolo EDIFACT"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Saiba mais sobre a integração da empresa arquivo plano"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Saiba mais sobre a integração da empresa arquivo plano"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Gerir metadados para artefactos de conta de integração"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Transformar JSON com modelos líquidos"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Codificar e descodificar mensagens que utilizam o protocolo X12"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Descodificar mensagens que usam o protocolo X12"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Codificar mensagens que usam o protocolo X12"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Transformar mensagens XML"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Validar mensagens XML"
