---
title: Ligar à base de dados do Oracle - Azure Logic Apps | Documentos da Microsoft
description: Inserir e gerir os registos com APIs de REST de base de dados Oracle e o Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 03/29/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 06f65aef203b4f0d765f21b9d17b90081de85c94
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551760"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Começar com o conector da base de dados Oracle

Utilizar o conector da base de dados Oracle, vai criar organizacionais fluxos de trabalho que utilizam dados na base de dados existente. Este conector pode ligar a uma base de dados do Oracle no local ou uma máquina virtual do Azure com a base de dados Oracle instalado. Com este conector, pode:

* Crie o seu fluxo de trabalho ao adicionar um novo cliente para uma base de dados de clientes, ou atualizando um pedido num banco de dados de pedidos.
* Utilize ações para obter uma linha de dados, inserir uma nova linha e até mesmo eliminar. Por exemplo, quando é criado um registo no Dynamics CRM Online (um acionador), em seguida, insira uma linha numa base de dados do Oracle (uma ação). 

Este artigo mostra-lhe como utilizar o conector da base de dados Oracle numa aplicação lógica.

## <a name="prerequisites"></a>Pré-requisitos

* Versões suportadas do Oracle: 
    * Oracle 9 e posterior
    * Software de cliente Oracle 8.1.7 e posterior

* Instale o gateway de dados no local. [Ligar a dados no local a partir de aplicações lógicas](../logic-apps/logic-apps-gateway-connection.md) lista os passos. O gateway é necessário para ligar a uma base de dados do Oracle no local ou uma VM do Azure com o Oracle DB instalado. 

    > [!NOTE]
    > O gateway de dados no local atua como uma ponte e fornece uma transferência de dados segura entre dados no local (dados que não esteja na cloud) e as aplicações lógicas. O mesmo gateway pode ser utilizado com vários serviços e várias origens de dados. Assim, apenas terá de instalar o gateway de uma vez.

* Instale o cliente Oracle no computador onde instalou o gateway de dados no local. Certifique-se de que instalar o 64-bit Oracle Data Provider for .NET da Oracle:  

  [64-bit ODAC 12C Release 4 (12.1.0.2.4) para Windows x64](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Se o cliente Oracle não estiver instalado, ocorrerá um erro ao tentar criar ou utilizar a ligação. Consulte os erros comuns neste artigo.


## <a name="add-the-connector"></a>Adicionar o conector

> [!IMPORTANT]
> Este conector não tem qualquer gatilhos. Ele tem apenas as ações. Por isso, quando criar a sua aplicação lógica, adicione outro acionador para iniciar a sua aplicação lógica, como **Schedule - Recurrence**, ou **pedido / resposta – resposta**. 

1. Na [portal do Azure](https://portal.azure.com), criar uma aplicação lógica em branco.

2. No início da sua aplicação lógica, selecione o **pedido / resposta - pedido** acionador: 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Selecione **Guardar**. Quando guarda, um URL do pedido é gerado automaticamente. 

4. Selecione **novo passo**e selecione **adicionar uma ação**. Escreva `oracle` para ver as ações disponíveis: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Isso também é a forma mais rápida para ver os acionadores e ações disponíveis para qualquer conector. Digite, como parte do nome do conector, `oracle`. O estruturador apresenta uma lista de acionadores e ações. 

5. Selecione uma das ações, como **base de dados do Oracle - obter linha**. Selecione **ligar através do gateway de dados no local**. Introduza o nome do servidor Oracle, o método de autenticação, nome de utilizador, palavra-passe e selecione o gateway:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Depois de ligado, selecione uma tabela na lista e introduza o ID de linha à tabela. Precisa saber o identificador para a tabela. Se não souber, contacte o administrador do Oracle DB e obtenha o resultado do `select * from yourTableName`. Isto dá-lhe as informações de identificação que precisa para continuar.

    No exemplo a seguir, os dados da tarefa estão a ser devolvidos de uma base de dados de recursos humanos: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. Neste passo, pode usar qualquer um dos outros conectores para criar seu fluxo de trabalho. Se pretender testar a obtenção de dados do Oracle, em seguida, envie para si próprio um e-mail com os dados do Oracle através de um dos conectores de e-mail do envio, do Office 365 ou Gmail. Utilize os tokens dinâmicos da tabela do Oracle para criar o `Subject` e `Body` da mensagem de e-mail:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Guarde** sua aplicação lógica e, em seguida, selecione **executar**. Feche o designer e ver o histórico de execuções para que o estado. Se falhar, selecione a linha de mensagem com falha. O designer abre e mostra quais passo falhou e também mostra as informações de erro. Se tiver êxito, deverá receber um e-mail com as informações que adicionou.


### <a name="workflow-ideas"></a>Idéias de fluxo de trabalho

* Pretende monitorizar a hashtag #oracle e colocar os tweets numa base de dados para que possam ser consultados e utilizados dentro de outros aplicativos. Numa aplicação lógica, adicione a `Twitter - When a new tweet is posted` acionar e introduza o **#oracle** hashtag. Em seguida, adicione o `Oracle Database - Insert row` ação e selecionar a tabela:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* As mensagens são enviadas para uma fila do Service Bus. Pretende obter estas mensagens e colocá-los numa base de dados. Numa aplicação lógica, adicione o `Service Bus - when a message is received in a queue` acionar e selecione a fila. Em seguida, adicione o `Oracle Database - Insert row` ação e selecionar a tabela:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Erros comuns

#### <a name="error-cannot-reach-the-gateway"></a>**Erro**: Não é possível aceder ao Gateway

**Motivo**: O gateway de dados no local não é possível estabelecer uma ligação para a cloud. 

**Atenuação**: Certificar-se de que o gateway está em execução na máquina no local onde a instalou e que possa estabelecer ligação à internet.  Recomendamos que não instale o gateway num computador que possa ser desativado ou modo de suspensão. Também pode reiniciar o serviço de gateway de dados no local (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Erro**: O fornecedor utilizado foi preterido: "System.Data.OracleClient requer Oracle versão do software de cliente 8.1.7 ou superior.". Ver [ https://go.microsoft.com/fwlink/p/?LinkID=272376 ](https://go.microsoft.com/fwlink/p/?LinkID=272376) para instalar o fornecedor oficial.

**Motivo**: O SDK do cliente Oracle não está instalado no computador onde está a executar o gateway de dados no local.  

**Resolução**: Transfira e instale o SDK do cliente Oracle no mesmo computador que o gateway de dados no local.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Erro**: A tabela "[Tablename]" não define quaisquer colunas de chave

**Motivo**: A tabela não tem nenhuma chave primária.  

**Resolução**: O conector da base de dados Oracle requer que uma tabela com uma coluna de chave primária ser usado.

#### <a name="currently-not-supported"></a>Atualmente não suportado

* Vistas 
* Qualquer tabela com as chaves compostas
* Tipos de objetos aninhados em tabelas
 
## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver os acionadores e as ações definidas no swagger e também ver quaisquer limites na [detalhes do conector](/connectors/oracle/). 

## <a name="get-some-help"></a>Obter ajuda

O [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) é um excelente local para fazer perguntas, responder a perguntas e ver o que os outros utilizadores do Logic Apps estão a fazer. 

Pode ajudar a melhorar o Logic Apps e conectores de voto e submeter as suas ideias em [ https://aka.ms/logicapps-wish ](https://aka.ms/logicapps-wish). 


## <a name="next-steps"></a>Passos Seguintes
[Criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md)e explore os conectores disponíveis no Logic Apps, no [lista APIs](apis-list.md).
