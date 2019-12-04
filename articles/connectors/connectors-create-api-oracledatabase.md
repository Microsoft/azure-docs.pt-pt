---
title: Conectar-se ao Oracle Database
description: Inserir e gerenciar registros com Oracle Database APIs REST e aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/29/2017
tags: connectors
ms.openlocfilehash: 99abd48bde97c2a2e085688cdfbb365e5e4cfd56
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789431"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Introdução ao conector de Oracle Database

Usando o conector de Oracle Database, você cria fluxos de trabalho organizacionais que usam dados em seu banco de dado existente. Esse conector pode se conectar a um Oracle Database local ou a uma máquina virtual do Azure com Oracle Database instalado. Com esse conector, você pode:

* Crie seu fluxo de trabalho adicionando um novo cliente a um banco de dados de clientes ou atualizando um pedido em um banco de dados de pedidos.
* Use ações para obter uma linha de dados, inserir uma nova linha e até mesmo excluir. Por exemplo, quando um registro é criado no Dynamics CRM Online (um gatilho), insira uma linha em uma Oracle Database (uma ação). 

Este artigo mostra como usar o conector de Oracle Database em um aplicativo lógico.

## <a name="prerequisites"></a>Pré-requisitos

* Versões do Oracle com suporte: 
    * Oracle 9 e posterior
    * Software cliente Oracle 8.1.7 e posterior

* Instale o gateway de dados local. [Conectar-se a dados locais de aplicativos lógicos](../logic-apps/logic-apps-gateway-connection.md) lista as etapas. O gateway é necessário para se conectar a um Oracle Database local ou a uma VM do Azure com o Oracle DB instalado. 

    > [!NOTE]
    > O gateway de dados local atua como uma ponte e fornece uma transferência de dados segura entre dados locais (dados que não estão na nuvem) e seus aplicativos lógicos. O mesmo gateway pode ser usado com vários serviços e várias fontes de dados. Portanto, você pode precisar instalar o gateway apenas uma vez.

* Instale o cliente Oracle no computador em que você instalou o gateway de dados local. Certifique-se de instalar o Provedor de Dados do Oracle de 64 bits para .NET da Oracle:  

  [64-bit ODAC 12c Release 4 (12.1.0.2.4) para Windows x64](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Se o cliente Oracle não estiver instalado, ocorrerá um erro quando você tentar criar ou usar a conexão. Consulte os erros comuns neste artigo.


## <a name="add-the-connector"></a>Adicionar o conector

> [!IMPORTANT]
> Este conector não tem nenhum gatilho. Ele tem apenas ações. Portanto, quando você cria seu aplicativo lógico, adicione outro gatilho para iniciar seu aplicativo lógico, como **Schedule-recorrência**ou **solicitação/resposta-resposta**. 

1. No [portal do Azure](https://portal.azure.com), crie um aplicativo lógico em branco.

2. No início do aplicativo lógico, selecione o gatilho **solicitação/resposta-solicitação** : 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Selecione **Guardar**. Quando você salva, uma URL de solicitação é gerada automaticamente. 

4. Selecione **nova etapa**e selecione **Adicionar uma ação**. Digite `oracle` para ver as ações disponíveis: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Essa também é a maneira mais rápida de ver os gatilhos e as ações disponíveis para qualquer conector. Digite parte do nome do conector, como `oracle`. O designer lista os gatilhos e as ações. 

5. Selecione uma das ações, como **Oracle Database-obter linha**. Selecione **conectar por meio do gateway de dados local**. Insira o nome do servidor Oracle, o método de autenticação, o nome de usuário, a senha e selecione o gateway:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Uma vez conectado, selecione uma tabela na lista e insira a ID da linha em sua tabela. Você precisa saber o identificador para a tabela. Se você não souber, entre em contato com o administrador do Oracle DB e obtenha a saída de `select * from yourTableName`. Isso fornece as informações de identificação necessárias para continuar.

    No exemplo a seguir, os dados de trabalho estão sendo retornados de um banco de dado de recursos humanos: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. Nesta próxima etapa, você pode usar qualquer um dos outros conectores para criar seu fluxo de trabalho. Se você quiser testar a obtenção de dados do Oracle, envie um email com os dados da Oracle usando um dos conectores de envio de email, como o Office 365 ou o gmail. Use os tokens dinâmicos da tabela do Oracle para criar o `Subject` e `Body` do seu email:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Salve** seu aplicativo lógico e, em seguida, selecione **executar**. Feche o designer e examine o histórico de execuções para o status. Se falhar, selecione a linha de mensagem com falha. O designer é aberto e mostra a você qual etapa falhou e também mostra as informações do erro. Se tiver sucesso, você deverá receber um email com as informações adicionadas.


### <a name="workflow-ideas"></a>Ideias de fluxo de trabalho

* Você deseja monitorar a hashtag de #oracle e colocar os tweets em um banco de dados para que eles possam ser consultados e usados em outros aplicativos. Em um aplicativo lógico, adicione o gatilho de `Twitter - When a new tweet is posted` e insira a hashtag de **#oracle** . Em seguida, adicione a ação de `Oracle Database - Insert row` e selecione a tabela:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* As mensagens são enviadas para uma fila do barramento de serviço. Você deseja obter essas mensagens e colocá-las em um banco de dados. Em um aplicativo lógico, adicione o gatilho `Service Bus - when a message is received in a queue` e selecione a fila. Em seguida, adicione a ação de `Oracle Database - Insert row` e selecione a tabela:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Erros comuns

#### <a name="error-cannot-reach-the-gateway"></a>**Erro**: não é possível acessar o gateway

**Causa**: o gateway de dados local não é capaz de se conectar à nuvem. 

**Mitigação**: Verifique se o gateway está em execução no computador local onde você o instalou e se ele pode se conectar à Internet.  Recomendamos não instalar o gateway em um computador que possa ser desligado ou suspenso. Você também pode reiniciar o serviço de gateway de dados local (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Erro**: o provedor que está sendo usado é preterido: ' System. Data. OracleClient requer o software cliente Oracle versão 8.1.7 ou superior. '. Consulte [https://go.microsoft.com/fwlink/p/?LinkID=272376](https://go.microsoft.com/fwlink/p/?LinkID=272376) para instalar o provedor oficial.

**Causa**: o SDK do cliente Oracle não está instalado no computador em que o gateway de dados local está em execução.  

**Resolução**: Baixe e instale o SDK do cliente Oracle no mesmo computador que o gateway de dados local.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Erro**: a tabela ' [TableName] ' não define nenhuma coluna de chave

**Causa**: a tabela não tem nenhuma chave primária.  

**Resolução**: o conector de Oracle Database requer que uma tabela com uma coluna de chave primária seja usada.

#### <a name="currently-not-supported"></a>Atualmente sem suporte

* Vistas 
* Qualquer tabela com chaves compostas
* Tipos de objeto aninhados em tabelas
 
## <a name="connector-specific-details"></a>Detalhes específicos do conector

Exiba todos os gatilhos e ações definidos no Swagger e também veja os limites nos [detalhes do conector](/connectors/oracle/). 

## <a name="get-some-help"></a>Obtenha ajuda

O [Fórum de aplicativos lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) é um ótimo lugar para fazer perguntas, responder a perguntas e ver o que os outros usuários de aplicativos lógicos estão fazendo. 

Você pode ajudar a melhorar os aplicativos lógicos e conectores votando e enviando suas ideias em [https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish). 


## <a name="next-steps"></a>Passos seguintes
[Crie um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md)e explore os conectores disponíveis nos aplicativos lógicos na [lista de APIs](apis-list.md).
