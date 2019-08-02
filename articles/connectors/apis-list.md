---
title: Conectores para aplicativos lógicos do Azure
description: Automatizar fluxos de trabalho com conectores para aplicativos lógicos do Azure, incluindo conectores corporativos internos, gerenciados, locais, de integração e Enterprise
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 126c3fb348b1d53769a818ae4b21fcdbbef65615
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517242"
---
# <a name="connectors-for-azure-logic-apps"></a>Conectores para aplicativos lógicos do Azure

Os conectores proporcionam um acesso rápido a partir do Azure Logic Apps a eventos, dados e ações noutros serviços, aplicações, sistemas, protocolos e plataformas. Ao utilizar conectores nas suas aplicações lógicas, expande as capacidades das aplicações na cloud e locais para executarem tarefas com os dados que cria e já tem.

Embora os aplicativos lógicos ofereçam [centenas de conectores](https://docs.microsoft.com/connectors), este artigo descreve os conectores populares e mais comumente usados que são usados com êxito por milhares de aplicativos e milhões de execuções para processamento de dados e informações. Para localizar a lista completa de conectores e as informações de referência de cada conector, como gatilhos, ações e limites, examine as páginas de referência do conector em [conectores visão geral](https://docs.microsoft.com/connectors). Além disso, saiba mais sobre [gatilhos e ações](#triggers-actions), [modelo de preços de aplicativos lógicos](../logic-apps/logic-apps-pricing.md)e detalhes de preços de [aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/). 

> [!NOTE]
> Para integrar com um serviço ou uma API que não tem conector, você pode chamar diretamente o serviço por meio de um protocolo como HTTP ou criar um [conector personalizado](#custom).

Os conectores estão disponíveis como gatilhos e ações internas ou como conectores gerenciados:

<a name="built-in"></a>

* [**Entradas internas**](#built-ins): Esses gatilhos e ações internos são "nativos" para aplicativos lógicos do Azure e ajudam a criar aplicativos lógicos que são executados em agendas personalizadas, se comunicam com outros pontos de extremidade, recebem e respondem a solicitações e chamam o Azure functions, aplicativos de API do Azure (aplicativos Web), suas próprias APIs gerenciado e publicado com o gerenciamento de API do Azure e aplicativos lógicos aninhados que podem receber solicitações. Você também pode usar ações internas que ajudam a organizar e controlar o fluxo de trabalho do aplicativo lógico e também a trabalhar com dados.

  > [!NOTE]
  > Os aplicativos lógicos em um [ISE (ambiente do serviço de integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) podem acessar diretamente os recursos em uma rede virtual do Azure.
  > Quando você usa um ISE, gatilhos internos e ações que exibem a execução do rótulo **principal** no mesmo ISE que seus aplicativos lógicos. Os aplicativos lógicos, os gatilhos internos e as ações internas executadas em seu ISE usam um plano de preços diferente do plano de preços baseado em consumo.
  >
  > Para obter mais informações sobre como criar ISEs, consulte [conectar-se a redes virtuais do Azure de aplicativos lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). 
  > Para obter mais informações sobre preços, consulte [modelo de preços de aplicativos lógicos](../logic-apps/logic-apps-pricing.md).

<a name="managed-connectors"></a>

* **Conectores gerenciados**: Implantado e gerenciado pela Microsoft, esses conectores fornecem gatilhos e ações para acessar serviços de nuvem, sistemas locais ou ambos, incluindo o Office 365, o armazenamento de BLOBs do Azure, o SQL Server, o Dynamics, o Salesforce, o SharePoint e muito mais. Alguns conectores especificamente dão suporte a cenários de comunicação entre empresas (B2B) e exigem uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) vinculada ao seu aplicativo lógico. Antes de usar determinados conectores, talvez você precise primeiro criar conexões, que são gerenciadas pelos aplicativos lógicos do Azure. 

  Por exemplo, se você estiver usando o Microsoft BizTalk Server, seus aplicativos lógicos poderão se conectar e se comunicar com seus BizTalk Server usando o [BizTalk Server on-premises Connector](#on-premises-connectors). 
  Em seguida, você pode estender ou executar operações semelhantes ao BizTalk em seus aplicativos lógicos usando os conectores de [conta de integração](#integration-account-connectors).

  Os conectores são classificados como Standard ou Enterprise. 
  [Conectores corporativos](#enterprise-connectors) fornecem acesso a sistemas corporativos como SAP, IBM MQ e IBM 3270 por um custo adicional. Para determinar se um conector é Standard ou Enterprise, consulte a página de referência detalhes técnicos em cada conector em [conectores visão geral](https://docs.microsoft.com/connectors). 

  Você também pode identificar conectores usando essas categorias, embora alguns conectores possam cruzar várias categorias. 
  Por exemplo, o SAP é um conector corporativo e um conector local:

  |   |   |
  |---|---|
  | [**Conectores de API gerenciados**](#managed-api-connectors) | Crie aplicativos lógicos que usam serviços como o armazenamento de BLOBs do Azure, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online e muito mais. |
  | [**Conectores locais**](#on-premises-connectors) | Depois de instalar e configurar o [Gateway de dados local][gateway-doc], esses conectores ajudam seus aplicativos lógicos a acessar sistemas locais, como SQL Server, SharePoint Server, Oracle DB, compartilhamentos de arquivos e outros. |
  | [**Conectores de conta de integração**](#integration-account-connectors) | Disponível quando você cria e paga uma conta de integração, esses conectores transformam e validam XML, codificam e decodificam arquivos simples e processam mensagens B2B (entre empresas) com os protocolos AS2, EDIFACT e X12. |
  |||

  > [!NOTE]
  > Os aplicativos lógicos em um [ISE (ambiente do serviço de integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) podem acessar diretamente os recursos em uma rede virtual do Azure. Quando você usa um ISE, os conectores Standard e Enterprise que exibem a etiqueta do **ISE** executadas no mesmo ISE que seus aplicativos lógicos. Os conectores que não exibem a etiqueta do ISE são executados no serviço de aplicativos lógicos globais.
  >
  > Para sistemas locais conectados a uma rede virtual do Azure, insira o ISE nessa rede para que seus aplicativos lógicos possam acessar diretamente esses sistemas usando um conector que tenha um rótulo de **ISE** , uma ação http ou um [conector personalizado](#custom). Os aplicativos lógicos e os conectores executados em seu ISE usam um plano de preços diferente do plano de preços baseado em consumo. 
  >
  > Para obter mais informações sobre como criar ISEs, consulte [conectar-se a redes virtuais do Azure de aplicativos lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).
  > Para obter mais informações sobre preços, consulte [modelo de preços de aplicativos lógicos](../logic-apps/logic-apps-pricing.md).

  Para obter a lista completa de conectores e informações de referência de cada conector, como ações e qualquer gatilho, que são definidos por uma descrição de OpenAPI (antigamente Swagger), além de quaisquer limites, você pode encontrar a lista completa na [visão geral](/connectors/)dos conectores. Para obter informações sobre preços, consulte [modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md)e [detalhes de preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/). 

<a name="built-ins"></a>

## <a name="built-ins"></a>Entradas internas

Os aplicativos lógicos fornecem gatilhos e ações internas para que você possa criar fluxos de trabalho baseados em agendamento, ajudar seus aplicativos lógicos a se comunicar com outros aplicativos e serviços, controlar o fluxo de trabalho por meio de seus aplicativos lógicos e gerenciar ou manipular dados.

|   |   |   |   | 
|---|---|---|---| 
| [![Ícone][schedule-icon]<br/>da API**agenda**][recurrence-doc] | -Execute seu aplicativo lógico em um agendamento especificado, variando de recorrências básicas a complexas, com o gatilho de recorrência. <p>-Pause seu aplicativo lógico por uma duração especificada com a ação de **atraso** . <p>-Pause seu aplicativo lógico até a data e a hora especificadas com a ação de **atraso até** . | [![Ícone][http-icon]<br/>de API**http**][http-doc] | Comunique-se com qualquer ponto de extremidade sobre HTTP com gatilhos e ações para HTTP, HTTP + Swagger e HTTP + webhook. | 
| [![**Solicitação** de][http-request-icon]<br/>ícone de API][http-request-doc] | -Tornar seu aplicativo lógico chamável de outros aplicativos ou serviços, disparar eventos de recurso da grade de eventos ou disparar respostas para alertas da central de segurança do Azure com o gatilho de **solicitação** . <p>– Envie respostas para um aplicativo ou serviço com a ação de **resposta** . | [![Ícone][batch-icon]<br/>da API**lote**][batch-doc] | -Processar mensagens em lotes com o gatilho de **mensagens em lotes** . <p>-Chamar aplicativos lógicos que têm gatilhos de lote existentes com a ação **enviar mensagens para o lote** . | 
| [![Ícone][azure-functions-icon]<br/>de API**Azure Functions**][azure-functions-doc] | Chame o Azure Functions que executam trechosC# de código personalizados (ou node. js) de seus aplicativos lógicos. | [![Ícone][azure-api-management-icon]</br>da API**Gerenciamento de API do Azure**][azure-api-management-doc] | Chame gatilhos e ações definidas por suas próprias APIs que você gerencia e publica com o gerenciamento de API do Azure. | 
| [![Ícone][azure-app-services-icon]<br/>de API**Azure app serviços**][azure-app-services-doc] | Chamar aplicativos de API do Azure ou aplicativos Web, hospedados no serviço Azure App. Os gatilhos e as ações definidos por esses aplicativos aparecem como quaisquer outros gatilhos de primeira classe e ações quando o Swagger é incluído. | [![Ícone][azure-logic-apps-icon]<br/>da API**aplicativos lógicos do Azure<br/>** ][nested-logic-app-doc] | Chame outros aplicativos lógicos que comecem com um gatilho de solicitação. | 
||||| 

### <a name="control-workflow"></a>Fluxo de trabalho de controle

Os aplicativos lógicos fornecem ações internas para estruturar e controlar as ações no fluxo de trabalho do aplicativo lógico:

|   |   |   |   | 
|---|---|---|---| 
| [![][condition-icon]<br/>**Condição** de ícone interno][condition-doc] | Avalie uma condição e execute ações diferentes com base em se a condição é verdadeira ou falsa. | [![Ícone][for-each-icon]</br>interno**para cada**][for-each-doc] | Execute as mesmas ações em cada item em uma matriz. | 
| [![][scope-icon]<br/>**Escopo** de ícone interno][scope-doc] | Agrupe ações emescopos, que obtêm seu próprio status depois que as ações no escopo terminarem de ser executadas. | [![][switch-icon]</br>**Opção** de ícone interno][switch-doc] | Agrupe ações em *casos*, que recebem valores exclusivos, exceto o caso padrão. Execute somente esse caso cujo valor atribuído corresponda ao resultado de uma expressão, objeto ou token. Se nenhuma correspondência existir, execute o caso padrão. | 
| [![][terminate-icon]<br/>**Término** do ícone interno][terminate-doc] | Pare um fluxo de trabalho do aplicativo lógico em execução ativamente. | [![Ícone][until-icon]<br/>interno**até**][until-doc] | Repita as ações até que a condição especificada seja verdadeira ou algum Estado tenha sido alterado. | 
||||| 

### <a name="manage-or-manipulate-data"></a>Gerenciar ou manipular dados

Os aplicativos lógicos fornecem ações internas para trabalhar com saídas de dados e seus formatos:  

|   |   | 
|---|---| 
| [![**Operações de dados** de][data-operations-icon]<br/>ícone interno][data-operations-doc] | Executar operações com dados: <p>- **Compor**: Crie uma única saída de várias entradas com vários tipos. <br>- **Criar tabela CSV**: Crie uma tabela de valores separados por vírgula (CSV) de uma matriz com objetos JSON. <br>- **Criar tabela HTML**: Crie uma tabela HTML de uma matriz com objetos JSON. <br>- **Filtrar matriz**: Crie uma matriz de itens em outra matriz que atenda aos seus critérios. <br>- **Ingressar**: Crie uma cadeia de caracteres de todos os itens em uma matriz e separe os itens com o delimitador especificado. <br>- **Analisar JSON**: Crie tokens amigáveis de propriedades e seus valores no conteúdo JSON para que você possa usar essas propriedades em seu fluxo de trabalho. <br>- **Selecione**: Crie uma matriz com objetos JSON transformando itens ou valores em outra matriz e mapeando esses itens para as propriedades especificadas. | 
| ![Ícone interno][date-time-icon]<br/>**Data e hora** | Executar operações com carimbos de data/hora: <p>- **Adicionar ao horário**: Adicione o número especificado de unidades a um carimbo de data/hora. <br>- **Converter fuso horário**: Converta um carimbo de data/hora do fuso horário de origem para o fuso horário de destino. <br>- **Hora atual**: Retornar o carimbo de data/hora atual como uma cadeia de caracteres. <br>- **Obter tempo futuro**: Retornar o carimbo de data/hora atual mais as unidades de tempo especificadas. <br>- **Obter última hora**: Retorna o carimbo de data/hora atual menos as unidades de tempo especificadas. <br>- **Subtrair da hora**: Subtraia um número de unidades de tempo de um carimbo de data/hora. |
| [![][variables-icon]<br/>**Variáveis** de ícone internas][variables-doc] | Executar operações com variáveis: <p>- **Acrescentar à variável de matriz**: Insira um valor como o último item em uma matriz armazenada por uma variável. <br>- **Acrescentar à variável de cadeia de caracteres**: Insere um valor como o último caractere em uma cadeia de caracteres armazenada por uma variável. <br>- **Diminuir variável**: Diminuir uma variável por um valor constante. <br>- **Variável**de incremento: Aumentar uma variável por um valor constante. <br>- **Inicializar variável**: Crie uma variável e declare seu tipo de dados e o valor inicial. <br>- **Definir variável**: Atribua um valor diferente a uma variável existente. |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>Conectores de API gerenciados

Os aplicativos lógicos fornecem esses conectores padrão populares para automatizar tarefas, processos e fluxos de trabalho com esses serviços ou sistemas.

|   |   |   |   | 
|---|---|---|---| 
| [![Ícone][azure-service-bus-icon]<br/>da API**barramento de serviço do Azure**][azure-service-bus-doc] | Faça a gestão de mensagens assíncronas, sessões e subscrições de tópicos com o conector mais usado em Logic Apps. | [![Ícone][sql-server-icon]<br/>de API**SQL Server**][sql-server-doc] | Conecte-se ao seu SQL Server local ou a um banco de dados SQL do Azure na nuvem para que você possa gerenciar registros, executar procedimentos armazenados ou executar consultas. | 
| [![Ícone][office-365-outlook-icon]<br/>da API**Office<br/>365 Outlook**][office-365-outlook-doc] | Conecte-se à sua conta de email do Office 365 para que você possa criar e gerenciar emails, tarefas, eventos de calendário e reuniões, contatos, solicitações e muito mais. | [![Ícone][azure-blob-storage-icon]<br/>da API**armazenamento<br/>de BLOBs do Azure**][azure-blob-storage-doc] | Conecte-se à sua conta de armazenamento para que você possa criar e gerenciar conteúdo de BLOB. | 
| [![Ícone][sftp-icon]<br/>de API**SFTP**][sftp-doc] | Ligue-se a servidores SFTP aos quais possa aceder a partir da Internet para poder trabalhar com seus ficheiros e pastas. | [![Ícone][sharepoint-online-icon]<br/>da API**SharePoint<br/>online**][sharepoint-online-doc] | Conecte-se ao SharePoint Online para que você possa gerenciar arquivos, anexos, pastas e muito mais. | 
| [![Ícone][dynamics-365-icon]<br/>da API**Dynamics<br/>365 CRM Online**][dynamics-365-doc] | Conecte-se à sua conta do Dynamics 365 para que você possa criar e gerenciar registros, itens e muito mais. | [![Ícone][ftp-icon]<br/>da API**FTP**][ftp-doc] | Conecte-se a servidores FTP que você pode acessar pela Internet para que você possa trabalhar com seus arquivos e pastas. | 
| [![Ícone][salesforce-icon]<br/>da API**Salesforce**][salesforce-doc] | Conecte-se à sua conta do Salesforce para que você possa criar e gerenciar itens como registros, trabalhos, objetos e muito mais. | [![Ícone][twitter-icon]<br/>da API**Twitter**][twitter-doc] | Conecte-se à sua conta do Twitter para que você possa gerenciar tweets, seguidores, sua linha do tempo e muito mais. Salve seus tweets em SQL, Excel ou SharePoint. | 
| [![Ícone][azure-event-hubs-icon]<br/>da API**hubs de eventos do Azure**][azure-event-hubs-doc] | Consumir e publicar eventos por meio de um hub de eventos. Por exemplo, obtenha a saída de seu aplicativo lógico com hubs de eventos e, em seguida, envie essa saída para um provedor de análise em tempo real. | [![Ícone][azure-event-grid-icon]<br/>da API**grade** de</br>**eventos do Azure**][azure-event-grid-doc] | Monitore eventos publicados por uma grade de eventos, por exemplo, quando os recursos do Azure ou os recursos de terceiros mudam. | 
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Conectores no local 

Aqui estão alguns conectores padrão comumente usados que os aplicativos lógicos fornecem para acessar dados e recursos em sistemas locais. Antes de poder criar uma conexão com um sistema local, você deve primeiro [baixar, instalar e configurar um gateway de dados local][gateway-doc]. Esse Gateway fornece um canal de comunicação seguro sem a necessidade de configurar a infraestrutura de rede necessária. 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![Ícone de API][biztalk-server-icon]<br/>**BizTalk**</br> **servidor** | [![Ícone][file-system-icon]<br/>da API**sistema de arquivos</br>** ][file-system-doc] | [![Ícone][ibm-db2-icon]<br/>da API**IBM DB2**][ibm-db2-doc] | [![Ícone][ibm-informix-icon]<br/>da API**IBM** </br> **Informix**][ibm-informix-doc] | ![Ícone de API][mysql-icon]<br/>**MySQL** | 
| [![Ícone][oracle-db-icon]<br/>de API**Oracle DB**][oracle-db-doc] | ![Ícone de API][postgre-sql-icon]<br/>**PostgreSQL** | [![Ícone][sharepoint-server-icon]<br/>da API**SharePoint</br> Server**][sharepoint-server-doc] | [![Ícone][sql-server-icon]<br/>da API**SQL</br> Server**][sql-server-doc] | ![Ícone de API][teradata-icon]<br/>**Teradata** | 
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Conectores de conta de integração

Os aplicativos lógicos fornecem conectores padrão para a criação de Soluções B2B (entre empresas) com seus aplicativos lógicos quando você cria e paga por uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), que está disponível por meio do Enterprise Integration Pack (EIP) no Azure. Com essa conta, você pode criar e armazenar artefatos B2B, como parceiros comerciais, contratos, mapas, esquemas, certificados e assim por diante. Para usar esses artefatos, associe seus aplicativos lógicos à sua conta de integração. Se você usa BizTalk Server no momento, esses conectores podem parecer já conhecidos.

|   |   |   |   | 
|---|---|---|---| 
| [![Ícone][as2-icon]<br/>da API decodificação de**AS2</br>** ][as2-doc] | [![Ícone][as2-icon]<br/>da API**codificação AS2</br>** ][as2-doc] | [![Ícone][edifact-icon]<br/>da API decodificação de**EDIFACT</br>** ][edifact-decode-doc] | [![Ícone][edifact-icon]<br/>da API**codificação EDIFACT</br>** ][edifact-encode-doc] | 
| [![Ícone][flat-file-decode-icon]<br/>da API decodificação de**arquivo</br> simples**][flat-file-decode-doc] | [![Ícone][flat-file-encode-icon]<br/>da API**codificação</br> de arquivo simples**][flat-file-encode-doc] | [![Ícone][integration-account-icon]<br/>da API**conta de integração<br/>** ][integration-account-doc] | [![Ícone][liquid-icon]<br/>da API transformações do**Liquid**</br>][json-liquid-transform-doc] | 
| [![Ícone][x12-icon]<br/>da API decodificação de**X12</br>** ][x12-decode-doc] | [![Ícone][x12-icon]<br/>da API**codificação X12</br>** ][x12-encode-doc] | [![Ícone][xml-transform-icon]<br/>da API transformações**XML**</br>][xml-transform-doc] | [![Ícone][xml-validate-icon]<br/>da API**validação de XML <br/>** ][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Conectores empresariais

Os aplicativos lógicos fornecem esses conectores empresariais para acessar sistemas empresariais, como SAP e IBM MQ:

|   |   |   | 
|---|---|---| 
| [![Ícone][ibm-3270-icon]<br/>da API**IBM 3270**][ibm-3270-doc] | [![Ícone][ibm-mq-icon]<br/>da API**IBM MQ**][ibm-mq-doc] | [![Ícone][sap-icon]<br/>da API**SAP**][sap-connector-doc] |
|||| 

<a name="triggers-actions"></a>

## <a name="triggers-and-actions---more-info"></a>Gatilhos e ações-mais informações

Os conectorespodem fornecer gatilhos, *ações*ou ambos. Um *gatilho* é a primeira etapa em qualquer aplicativo lógico, geralmente especificando o evento que dispara o gatilho e começa a executar seu aplicativo lógico. Por exemplo, o conector de FTP tem um gatilho que inicia seu aplicativo lógico "quando um arquivo é adicionado ou modificado". Alguns gatilhos verificam regularmente o evento ou os dados especificados e, em seguida, são acionados quando detectam o evento ou os dados especificados. Outros gatilhos esperam, mas são imediatamente acionados quando um evento específico ocorre ou quando novos dados estão disponíveis. Os gatilhos também passam todos os dados necessários para seu aplicativo lógico. Seu aplicativo lógico pode ler e usar esses dados em todo o fluxo de trabalho.
Por exemplo, o conector do Twitter tem um gatilho "quando um novo tweet é Postado", que passa o conteúdo do tweet para o fluxo de trabalho do seu aplicativo lógico. 

Depois que um gatilho é disparado, o aplicativo lógico do Azure cria uma instância de sua aplicação lógica e começa a executar as *ações* no fluxo de trabalho do seu aplicativo lógico. As ações são as etapas que seguem o gatilho e executam tarefas no fluxo de trabalho do aplicativo lógico. Por exemplo, você pode criar um aplicativo lógico que obtém dados do cliente de um banco de dado SQL e processa esses dados em ações posteriores. 

Aqui estão os tipos gerais de gatilhos que o aplicativo lógico do Azure fornece:

* *Gatilho*de recorrência: Esse gatilho é executado em um agendamento especificado e não está rigidamente associado a um serviço ou sistema específico.

* *Gatilho*de sondagem: Esse gatilho sonda regularmente um serviço ou sistema específico com base no agendamento especificado, verificando se há novos dados ou se ocorreu um evento específico. Se novos dados estiverem disponíveis ou o evento específico tiver ocorrido, o gatilho criará e executará uma nova instância do seu aplicativo lógico, que agora pode usar os dados passados como entrada.

* *Gatilho de push*: Esse gatilho espera e escuta novos dados ou para que um evento aconteça. Quando novos dados estão disponíveis ou quando o evento ocorre, o gatilho cria e executa uma nova instância do seu aplicativo lógico, que agora pode usar os dados passados como entrada.

<a name="custom"></a>

## <a name="connector-configuration"></a>Configuração do conector

Os gatilhos e as ações de cada conector fornecem suas próprias propriedades para você configurar. Muitos conectores também exigem que você primeiro crie uma *conexão* com o serviço ou sistema de destino e forneça credenciais de autenticação ou outros detalhes de configuração para poder usar um gatilho ou uma ação em seu aplicativo lógico. Por exemplo, você deve autorizar uma conexão com uma conta do Twitter para acessar dados ou postar em seu nome. 

Para conectores que usam o OAuth, criar uma conexão significa entrar no serviço, como o Office 365, Salesforce ou GitHub, em que o token de acesso é criptografado e armazenado com segurança em um repositório de segredo do Azure. Outros conectores, como FTP e SQL, exigem uma conexão com detalhes de configuração, como o endereço do servidor, o nome de usuário e a senha. Esses detalhes de configuração de conexão também são criptografados e armazenados com segurança. 

As conexões podem acessar o serviço ou sistema de destino pelo tempo que o serviço ou o sistema permitir. Para serviços que usam conexões OAuth do Azure Active Directory (AD), como o Office 365 e Dynamics, os aplicativos lógicos do Azure atualizam os tokens de acesso indefinidamente. Outros serviços podem ter limites de quanto tempo os aplicativos lógicos do Azure podem usar um token sem Atualizar. Em geral, algumas ações invalidam todos os tokens de acesso, como alterar sua senha.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>APIs e conectores personalizados

Para chamar APIs que executam código personalizado ou que não estão disponíveis como conectores, você pode estender a plataforma de aplicativos lógicos [criando aplicativos de API personalizados](../logic-apps/logic-apps-create-api-app.md). Você também pode [criar conectores personalizados](../logic-apps/custom-connector-overview.md) para *qualquer* API com base em REST ou SOAP, o que torna essas APIs disponíveis para qualquer aplicativo lógico em sua assinatura do Azure.
Para tornar os aplicativos de API personalizados ou conectores públicos para qualquer pessoa usar no Azure, você pode [Enviar conectores para certificação da Microsoft](../logic-apps/custom-connector-submit-certification.md).

> [!NOTE]
> Os aplicativos lógicos em um [ISE (ambiente do serviço de integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) podem acessar diretamente os recursos em uma rede virtual do Azure.
> Se você tiver conectores personalizados que exigem o gateway de dados local e tiver criado esses conectores fora de um ISE, os aplicativos lógicos em um ISE também poderão usar esses conectores.
>
> Os conectores personalizados criados em um ISE não funcionam com o gateway de dados local. No entanto, esses conectores podem acessar diretamente fontes de dados locais que estão conectadas a uma rede virtual do Azure que hospeda o ISE. Portanto, os aplicativos lógicos em um ISE provavelmente não precisam do gateway de dados ao se comunicar com esses recursos.
>
> Para obter mais informações sobre como criar ISEs, consulte [conectar-se a redes virtuais do Azure de aplicativos lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

## <a name="next-steps"></a>Passos Seguintes

* Localizar a [lista completa](https://docs.microsoft.com/connectors) dos conectores
* [Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Criar conectores personalizados para aplicativos lógicos](https://docs.microsoft.com/connectors/custom-connectors/)
* [Criar APIs personalizadas para aplicações lógicas](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Ligar a origens de dados no local a partir de aplicações lógicas com gateway de dados no local"

<!--Built-ins doc links-->
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrar aplicações lógicas com Funções do Azure"
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Criar uma instância do serviço de gerenciamento de API do Azure para gerenciar e publicar suas APIs"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integrar aplicações lógicas com Aplicações API do Serviço de Aplicações"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Envie mensagens das Filas e dos Tópicos do Service Bus e receba mensagens das Filas e das Subscrições do Service Bus"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Processar mensagens em grupos ou como lotes"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Avaliar uma condição e executar ações diferentes com base em se a condição é verdadeira ou falsa"
[delay-doc]: ./connectors-native-delay.md "Efetue ações atrasadas"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Executar as mesmas ações em cada item de uma matriz"
[http-doc]: ./connectors-native-http.md "Faça chamadas HTTP com o conector HTTP"
[http-request-doc]: ./connectors-native-reqres.md "Adicione ações para pedidos HTTP e respostas para as suas aplicações lógicas"
[http-response-doc]: ./connectors-native-reqres.md "Adicione ações para pedidos HTTP e respostas para as suas aplicações lógicas"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Faça chamadas HTTP com o conector HTTP + Swagger."
[http-webook-doc]: ./connectors-native-webhook.md "Adicionar ações e gatilhos de webhook HTTP a seus aplicativos lógicos"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integre aplicações lógicas com fluxos de trabalho aninhados"
[query-doc]: ./connectors-native-query.md "Selecione e filtre matrizes com a ação Consultar"
[recurrence-doc]:  ./connectors-native-recurrence.md "Acione ações periódicas para aplicações lógicas"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organizar ações em grupos, que obtêm seu próprio status depois que as ações na conclusão do grupo são executadas" 
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organize ações em casos, que recebem valores exclusivos. Execute somente o caso cujo valor corresponde ao resultado de uma expressão, objeto ou token. Se não houver correspondências, execute o caso padrão"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Parar ou cancelar um fluxo de trabalho em execução ativamente para seu aplicativo lógico"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Repetir ações até que a condição especificada seja verdadeira ou algum estado seja alterado"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Executar operações de dados, como a filtragem de matrizes ou a criação de tabelas CSV e HTML"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Executar operações com variáveis, como inicializar, definir, incrementar, decrementar e acrescentar à cadeia de caracteres ou variável de matriz"

<!--Managed API doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Gerir ficheiros no seu contentor de blobs com o conector de armazenamento de blobs do Azure"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Monitorar eventos publicados por uma grade de eventos, por exemplo, quando os recursos do Azure ou os recursos de terceiros mudam"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Ligue aos Hubs de Eventos do Azure. Receba e envie eventos entre aplicações lógicas e os Hubs de Eventos"
[box-doc]: ./connectors-create-api-box.md "Ligue à Caixa. Carregue, obtenha, elimine, liste os seus ficheiros e muito mais"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Ligue à Dropbox. Carregue, obtenha, elimine, liste os seus ficheiros e muito mais"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Ligue ao Dynamics CRM Online para que possa trabalhar com os seus dados do CRM Online"
[facebook-doc]: ./connectors-create-api-facebook.md "Ligue ao Facebook. Publique numa linha cronológica, obtenha um feed de página, entre outras coisas"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Ligue a um sistema de ficheiros no local"
[ftp-doc]: ./connectors-create-api-ftp.md "Ligue a um servidor FTP / FTPS e para tarefas de FTP, como carregar, obter, eliminar ficheiros, entre outras"
[github-doc]: ./connectors-create-api-github.md "Ligue o GitHub e controle problemas"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Liga ao Calendário Google e pode gerir o calendário."
[google-drive-doc]: ./connectors-create-api-googledrive.md "Ligue ao GoogleDrive para que possa trabalhar com os seus dados"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Conectar-se a planilhas do Google para que você possa modificar suas planilhas"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Ligue o Google Tarefas para que possa gerir as suas tarefas"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Conecte-se a aplicativos 3270 em mainframes IBM"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Ligue ao IBM DB2 na cloud ou no local. Atualize uma linha, obtenha uma tabela e muito mais"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Ligue o Informix na cloud ou no local. Leia uma linha, liste as tabelas e muito mais"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Conectar-se ao IBM MQ local ou no Azure para enviar e receber mensagens"
[instagram-doc]: ./connectors-create-api-instagram.md "Ligue ao Instagram. Acione ou interaja em eventos"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Ligue à sua conta do MailChimp. Gira e automatize e-mails"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Ligue ao Mandrill para comunicação"
[microsoft-translator-doc]: ./connectors-create-api-microsofttranslator.md "Ligue-se ao Microsoft Translator. Traduza texto, detete idiomas e muito mais" 
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Ligue à sua conta do Office 365. Envie e receba e-mails, gira o seu calendário e contactos, e muito mais"
[office-365-users-doc]: ./connectors-create-api-office365-users.md 
[office-365-video-doc]: ./connectors-create-api-office365-video.md "Obtenha informações de vídeo, listas de vídeos e canais, e URLs de reprodução para vídeos do Office 365"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Ligue ao seu Microsoft OneDrive pessoal. Carregue, elimine, liste os ficheiros e muito mais"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Ligue ao seu Microsoft OneDrive empresarial. Carregue, elimine, liste os seus ficheiros e muito mais"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Ligue a uma base de dados Oracle para adicionar, inserir, eliminar linhas e mais"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Ligue à sua caixa de correio do Outlook. Gira o seu e-mail, calendários, contactos e muito mais"
[project-online-doc]: ./connectors-create-api-projectonline.md "Ligue-se ao Microsoft Project Online. Gira os seus projetos, tarefas, recursos e muito mais"
[rss-doc]: ./connectors-create-api-rss.md "Publique e obtenha itens de feeds, acione operações quando um novo item é publicado num feed RSS."
[salesforce-doc]: ./connectors-create-api-salesforce.md "Ligue à sua conta do Salesforce. Gira contas, responsáveis, oportunidades e muito mais"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Ligue a um sistema SAP no local"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Ligue ao SendGrid. Enviar email e gerenciar listas de destinatários"
[sftp-doc]: ./connectors-create-api-sftp.md "Ligue à sua conta do SFTP. Carregue, obtenha, elimine ficheiros e muito mais"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Ligue ao servidor do SharePoint no local. Gira documentos, liste itens e muito mais"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Ligue ao SharePoint Online. Gira documentos, liste itens e muito mais"
[slack-doc]: ./connectors-create-api-slack.md "Ligue ao Slack e publica mensagens nos canais do Slack"
[smtp-doc]: ./connectors-create-api-smtp.md "Conectar-se a um servidor SMTP e enviar emails com anexos"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Ligue ao SparkPost para comunicação"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Conecte-se ao banco de dados SQL do Azure ou SQL Server. Crie, atualize, obtenha e elimine entradas numa tabela da Base de Dados SQL."
[trello-doc]: ./connectors-create-api-trello.md "Ligue ao Trello. Gira os seus projetos e organize tudo com qualquer pessoa"
[twilio-doc]: ./connectors-create-api-twilio.md "Ligue ao Twilio. Envie e receba mensagens, obtenha números disponíveis, gira números de telefone de chamadas recebidas, entre outras ações"
[twitter-doc]: ./connectors-create-api-twitter.md "Ligue ao Twitter. Obtenha linhas cronológicas, publique tweets e muito mais"
[wunderlist-doc]: ./connectors-create-api-wunderlist.md "Ligue ao Wunderlist. Gira tarefas e listas de itens pendentes, mantenha a sua vida em sincronização e muito mais"
[yammer-doc]: ./connectors-create-api-yammer.md "Ligue ao Yammer. Publique mensagens, obtenha novas mensagens e muito mais"
[youtube-doc]: ./connectors-create-api-youtube.md "Ligue ao YouTube. Gira os seus vídeos e canais"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Saiba mais sobre a integração empresarial AS2."
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Saiba mais sobre a integração empresarial de descodificação EDIFACT."
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Saiba mais sobre a integração empresarial de codificação EDIFACT."
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Saiba mais sobre a integração empresarial de ficheiros simples."
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Saiba mais sobre a integração empresarial de ficheiros simples."
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Procure esquemas, mapas, parceiros e mais na sua conta de integração"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Saiba mais sobre transformações de JSON com Liquid"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Saiba mais sobre a integração empresarial X12."
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Saiba mais sobre a integração empresarial de descodificação X12."
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Saiba mais sobre a integração empresarial de codificação X12."
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Saiba mais sobre a integração empresarial de transformações."
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Saiba mais sobre a integração empresarial de validação XML."

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
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed API icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[box-icon]: ./media/apis-list/box.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dropbox-icon]: ./media/apis-list/dropbox.png
[dynamics-365-icon]: ./media/apis-list/dynamics-crm-online.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[facebook-icon]: ./media/apis-list/facebook.png
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
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[microsoft-translator-icon]: ./media/apis-list/microsoft-translator.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[office-365-video-icon]: ./media/apis-list/office-365-video.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[rss-icon]: ./media/apis-list/rss.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[wunderlist-icon]: ./media/apis-list/wunderlist.png
[yammer-icon]: ./media/apis-list/yammer.png
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
