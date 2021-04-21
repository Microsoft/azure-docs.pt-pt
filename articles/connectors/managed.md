---
title: Conectores geridos para apps Azure Logic
description: Utilize gatilhos e ações geridos pela Microsoft para criar fluxos de trabalho automatizados que integram outras aplicações, dados, serviços e sistemas utilizando apps Azure Logic.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: d50cf1009fb674f2d3d5914715dcbd9699565489
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797182"
---
# <a name="managed-connectors-for-logic-apps"></a>Conectores geridos para aplicações lógicas

[Os conectores geridos](apis-list.md) fornecem formas de aceder a outros serviços e sistemas onde [os gatilhos e ações incorporados](built-in.md) não estão disponíveis. Pode utilizar estes gatilhos e ações para criar fluxos de trabalho que integrem dados, apps, serviços baseados na nuvem e sistemas no local. Em comparação com os gatilhos e ações incorporados, estes conectores estão geralmente ligados a um serviço ou sistema específico, tais como Azure Blob Storage, Office 365, SQL, Salesforce ou SFTP servidores. Geridos pela Microsoft e hospedados no Azure, os conectores geridos normalmente requerem que crie uma ligação a partir do seu fluxo de trabalho e autente a sua identidade. Tanto os gatilhos baseados em recorrência como baseados em webhook estão disponíveis, por isso, se utilizar um gatilho baseado em recorrência, reveja a visão geral do comportamento de [Recorrência](apis-list.md#recurrence-behavior).

Para alguns serviços, sistemas e protocolos, tais como Azure Service Bus, Azure Functions, SQL, AS2, e assim por diante, a Logic Apps também fornece versões incorporadas. O número e a gama variam de acordo com a criação de uma app lógica multi-inquilino ou de uma aplicação lógica de inquilino único. Em alguns casos, tanto uma versão incorporada como uma versão de conector gerida estão disponíveis. Na maioria dos casos, a versão incorporada proporciona um melhor desempenho, capacidades, preços, e assim por diante. Por exemplo, para [trocar mensagens B2B utilizando o protocolo AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), selecione a versão incorporada a menos que precise de capacidades de rastreamento, que estão disponíveis apenas na versão de conector gerida (precotado).

Alguns conectores geridos para Aplicações Lógicas pertencem a várias sub-categorias. Por exemplo, o conector SAP é simultaneamente um [conector da empresa](#enterprise-connectors) e um [conector no local](#on-premises-connectors).

* [Os conectores standard](#standard-connectors) fornecem acesso a serviços como Azure Blob Storage, Office 365, SharePoint, Salesforce, Power BI, OneDrive, entre muitos outros.
* [Os conectores no local](#on-premises-connectors) fornecem acesso a sistemas no local como SQL Server, SharePoint Server, SAP, Oracle DB, partilhas de ficheiros, entre outros.
* [Os conectores de conta](#integration-account-connectors) de integração ajudam-no a transformar e validar ficheiros XML, codificar e descodificar ficheiros planos e processar mensagens business-to-business (B2B) utilizando protocolos AS2, EDIFACT e X12. 

## <a name="standard-connectors"></a>Conetores padrão

A Azure Logic Apps fornece estes conectores Standard populares para a construção de fluxos de trabalho automatizados utilizando estes serviços e sistemas. Alguns conectores Standard também [suportam sistemas no local](#on-premises-connectors) ou contas de [integração](#integration-account-connectors).

Algumas aplicações lógicas Os conectores standard [suportam sistemas no local](#on-premises-connectors) ou contas de [integração.](#integration-account-connectors)

:::row:::
    :::column:::
        [![Azure Service Bus geriu ícone de conector em Logic Apps][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Ônibus de serviço Azure**][azure-service-bus-doc]
        \
        \
        Faça a gestão de mensagens assíncronas, sessões e subscrições de tópicos com o conector mais usado em Logic Apps.
    :::column-end:::
    :::column:::
        [![SQL Server geriu ícone de conector em Aplicativos Logic][sql-server-icon]][sql-server-doc]
        \
        \
        [**Servidor SQL**][sql-server-doc]
        \
        \
        Ligue ao seu SQL Server nas instalações ou numa Base de Dados Azure SQL na nuvem para que possa gerir registos, executar procedimentos armazenados ou realizar consultas.
    :::column-end:::
    :::column:::
        [![Azure Blog Storage geriu ícone de conector em Aplicativos Logic][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Armazenamento de blogs Azure**][azure-blob-storage-doc]
        \
        \
        Ligue-se à sua conta de Armazenamento Azure para que possa criar e gerir o conteúdo blob.
    :::column-end:::
    :::column:::
        [![Office 365 Outlook geriu ícone de conector em Apps lógicas][office-365-outlook-icon]][office-365-outlook-doc]
        \
        \
        [**Office 365 Outlook**][office-365-outlook-doc]
        \
        \
        Conecte-se à sua conta de e-mail de trabalho ou escola para que possa criar e gerir e-mails, tarefas, eventos de calendário e reuniões, contactos, pedidos e muito mais.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![StFP-SSH geriu ícone de conector em Apps lógicas][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**STFP-SSH**][sftp-ssh-doc]
        \
        \
        Ligue-se a servidores SFTP aos quais pode aceder a partir da Internet com o SSH para poder trabalhar com os ficheiros e as pastas.
    :::column-end:::
    :::column:::
        [![SharePoint Ícone de conector gerido online em Aplicativos Logic][sharepoint-online-icon]][sharepoint-online-doc]
        \
        \
        [**SharePoint Online**][sharepoint-online-doc]
        \
        \
        Ligue-se ao SharePoint Online para poder gerir ficheiros, anexos, pastas e mais.
    :::column-end:::
    :::column:::
        [![Azure Queues geriu ícone de conector em Aplicativos Lógicos][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Filas Azure**][azure-queues-doc]
        \
        \
        Ligue-se à sua conta de Armazenamento Azure para que possa criar e gerir filas e mensagens.
    :::column-end:::
    :::column:::
        [![FtP geriu ícone de conector em Logic Apps][ftp-icon]][ftp-doc]
        \
        \
        [**FTP**][ftp-doc]
        \
        \
        Ligue-se aos servidores FTP que pode aceder a partir da internet para que possa trabalhar com os seus ficheiros e pastas.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ícone de conector gerido do sistema de ficheiros em Apps lógicas][file-system-icon]][file-system-doc]
        \
        \
        [**Sistema de Ficheiros**][file-system-doc]
        \
        \
        Ligue-se à partilha de ficheiros no local para que possa criar e gerir ficheiros.
    :::column-end:::
    :::column:::
        [![Azure Event Hubs geriu ícone de conector em Apps lógicas][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Hubs de Eventos Azure**][azure-event-hubs-doc]
        \
        \
        Consuma e publique eventos através de um Hub de Eventos. Por exemplo, utilize o Hubs de Eventos para obter resultados da sua aplicação lógica e, em seguida, envie esses resultados para um fornecedor de análises em tempo real.
    :::column-end:::
    :::column:::
        [![Azure Event Grid geriu ícone de conector em Aplicativos Logic][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Grelha de eventos de Azure**][azure-event-grid-doc]
        \
        \
        Monitorize eventos publicados por uma Grade de Eventos, por exemplo, quando os recursos do Azure ou recursos de terceiros mudam.
    :::column-end:::
    :::column:::
        [![Salesforce geriu ícone de conector em Aplicativos Logic][salesforce-icon]][salesforce-doc]
        \
        \
        [**Salesforce**][salesforce-doc]
        \
        \
        Ligue-se à sua conta Salesforce para que possa criar e gerir itens como registos, empregos, objetos e muito mais.
    :::column-end:::
:::row-end:::


## <a name="on-premises-connectors"></a>Conectores no local

Antes de criar uma ligação a um sistema no local, tem primeiro de [descarregar, instalar e configurar um gateway de dados no local.][gateway-doc] Este gateway fornece um canal de comunicação seguro sem ter que configurar a infraestrutura de rede necessária. 

Os conectores seguintes são [alguns conectores Standard](#standard-connectors) comumente usados que as Aplicações Lógicas fornecem para aceder a dados e recursos em sistemas no local. Para a lista de conectores no local, consulte [fontes de dados suportadas](../logic-apps/logic-apps-gateway-connection.md#supported-connections).

:::row:::
    :::column:::
        [![Ícone de conector do servidor biztalk no local em Apps lógicas][biztalk-server-icon]][biztalk-server-doc]
        \
        \
        [**Biztalk Server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector do sistema de ficheiros nas instalações em Apps lógicas][file-system-icon]][file-system-doc]
        \
        \
        [**Sistema de Ficheiros**][file-system-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector IBM Db2 no local em Apps Lógicas][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM Db2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector IBM Informix no local em Aplicativos Logic][ibm-informix-icon]][ibm-informix-doc]
        \
        \
        [**IBM Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ícone de conector MySQL no local em Aplicativos Logic][mysql-icon]][mysql-doc]
        \
        \
        [**MySQL**][mysql-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector Oracle DB no local em Aplicativos Lógicos][oracle-db-icon]][oracle-db-doc]
        \
        \
        [**Oracle DB**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector postgreSQL nas instalações em Aplicativos Lógicos][postgre-sql-icon]][postgre-sql-doc]
        \
        \
        [**Postgresql**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector do Servidor SharePoint nas instalações em Apps lógicas][sharepoint-server-icon]][sharepoint-server-doc]
        \
        \
        [**SharePoint Server**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ícone de conector do servidor SQL nas instalações em Apps lógicas][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector teradata no local em Aplicativos Lógicos][teradata-icon]][teradata-doc]
        \
        \
        [**Teradata**][teradata-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="integration-account-connectors"></a>Conectores de conta de integração

Os conectores de conta de integração suportam especificamente [cenários de comunicação business-to-business (B2B)](../logic-apps/logic-apps-enterprise-integration-overview.md) em Azure Logic Apps. Depois de [criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e definir os seus artefactos B2B, tais como parceiros de negociação, acordos, mapas e esquemas, pode utilizar conectores de conta de integração para codificar e descodificar mensagens, transformar conteúdos e muito mais.

Por exemplo, se utilizar o Microsoft BizTalk Server, pode criar uma ligação a partir do seu fluxo de trabalho utilizando o [conector do Servidor BizTalk no local](#on-premises-connectors). Em seguida, pode estender ou executar operações semelhantes ao BizTalk no seu fluxo de trabalho utilizando estes conectores de conta de integração.

> [!NOTE]
> Antes de utilizar conectores de conta de integração, tem de [ligar a sua aplicação lógica a uma conta de integração.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)


:::row:::
    :::column:::
        [![As2 descoding ícone de ação em Aplicativos Lógicos][as2-icon]][as2-doc]
        \
        \
        [**Descodição AS2**][as2-doc]
    :::column-end:::
    :::column:::
        [![As2 codificando ícone de ação em Aplicativos Lógicos][as2-icon]][as2-doc]
        \
        \
        [**Codificação AS2**][as2-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT descoding ícone de ação em Aplicativos Lógicos][edifact-icon]][edifact-decode-doc]
        \
        \
        [**Descodificação de EDIFACT**][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT codificando ícone de ação em Aplicativos Lógicos][edifact-icon]][edifact-encode-doc]
        \
        \
        [**Codificação de EDIFACT**][edifact-encode-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ícone de ação de descoding de arquivo plano em Apps lógicas][flat-file-decode-icon]][flat-file-decode-doc]
        \
        \
        [**Descodificação de ficheiros simples**][flat-file-decode-doc]
    :::column-end:::
    :::column:::
        [![Ícone de ação de codificação de arquivo plano em Apps lógicas][flat-file-encode-icon]][flat-file-encode-doc]
        \
        \
        [**Codificação de ficheiros planos**][flat-file-encode-doc]
    :::column-end:::
    :::column:::
        [![Ícone de ação de conta de integração em Aplicativos Lógicos][integration-account-icon]][integration-account-doc]
        \
        \
        [**Conta de integração**][integration-account-doc]
    :::column-end:::
    :::column:::
        [![Líquido transforma ícone de ação em Apps lógicas][liquid-icon]][json-liquid-transform-doc]
        \
        \
        [**Transformação líquida**][json-liquid-transform-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![X12 descodindo ícone de ação em Aplicativos Lógicos][x12-icon]][x12-decode-doc]
        \
        \
        [**Descodificação de X12**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![X12 codificando ícone de ação em Aplicativos Lógicos][x12-icon]][x12-encode-doc]
        \
        \
        [**Codificação de X12**][x12-encode-doc]
    :::column-end:::
    :::column:::
        [![XML transforma ícone de ação em Apps Lógicas][xml-transform-icon]][xml-transform-doc]
        \
        \
        [**XML transforma**][xml-transform-doc]
    :::column-end:::
    :::column:::
        [![Ícone de ação de validação XML em Aplicativos Lógicos][xml-validate-icon]][xml-validate-doc]
        \
        \
        [**Validação XML**][xml-validate-doc]
    :::column-end:::
:::row-end:::

## <a name="enterprise-connectors"></a>Conectores empresariais

Os seguintes conectores fornecem acesso aos sistemas empresariais por um custo adicional:

:::row:::
    :::column:::
        [![Ícone de conector de empresa IBM 3270 em Aplicativos Logic][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [Conector empresarial **IBM 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector de empresa IBM MQ em Aplicativos Lógicos][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**Conector** de empresa IBM MQ][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector de empresa SAP em Aplicativos Lógicos][sap-icon]][sap-connector-doc]
        \
        \
        [**Conector** de empresa SAP][sap-connector-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::


## <a name="ise-connectors"></a>Conectores ISE

Num ambiente de serviço de integração (ISE), estes conectores geridos também têm [versões ISE,](apis-list.md#ise-and-connectors)que têm capacidades diferentes das suas versões multi-arrendatários:

> [!NOTE]
> As aplicações lógicas que funcionam num ISE e os seus conectores, independentemente do local onde esses conectores funcionam, seguem um plano de preços fixos em relação ao plano de preços baseado no consumo. Para obter mais informações, consulte [o modelo de preços de Apps Lógicas](../logic-apps/logic-apps-pricing.md) e detalhes de preços de Apps [Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps/)

:::row:::
    :::column:::
        [![Ícone de conector ISE AS2 em Aplicativos Lógicos][as2-icon]][as2-doc]
        \
        \
        [**AS2** ISE][as2-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector ISE de Azure Automation ISE em Apps Lógicas][azure-automation-icon]][azure-automation-doc]
        \
        \
        [**Azure Automation** ISE][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector ISE de armazenamento de blob Azure blob em apps lógicas][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Armazenamento Azure Blob** ISE][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector Azure Cosmos DB ISE em Aplicativos Lógicos][azure-cosmos-db-icon]][azure-cosmos-db-doc]
        \
        \
        [**Azure Cosmos DB** ISE][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ícone de conector ISE dos Hubs de EventoS Azure em Apps Lógicas][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Hubs de Eventos Azure** ISE][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector ISE de grade de evento azure em apps lógicas][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Grelha de eventos de Azure** ISE][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector ISE de armazenamento de ficheiros Azure em Apps Lógicas][azure-file-storage-icon]][azure-file-storage-doc]
        \
        \
        [**Armazenamento de arquivos Azure** ISE][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector ISE do cofre de chave Azure em Apps lógicas][azure-key-vault-icon]][azure-key-vault-doc]
        \
        \
        [**Cofre da Chave Azure** ISE][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Monitor Regista o ícone do conector ISE em Apps Lógicas][azure-monitor-logs-icon]][azure-monitor-logs-doc]
        \
        \
        [**Registos do Monitor Azure** ISE][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector ISE de ônibus de serviço Azure em Aplicativos Logic][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Ônibus de serviço Azure** ISE][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![Azure Synapse Analytics ise ícone de conector em Aplicativos Lógicas][azure-sql-data-warehouse-icon]][azure-sql-data-warehouse-doc]
        \
        \
        [**Azure Synapse Analytics** ISE][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector ISE de armazenamento de mesa Azure em Apps lógicas][azure-table-storage-icon]][azure-table-storage-doc]
        \
        \
        [**Armazenamento de mesa Azure** ISE][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ícone de conector ISE de Azure Queues em Aplicativos Lógicos][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Filas Azure** ISE][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector EDIFACT ISE em Aplicativos Lógicos][edifact-icon]][edifact-doc]
        \
        \
        [**EDIFACT** ISE][edifact-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector ISE do sistema de ficheiros em apps lógicas][file-system-icon]][file-system-doc]
        \
        \
        [**Sistema de Ficheiros** ISE][file-system-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector FTP ISE em Aplicativos Lógicos][ftp-icon]][ftp-doc]
        \
        \
        [**FTP** ISE][ftp-doc]
    :::column-end:::
:::row-end:::   
:::row:::
    :::column:::
        [![Ícone de conector ISE IBM 3270 em Aplicativos Lógicos][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**IBM 3270** ISE][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector IBM DB2 ISE em Aplicativos Lógicos][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM DB2** ISE][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector IBM MQ ISE em Aplicativos Lógicos][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**IBM MQ** ISE][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector SAP ISE em Aplicativos Lógicos][sap-icon]][sap-connector-doc]
        \
        \
        [**SAP** ISE][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ícone de conector ISE SFTP-SSH em Apps lógicas][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**SFTP-SSH** ISE][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector ISE SMTP em Apps lógicas][smtp-icon]][smtp-doc]
        \
        \
        [**SMTP** ISE][smtp-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector ISE do servidor SQL em Aplicativos Lógicos][sql-server-icon]][sql-server-doc]
        \
        \
        [**Servidor SQL** ISE][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Ícone de conector ISE X12 em Aplicativos Lógicos][x12-icon]][x12-doc]
        \
        \
        [**X12** ISE][x12-doc]
    :::column-end:::
:::row-end:::

Para obter mais informações, veja estes tópicos:

* [Acesso aos recursos de rede virtual Azure a partir de Apps Azure Logic](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Modelo de preços de Aplicações Lógicas](../logic-apps/logic-apps-pricing.md)
* [Ligue-se a redes virtuais Azure a partir de Apps Azure Logic](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Crie APIs personalizados que pode chamar a partir de Aplicações Lógicas](/logic-apps/logic-apps-create-api-app)

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

<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!-- Integration account connector docs -->

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


<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Ligar a origens de dados no local a partir de aplicações lógicas com gateway de dados no local"



<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!-- Integration account connector docs -->

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
