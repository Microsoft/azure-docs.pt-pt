---
title: Conectores para o Azure Logic Apps
description: Automatizar fluxos de trabalho com conectores para o Azure Logic Apps, incluindo incorporado, gerido, no local, a conta de integração e conectores empresariais
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: c02361cf69b98da61a0f551ac037e6d35ea42efc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65551875"
---
# <a name="connectors-for-azure-logic-apps"></a>Conectores para o Azure Logic Apps

Conectores fornecem acesso rápido do Azure Logic Apps para eventos, dados e ações em outras aplicações, serviços, sistemas, protocolos e plataformas. Ao utilizar conectores nas suas aplicações lógicas, expanda as capacidades para as suas aplicações na cloud e no local executar tarefas com os dados que criar e já tiver.

Embora as ofertas de aplicações lógicas [~ mais de 200 conectores](https://docs.microsoft.com/connectors), este artigo descreve os conectores populares e mais usados, que são utilizados com êxito por milhares de aplicações e milhões de execuções para processar dados e informações. Para encontrar a lista completa de conectores e informações de referência de cada conector, como disparadores, ações e limites, reveja as páginas de referência do conector sob [descrição geral dos conectores](https://docs.microsoft.com/connectors). Além disso, saiba mais sobre [acionadores e ações](#triggers-actions), [modelo de preços do Logic Apps](../logic-apps/logic-apps-pricing.md), e [detalhes dos preços do Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). 

> [!NOTE]
> Para integrar com um serviço ou a API que não tenha o conector, pode diretamente chamar o serviço através de um protocolo, como HTTP ou criar um [conector personalizado](#custom).

Conectores estão disponíveis como ações e acionadores incorporados ou conectores geridos:

* [**Built-ins**](#built-ins): Estes acionadores internos e as ações são "nativas" para o Azure Logic Apps e ajudam a criar aplicações lógicas que execute em agendas personalizadas, comunicam com outros pontos de extremidade, receberem e respondem a pedidos e chamam as funções do Azure, Azure API Apps (aplicações Web), as suas próprias APIs gerido e publicadas com gestão de API do Azure e aplicações lógicas aninhadas que podem receber pedidos. Também pode utilizar incorporados ações que o ajudam a organizar e controlar o fluxo de trabalho da sua aplicação lógica e também funcionam com dados.

  > [!NOTE]
  > Aplicações lógicas dentro de um [o ambiente de serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) pode acessar diretamente os recursos numa rede virtual do Azure.
  > Quando utilizar um ISE, incorporados acionadores e ações que apresentam os **Core** etiqueta executar no ISE do mesmo, como as aplicações lógicas. Aplicações lógicas, incorporados acionadores e ações incorporadas que executam no seu uso ISE um plano de preços diferente do plano de preços baseado no consumo.
  >
  > Para obter mais informações sobre a criação de ISEs, consulte [ligar a redes virtuais do Azure a partir do Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#create-logic-apps-environment). 
  > Para obter mais informações sobre preços, consulte [modelo de preços do Logic Apps](../logic-apps/logic-apps-pricing.md).

* **Conectores geridos**: Implementados e geridos pela Microsoft, estes conectores fornecem acionadores e ações para aceder aos serviços cloud, sistemas no local ou ambos, incluindo o Office 365, o armazenamento de Blobs do Azure, SQL Server, Dynamics, Salesforce, SharePoint e muito mais. Alguns conectores especificamente a oferecer suporte a cenários de comunicação empresa-empresa (B2B) e exigir uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) associado à sua aplicação lógica. Antes de utilizar determinados conectores, poderá ter de criar primeiro conexões, o que são geridos pelo Azure Logic Apps. 

  Por exemplo, se estiver a utilizar o Microsoft BizTalk Server, as logic apps podem ligar a e comunicar com o BizTalk Server utilizando o [BizTalk Server connector no local](#on-premises-connectors). 
  Em seguida, pode estender ou efetuar operações semelhantes ao BizTalk nas suas aplicações lógicas utilizando o [conectores de conta de integração](#integration-account-connectors).

  Conectores estão classificados como Standard ou Enterprise. 
  [Conectores empresariais](#enterprise-connectors) fornecer acesso a sistemas empresariais como SAP, IBM MQ e IBM 3270 por um custo adicional. Para determinar se um conector é Standard ou Enterprise, veja os detalhes técnicos na página de referência de cada conector sob [descrição geral dos conectores](https://docs.microsoft.com/connectors). 

  Também pode identificar conectores ao utilizar estas categorias, embora alguns conectores podem cruzar várias categorias. 
  Por exemplo, SAP é um conector empresarial e um conector no local:

  |   |   |
  |---|---|
  | [**Conectores de API geridos**](#managed-api-connectors) | Crie aplicações lógicas que utilizam serviços como o armazenamento de Blobs do Azure, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online e muito mais. |
  | [**Conectores no local**](#on-premises-connectors) | Depois de instalar e configurar o [gateway de dados no local][gateway-doc], estes ajuda de conectores o acesso de aplicações lógicas sistemas no local, como o SQL Server, SharePoint Server, Oracle DB, partilhas de ficheiros e outros. |
  | [**Conectores de conta de integração**](#integration-account-connectors) | Disponível quando cria e pague uma conta de integração, estes conectores de transformação e validar XML, codificar e descodificar ficheiros simples e processar empresa-empresa (B2B) mensagens com AS2, EDIFACT e X12 protocolos. |
  |||

  > [!NOTE]
  > Aplicações lógicas dentro de um [o ambiente de serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) pode acessar diretamente os recursos numa rede virtual do Azure. Quando utilizar um ISE, conectores Standard e Enterprise que apresentam os **ISE** etiqueta executar no ISE do mesmo, como as aplicações lógicas. Conectores que não apresentam a etiqueta ISE executada no serviço de aplicações lógicas global.
  >
  > Para sistemas no local que estão ligados a uma rede virtual do Azure, injetar seu ISE nessa rede, para as logic apps podem acessar diretamente desses sistemas, utilizando qualquer um dos conectores que tem um **ISE** etiqueta, uma ação de HTTP ou um [conector personalizado](#custom). Logic apps e conectores que são executados na sua utilização do ISE um preço planear diferente do plano de preços baseado no consumo. 
  >
  > Para obter mais informações sobre a criação de ISEs, consulte [ligar a redes virtuais do Azure a partir do Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#create-logic-apps-environment).
  > Para obter mais informações sobre preços, consulte [modelo de preços do Logic Apps](../logic-apps/logic-apps-pricing.md).

  Para obter a lista completa de conectores e informações de referência de cada conector, por exemplo, ações e acionadores, que é definido por um OpenAPI (anteriormente Swagger) descrição, além de nenhum limite, pode encontrar a lista completa sob o [descrição geral dos conectores ](/connectors/). Para obter informações sobre preços, consulte [modelo de preços do Logic Apps](../logic-apps/logic-apps-pricing.md), e [os detalhes dos preços do Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). 

<a name="built-ins"></a>

## <a name="built-ins"></a>Built-ins

O Logic Apps fornece acionadores incorporados e ações para que possa criar fluxos de trabalho baseados no agendamento, ajudam as suas aplicações lógicas comunicar com outras aplicações e serviços, controle o fluxo de trabalho por meio de suas logic apps e gerir ou manipular os dados.

|   |   |   |   | 
|---|---|---|---| 
| [![Ícone de API][schedule-icon]<br/>**agenda**][recurrence-doc] | -Executar a aplicação lógica numa agenda especificada, que vão desde a básica recorrências complexas, com o **periodicidade** acionador. <p>-Colocar em pausa a aplicação lógica para uma duração especificada com o **atraso** ação. <p>-Colocar em pausa a aplicação lógica até à data especificada e a hora com o **atraso até** ação. | [![Ícone de API][http-icon]<br/>**HTTP**][http-doc] | Comunicar com qualquer ponto final através de HTTP com acionadores e ações para HTTP, HTTP + Swagger e o HTTP + Webhook. | 
| [![Ícone de API][http-request-icon]<br/>**do pedido**][http-request-doc] | -Tornar a aplicação lógica pode ser chamada a partir de outras aplicações ou serviços, acionar em eventos de recursos do Event Grid ou acionador sobre respostas de alertas do Centro de segurança do Azure com o **pedir** acionador. <p>-Enviar respostas a uma aplicação ou serviço com o **resposta** ação. | [![Ícone de API][batch-icon]<br/>**Batch**][batch-doc] | -Processar mensagens em lotes com o **mensagens de lote** acionador. <p>-Aplicações lógicas chamada que tenham existente do batch acionadores com o **enviar mensagens para o lote** ação. | 
| [![Ícone de API][azure-functions-icon]<br/>**as funções do Azure**][azure-functions-doc] | Chame as funções do Azure que executam fragmentos de código personalizado (c# ou node. js) das suas aplicações lógicas. | [![Ícone de API][azure-api-management-icon]</br>**API Management do Azure**][azure-api-management-doc] | Chame acionadores e ações definidos pela sua própria que gerir e publicar APIs com a API Management do Azure. | 
| [![Ícone de API][azure-app-services-icon]<br/>**serviços aplicacionais do Azure**][azure-app-services-doc] | Chame aplicações API do Azure ou aplicações Web, alojado no serviço de aplicações do Azure. Os acionadores e ações definidas por essas aplicações aparecem como qualquer gatilhos de primeira classe e ações quando Swagger está incluído. | [![Ícone de API][azure-logic-apps-icon]<br/>**Azure<br/>Logic Apps**][nested-logic-app-doc] | Chame a outras aplicações lógicas que começam com um acionador de pedido. | 
||||| 

### <a name="control-workflow"></a>Fluxo de trabalho de controle

O Logic Apps fornece ações incorporadas para estruturar e controlar as ações no fluxo de trabalho da sua aplicação lógica:

|   |   |   |   | 
|---|---|---|---| 
| [![Ícone interno][condition-icon]<br/>**condição**][condition-doc] | Avaliar uma condição e executadas ações diferentes, consoante a condição for true ou false. | [![Ícone interno][for-each-icon]</br>**para cada**][for-each-doc] | Execute as mesmas ações em cada item numa matriz. | 
| [![Ícone interno][scope-icon]<br/>**âmbito**][scope-doc] | Grupo de ações em *âmbitos*, que obter o seu próprio Estado depois das ações no âmbito termine a execução. | [![Ícone interno][switch-icon]</br>**comutador**][switch-doc] | Grupo de ações em *casos*, que é atribuído valores exclusivos, exceto caso padrão. Execute apenas esse caso cujo valor atribuído faz corresponder o resultado de uma expressão, o objeto ou o token. Não se existir nenhuma correspondência, execute o caso de predefinição. | 
| [![Ícone interno][terminate-icon]<br/>**terminar**][terminate-doc] | Pare um fluxo de trabalho de aplicação de lógica ativamente em execução. | [![Ícone interno][until-icon]<br/>**até**][until-doc] | Repetir ações até que a condição especificada for verdadeira ou algum Estado foi alterado. | 
||||| 

### <a name="manage-or-manipulate-data"></a>Gerir ou manipular os dados

O Logic Apps fornece ações incorporadas para trabalhar com dados saídas e seus formatos:  

|   |   | 
|---|---| 
| [![Ícone interno][data-operations-icon]<br/>**operações de dados**][data-operations-doc] | Execute operações com dados: <p>- **Compor**: Crie uma única saída a partir de várias entradas com vários tipos. <br>- **Criar tabela CSV**: Crie uma tabela do separados por vírgulas (CSV) de uma matriz com objetos JSON. <br>- **Criar tabela HTML**: Crie uma tabela HTML a partir de uma matriz com objetos JSON. <br>- **Matriz de filtro**: Crie uma matriz de itens na outra matriz que satisfazem os critérios. <br>- **Junte-se a**: Criar uma cadeia de caracteres a partir de todos os itens numa matriz e separar esses itens com o delimitador especificado. <br>- **Analisar JSON**: Crie tokens amigável de utilizador de propriedades e os respetivos valores no conteúdo JSON, pelo que pode utilizar essas propriedades no fluxo de trabalho. <br>- **Selecione**: Crie uma matriz com objetos JSON ao transformar itens ou valores na outra matriz e mapear esses itens para propriedades especificadas. | 
| ![Ícone interno][date-time-icon]<br/>**Data hora** | Realizar operações de carimbos de data: <p>- **Adicionar ao tempo**: Adicione o número de unidades especificado a um carimbo. <br>- **Converter fuso horário**: Converta um carimbo de fuso horário de origem para o fuso horário de destino. <br>- **Hora atual**: Devolva o timestamp atual como uma cadeia de caracteres. <br>- **Obter tempo futuro**: Devolva o timestamp atual, bem como as unidades de tempo especificado. <br>- **Obter tempo passado**: Devolva o carimbo de hora atual menos as unidades de tempo especificado. <br>- **Subtrair a partir da hora**: Subtrai um número de unidades de tempo de um carimbo. |
| [![Ícone interno][variables-icon]<br/>**variáveis**][variables-doc] | Execute operações com variáveis: <p>- **Acrescentar à variável de matriz**: Inserir um valor como o último item numa matriz armazenado por uma variável. <br>- **Acrescentar à variável da cadeia de caracteres**: Inserir um valor como o último caráter numa cadeia armazenado por uma variável. <br>- **Variável de diminuição**: Diminua a uma variável um valor constante. <br>- **Variável de incremento**: Aumente a uma variável um valor constante. <br>- **Inicializar variável**: Crie uma variável e declarar o tipo de dados e o valor inicial. <br>- **Definir variável**: Atribua um valor diferente para uma variável existente. |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>Conectores de API geridos

O Logic Apps fornece estes conectores padrão populares para automatizar tarefas, processos e fluxos de trabalho com esses serviços ou sistemas.

|   |   |   |   | 
|---|---|---|---| 
| [![Ícone de API][azure-service-bus-icon]<br/>**Azure Service Bus**][azure-service-bus-doc] | Gerir mensagens assíncronas, sessões e subscrições de tópicos com o conector mais comumente usada no Logic Apps. | [![Ícone de API][sql-server-icon]<br/>**do SQL Server**][sql-server-doc] | Ligar ao seu SQL Server no local ou uma base de dados do SQL do Azure na cloud para que possa gerir os registos, executar procedimentos armazenados ou executar consultas. | 
| [![Ícone de API][office-365-outlook-icon]<br/>**do Office 365<br/>Outlook**][office-365-outlook-doc] | Ligar à sua conta de e-mail do Office 365 para que possa criar e gerir e-mails, tarefas, eventos do calendário e reuniões, contatos, pedidos e muito mais. | [![Ícone de API][azure-blob-storage-icon]<br/>**BLOBs do Azure<br/>armazenamento**][azure-blob-storage-doc] | Ligar à sua conta de armazenamento para que possa criar e gerir o conteúdo do blob. | 
| [![Ícone de API][sftp-icon]<br/>**SFTP**][sftp-doc] | Ligar a servidores SFTP que pode aceder a partir da internet para que possa trabalhar com os seus ficheiros e pastas. | [![Ícone de API][sharepoint-online-icon]<br/>**SharePoint<br/>Online**][sharepoint-online-doc] | Ligue-se ao SharePoint Online, para que pode gerenciar arquivos, anexos, pastas e muito mais. | 
| [![Ícone de API][dynamics-365-icon]<br/>**do Dynamics 365<br/>CRM Online**][dynamics-365-doc] | Ligue-se à sua conta do Dynamics 365 para que possa criar e gerir os registos, itens e muito mais. | [![Ícone de API][ftp-icon]<br/>**FTP**][ftp-doc] | Ligar a servidores FTP, que pode aceder a partir da internet para que possa trabalhar com os seus ficheiros e pastas. | 
| [![Ícone de API][salesforce-icon]<br/>**Salesforce**][salesforce-doc] | Ligar à sua conta do Salesforce para que possa criar e gerir itens tais como registos, tarefas, objetos e muito mais. | [![Ícone de API][twitter-icon]<br/>**do Twitter**][twitter-doc] | Ligar à sua conta do Twitter para que possa gerir tweets, seguidores, sua linha cronológica e muito mais. Guarde os seus tweets para SQL, o Excel ou o SharePoint. | 
| [![Ícone de API][azure-event-hubs-icon]<br/>**Event Hubs do Azure**][azure-event-hubs-doc] | Consuma e publique eventos por meio de um Hub de eventos. Por exemplo, obter resultados da sua aplicação lógica com os Hubs de eventos e, em seguida, enviar essa saída a um fornecedor de análises em tempo real. | [![Ícone de API][azure-event-grid-icon]<br/>**eventos do Azure**</br>**grade**][azure-event-grid-doc] | Monitorizar eventos publicados por uma grelha de eventos, por exemplo, quando os recursos do Azure ou recursos de terceiros são alterados. | 
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Conectores no local 

Aqui estão alguns comumente utilizados conectores padrão que o Logic Apps fornece para aceder a dados e recursos em sistemas no local. Antes de poder criar uma ligação a um sistema no local, terá primeiro [transferir, instalar e configurar um gateway de dados no local][gateway-doc]. Este gateway fornece um canal de comunicação seguro sem ter de configurar a infraestrutura de rede necessárias. 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![Ícone de API][biztalk-server-icon]<br/>**BizTalk**</br> **servidor** | [![Ícone de API][file-system-icon]<br/>**ficheiro</br> sistema**][file-system-doc] | [![Ícone de API][ibm-db2-icon]<br/>**IBM DB2**][ibm-db2-doc] | [![API icon][ibm-informix-icon]<br/>**IBM**</br> **Informix**][ibm-informix-doc] | ![Ícone de API][mysql-icon]<br/>**MySQL** | 
| [![Ícone de API][oracle-db-icon]<br/>**Oracle DB**][oracle-db-doc] | ![Ícone de API][postgre-sql-icon]<br/>**PostgreSQL** | [![Ícone de API][sharepoint-server-icon]<br/>**SharePoint</br> Server**][sharepoint-server-doc] | [![Ícone de API][sql-server-icon]<br/>**SQL</br> Server**][sql-server-doc] | ![Ícone de API][teradata-icon]<br/>**Teradata** | 
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Conectores de conta de integração

O Logic Apps fornece conectores padrão para a criação de soluções de empresa-empresa (B2B) com as aplicações lógicas ao criar e pague um [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), que está disponível através do Enterprise Integration Pack (EIP) no Azure. Com esta conta, pode criar e armazenar artefactos de B2B, como comércio de parceiros, contratos, mapas, esquemas, certificados e assim por diante. Para utilizar estes artefactos, associe as aplicações lógicas com a sua conta de integração. Se utilizar atualmente o BizTalk Server, estes conectores podem parecer familiares já.

|   |   |   |   | 
|---|---|---|---| 
| [![Ícone de API][as2-icon]<br/>**AS2</br> descodificação**][as2-doc] | [![Ícone de API][as2-icon]<br/>**AS2</br> codificação**][as2-doc] | [![Ícone de API][edifact-icon]<br/>**EDIFACT</br> descodificação**][edifact-decode-doc] | [![Ícone de API][edifact-icon]<br/>**EDIFACT</br> codificação**][edifact-encode-doc] | 
| [![Ícone de API][flat-file-decode-icon]<br/>**ficheiro simples</br> descodificação**][flat-file-decode-doc] | [![Ícone de API][flat-file-encode-icon]<br/>**ficheiro simples</br> codificação**][flat-file-encode-doc] | [![Ícone de API][integration-account-icon]<br/>**integração<br/>conta**][integration-account-doc] | [![Ícone de API][liquid-icon]<br/>**líquidos**</br>**transforma**][json-liquid-transform-doc] | 
| [![Ícone de API][x12-icon]<br/>**X12</br> descodificação**][x12-decode-doc] | [![Ícone de API][x12-icon]<br/>**X12</br> codificação**][x12-encode-doc] | [![Ícone de API][xml-transform-icon]<br/>**XML**</br>**transforma**][xml-transform-doc] | [![Ícone de API][xml-validate-icon]<br/>**XML <br/>validação**][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Conectores empresariais

O Logic Apps fornece estes conectores empresariais para o acesso a sistemas empresariais, como SAP e IBM MQ:

|   |   |   | 
|---|---|---| 
| [![Ícone de API][ibm-3270-icon]<br/>**IBM 3270**][ibm-3270-doc] | [![Ícone de API][ibm-mq-icon]<br/>**IBM MQ**][ibm-mq-doc] | [![Ícone de API][sap-icon]<br/>**SAP**][sap-connector-doc] |
|||| 

<a name="triggers-actions"></a>

## <a name="triggers-and-actions---more-info"></a>Acionadores e ações - obter mais informações

Conectores podem fornecer *acionadores*, *ações*, ou ambos. R *acionador* é o primeiro passo em qualquer aplicação lógica, normalmente, especificando o evento que aciona o acionador e começa a executar a sua aplicação lógica. Por exemplo, o conector FTP tem um acionador que inicia a aplicação lógica "quando é adicionado ou modificado um ficheiro". Alguns acionadores verificar regularmente o evento especificado ou os dados e, em seguida, são disparados quando detetam o evento especificado ou os dados. Outros acionadores aguardarem, mas são acionados instantaneamente quando um evento específico acontece ou quando estão disponíveis novos dados. Acionadores também passam por todos os dados necessários à sua aplicação lógica. A aplicação lógica pode ler e utilizar esses dados em todo o fluxo de trabalho.
Por exemplo, o conector do Twitter tem um acionador, "Quando um novo tweet é publicado", que passa o tweet do conteúdo em fluxo de trabalho da sua aplicação lógica. 

Depois de um acionador é acionado, o Azure Logic Apps cria uma instância da sua aplicação lógica e começa a executar o *ações* no fluxo de trabalho da sua aplicação lógica. As ações são os passos que siga o acionador e executam tarefas no fluxo de trabalho da sua aplicação lógica. Por exemplo, pode criar uma aplicação lógica que obtém dados do cliente da base de dados SQL e processar dados em ações subsequentes. 

Aqui estão os tipos gerais de acionadores que o Azure Logic Apps fornece:

* *Acionador de periodicidade*: Este acionador é executado numa agenda especificada e não está fortemente associado com um sistema ou serviço específico.

* *Acionador de consulta*: Este acionador consulta regularmente um serviço específico ou um sistema com base na agenda especificada, verificar a existência de novos dados ou se aconteceu um evento específico. Se estão disponíveis novos dados ou ocorreu o evento específico, o acionador cria e executa uma instância nova da sua aplicação lógica, que agora pode utilizar os dados que são passados como entrada.

* *Push acionador*: Este acionador deve aguardar e escuta de dados nova ou da ocorrência de um evento. Quando estão disponíveis novos dados ou quando o evento ocorre, o acionador cria e executa uma instância nova da sua aplicação lógica, que agora pode utilizar os dados que são passados como entrada.

<a name="custom"></a>

## <a name="connector-configuration"></a>Configuração do conector

Acionadores e ações de cada conector fornecem as suas próprias propriedades de configurar. Muitos conetores também exigem que se crie primeiro um *ligação* para o serviço de destino ou o sistema e forneça as credenciais de autenticação ou outros detalhes de configuração antes de poder utilizar um acionador ou ação na sua aplicação lógica. Por exemplo, tem de autorizar uma ligação para uma conta do Twitter para aceder aos dados ou para publicar em seu nome. 

Para os conectores que utilizam a OAuth, a criação de uma ligação significa a iniciar sessão no serviço, como o Office 365, Salesforce ou GitHub, onde o token de acesso é encriptado e armazenado em segurança num arquivo de segredos do Azure. Outros conectores, como o FTP e o SQL, requerem uma ligação que tenha os detalhes de configuração, como o endereço do servidor, nome de utilizador e palavra-passe. Estes detalhes de configuração de ligação são também são encriptados e armazenados em segurança. 

Ligações podem acessar o serviço de destino ou o sistema para, desde que esse serviço ou o sistema permite. Para serviços que utilizam ligações de OAuth do Azure Active Directory (AD), como o Office 365 e Dynamics, Azure Logic Apps atualiza os tokens de acesso indefinidamente. Outros serviços podem ter limites sobre o tempo que o Azure Logic Apps pode utilizar um token sem atualizar. Em geral, algumas ações invalidar todos os tokens de acesso, tal como alterar a palavra-passe.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>APIs personalizadas e dos conectores

Para chamar as APIs que executam o código personalizado ou não estão disponíveis como conectores, pode expandir a plataforma do Logic Apps por [criar aplicações API personalizadas](../logic-apps/logic-apps-create-api-app.md). Também pode [criar conectores personalizados](../logic-apps/custom-connector-overview.md) para *qualquer* REST ou APIs baseados em SOAP, o que fazer com que essas APIs disponíveis para qualquer aplicação lógica na sua subscrição do Azure.
Para tornar as aplicações API personalizadas ou de conectores pública para qualquer pessoa utilizar no Azure, pode [submeter os conectores para certificação Microsoft](../logic-apps/custom-connector-submit-certification.md).

> [!NOTE]
> Aplicações lógicas dentro de um [o ambiente de serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) pode acessar diretamente os recursos numa rede virtual do Azure.
> Se tiver de conectores personalizados que exigem o gateway de dados no local e criou esses conectores fora um ISE, aplicações lógicas num ISE também podem utilizar os conectores.
>
> Conectores personalizados criados dentro de um ISE não funcionam com o gateway de dados no local. No entanto, estes conectores podem aceder diretamente a origens de dados no local que estão ligadas a uma rede virtual do Azure que aloja o ISE. Então, aplicações lógicas num ISE é muito provável que não precisam o gateway de dados ao se comunicar com esses recursos.
>
> Para obter mais informações sobre a criação de ISEs, consulte [ligar a redes virtuais do Azure a partir do Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#create-logic-apps-environment).

## <a name="next-steps"></a>Passos Seguintes

* Encontrar o [lista completa dos conectores](https://docs.microsoft.com/connectors)
* [Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Criar conectores personalizados para o logic apps](https://docs.microsoft.com/connectors/custom-connectors/)
* [Criar APIs personalizadas para aplicações lógicas](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Ligar a origens de dados no local a partir de aplicações lógicas com gateway de dados no local"

<!--Built-ins doc links-->
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrar aplicações lógicas com Funções do Azure"
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Criar uma instância de serviço de gestão de API do Azure para gerir e publicar as suas APIs"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integrar aplicações lógicas com Aplicações API do Serviço de Aplicações"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Envie mensagens das Filas e dos Tópicos do Service Bus e receba mensagens das Filas e das Subscrições do Service Bus"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Processar mensagens em grupos ou como lotes"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Avaliar uma condição e executadas ações diferentes, consoante a condição for true ou false"
[delay-doc]: ./connectors-native-delay.md "Efetue ações atrasadas"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Executar as mesmas ações em cada item numa matriz"
[http-doc]: ./connectors-native-http.md "Faça chamadas HTTP com o conector HTTP"
[http-request-doc]: ./connectors-native-reqres.md "Adicione ações para pedidos HTTP e respostas para as suas aplicações lógicas"
[http-response-doc]: ./connectors-native-reqres.md "Adicione ações para pedidos HTTP e respostas para as suas aplicações lógicas"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Faça chamadas HTTP com o conector HTTP + Swagger."
[http-webook-doc]: ./connectors-native-webhook.md "Adicionar ações de webhook HTTP e acionadores para as aplicações lógicas"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integre aplicações lógicas com fluxos de trabalho aninhados"
[query-doc]: ./connectors-native-query.md "Selecione e filtre matrizes com a ação Consultar"
[recurrence-doc]:  ./connectors-native-recurrence.md "Acione ações periódicas para aplicações lógicas"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organizar as ações em grupos, o qual obtenham o seu próprio Estado depois das ações no grupo termine a execução" 
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organize as ações em casos, o que são atribuídos valores exclusivos. Execute apenas o caso cujo valor faz corresponder o resultado de uma expressão, o objeto ou o token. Não se existir nenhuma correspondência, execute o caso de predefinição"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Parar ou cancelar um fluxo de trabalho ativamente em execução para a aplicação lógica"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Repetir ações até que a condição especificada for verdadeira ou algum Estado foi alterado"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Realizar operações de dados, como a filtragem de matrizes ou criar tabelas CSV e HTML"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Executar operações com variáveis, como initialize, conjunto, incremento, diminuição e acrescentar à variável da cadeia de caracteres ou matriz"

<!--Managed API doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Gerir ficheiros no seu contentor de blobs com o conector de armazenamento de blobs do Azure"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md " Monitorizar eventos publicados por uma grelha de eventos, por exemplo, quando os recursos do Azure ou recursos de terceiros são alterados"
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
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Ligue ao planilhas Google, para que possa modificar as suas folhas"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Ligue o Google Tarefas para que possa gerir as suas tarefas"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Ligar a aplicações de 3270 em IBM mainframes"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Ligue ao IBM DB2 na cloud ou no local. Atualize uma linha, obtenha uma tabela e muito mais"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Ligue o Informix na cloud ou no local. Leia uma linha, liste as tabelas e muito mais"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Ligue ao IBM MQ no local ou no Azure para enviar e receber mensagens"
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
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Ligue ao SendGrid. Enviar e-mail e gerir listas de destinatários"
[sftp-doc]: ./connectors-create-api-sftp.md "Ligue à sua conta do SFTP. Carregue, obtenha, elimine ficheiros e muito mais"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Ligue ao servidor do SharePoint no local. Gira documentos, liste itens e muito mais"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Ligue ao SharePoint Online. Gira documentos, liste itens e muito mais"
[slack-doc]: ./connectors-create-api-slack.md "Ligue ao Slack e publica mensagens nos canais do Slack"
[smtp-doc]: ./connectors-create-api-smtp.md "Ligar a um servidor de SMTP e enviar e-mails com anexos"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Ligue ao SparkPost para comunicação"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Ligar à base de dados SQL do Azure ou o SQL Server. Crie, atualize, obtenha e elimine entradas numa tabela da Base de Dados SQL."
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
