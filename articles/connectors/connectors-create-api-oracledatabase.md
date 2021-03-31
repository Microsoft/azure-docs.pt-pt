---
title: Ligar à Base de Dados do Oráculo
description: Insira e gere registos com as APIs rest da Oracle Database e aplicações lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/20/2020
tags: connectors
ms.openlocfilehash: 91873a2d6a498712773bfe721653e64c3364666f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92674824"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Introdução ao conector da Oracle Database

Utilizando o conector Oracle Database, cria fluxos de trabalho organizacionais que utilizam dados na sua base de dados existente. Este conector pode ligar-se a uma Oracle Database no local ou a uma máquina virtual Azure com a Oracle Database instalada. Com este conector, pode:

* Construa o seu fluxo de trabalho adicionando um novo cliente a uma base de dados de clientes ou atualizando uma encomenda numa base de dados de encomendas.
* Use ações para obter uma linha de dados, insira uma nova linha e até mesmo apagar. Por exemplo, quando um registo é criado em Dynamics CRM Online (um gatilho), insira uma linha numa Base de Dados do Oráculo (uma ação). 

Este conector não suporta os seguintes itens:

* Vistas 
* Qualquer mesa com chaves compostas
* Tipos de objetos aninhados em tabelas
* Funções de base de dados com valores não escalares

Este artigo mostra-lhe como utilizar o conector Oracle Database numa aplicação lógica.

## <a name="prerequisites"></a>Pré-requisitos

* Versões do Oracle suportadas: 
    * Oracle 9 e posterior
    * Software de cliente Oracle 8.1.7 e posterior

* Instale o portal de dados no local. [Ligar aos dados no local de aplicações lógicas](../logic-apps/logic-apps-gateway-connection.md) lista os passos. O gateway é necessário para ligar a uma Base de Dados Oracle no local, ou a um VM Azure com o Oracle DB instalado. 

    > [!NOTE]
    > O gateway de dados no local funciona como uma ponte, e fornece uma transferência segura de dados entre dados no local (dados que não estão na nuvem) e as suas aplicações lógicas. O mesmo gateway pode ser usado com vários serviços e várias fontes de dados. Por isso, só pode precisar de instalar o portal uma vez.

* Instale o Cliente Oracle na máquina onde instalou o portal de dados no local. Certifique-se de instalar o Fornecedor de Dados Oracle de 64 bits para .NET da Oracle:  

  [64-bit ODAC 12c Release 4 (12.1.0.2.4) para o Windows x64](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Se o cliente Oracle não estiver instalado, ocorre um erro quando se tenta criar ou utilizar a ligação. Veja os erros comuns neste artigo.


## <a name="add-the-connector"></a>Adicione o conector

> [!IMPORTANT]
> Este conector não tem gatilhos. Só tem ações. Assim, quando criar a sua aplicação lógica, adicione outro gatilho para iniciar a sua aplicação lógica, como **Agenda - Recorrência,** ou **Pedido / Resposta - Resposta**. 

1. No [portal Azure,](https://portal.azure.com)crie uma aplicação lógica em branco.

2. No início da sua aplicação lógica, selecione o **pedido /resposta - Pedido** de gatilho: 

    ![Uma caixa de diálogo tem uma caixa para procurar todos os gatilhos. Há também um único gatilho mostrado, "Pedido/Pedido de Resposta", com um botão de seleção.](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Selecione **Guardar**. Quando se guarda, um URL de pedido é gerado automaticamente. 

4. Selecione **Novo passo** e selecione **Adicionar uma ação**. Digite `oracle` para ver as ações disponíveis: 

    ![Uma caixa de pesquisa contém "oráculo". A pesquisa produz um sucesso com o rótulo "Oracle Database". Há uma página com separador, um separador que mostra "TRIGGERS (0)", outro mostrando "AÇÕES (6)". Seis ações estão listadas. A primeira delas é "Get row Preview".](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Esta é também a forma mais rápida de ver os gatilhos e ações disponíveis para qualquer conector. Digite parte do nome do conector, tal como `oracle` . O designer lista quaisquer gatilhos e ações. 

5. Selecione uma das ações, como **Oracle Database - Get row**. Selecione **Connect via gateway de dados no local.** Introduza o nome do servidor Oracle, método de autenticação, nome de utilizador, senha e selecione o gateway:

    ![A caixa de diálogo é intitulada "Oracle Database - Get row". Há uma caixa, verificada, com a etiqueta "Connect via on-in data gateway". Abaixo estão as outras cinco caixas de texto.](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Uma vez ligado, selecione uma tabela da lista e introduza o ID da linha na sua mesa. Precisa saber o identificador da mesa. Se não souber, contacte o administrador da Oracle DB e obtenha a saída de `select * from yourTableName` . Isto dá-lhe a informação identificável que precisa para prosseguir.

    No exemplo seguinte, os dados do trabalho estão a ser devolvidos a partir de uma base de dados de Recursos Humanos: 

    ![A caixa de diálogo intitulada "Get row (Preview)" tem duas caixas de texto: "Table name", que contém "H R JOBS" e tem uma lista de down-down, e "Row i d", que contém "S A _ REP".](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. Neste próximo passo, pode utilizar qualquer um dos outros conectores para construir o seu fluxo de trabalho. Se quiser testar a obtenção de dados da Oracle, envie um e-mail com os dados da Oracle utilizando um dos conectores de e-mail de envio, como o Office 365 Outlook. Use os tokens dinâmicos da mesa Oráculo para construir o `Subject` e `Body` do seu e-mail:

    ![Há duas caixas de diálogo. O endereço "Enviar uma caixa de e-mail" tem caixas para especificar o endereço "Corpo", "Assunto" e "To" do e-mail. A caixa de diálogo "Adicionar conteúdo dinâmico" proporciona uma pesquisa de conteúdo dinâmico a partir das aplicações e serviços do fluxo.](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Guarde** a sua aplicação lógica e, em seguida, selecione **Executar**. Feche o designer, e olhe para a história das corridas para o estado. Se falhar, selecione a linha de mensagem falhada. O designer abre e mostra-lhe qual passo falhou, e também mostra a informação de erro. Se for bem sucedido, deverá receber um e-mail com a informação que adicionou.


### <a name="workflow-ideas"></a>Ideias de fluxo de trabalho

* Você quer monitorizar a hashtag #oracle, e colocar os tweets em uma base de dados para que eles possam ser questionados, e usados dentro de outras aplicações. Numa aplicação lógica, adicione o `Twitter - When a new tweet is posted` gatilho e introduza a **hashtag #oracle.** Em seguida, adicione a `Oracle Database - Insert row` ação e selecione a sua tabela:

    ![A caixa de diálogo "Quando um novo tweet é publicado" mostra "hashtag oráculo" como texto de pesquisa e permite especificar a frequência de verificação. Esta caixa de diálogo leva à caixa de diálogo "Oracle Database" que permite selecionar a ação.](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* As mensagens são enviadas para uma fila de autocarros de serviço. Queres receber estas mensagens e colocá-las numa base de dados. Numa aplicação lógica, adicione o `Service Bus - when a message is received in a queue` gatilho e selecione a fila. Em seguida, adicione a `Oracle Database - Insert row` ação e selecione a sua tabela:

    ![O "Quando uma mensagem é recebida..." a caixa de diálogo mostra "encomendas" como "nome da fila" e permite especificar a frequência de verificação. Esta caixa leva à caixa de diálogo "Inserir linha (pré-visualização)", que permite selecionar "Nome de tabela".](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Erros comuns

#### <a name="error-cannot-reach-the-gateway"></a>**Erro**: Não consigo chegar ao Portal

**Causa**: O portal de dados no local não é capaz de se ligar à nuvem. 

**Mitigação**: Certifique-se de que o seu portal está a funcionar na máquina onde o instalou e que pode ligar-se à internet.  Recomendamos que não instale o portal num computador que possa estar desligado ou adormecido. Também pode reiniciar o serviço de gateway de dados no local (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Erro**: O fornecedor que está a ser utilizado é precedido: 'System.Data.OracleClient requer a versão de software do cliente da Oracle 8.1.7 ou maior.'. Consulte [https://go.microsoft.com/fwlink/p/?LinkID=272376](/power-bi/connect-data/desktop-connect-oracle-database) a instalação do fornecedor oficial.

**Causa**: O cliente Oracle SDK não está instalado na máquina onde o portal de dados no local está em funcionamento.  

**Resolução**: Descarregue e instale o Cliente Oracle SDK no mesmo computador que o portal de dados no local.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Erro**: Tabela '[Tablename]' não define quaisquer colunas-chave

**Causa**: A tabela não tem nenhuma chave primária.  

**Resolução**: O conector Oracle Database exige a utilização de uma tabela com uma coluna-chave primária.
 
## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver quaisquer gatilhos e ações definidos no swagger, e também ver quaisquer limites nos detalhes do [conector](/connectors/oracle/). 

## <a name="get-some-help"></a>Obter alguma ajuda

O [Microsoft Q&Uma página de perguntas para aplicações Azure Logic](/answers/topics/azure-logic-apps.html) é um ótimo lugar para fazer perguntas, responder a perguntas e ver o que outros utilizadores de Aplicações Lógicas estão a fazer. 

Pode ajudar a melhorar as Aplicações lógicas e os conectores votando e submetendo as suas ideias em [https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish) . 


## <a name="next-steps"></a>Passos seguintes
[Crie uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md)e explore os conectores disponíveis em Aplicações Lógicas na [lista de APIs.](apis-list.md)