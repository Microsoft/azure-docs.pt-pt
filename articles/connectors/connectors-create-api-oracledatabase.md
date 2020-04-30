---
title: Ligar à Base de Dados Oracle
description: Insira e gerea os registos com as APIs REST da Oracle Database e as Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/29/2017
tags: connectors
ms.openlocfilehash: fdbf7fd7dded2fc0026e5c819ca579eeddc5cdb6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147803"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Começar com o conector Oracle Database

Utilizando o conector Oracle Database, cria fluxos de trabalho organizacionais que utilizam dados na sua base de dados existente. Este conector pode ligar-se a uma base de dados oracle no local, ou a uma máquina virtual Azure com base de dados Oracle instalada. Com este conector, pode:

* Construa o seu fluxo de trabalho adicionando um novo cliente a uma base de dados de clientes ou atualizando uma encomenda numa base de dados de encomendas.
* Use ações para obter uma linha de dados, insira uma nova linha e até mesmo eliminar. Por exemplo, quando um disco é criado no Dynamics CRM Online (um gatilho), em seguida, insira uma linha numa Base de Dados Oracle (uma ação). 

Este artigo mostra-lhe como usar o conector Oracle Database numa aplicação lógica.

## <a name="prerequisites"></a>Pré-requisitos

* Versões do Oracle suportadas: 
    * Oracle 9 e posterior
    * Software de cliente Oracle 8.1.7 e posterior

* Instale o portal de dados no local. [Ligar-se aos dados no local a partir de aplicações lógicas](../logic-apps/logic-apps-gateway-connection.md) lista os passos. O portal é necessário para ligar a uma Base de Dados Oracle no local, ou a um VM Azure com o Oracle DB instalado. 

    > [!NOTE]
    > O gateway de dados no local funciona como uma ponte, e fornece uma transferência segura de dados entre dados no local (dados que não estão na nuvem) e as suas aplicações lógicas. O mesmo portal pode ser usado com vários serviços e múltiplas fontes de dados.Então, pode apenas precisar instalar o portal uma vez.

* Instale o Cliente Oracle na máquina onde instalou o portal de dados no local.Certifique-se de instalar o Provedor de Dados Oracle de 64 bits para .NET da Oracle:  

  [64-bit ODAC 12c Release 4 (12.1.0.2.4) para o Windows x64](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Se o cliente Oracle não estiver instalado, ocorre um erro quando tenta criar ou utilizar a ligação. Veja os erros comuns neste artigo.


## <a name="add-the-connector"></a>Adicione o conector

> [!IMPORTANT]
> Este conector não tem gatilhos. Só tem ações. Assim, quando criar a sua aplicação lógica, adicione outro gatilho para iniciar a sua aplicação lógica, como **Agenda - Recorrência,** ou **Pedido / Resposta - Resposta**. 

1. No [portal Azure, crie](https://portal.azure.com)uma aplicação lógica em branco.

2. No início da sua aplicação lógica, selecione o **gatilho Pedido / Resposta - Pedido:** 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Selecione **Guardar**. Quando economiza, um URL de pedido é gerado automaticamente. 

4. Selecione **Novo passo** e selecione **Adicionar uma ação**. Digite `oracle` para ver as ações disponíveis: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Esta é também a forma mais rápida de ver os gatilhos e ações disponíveis para qualquer conector. Digite em parte do nome `oracle`do conector, tais como . O designer lista quaisquer gatilhos e quaisquer ações. 

5. Selecione uma das ações, como **oracle Database - Obtenha linha**. Selecione **Connect via gateway de dados no local**. Introduza o nome do servidor Oracle, método de autenticação, nome de utilizador, palavra-passe e selecione o gateway:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Uma vez ligado, selecione uma tabela da lista e introduza a identificação da linha na sua mesa. Precisa de saber o identificador para a mesa. Se não souber, contacte o administrador da Oracle `select * from yourTableName`DB e obtenha a saída de . Isto dá-lhe a informação identificável que precisa de proceder.

    No exemplo seguinte, os dados do emprego estão a ser devolvidos de uma base de dados de Recursos Humanos: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. Neste próximo passo, pode utilizar qualquer um dos outros conectores para construir o seu fluxo de trabalho. Se quiser testar a obtenção de dados da Oracle, envie um e-mail com os dados da Oracle utilizando um dos conectores de e-mail de envio, tal Office 365 Outlook. Use os tokens dinâmicos da `Subject` mesa `Body` Oracle para construir o e do seu e-mail:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Guarde** a sua aplicação lógica e, em seguida, selecione **Executar**. Feche o designer, e olhe para a história das corridas para o estado. Se falhar, selecione a linha de mensagem falhada. O designer abre e mostra qual o passo falhado, e também mostra a informação de erro. Se for bem sucedido, deverá receber um e-mail com a informação que adicionou.


### <a name="workflow-ideas"></a>Ideias de fluxo de trabalho

* Você quer monitorizar a hashtag #oracle, e colocar os tweets numa base de dados para que possam ser consultados, e usados dentro de outras aplicações. Numa aplicação lógica, `Twitter - When a new tweet is posted` adicione o gatilho e introduza a **hashtag #oracle.** Em seguida, `Oracle Database - Insert row` adicione a ação e selecione a sua tabela:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* As mensagens são enviadas para uma fila de autocarros de serviço. Queres receber estas mensagens e colocá-las numa base de dados. Numa aplicação lógica, `Service Bus - when a message is received in a queue` adicione o gatilho e selecione a fila. Em seguida, `Oracle Database - Insert row` adicione a ação e selecione a sua tabela:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Erros comuns

#### <a name="error-cannot-reach-the-gateway"></a>**Erro**: Não pode chegar ao Gateway

**Causa**: A porta de dados no local não é capaz de se ligar à nuvem. 

**Mitigação**: Certifique-se de que o seu portal está a funcionar na máquina no local onde o instalou e que pode ligar-se à internet.Recomendamos que não instale o portal num computador que possa ser desligado ou dormir.Também pode reiniciar o serviço de gateway de dados no local (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Erro**: O fornecedor utilizado é depreciado: 'System.Data.OracleClient requer a versão de software do cliente Oracle 8.1.7 ou superior.'. Consulte [https://go.microsoft.com/fwlink/p/?LinkID=272376](https://go.microsoft.com/fwlink/p/?LinkID=272376) a instalação do fornecedor oficial.

**Causa**: O cliente Oracle SDK não está instalado na máquina onde está em funcionamento o portal de dados no local.  

**Resolução**: Descarregue e instale o cliente Oracle SDK no mesmo computador que o portal de dados no local.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Erro**: Tabela '[Nome de quadro]» não define quaisquer colunas-chave

**Causa:** A mesa não tem nenhuma chave primária.  

**Resolução**: O conector Oracle Database requer a sua utilizada uma tabela com uma coluna principal de chaves.

#### <a name="currently-not-supported"></a>Atualmente não suportado

* Vistas 
* Qualquer mesa com chaves compostas
* Tipos de objetos aninhados em tabelas
 
## <a name="connector-specific-details"></a>Detalhes específicos do conector

Veja quaisquer gatilhos e ações definidas no swagger, e consulte também quaisquer limites nos detalhes do [conector](/connectors/oracle/). 

## <a name="get-some-help"></a>Obter alguma ajuda

O [fórum de Aplicações Lógicas Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) é um ótimo local para fazer perguntas, responder a perguntas e ver o que outros utilizadores de Aplicações Lógicas estão a fazer. 

Pode ajudar a melhorar as Aplicações lógicas e os [https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish)conectores votando e submetendo as suas ideias em . 


## <a name="next-steps"></a>Passos seguintes
[Crie uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md)e explore os conectores disponíveis em Aplicações Lógicas na [lista DE APIs.](apis-list.md)
