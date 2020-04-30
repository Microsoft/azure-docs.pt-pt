---
title: Conectores do Azure Logic Apps
description: Automatizar fluxos de trabalho com conectores para Aplicações Lógicas Azure, tais como incorporados, geridos, no local, conta de integração, ISE e conectores empresariais
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 04/24/2020
ms.openlocfilehash: 27f86a3fa34f8029e59d11066de9ea5a25a8c5ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147985"
---
# <a name="connectors-for-azure-logic-apps"></a>Conectores do Azure Logic Apps

Os conectores proporcionam um acesso rápido a partir do Azure Logic Apps a eventos, dados e ações noutros serviços, aplicações, sistemas, protocolos e plataformas. Ao utilizar conectores nas suas aplicações lógicas, expande as capacidades das aplicações na cloud e locais para executarem tarefas com os dados que cria e já tem.

Embora as Aplicações Lógicas [ofereçam centenas de conectores,](https://docs.microsoft.com/connectors)este artigo descreve os conectores *populares e mais utilizados* que são usados com sucesso por milhares de apps e milhões de execuções para o processamento de dados e informações. Para encontrar a lista completa dos conectores e as informações de referência de cada conector, tais como gatilhos, ações e limites, reveja as páginas de referência do conector sob a visão geral dos [Conectores](https://docs.microsoft.com/connectors). Além disso, saiba mais sobre [gatilhos e ações,](#triggers-actions)modelo de [preços de Apps Lógicas](../logic-apps/logic-apps-pricing.md)e detalhes de preços de [Aplicações Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps/)

> [!TIP]
> Para integrar com um serviço ou API que não tenha conector, pode ligar diretamente para o serviço através de um protocolo como HTTP, ou criar um [conector personalizado](#custom).

## <a name="connector-types"></a>Tipos de conector

Os conectores estão disponíveis como gatilhos e ações incorporados ou como conectores geridos.

<a name="built-in"></a>

* Incorporado : Os gatilhos e ações incorporados são "nativos" das Aplicações Lógicas Azure e [**ajudam-no**](#built-ins)a executar estas tarefas para as suas aplicações lógicas:

  * Corra em horários personalizados e avançados.

  * Organize e controle o fluxo de trabalho da sua aplicação lógica, por exemplo, loops e condições, e também para trabalhar com variáveis e operações de dados.

  * Comunicar com outros pontos finais.

  * Receber e responder a pedidos.

  * Ligue para as funções Azure, Azure API Apps (Web Apps), as suas próprias APIs geridas e publicadas com a Azure API Management, e aplicações lógicas aninhadas que podem receber pedidos.

<a name="managed-connectors"></a>

* [**Conectores geridos**](#managed-api-connectors): Implantados e geridos pela Microsoft, estes conectores fornecem gatilhos e ações para aceder a serviços na nuvem, sistemas no local, ou ambos, incluindo Office 365, Armazenamento Azure Blob, SQL Server, Dynamics, Salesforce, SharePoint, e muito mais. Alguns conectores apoiam especificamente cenários de comunicação entre empresas (B2B) e exigem uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) que esteja ligada à sua aplicação lógica. Antes de utilizar certos conectores, poderá ter de criar primeiro ligações, que são geridas pela Azure Logic Apps.

  Por exemplo, se estiver a utilizar o Microsoft BizTalk Server, as suas aplicações lógicas podem ligar-se e comunicar com o seu Servidor BizTalk utilizando o [conector no local](#on-premises-connectors)do BizTalk Server . Em seguida, pode estender ou executar operações semelhantes ao BizTalk nas suas aplicações lógicas utilizando os [conectores](#integration-account-connectors)de conta de integração.

  Os conectores são classificados como Standard ou Enterprise. [Os conectores empresariais](#enterprise-connectors) fornecem acesso a sistemas empresariais como sAP, IBM MQ e IBM 3270 por um custo adicional. Para determinar se um conector é Standard ou Enterprise, consulte os detalhes técnicos na página de referência de cada conector sob a visão geral dos [Conectores](https://docs.microsoft.com/connectors).

  Também pode identificar conectores utilizando estas categorias, embora alguns conectores possam cruzar várias categorias. Por exemplo, o SAP é um conector Enterprise e um conector no local:

  |   |   |
  |---|---|
  | [**Conectores geridos**](#managed-api-connectors) | Crie aplicações lógicas que utilizem serviços como O Armazenamento Azure Blob, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online, entre muitos outros. |
  | [**Conectores no local**](#on-premises-connectors) | Depois de instalar e configurar o [portal de dados no local,][gateway-doc]estes conectores ajudam as suas aplicações lógicas a aceder a sistemas no local, tais como SQL Server, SharePoint Server, Oracle DB, partilhas de ficheiros, entre outros. |
  | [**Conectores de conta de integração**](#integration-account-connectors) | Disponíveis quando cria e paga por uma conta de integração, estes conectores transformam e validam xML, codificam e descodificam ficheiros planos, e processam mensagens negócio-a-negócio (B2B) com protocolos AS2, EDIFACT e X12. |
  |||

  > [!IMPORTANT]
  > Se pretender utilizar o conector Gmail, apenas as contas de negócio g-Suite podem utilizar este conector sem restrições em aplicações lógicas. Se tiver uma conta de consumo do Gmail, pode utilizar este conector apenas com serviços específicos aprovados pela Google, ou pode criar uma aplicação de [cliente da Google para usar para autenticação com o seu conector Gmail](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application). Para mais informações, consulte as políticas de [segurança e privacidade dos dados para os conectores da Google em Aplicações Lógicas Azure](../connectors/connectors-google-data-security-privacy-policy.md).

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment"></a>Conecte-se a partir de um ambiente de serviço de integração

Para aplicações lógicas que precisam de acesso direto aos recursos numa rede virtual Azure, pode criar um ambiente de serviço de integração isolado [(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) onde pode construir, implementar e executar as suas aplicações lógicas em recursos dedicados. No Logic App Designer, quando navega nos conectores que pretende utilizar para aplicações lógicas num ISE, aparece uma etiqueta **CORE** em gatilhos e ações incorporadas, enquanto a etiqueta **ISE** aparece em alguns conectores:

* **CORE**: Gatilhos e ações incorporados com esta etiqueta funcionam no mesmo ISE que as suas aplicações lógicas, por exemplo:

  ![Conector ISE exemplo](./media/apis-list/example-core-connector.png)

* **ISE**: Conectores geridos com esta etiqueta funcionam no mesmo ISE que as suas aplicações lógicas, por exemplo:

  ![Conector ISE exemplo](./media/apis-list/example-ise-connector.png)

  Se tiver um sistema no local ligado a uma rede virtual Azure, um ISE permite que as suas aplicações lógicas acedam diretamente a esse sistema sem a [porta de dados no local.](../logic-apps/logic-apps-gateway-connection.md) Em vez disso, pode utilizar o conector **ISE** desse sistema, se estiver disponível, uma ação HTTP ou um [conector personalizado](#custom). Para sistemas no local que não possuam conectores **ISE,** utilize o portal de dados no local. Para rever os conectores ISE disponíveis, consulte [os conectores ISE](#ise-connectors).

* Todos os outros conectores sem a etiqueta **CORE** ou **ISE,** que pode continuar a usar, funcionam no serviço global de aplicações lógicas multi-inquilinos, por exemplo:

  ![Conector multi-inquilino de exemplo](./media/apis-list/example-multi-tenant-connector.png)

As aplicações lógicas que funcionam num ISE e nos seus conectores, independentemente do local onde esses conectores funcionam, seguem um plano de preços fixo sintetizado em relação ao plano de preços baseado no consumo. Para mais informações, consulte estas páginas:

* [Modelo de preços de Aplicações Lógicas](../logic-apps/logic-apps-pricing.md)
* [Detalhes de preços de Apps Lógicas](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Ligue-se a redes virtuais Azure a partir de Aplicações Lógicas Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="built-ins"></a>

## <a name="built-in"></a>Incorporado

As Aplicações Lógicas fornecem gatilhos e ações incorporados para que possa criar fluxos de trabalho baseados em horários, ajudar as suas aplicações lógicas a comunicar com outras apps e serviços, controlar o fluxo de trabalho através das suas aplicações lógicas e gerir ou manipular dados.

|   |   |   |   |
|---|---|---|---|
| [![][schedule-icon]<br>**Agenda do** ícone da API][schedule-doc] | - Executar uma aplicação lógica numa recorrência especificada, que vai desde horários básicos a avançados com o gatilho [ **de Recorrência** ][schedule-recurrence-doc]. <p>- Executar uma aplicação lógica que precisa de lidar com dados em pedaços contínuos com o gatilho [ **da janela deslizante** ][schedule-sliding-window-doc]. <p>- Pausa na sua aplicação lógica durante uma duração especificada com a ação [ **Delay** ][schedule-delay-doc]. <p>- Pausa na sua aplicação lógica até à data e hora especificadas com o [ **Atraso até à** ação][schedule-delay-until-doc]. | [![][batch-icon]<br>**Lote de** ícone aPI][batch-doc] | - Processar mensagens em lotes com o gatilho das **mensagens 'Lote'.** <p>- Ligue para aplicações lógicas que tenham gatilhos de lote existentes com as **mensagens Enviar para** a ação do lote. |
| [![Ícone da][http-icon]<br>API**HTTP**][http-doc] | Ligue para os pontos finais HTTP ou HTTPS com gatilhos e ações para HTTP. Outros gatilhos e ações incorporados http incluem [HTTP + Swagger][http-swagger-doc] e HTTP + [Webhook][http-webhook-doc]. | [![][http-request-icon]<br>**Pedido** de ícone da API][http-request-doc] | - Torne a sua aplicação lógica chamada de outras aplicações ou serviços, desencadeie eventos de recursos da Rede de Eventos ou desencadeie respostas para alertas do Azure Security Center com o gatilho **do Pedido.** <p>- Envie respostas para uma aplicação ou serviço com a ação **Resposta.** |
| [![Ícone api][azure-api-management-icon]<br>**Azure API <br>Management**][azure-api-management-doc] | Desloques e ações definidas pelas suas próprias APIs que gere e publica com a Azure API Management. | [![API][azure-app-services-icon]<br>ícone**Azure App <br>Services**][azure-app-services-doc] | Ligue para as aplicações Azure API, ou Web Apps, hospedadas no Azure App Service. Os gatilhos e ações definidas por estas apps aparecem como quaisquer outros gatilhos e ações de primeira classe quando o Swagger está incluído.|
| [![Aplicativos][azure-logic-apps-icon]<br>**lógicos <br>azure ícone** da API][nested-logic-app-doc] | Ligue para outras aplicações lógicas que começam com o gatilho **do Pedido.** |
|||||

### <a name="run-code-from-logic-apps"></a>Executar código de aplicações lógicas

Logic Apps fornece ações incorporadas para executar o seu próprio código no fluxo de trabalho da sua aplicação lógica:

|   |   |   |   |
|---|---|---|---|
| [![][azure-functions-icon]<br>**Funções azure** do ícone API][azure-functions-doc] | Ligue para as funções Azure que executam snippets de código personalizado (C# ou Node.js) das suas aplicações lógicas. | [![][inline-code-icon]<br>**Código inline** do ícone API][azure-functions-doc] | Adicione e execute os snippets de código JavaScript das suas aplicações lógicas. |
|||||

### <a name="control-workflow"></a>Controlar o fluxo de trabalho

Logic Apps fornece ações incorporadas para estruturar e controlar as ações no fluxo de trabalho da sua aplicação lógica:

|   |   |   |   |
|---|---|---|---|
| [![][condition-icon]<br>**Condição** de ícone incorporado][condition-doc] | Avalie uma condição e execute diferentes ações com base em se a condição é verdadeira ou falsa. | [![Ícone][for-each-icon]<br>incorporado**para cada**][for-each-doc] | Execute as mesmas ações em cada item de uma matriz. |
| [![][scope-icon]<br>**Scope** de ícone incorporado][scope-doc] | As ações de grupo em *âmbitos*, que obtêm o seu próprio estatuto após as ações no âmbito terminam em execução. | [![][switch-icon]<br>**Switch** de ícone incorporado][switch-doc] | Ações de grupo em *casos*, que são atribuídos valores únicos, com exceção do caso predefinido. Executar apenas aquele caso cujo valor atribuído corresponde ao resultado de uma expressão, objeto ou símbolo. Se não existirem correspondências, execute o caso predefinido. |
| [![][terminate-icon]<br>**Ícone** incorporado termina][terminate-doc] | Pare um fluxo de trabalho de aplicações lógicas de funcionamento ativo. | [![Ícone][until-icon]<br>incorporado**até**][until-doc] | Repita as ações até que a condição especificada seja verdadeira ou algum estado tenha mudado. |
|||||

### <a name="manage-or-manipulate-data"></a>Gerir ou manipular dados

As Aplicações Lógicas fornecem ações incorporadas para trabalhar com saídas de dados e seus formatos:

|   |   |
|---|---|
| [![Operações de][data-operations-icon]<br>dados de ícones**incorporados**][data-operations-doc] | Realizar operações com dados: <p>- **Compor**: Criar uma única saída a partir de várias inputs com vários tipos. <br>- **Criar tabela CSV**: Criar uma tabela de valor separado de víramida (CSV) de uma matriz com objetos JSON. <br>- **Criar tabela HTML**: Criar uma tabela HTML a partir de uma matriz com objetos JSON. <br>- **Matriz de filtro**: Crie uma matriz a partir de itens em outra matriz que satisfaça os seus critérios. <br>- **Junte-se**: Crie uma corda de todos os itens numa matriz e separe os itens com o delimitador especificado. <br>- **Parse JSON**: Crie fichas fáceis de utilizar a partir de propriedades e seus valores em conteúdo JSON para que possa utilizar essas propriedades no seu fluxo de trabalho. <br>- **Selecione:** Crie uma matriz com objetos JSON transformando itens ou valores noutra matriz e mapeando esses itens para propriedades especificadas. |
| ![Ícone incorporado][date-time-icon]<br>**Data Hora** | Realizar operações com selos temporais: <p>- **Adicione ao tempo**: Adicione o número especificado de unidades a uma marca de tempo. <br>- **Converter o fuso horário**: Converta uma marca de tempo do fuso horário de origem para o fuso horário alvo. <br>- **Tempo atual**: Devolva a marca atual como uma corda. <br>- **Obtenha tempo futuro**: Devolva a marca de tempo atual mais as unidades de tempo especificadas. <br>- **Passar o tempo**: Devolva o carimbo de tempo atual menos as unidades de tempo especificadas. <br>- **Subtrair do tempo**: Subtrair várias unidades de tempo a partir de uma marca de tempo. |
| [![Variáveis de][variables-icon]<br>ícone**incorporados**][variables-doc] | Realizar operações com variáveis: <p>- **Apêndice à variável de matriz**: Insira um valor como último item numa matriz armazenada por uma variável. <br>- **Apêndice à variável de cadeia**: Insira um valor como último personagem numa cadeia armazenada por uma variável. <br>- **Variável de decreção:** Diminuir uma variável por um valor constante. <br>- **Variável de incremento**: Aumentar uma variável por um valor constante. <br>- **Inicializar variável**: Criar uma variável e declarar o seu tipo de dados e valor inicial. <br>- **Variável de conjunto**: Atribuir um valor diferente a uma variável existente. |
|  |  |

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>Conectores geridos

As Aplicações Lógicas fornecem estes populares conectores Standard para automatizar tarefas, processos e fluxos de trabalho com estes serviços ou sistemas:

|   |   |   |   |
|---|---|---|---|
| [![Ônibus][azure-service-bus-icon]<br>**de serviço azure do** ícone API][azure-service-bus-doc] | Faça a gestão de mensagens assíncronas, sessões e subscrições de tópicos com o conector mais usado em Logic Apps. | [![][sql-server-icon]<br>**Servidor SQL do** ícone API][sql-server-doc] | Ligue-se ao seu Servidor SQL nas instalações ou a uma Base de Dados Azure SQL na nuvem para que possa gerir registos, executar procedimentos armazenados ou realizar consultas. |
| [![API][azure-blob-storage-icon]<br>ícone**Azure<br>Blob Armazenamento**][azure-blob-storage-doc] | Conecte-se à sua conta de armazenamento para que possa criar e gerir o conteúdo blob. | [![Ícone DaPi][office-365-outlook-icon]<br>**Office<br>365 Outlook**][office-365-outlook-doc] | Ligue-se à sua conta de e-mail office 365 para que possa criar e gerir e-mails, tarefas, eventos de calendário e reuniões, contactos, pedidos e muito mais. |
| [![Ícone][sftp-ssh-icon]<br>API**SFTP-SSH**][sftp-ssh-doc] | Ligue-se aos servidores SFTP a que pode aceder a partir da internet utilizando o SSH para que possa trabalhar com os seus ficheiros e pastas. | [![Ícone API][sharepoint-online-icon]<br>**SharePoint<br>Online**][sharepoint-online-doc] | Ligue-se ao SharePoint Online para que possa gerir ficheiros, anexos, pastas e muito mais. | 
| [![Ícone API][dynamics-365-icon]<br>**Dinâmica<br> 365**][dynamics-365-doc] | Ligue-se à sua conta Dynamics 365 para que possa criar e gerir registos, itens e muito mais. | [![Filas][azure-queues-icon]<br>**de ícones <br>** API Azure][azure-queues-doc] | Ligue-se à sua conta de Armazenamento Azure para que possa criar e gerir filas e mensagens |
| [![Ícone da][ftp-icon]<br>API**FTP**][ftp-doc] | Ligue-se aos servidores FTP a que pode aceder a partir da internet para que possa trabalhar com os seus ficheiros e pastas. | [![][file-system-icon]<br>**Sistema de <br>ficheiros** de ícone sípio API][file-system-doc] | Ligue-se à partilha de ficheiros no local para que possa criar e gerir ficheiros. |
| [![API][azure-event-hubs-icon]<br>ícone**Azure Event Hubs**][azure-event-hubs-doc] | Consumir e publicar eventos através de um Hub de Eventos. Por exemplo, obtenha saída da sua aplicação lógica com Hubs de Eventos e, em seguida, envie essa saída para um fornecedor de análise em tempo real. | [![API][azure-event-grid-icon]<br>ícone**Azure Event**<br>**Grid**][azure-event-grid-doc] | Monitorize os eventos publicados por uma Rede de Eventos, por exemplo, quando os recursos do Azure ou recursos de terceiros mudarem. |
| [![API][salesforce-icon]<br>ícone**Salesforce**][salesforce-doc] | Ligue-se à sua conta Salesforce para que possa criar e gerir itens como registos, empregos, objetos e muito mais. | [![Twitter][twitter-icon]<br>**ícone** da API][twitter-doc] | Conecte-se à sua conta de Twitter para que possa gerir tweets, seguidores, a sua linha temporal e muito mais. Guarde os seus tweets para SQL, Excel ou SharePoint. |
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Conectores no local

Aqui estão alguns conectores Standard comumente usados que as Aplicações Lógicas fornecem para aceder a dados e recursos em sistemas no local. Antes de poder criar uma ligação a um sistema no local, tem primeiro de [descarregar, instalar e configurar uma porta de dados no local.][gateway-doc] Esta porta de entrada fornece um canal de comunicação seguro sem ter de criar a infraestrutura de rede necessária.

|   |   |   |   |   |
|---|---|---|---|---|
| [![Ícone DaPi][biztalk-server-icon]<br>**BizTalk** <br> **Server**][biztalk-server-doc] | [![][file-system-icon]<br>**Sistema de <br>ficheiros** de ícone sípio API][file-system-doc] | [![Ícone da][ibm-db2-icon]<br>API**IBM DB2**][ibm-db2-doc] | [![Ícone][ibm-informix-icon]<br>api**IBM** <br> **Informix**][ibm-informix-doc] | [![Ícone API][mysql-icon]<br>**MySQL**][mysql-doc] |
| [![Ícone da][oracle-db-icon]<br>API**Oracle DB**][oracle-db-doc] | [![Ícone][postgre-sql-icon]<br>API**PostgreSQL**][postgre-sql-doc] | [![][sharepoint-server-icon]<br>**Servidor SharePoint <br>do** ícone API][sharepoint-server-doc] | [![][sql-server-icon]<br>**Servidor SQL <br>do** ícone API][sql-server-doc] | [![Ícone da][teradata-icon]<br>API**Teradata**][teradata-doc] |
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Conectores de conta de integração

Logic Apps fornece conectores Standard para construir soluções business-to-business (B2B) com as suas aplicações lógicas quando cria e paga por uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), que está disponível através do Enterprise Integration Pack (EIP) no Azure. Com esta conta, pode criar e armazenar artefactos B2B, tais como parceiros comerciais, acordos, mapas, schemas, certificados, e assim por diante. Para utilizar estes artefactos, associe as suas aplicações lógicas à sua conta de integração. Se utilizar atualmente o BizTalk Server, estes conectores podem parecer já familiares.

|   |   |   |   |
|---|---|---|---|
| [![Descodificação do ícone][as2-icon]<br>**API AS2 <br>**][as2-doc] | [![Codificação][as2-icon]<br>do ícone**API AS2 <br>**][as2-doc] | [![Descodificação do ícone][edifact-icon]<br>**API EDIFACT <br>**][edifact-decode-doc] | [![Codificação][edifact-icon]<br>do ícone**API EDIFACT <br>**][edifact-encode-doc] |
| [![][flat-file-decode-icon]<br>**Descodificação <br>de ficheiros planos** do ícone API][flat-file-decode-doc] | [![Codificação][flat-file-encode-icon]<br>de**ficheiro <br>** plano do ícone API][flat-file-encode-doc] | [![][integration-account-icon]<br>**Conta de <br>integração do** ícone API][integration-account-doc] | [![Ícone API][liquid-icon]<br>**O líquido** <br> **transforma-se**][json-liquid-transform-doc] |
| [![Descodificação do ícone][x12-icon]<br>API**X12 <br>**][x12-decode-doc] | [![Codificação][x12-icon]<br>do ícone API**X12 <br>**][x12-encode-doc] | [![Ícone API][xml-transform-icon]<br>**XML** <br> **transforma**][xml-transform-doc] | [![Validação][xml-validate-icon]<br>do ícone API**XML <br>**][xml-validate-doc] |  
|||||

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Conectores empresariais

As Aplicações Lógicas fornecem estes conectores Enterprise para aceder a sistemas empresariais, tais como SAP e IBM MQ:

|   |   |   |
|---|---|---|
| [![Ícone da][ibm-3270-icon]<br>API**IBM 3270**][ibm-3270-doc] | [![Ícone da][ibm-mq-icon]<br>API**IBM MQ**][ibm-mq-doc] | [![Ícone API][sap-icon]<br>**SAP**][sap-connector-doc] |
||||

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>Conectores ISE

Para aplicações lógicas que cria e executa um ambiente de serviço de integração isolado [(ISE),](#integration-service-environment)o Logic App Designer identifica gatilhos e ações incorporadas que funcionam no seu ISE utilizando a etiqueta **CORE.** Os conectores geridos que funcionam num ISE exibem a etiqueta **ISE,** enquanto os conectores que funcionam no serviço global de aplicações lógicas multi-inquilinos não apresentam nenhuma das etiquetas. Esta lista mostra os conectores que atualmente têm versões ISE:

|   |   |   |   |   |
|---|---|---|---|---|
[![Ícone][as2-icon]<br>API**AS2**][as2-doc] | [![API][azure-blob-storage-icon]<br>ícone**Azure<br>Blob Armazenamento**][azure-blob-storage-doc] | [![Ícone da][azure-cosmos-db-icon]<br>API**Azure Cosmos <br> DB**][azure-cosmos-db-doc] | [![API][azure-event-hubs-icon]<br>ícone**Azure <br>Event Hubs**][azure-event-hubs-doc] | [![Armazenamento de][azure-file-storage-icon]<br>**ficheiros<br>Azure**][azure-file-storage-doc] |
| [![Ônibus][azure-service-bus-icon]<br>**de serviço <br>azure do** ícone API][azure-service-bus-doc] | [![][azure-sql-data-warehouse-icon]<br>Armazém de**dados <br>Azure SQL** ícone DaA][azure-sql-data-warehouse-doc] | [![Armazenamento de][azure-table-storage-icon]<br>** <br>mesa azure** ícone API][azure-table-storage-doc] | [![Filas][azure-queues-icon]<br>**de ícones <br>** API Azure][azure-queues-doc] | [![Ícone da][edifact-icon]<br>API**EDIFACT**][edifact-doc] |
| [![][file-system-icon]<br>**Sistema de <br>ficheiros** de ícone sípio API][file-system-doc] | [![Ícone da][ftp-icon]<br>API**FTP**][ftp-doc] | [![Ícone da][ibm-3270-icon]<br>API**IBM 3270**][ibm-3270-doc] | [![Ícone da][ibm-db2-icon]<br>API**IBM DB2**][ibm-db2-doc] | [![Ícone da][ibm-mq-icon]<br>API**IBM MQ**][ibm-mq-doc] |
| [![Ícone API][sap-icon]<br>**SAP**][sap-connector-doc] | [![Ícone][sftp-ssh-icon]<br>API**SFTP-SSH**][sftp-ssh-doc] | [![Ícone][smtp-icon]<br>**API SMTP**][smtp-doc] | [![][sql-server-icon]<br>**Servidor SQL <br>do** ícone API][sql-server-doc] | [![Ícone][x12-icon]<br>API**X12**][x12-doc] |
||||||

Para obter mais informações, veja estes tópicos:

* [Acesso aos recursos da rede virtual Azure a partir de Aplicações Lógicas Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Modelo de preços de Aplicações Lógicas](../logic-apps/logic-apps-pricing.md)
* [Ligue-se a redes virtuais Azure a partir de Aplicações Lógicas Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>Gatilhos e tipos de ação

Os conectores podem fornecer *gatilhos,* *ações,* ou ambos. Um *gatilho* é o primeiro passo em qualquer aplicação lógica, normalmente especificando o evento que dispara o gatilho e começa a executar a sua aplicação lógica. Por exemplo, o conector FTP tem um gatilho que inicia a sua aplicação lógica "quando um ficheiro é adicionado ou modificado". Alguns gatilhos verificam regularmente o evento ou dados especificados e depois disparam quando detetam o evento ou dados especificados. Outros gatilhos esperam, mas disparam instantaneamente quando um evento específico acontece ou quando novos dados estão disponíveis. Os gatilhos também transmitem todos os dados necessários para a sua aplicação lógica. A sua aplicação lógica pode ler e utilizar esses dados em todo o fluxo de trabalho. Por exemplo, o conector do Twitter tem um gatilho: "Quando um novo tweet é publicado", que passa o conteúdo do tweet para o fluxo de trabalho da sua aplicação lógica.

Depois de um gatilho, o Azure Logic Apps cria uma instância da sua aplicação lógica e começa a executar as *ações* no fluxo de trabalho da sua aplicação lógica. As ações são os passos que seguem o gatilho e executam tarefas no fluxo de trabalho da sua aplicação lógica. Por exemplo, pode criar uma aplicação lógica que obtém dados do cliente a partir de uma base de dados SQL e processa esses dados em ações posteriores.

Aqui estão os tipos gerais de gatilhos que as Aplicações Lógicas Azure fornecem:

* *Gatilho de recorrência*: Este gatilho funciona num horário especificado e não está fortemente associado a um determinado serviço ou sistema.

* *Gatilho de sondagens*: Este gatilho vota regularmente um serviço ou sistema específico com base no horário especificado, verificando se um evento específico aconteceu. Se novos dados estiverem disponíveis ou se o evento específico acontecer, o gatilho cria e executa uma nova instância da sua aplicação lógica, que agora pode usar os dados que são transmitidos como entrada.

* *Premir*o gatilho : Este gatilho aguarda e ouve novos dados ou para que um evento aconteça. Quando novos dados estão disponíveis ou quando o evento acontece, o gatilho cria e executa uma nova instância da sua aplicação lógica, que agora pode usar os dados que são transmitidos como entrada.

<a name="connections"></a>

## <a name="connector-configuration"></a>Configuração do conector

Os gatilhos e ações de cada conector fornecem as suas próprias propriedades para que possa configurar. Muitos conectores também exigem que crie primeiro uma *ligação* ao serviço ou sistema alvo e forneça credenciais de autenticação ou outros detalhes de configuração antes de poder usar um gatilho ou ação na sua aplicação lógica. Por exemplo, deve autorizar uma ligação a uma conta do Twitter para aceder a dados ou publicar em seu nome.

Para conectores que utilizam o Azure Ative Directory (Azure AD) OAuth, criar uma ligação significa assinar no serviço, como office 365, Salesforce ou GitHub, onde o seu token de acesso é [encriptado](../security/fundamentals/encryption-overview.md) e armazenado de forma segura numa loja secreta Azure. Outros conectores, como FTP e SQL, requerem uma ligação que tenha detalhes de configuração, tais como o endereço do servidor, nome de utilizador e senha. Estes detalhes de configuração de ligação também são encriptados e armazenados de forma segura. Saiba mais sobre [encriptação no Azure.](../security/fundamentals/encryption-overview.md)

As ligações podem aceder ao serviço ou sistema alvo durante o tempo que esse serviço ou sistema permitir. Para serviços que utilizam ligações Azure AD OAuth, como office 365 e Dynamics, a Azure Logic Apps atualiza fichas de acesso indefinidamente. Outros serviços podem ter limites para quanto tempo as Aplicações Lógicas Azure podem usar um símbolo sem refrescar. Geralmente, algumas ações invalidam todos os tokens de acesso, tais como alterar a sua palavra-passe.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>APIs e conectores personalizados

Para ligar para APIs que executam código personalizado ou não estão disponíveis como conectores, pode estender a plataforma de Aplicações Lógicas [criando aplicações API personalizadas](../logic-apps/logic-apps-create-api-app.md). Também pode [criar conectores personalizados](../logic-apps/custom-connector-overview.md) para *quaisquer* APIs baseados em REST ou SOAP, que disponibilizam essas APIs a qualquer aplicação lógica na sua subscrição Azure. Para tornar publicamente aplicações ou conectores API personalizados para qualquer pessoa que utilize no Azure, pode [submeter conectores para certificação da Microsoft](../logic-apps/custom-connector-submit-certification.md).

> [!NOTE]
> Aplicações lógicas que implementa e executa num ambiente de serviço de [integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) podem aceder diretamente a recursos numa rede virtual Azure. Se tiver conectores personalizados que exijam a porta de dados no local, e criou esses conectores fora de um ISE, as aplicações lógicas num ISE também podem usar esses conectores.
>
> Os conectores personalizados criados dentro de um ISE não funcionam com a porta de dados no local. No entanto, estes conectores podem aceder diretamente a fontes de dados no local que estão ligadas a uma rede virtual Azure que acolhe o ISE. Assim, aplicações lógicas num ISE provavelmente não precisam da porta de dados quando comunicam com esses recursos.
>
> Para obter mais informações sobre a criação de ISEs, consulte [Redes virtuais Connect to Azure a partir de Aplicações Lógicas Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

## <a name="next-steps"></a>Passos seguintes

* Ver a lista completa do [conector](https://docs.microsoft.com/connectors)
* [Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Criar conectores personalizados para aplicações lógicas](https://docs.microsoft.com/connectors/custom-connectors/)
* [Criar APIs personalizadas para aplicações lógicas](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Ligar a origens de dados no local a partir de aplicações lógicas com gateway de dados no local"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Crie uma instância de serviço de Gestão API Azure para gerir e publicar as suas APIs"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integrar aplicações lógicas com Aplicações API do Serviço de Aplicações"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrar aplicações lógicas com funções azure"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Processar mensagens em grupos, ou como lotes"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Avaliar uma condição e executar diferentes ações com base em se a condição é verdadeira ou falsa"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Realizar as mesmas ações em cada item de uma matriz"
[http-doc]: ./connectors-native-http.md "Ligue para os pontos finais HTTP ou HTTPS das suas aplicações lógicas"
[http-request-doc]: ./connectors-native-reqres.md "Receba pedidos de HTTP nas suas aplicações lógicas"
[http-response-doc]: ./connectors-native-reqres.md "Responda aos pedidos de HTTP das suas aplicações lógicas"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Ligue para os pontos finais rest das suas aplicações lógicas"
[http-webhook-doc]: ./connectors-native-webhook.md "Aguarde eventos específicos de HTTP ou HTTPS endpoints"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integre aplicações lógicas com fluxos de trabalho aninhados"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Selecione e filtre matrizes com a ação Consultar"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Executar aplicativos lógicos baseados num horário"
[schedule-delay-doc]: ./connectors-native-delay.md "Adiar a próxima ação"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Adiar a próxima ação"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Executar aplicativos lógicos em um horário recorrente"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Executar aplicativos lógicos que precisam lidar com dados em pedaços contíguos"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organize ações em grupos, que obtêm o seu próprio estatuto após as ações em grupo terminar em execução"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organize ações em casos, que são atribuídos valores únicos. Executar apenas o caso cujo valor corresponde ao resultado de uma expressão, objeto ou símbolo. Se não existirem correspondências, execute o caso padrão"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Pare ou cancele um fluxo de trabalho ativamente em execução para a sua aplicação lógica"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Repita as ações até que a condição especificada seja verdadeira ou algum estado tenha mudado"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Efetuar operações de dados tais como matrizes de filtragem ou criação de tabelas CSV e HTML"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Realizar operações com variáveis, tais como inicializar, definir, incrementar, decremente e apêndice à variável de cadeia ou matriz"

<!--Managed connector doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Gerir ficheiros no seu contentor de blobs com o conector de armazenamento de blobs do Azure"
[azure-cosmos-db-doc]: https://docs.microsoft.com/connectors/documentdb/ "Ligue-se ao Azure Cosmos DB para que possa aceder a documentos e procedimentos armazenados"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Monitorize eventos publicados por uma Rede de Eventos, por exemplo, quando os recursos do Azure ou recursos de terceiros mudarem"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Ligue-se aos Hubs de Eventos Azure para que possa receber e enviar eventos entre aplicações lógicas e Centros de Eventos"
[azure-file-storage-doc]: https://docs.microsoft.com/connectors/azurefile/ "Ligue-se à sua conta de Armazenamento Azure para que possa criar, atualizar, obter e eliminar ficheiros"
[azure-queues-doc]: https://docs.microsoft.com/connectors/azurequeues/ "Ligue-se à sua conta de Armazenamento Azure para que possa criar e gerir filas e mensagens"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Envie mensagens de filas e tópicos de ônibus de serviço e receba mensagens de filas e subscrições de ônibus de serviço"
[azure-sql-data-warehouse-doc]: https://docs.microsoft.com/connectors/sqldw/ "Ligue-se ao Azure SQL Data Warehouse para que possa ver os seus dados"
[azure-table-storage-doc]: https://docs.microsoft.com/connectors/azuretables/ "Ligue-se à sua conta de Armazenamento Azure para que possa criar, atualizar e consultar tabelas e muito mais"
[biztalk-server-doc]: https://docs.microsoft.com/connectors/biztalk/ "Conecte-se ao seu Servidor BizTalk para que possa executar aplicações baseadas no BizTalk lado a lado com aplicações da Lógica Azure"
[box-doc]: ./connectors-create-api-box.md "Ligue-se à Caixa. Faça upload, obtenha, elimine, liste os seus ficheiros e muito mais"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Ligue-se ao Dropbox. Faça upload, obtenha, elimine, liste os seus ficheiros e muito mais"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Ligue-se à Dynamics CRM Online para que possa trabalhar com dados online CRM"
[facebook-doc]: ./connectors-create-api-facebook.md "Ligue-se ao Facebook. Poste para uma linha do tempo, obter um feed de página, e muito mais"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Ligue a um sistema de ficheiros no local"
[ftp-doc]: ./connectors-create-api-ftp.md "Ligue a um servidor FTP / FTPS e para tarefas de FTP, como carregar, obter, eliminar ficheiros, entre outras"
[github-doc]: ./connectors-create-api-github.md "Ligue o GitHub e controle problemas"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Conecta-se ao Google Calendar e pode gerir o calendário"
[google-drive-doc]: ./connectors-create-api-googledrive.md "Ligue-se ao GoogleDrive para que possa trabalhar com os seus dados"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Conecte-se ao Google Sheets para que possa modificar as suas folhas"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Conecta-se às Tarefas do Google para que possa gerir as suas tarefas"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Ligue-se a 3270 aplicações nos mainframes da IBM"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Ligue-se ao IBM DB2 na nuvem ou no local. Atualizar uma linha, obter uma mesa, e muito mais"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Ligue-se ao Informix na nuvem ou no local. Leia uma linha, lista as mesas, e muito mais"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Ligue-se ao IBM MQ no local ou em Azure para enviar e receber mensagens"
[instagram-doc]: ./connectors-create-api-instagram.md "Ligue-se ao Instagram. Desencadear ou agir em eventos"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Ligue-se à sua conta MailChimp. Gerir e automatizar e-mails"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Ligue ao Mandrill para comunicação"
[mysql-doc]: https://docs.microsoft.com/connectors/mysql/ "Conecte-se à sua base de dados MySQL no local para que possa ler e escrever dados"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Ligue-se à sua conta Office 365 para que possa enviar e receber e-mails, gerir o seu calendário e contactos, e muito mais"
[office-365-users-doc]: ./connectors-create-api-office365-users.md
[onedrive-doc]: ./connectors-create-api-onedrive.md "Ligue-se ao seu Microsoft OneDrive pessoal para que possa carregar, eliminar, listar ficheiros e muito mais"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Ligue-se ao seu negócio Microsoft OneDrive para que possa carregar, eliminar, listar os seus ficheiros e muito mais"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Ligue-se a uma base de dados da Oracle para que possa adicionar, inserir, eliminar linhas e muito mais"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Ligue-se à sua caixa de correio Outlook para que possa gerir o seu e-mail, calendários, contactos e muito mais"
[postgre-sql-doc]: https://docs.microsoft.com/connectors/postgresql/ "Ligue-se à sua base de dados PostgreSQL para que possa ler dados a partir de tabelas"
[project-online-doc]: ./connectors-create-api-projectonline.md "Ligue-se ao Microsoft Project Online para que possa gerir os seus projetos, tarefas, recursos e muito mais"
[rss-doc]: ./connectors-create-api-rss.md "Publique e recupere itens de alimentação, desencadeie operações quando um novo item é publicado para um feed RSS"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Ligue-se à sua conta Salesforce. Gerir contas, leads, oportunidades e muito mais"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Ligue a um sistema SAP no local"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Ligue-se a SendGrid. Enviar e-mail e gerir listas de destinatários"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Ligue-se à sua conta SFTP utilizando o SSH. Carregar, obter, apagar ficheiros e muito mais"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Ligue-se ao servidor no local do SharePoint. Gerir documentos, listar itens e muito mais"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Ligue-se ao SharePoint Online. Gerir documentos, listar itens e muito mais"
[slack-doc]: ./connectors-create-api-slack.md "Ligue-se a Slack e poste mensagens para canais Slack"
[smtp-doc]: ./connectors-create-api-smtp.md "Ligue-se a um servidor SMTP e envie e-mail com anexos"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Ligue ao SparkPost para comunicação"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Ligue-se à Base de Dados Azure SQL ou ao Servidor SQL. Criar, atualizar, obter e eliminar entradas numa tabela de bases de dados SQL"
[teradata-doc]: https://docs.microsoft.com/connectors/teradata/ "Ligue-se à sua base de dados Teradata para ler dados a partir de tabelas"
[trello-doc]: ./connectors-create-api-trello.md "Ligue-se a Trello. Gerencie os seus projetos e organize qualquer coisa com qualquer um"
[twilio-doc]: ./connectors-create-api-twilio.md "Ligue-se a Twilio. Enviar e obter mensagens, obter números disponíveis, gerir números de telefone e muito mais"
[twitter-doc]: ./connectors-create-api-twitter.md "Ligue-se ao Twitter. Obtenha cronologias, post tweets e muito mais"
[yammer-doc]: ./connectors-create-api-yammer.md "Ligue-se ao Yammer. Postmessages messages, get new messages, and more"
[youtube-doc]: ./connectors-create-api-youtube.md "Ligue-se ao YouTube. Gerencie os seus vídeos e canais"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Codificar e descodificar mensagens que utilizem o protocolo AS2"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Codificar e descodificar mensagens que utilizem o protocolo EDIFACT"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Descodificar mensagens que utilizam o protocolo EDIFACT"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Codificar mensagens que utilizam o protocolo EDIFACT"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Saiba mais sobre o arquivo plano de integração da empresa"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Saiba mais sobre o arquivo plano de integração da empresa"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Gerir metadados para artefactos de conta de integração"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Transforme jSON com modelos líquidos"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Codificar e descodificar mensagens que usam o protocolo X12"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Descodificar mensagens que usam o protocolo X12"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Codificar mensagens que usam o protocolo X12"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Transforme as mensagens XML"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Validar mensagens XML"

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
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
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
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
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
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
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
