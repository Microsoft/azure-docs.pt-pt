---
title: Ligue-se a 3270 aplicações nos mainframes da IBM
description: Integrar e automatizar 3270 aplicações orientadas para ecrãs com o Azure utilizando apps azure logic e conector IBM 3270
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
ms.openlocfilehash: 808eef5424d678559ae94ffd04e41eacd0f16aee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371108"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Integre 3270 aplicações orientadas para ecrãs nos mainframes da IBM com o Azure utilizando apps azure logic e conector IBM 3270

> [!NOTE]
> Este conector está em [*pré-visualização pública*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Com as Aplicações Lógicas Azure e o conector IBM 3270, pode aceder e executar aplicações coreframe IBM que normalmente conduz navegando através de 3270 ecrãs de emulador. Desta forma, pode integrar as suas aplicações coreframe IBM com o Azure, microsoft e outras aplicações, serviços e sistemas através da criação de fluxos de trabalho automatizados com apps lógicas do Azure. O conector comunica com os principais quadros da IBM utilizando o protocolo TN3270 e está disponível em todas as regiões de Aplicações Lógicas Azure, com exceção do Governo Azure e da Azure China 21Vianet. Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md)

Este artigo descreve estes aspetos para a utilização do conector 3270: 

* Porquê utilizar o conector IBM 3270 em Aplicações Lógicas Azure e como o conector executa 3270 aplicações orientadas para ecrãs

* Os pré-requisitos e configuração para a utilização do conector 3270

* Os passos para adicionar 3270 ações de conector à sua aplicação lógica

## <a name="why-use-this-connector"></a>Porquê usar este conector?

Para aceder a aplicações nos mainframes da IBM, normalmente utiliza-se um emulador terminal 3270, muitas vezes chamado de "ecrã verde". Este método é uma forma testada no tempo, mas tem limitações. Embora o Host Integration Server (HIS) o ajude a trabalhar diretamente com estas aplicações, por vezes, separar o ecrã e a lógica do negócio pode não ser possível. Ou talvez já não tenhainformações sobre como funcionam as aplicações do anfitrião.

Para alargar estes cenários, o conector IBM 3270 em Aplicações Lógicas Azure trabalha com a Ferramenta de Design 3270, que utiliza para gravar, ou "capturar", os ecrãs hospedeiros utilizados para uma tarefa específica, definir o fluxo de navegação para essa tarefa através da sua aplicação mainframe, e definir o métodos com parâmetros de entrada e saída para essa tarefa. A ferramenta de design converte essa informação em metadados que o conector 3270 utiliza ao chamar uma ação que representa essa tarefa a partir da sua aplicação lógica.

Depois de gerar o ficheiro de metadados a partir da ferramenta de design, adicione esse ficheiro a uma conta de integração no Azure. Desta forma, a sua aplicação lógica pode aceder aos metadados da sua aplicação quando adicionar uma ação de conector 3270. O conector lê o ficheiro de metadados da sua conta de integração, lida com a navegação através dos ecrãs 3270 e apresenta dinamicamente os parâmetros para a ação do conector 3270. Em seguida, pode fornecer dados à aplicação anfitriã, e o conector devolve os resultados à sua aplicação lógica. Desta forma, pode integrar as suas aplicações antigas com o Azure, microsoft e outras aplicações, serviços e sistemas que a Azure Logic Apps suporta.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Recomendado: Um ambiente de [serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  Pode selecionar este ambiente como o local para criar e executar a sua aplicação lógica. Um ISE fornece acesso da sua app lógica a recursos protegidos dentro das redes virtuais do Azure.

* A aplicação lógica para usar para automatizar e executar a sua aplicação orientada para o ecrã 3270

  O conector IBM 3270 não tem gatilhos, por isso use outro gatilho para iniciar a sua aplicação lógica, como o gatilho **recurrence.** Em seguida, pode adicionar 3270 ações de conector. Para começar, [crie uma aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md) 
  Se utilizar um ISE, selecione esse ISE como localização da sua aplicação lógica.

* [Descarregue e instale a Ferramenta de Design 3270.](https://aka.ms/3270-design-tool-download)
O único pré-requisito é [microsoft .NET Framework 4.6.1](https://aka.ms/net-framework-download).

  Esta ferramenta ajuda-o a registar os ecrãs, percursos de navegação, métodos e parâmetros para as tarefas na sua aplicação que adiciona e executa como 3270 ações de conector. A ferramenta gera um ficheiro Host Integration Designer XML (HIDX) que fornece os metadados necessários para o conector utilizar para conduzir a sua aplicação mainframe.
  
  Depois de descarregar e instalar esta ferramenta, siga estes passos para se ligar ao seu anfitrião:

  1. Abra a ferramenta de design 3270. No menu **Sessão,** selecione **Host Sessions**.
  
  1. Forneça as informações do seu servidor de anfitriões TN3270.

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), que é o local onde armazena o seu ficheiro HIDX como um mapa para que a sua aplicação lógica possa aceder aos metadados e definições de métodos nesse ficheiro. 

  Certifique-se de que a sua conta de integração está ligada à aplicação lógica que está a usar. Além disso, se utilizar um ISE, certifique-se de que a localização da sua conta de integração é a mesma ISE que a sua aplicação lógica utiliza.

* Acesso ao servidor TN3270 que acolhe a sua aplicação mainframe

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Criar visão geral dos metadados

Numa aplicação orientada para o ecrã 3270, os ecrãs e os campos de dados são únicos nos seus cenários, pelo que o conector 3270 precisa desta informação sobre a sua app, que pode fornecer como metadados. Estes metadados descrevem informações que ajudam a sua aplicação lógica a identificar e reconhecer ecrãs, descreve como navegar entre ecrãs, onde inserir dados e onde esperar resultados. Para especificar e gerar estes metadados, utiliza a Ferramenta de Design 3270, que o acompanha através *destes modos específicos*, ou estágios, como descrito mais tarde em mais detalhes:

* **Capturar**: Neste modo, regista os ecrãs necessários para completar uma tarefa específica com a sua aplicação mainframe, por exemplo, obtendo um saldo bancário.

* **Navegação**: Neste modo, especifica o plano ou o caminho para como navegar pelos ecrãs da sua aplicação mainframe para a tarefa específica.

* **Métodos**: Neste modo, define-se `GetBalance`o método, por exemplo, que descreve a via de navegação do ecrã. Também escolhe os campos em cada ecrã que se tornam os parâmetros de entrada e saída do método.

### <a name="unsupported-elements"></a>Elementos não apoiados

A ferramenta de design não suporta estes elementos:

* Mapas parciais de suporte de mapeamento básico IBM (BMS): Se importar um mapa BMS, a ferramenta de design ignora definições parciais de ecrã.
* Parâmetros in/out: Não é possível definir os parâmetros In/out.
* Processamento do menu: Não suportado durante a pré-visualização
* Processamento de matriz: Não suportado durante a pré-visualização

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Ecrãs de captura

Neste modo, marca-se um item em cada ecrã 3270 que identifica exclusivamente esse ecrã. Por exemplo, pode especificar uma linha de texto ou um conjunto de condições mais complexas, tais como texto específico e um campo não vazio. Pode gravar estes ecrãs através de uma ligação ao vivo com o servidor anfitrião, ou importar esta informação a partir de um mapa de Suporte básico de Mapeamento (BMS) da IBM. A ligação ao vivo utiliza um emulador TN3270 para a ligação ao hospedeiro. Cada ação do conector deve mapear uma única tarefa que comece por ligar à sua sessão e terminar com a desconexão da sua sessão.

1. Se ainda não o fez, abra a Ferramenta de Design 3270. Na barra de ferramentas, escolha **capturar** para que introduza o modo Capture.

1. Para começar a gravar, prima a tecla F5 ou a partir do menu **Gravação,** **selecione Iniciar a gravação**. 

1. No menu **Sessão,** selecione **Connect**.

1. No painel **'Capturar',** a partir do primeiro ecrã da sua aplicação, passe pela sua aplicação para a tarefa específica que está a gravar.

1. Depois de terminar a tarefa, assine a partir da sua aplicação como normalmente faz.

1. No menu **Sessão,** selecione **Desligar**.

1. Para parar de gravar, prima as teclas Shift + F5 ou a partir do menu **Gravação,** selecione **Stop Recording**.

   Depois de capturar os ecrãs para uma tarefa, a ferramenta de design mostra miniaturas que representam esses ecrãs. Algumas notas sobre estas miniaturas:

   * Incluído com os ecrãs capturados, tem um ecrã chamado "Empty".

     Quando ligar pela primeira vez ao [CICS,](https://www.ibm.com/it-infrastructure/z/cics)deve enviar a tecla "Clear" antes de poder introduzir o nome para a transação que pretende executar. O ecrã onde envia a tecla "Clear" não tem quaisquer *atributos*de reconhecimento , como um título de ecrã, que pode adicionar utilizando o editor de Reconhecimento de Ecrã. Para representar este ecrã, as miniaturas incluem um ecrã chamado "Empty". Mais tarde, pode utilizar este ecrã para representar o ecrã onde introduz o nome da transação.

   * Por padrão, o nome para um ecrã capturado utiliza a primeira palavra no ecrã. Se esse nome já existir, a ferramenta de design anexa o nome com um sublinhado e um número, por exemplo, "WBGB" e "WBGB_1".

1. Para dar um nome mais significativo a um ecrã capturado, siga estes passos:

   1. No painel de ecrãs de **anfitriões,** selecione o ecrã que pretende mudar de nome.

   1. No mesmo painel, perto do fundo no mesmo painel, encontre a propriedade **Screen Name.**

   1. Mude o nome do ecrã atual para um nome mais descritivo.

1. Agora especifique os campos para identificar cada ecrã.

   Com o fluxo de dados 3270, os ecrãs não têm identificadores predefinidos, pelo que é necessário selecionar texto único em cada ecrã. Para cenários complexos, pode especificar múltiplas condições, por exemplo, texto único e um campo com uma condição específica.

Depois de terminar de selecionar os campos de reconhecimento, passe para o modo seguinte.

### <a name="conditions-for-identifying-repeated-screens"></a>Condições para identificar ecrãs repetidos

Para que o conector navegue e diferencie entre ecrãs, normalmente encontra texto único num ecrã que pode utilizar como identificador entre os ecrãs capturados. Para ecrãs repetidos, pode precisar de mais métodos de identificação. Por exemplo, suponha que tenha dois ecrãs que parecem iguais, exceto que um ecrã devolve um valor válido, enquanto o outro ecrã devolve uma mensagem de erro.

Na ferramenta de design, pode adicionar *atributos*de reconhecimento, por exemplo, um título de ecrã como "Obter Saldo de Conta", utilizando o editor de Reconhecimento de Ecrã. Se tiver um caminho bifurcado e ambos os ramos devolverem o mesmo ecrã, mas com resultados diferentes, precisa de outros atributos de reconhecimento. No tempo de execução, o conector utiliza estes atributos para determinar o ramo e o garfo atuais. Aqui estão as condições que pode usar:

* Valor específico: Este valor corresponde à corda especificada no local especificado.
* NÃO um valor específico: Este valor não corresponde à corda especificada no local especificado.
* Vazio: Este campo está vazio.
* Não está vazio: este campo não está vazio.

Para saber mais, consulte o plano de [navegação Exemplo](#example-plan) mais tarde neste tópico.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Definir planos de navegação

Neste modo, define o fluxo ou os passos para navegar através dos ecrãs da sua aplicação mainframe para a sua tarefa específica. Por exemplo, por vezes, pode ter mais do que um caminho que a sua aplicação pode tomar onde um caminho produz o resultado correto, enquanto o outro caminho produz um erro. Para cada ecrã, especifique as teclas necessárias para se deslocar para o ecrã seguinte, tais como `CICSPROD <enter>`.

> [!TIP]
> Se estiver a automatizar várias tarefas que utilizam os mesmos ecrãs de ligação e desconexão, a ferramenta de design fornece tipos especiais de planos de ligação e desconexão. Quando definir estes planos, pode adicioná-los ao início e fim do seu plano de navegação.

### <a name="guidelines-for-plan-definitions"></a>Orientações para definições de planos

* Inclua todos os ecrãs, começando com a ligação e terminando com a desconexão.

* Pode criar um plano autónomo ou utilizar os planos Connect and Disconnect, que permitem reutilizar uma série de ecrãs comuns a todas as suas transações.

  * O último ecrã do seu plano Connect deve ser o mesmo ecrã que o primeiro ecrã do seu plano de navegação.

  * O primeiro ecrã do seu plano de desconexão deve ser o mesmo ecrã que o último ecrã do seu plano de navegação.

* Os ecrãs capturados podem conter muitos ecrãs repetidos, por isso selecione e utilize apenas uma instância de quaisquer ecrãs repetidos no seu plano. Aqui estão alguns exemplos de ecrãs repetidos:

  * O ecrã de inscrição, por exemplo, o ecrã **MSG-10**
  * O ecrã de boas-vindas para o CICS
  * O ecrã "Claro" ou **Vazio**

<a name="create-plans"></a>

### <a name="create-plans"></a>Criar planos

1. Na barra de ferramentas da Ferramenta de Design 3270, escolha **navegação** para que introduza o modo navegação.

1. Para iniciar o seu plano, no painel **navegação,** escolha **Novo Plano**.

1. Em **'Escolha novo nome de plano',** insira um nome para o seu plano. Na lista **Tipo,** selecione o tipo de plano:

   | Tipo de plano | Descrição |
   |-----------|-------------|
   | **Processo** | Para planos autónomos ou combinados |
   | **Ligar** | Para planos de Ligação |
   | **Desligar** | Para planos de desconexão |
   |||

1. A partir do painel de **ecrãs hospedeiros,** arraste as miniaturas capturadas para a superfície do plano de navegação no painel **navigation.**

   Para representar o ecrã em branco onde introduz o nome da transação, utilize o ecrã "Vazio".

1. Organize os ecrãs na ordem que descreve a tarefa que está a definir.

1. Para definir o percurso de fluxo entre ecrãs, incluindo garfos e uniões, na barra de ferramentas da ferramenta de design, escolha **Flow**.

1. Escolha o primeiro ecrã do fluxo. Arraste e desenhe uma ligação com o próximo ecrã no fluxo.

1. Para cada ecrã, forneça os valores para a propriedade **chave de ajuda** (Identificador de atenção) e para a propriedade Texto **Fixo,** que move o fluxo para o ecrã seguinte.

   Pode ter apenas a chave AID, ou tanto a chave AID como o texto fixo.

Depois de terminar o seu plano de navegação, pode [definir métodos no modo seguinte](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Exemplo

Neste exemplo, suponha que execute uma transação CICS chamada "WBGB" que tem estes passos: 

* No primeiro ecrã, introduz-se um nome e uma conta.
* No segundo ecrã, obtém-se o saldo da conta.
* Sais para o ecrã "Vazio".
* Assinas do CICS para o ecrã "MSG-10".

Também suponha que repita estes passos, mas introduze dados incorretos para que possa capturar o ecrã que mostra o erro. Aqui estão os ecrãs que captura:

* MSG-10
* Cics Bem-vindo
* Vazio
* WBGB_1 (entrada)
* WBGB_2 (erro)
* Empty_1
* MSG-10_1

Apesar de muitos ecrãs aqui obterem nomes únicos, alguns ecrãs são o mesmo ecrã, por exemplo, "MSG-10" e "Empty". Para um ecrã repetido, utilize apenas um exemplo para esse ecrã no seu plano. Aqui estão exemplos que mostram como um plano autónomo, plano de ligação, plano de desconexão e um plano combinado podem parecer:

* Plano autónomo

  ![Plano de navegação autónoma](./media/connectors-create-api-3270/standalone-plan.png)

* Plano de ligação

  ![Plano de ligação](./media/connectors-create-api-3270/connect-plan.png)

* Plano de desconexão

  ![Plano de desconexão](./media/connectors-create-api-3270/disconnect-plan.png)

* Plano combinado

  ![Plano combinado](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Exemplo: Identificar ecrãs repetidos

Para que o conector navegue e diferencia os ecrãs, normalmente encontra texto único num ecrã que pode utilizar como identificador através dos ecrãs capturados. Para ecrãs repetidos, pode precisar de mais métodos de identificação. O plano de exemplo tem um garfo onde você pode obter ecrãs que parecem semelhantes. Um ecrã devolve um saldo de conta, enquanto o outro ecrã devolve uma mensagem de erro.

A ferramenta de design permite adicionar atributos de reconhecimento, por exemplo, um título de ecrã chamado "Obter Saldo de Conta", utilizando o editor de Reconhecimento de Ecrã. No caso de ecrãs semelhantes, precisa de outros atributos. No tempo de execução, o conector utiliza estes atributos para determinar o ramo e o garfo.

* No ramo que devolve entrada válida, que é o ecrã com o saldo da conta, pode adicionar um campo que tem uma condição "não vazia".

* No ramo que regressa com um erro, pode adicionar um campo que tem uma condição "vazia".

<a name="define-method"></a>

## <a name="define-methods"></a>Definir métodos

Neste modo, define um método associado ao seu plano de navegação. Para cada parâmetro de método, especifice o tipo de dados, como uma cadeia, inteiro, data ou hora, e assim por diante. Quando terminar, pode testar o seu método no hospedeiro ao vivo e confirmar que o método funciona como esperado. Em seguida, gera o ficheiro de metadados, ou ficheiro Host Integration Designer XML (HIDX), que agora tem as definições de método a utilizar para criar e executar uma ação para o conector IBM 3270.

1. Na barra de ferramentas da Ferramenta de Design 3270, escolha **métodos** para que introduza o modo Métodos. 

1. No painel **Navegação,** selecione o ecrã que tem os campos de entrada que deseja.

1. Para adicionar o primeiro parâmetro de entrada para o seu método, siga estes passos:

   1. No painel **Capture,** no ecrã emulador 3270, escolha todo o campo, não apenas texto dentro do campo, que deseja como primeira entrada.

      > [!TIP]
      > Para visualizar todos os campos e certifique-se de que seleciona o campo completo, no menu **'Ver',** selecione **Todos os Campos**.

   1. Na barra de ferramentas da ferramenta de design, escolha campo de **entrada**. 

   Para adicionar mais parâmetros de entrada, repita os passos anteriores para cada parâmetro.

1. Para adicionar o primeiro parâmetro de saída para o seu método, siga estes passos:

   1. No painel **Capture,** no ecrã emulador 3270, escolha todo o campo, não apenas texto dentro do campo, que deseja como primeira saída.

      > [!TIP]
      > Para visualizar todos os campos e certifique-se de que seleciona o campo completo, no menu **'Ver',** selecione **Todos os Campos**.

   1. Na barra de ferramentas da ferramenta de design, escolha **campo de saída**.

   Para adicionar mais parâmetros de saída, repita os passos anteriores para cada parâmetro.

1. Depois de adicionar todos os parâmetros do seu método, defina estas propriedades para cada parâmetro:

   | Nome da propriedade | Valores possíveis | 
   |---------------|-----------------|
   | **Tipo de Dados** | Byte, Hora da Data, Decimal, Int, Long, Short, String |
   | **Técnica de enchimento de campo** | Os parâmetros suportam estes tipos de enchimento, preenchendo com espaços em branco, se necessário: <p><p>- **Tipo**: Introduza caracteres sequencialmente no campo. <p>- **Enchimento**: Substitua o conteúdo do campo por caracteres, preenchendo com espaços em branco, se necessário. <p>- **AéeofType**: Limpe o campo e, em seguida, introduza caracteres sequencialmente no campo. |
   | **Cadeia de formato** | Alguns tipos de dados de parâmetros utilizam uma cadeia de formato, que informa o conector 3270 como converter texto do ecrã num tipo de dados .NET: <p><p>- **DataTempo**: A cadeia de formato DateTime segue as cordas de data e formato de [tempo .NET.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) Por exemplo, `06/30/2019` a data `MM/dd/yyyy`utiliza a cadeia de formato . <p>- **Decimal:** A cadeia de formato decimal utiliza a [cláusula cobol Imagem](https://www.ibm.com/support/knowledgecenter/SS6SG3_5.2.0/com.ibm.cobol52.ent.doc/PGandLR/ref/rlddepic.html). Por exemplo, `100.35` o número `999V99`utiliza a cadeia de formato . |
   |||

## <a name="save-and-view-metadata"></a>Guardar e ver metadados

Depois de definir o seu método, mas antes de testar o seu método, guarde todas as informações que definiu até agora para um ficheiro RAP (.rap).
Pode guardar para este ficheiro RAP a qualquer momento durante qualquer modo. A ferramenta de design também inclui um ficheiro RAP de amostra que pode abrir e rever navegando na pasta de instalação da ferramenta de design neste local e abrindo o ficheiro "WoodgroveBank.rap":

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

No entanto, se tentar guardar alterações no ficheiro RAP da amostra ou gerar um ficheiro HIDX a partir do ficheiro RAP da amostra enquanto o ficheiro permanece na pasta de instalação da ferramenta de design, poderá obter um erro de "acesso negado". Por predefinição, a ferramenta de design instala-se na pasta 'Ficheiros do Programa' sem permissões elevadas. Se tiver um erro, experimente uma destas soluções:

* Copie o ficheiro da amostra para um local diferente.
* Execute a ferramenta de design como administrador.
* Torne-se o proprietário da pasta SDK.

## <a name="test-your-method"></a>Teste o seu método

1. Para executar o seu método contra o hospedeiro vivo, enquanto ainda estiver no modo Métodos, prima a tecla F5 ou a partir da barra de ferramentas da ferramenta de design, escolha **Executar**.

   > [!TIP]
   > Pode alterar os modos a qualquer momento. No menu **'Ficheiro',** selecione **Modo**, e, em seguida, selecione o modo que deseja.

1. Insira os valores dos seus parâmetros e escolha **OK**.

1. Para continuar ao próximo ecrã, escolha **Next**.

1. Quando terminar, escolha **Done**, que mostra os valores dos parâmetros de saída.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>Gerar e carregar ficheiro HIDX

Quando estiver pronto, gere o ficheiro HIDX para que possa fazer o upload para a sua conta de integração. A Ferramenta de Design 3270 cria o ficheiro HIDX numa nova subpasta onde guardou o ficheiro RAP.

1. Na barra de ferramentas da Ferramenta de Design 3270, escolha **Generate Code**.

1. Vá para a pasta que contém o seu ficheiro RAP e abra a subpasta que a ferramenta criou depois de gerar o seu ficheiro HIDX. Confirme que a ferramenta criou o ficheiro HIDX.

1. Inscreva-se no [portal Azure](https://portal.azure.com)e encontre a sua conta de integração.

1. Adicione o seu ficheiro HIDX como um mapa à sua conta de [integração, siga estes passos semelhantes para adicionar mapas,](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md)mas quando selecionar o tipo de mapa, selecione **HIDX**.

Mais tarde neste tópico, quando adiciona uma ação IBM 3270 à sua aplicação lógica pela primeira vez, é-lhe solicitado que crie uma ligação entre a sua aplicação lógica e o servidor anfitrião, fornecendo informações de ligação, como os nomes para a sua conta de integração e servidor anfitrião . Depois de criar a ligação, pode selecionar o ficheiro HIDX anteriormente adicionado, o método a executar e os parâmetros a utilizar.

Quando terminar todos estes passos, pode utilizar a ação que cria na sua aplicação lógica para se conectar ao seu mainframe IBM, conduzir ecrãs para a sua aplicação, introduzir dados, devolver resultados, e assim por diante. Também pode continuar a adicionar outras ações à sua app lógica para integrar com outras aplicações, serviços e sistemas.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>Executar ações da IBM 3270

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se ainda não estiver aberta.

1. Sob o último passo em que pretende adicionar uma ação, escolha **novo passo,** e selecione **Adicionar uma ação**. 

1. Sob a caixa de pesquisa, escolha **Enterprise**. Na caixa de pesquisa, introduza o "3270" como filtro. A partir da lista de ações, selecione esta ação: **Executa um programa mainframe sobre uma ligação TN3270**

   ![Selecione ação 3270](./media/connectors-create-api-3270/select-3270-action.png)

   Para adicionar uma ação entre passos, mova o ponteiro sobre a seta entre os degraus. 
   Escolha o sinal**+** de mais () que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Se ainda não existir nenhuma ligação, forneça as informações necessárias para a sua ligação e escolha **Criar**.

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome de ligação** | Sim | <*nome de ligação*> | O nome da sua ligação |
   | **ID da conta de integração** | Sim | <*integração-nome de conta*> | O nome da sua conta de integração |
   | **URL da Conta de Integração SAS** | Sim | <*conta de integração-SAS-URL*> | O URL de Assinatura de Acesso Partilhado (SAS) da sua conta de integração, que pode gerar a partir das definições da sua conta de integração no portal Azure. <p>1. No menu da sua conta de integração, em **Definições,** selecione **URL callback**. <br>2. No painel da direita, copie o valor **url de callback gerado.** |
   | **Servidor** | Sim | <*TN3270-servidor-nome*> | O nome do servidor para o seu serviço TN3270 |
   | **Porto** | Não | <*TN3270-servidor-porta*> | A porta utilizada pelo seu servidor TN3270. Se ficar em branco, `23` o conector utiliza-se como valor predefinido. |
   | **Tipo de Dispositivo** | Não | <*Ibm-terminal-modelo*> | O nome ou número do modelo para o terminal IBM emular. Se ficar em branco, o conector utiliza valores predefinidos. |
   | **Página de Códigos** | Não | <*número de página de código*> | O número da página de código para o anfitrião. Se ficar em branco, `37` o conector utiliza-se como valor predefinido. |
   | **Nome de unidade lógica** | Não | <*nome de unidade lógica*> | O nome específico da unidade lógica a solicitar ao anfitrião |
   | **Ativar o SSL?** | Não | Dentro ou fora | Ligue ou desligue a encriptação TLS. |
   | **Validar o certificado ssl anfitrião?** | Não | Dentro ou fora | Ligue ou desligue a validação para o certificado do servidor. |
   ||||

   Por exemplo:

   ![Propriedades de ligação](./media/connectors-create-api-3270/connection-properties.png)

1. Fornecer as informações necessárias para a ação:

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome Hidx** | Sim | <*Nome de ficheiro HIDX*> | Selecione o ficheiro 3270 HIDX que pretende utilizar. |
   | **Nome do método** | Sim | <*nome do método*> | Selecione o método no ficheiro HIDX que pretende utilizar. Depois de selecionar um método, aparece a nova lista de **parâmetros Adicionar** para selecionar parâmetros para utilizar com esse método. |
   ||||

   Por exemplo:

   **Selecione o ficheiro HIDX**

   ![Selecione ficheiro HIDX](./media/connectors-create-api-3270/select-hidx-file.png)

   **Selecione o método**

   ![Método selecionado](./media/connectors-create-api-3270/select-method.png)

   **Selecione os parâmetros**

   ![Selecionar parâmetros](./media/connectors-create-api-3270/add-parameters.png)

1. Quando terminar, guarde e execute a sua aplicação lógica.

   Depois da sua aplicação lógica terminar de funcionar, os passos da corrida aparecem. 
   Os passos bem sucedidos mostram marcas de verificação, enquanto passos mal sucedidos mostram a letra "X".

1. Para rever as inputs e saídas para cada passo, expanda esse passo.

1. Para rever as saídas, escolha **Ver saídas brutas**.

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como gatilhos, ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/si3270/).

> [!NOTE]
> Para aplicações lógicas num ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão do conector com o rótulo ISE utiliza os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
