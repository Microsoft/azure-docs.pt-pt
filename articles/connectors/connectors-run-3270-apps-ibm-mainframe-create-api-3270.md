---
title: Ligar a aplicações de 3270 em mainframes IBM e o Azure - Azure Logic Apps
description: Faça a integração e automatizar 3270 aplicações baseadas em tela com o Azure utilizando o conector Azure Logic Apps e o IBM 3270
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
ms.openlocfilehash: ebf858ba86758b11ee896d745d70bdf2f0d0cde6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57878385"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Integre 3270 aplicações baseadas em tela em IBM mainframes no Azure utilizando o conector Azure Logic Apps e o IBM 3270

> [!NOTE]
> Este conector está em [ *pré-visualização pública*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Com o Azure Logic Apps e o conector do IBM 3270, pode aceder e executar aplicações de mainframe IBM que normalmente de unidade ao navegar por meio de 3270 telas do emulador. Dessa forma, pode integrar seus aplicativos de mainframe IBM com o Azure, Microsoft e outras aplicações, serviços e sistemas através da criação de fluxos de trabalho automatizados com o Azure Logic Apps. O conector comunica com IBM mainframes utilizando o protocolo de TN3270 e está disponível em todas as regiões do Azure Logic Apps, exceto para o Azure Government e Azure China 21Vianet. Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Este artigo descreve esses aspectos para utilizar o conector de 3270: 

* Porquê utilizar o conector do IBM 3270 no Azure Logic Apps e como o conector seja executado 3270 aplicações baseadas em tela

* Os pré-requisitos e a configuração para utilizar o conector de 3270

* Os passos para adicionar ações de conector 3270 direcionado à sua aplicação lógica

## <a name="why-use-this-connector"></a>Por que usar este conector?

Para aceder às aplicações em IBM mainframes, geralmente usa um emulador do terminal 3270 direcionado, costuma ser chamado de uma "tela verde". Esse método é uma maneira comprovada, mas tem limitações. Embora o Host Integration Server (HIS) a que trabalhar diretamente com estas aplicações, por vezes, separando a ecrã e lógica de negócio pode não ser possível. Ou, talvez já não tem informações de como funcionam os aplicativos host.

Para expandir estes cenários, o conector do IBM 3270 no Azure Logic Apps funciona com a ferramenta de Design de 3270, o que utilizar para o registo, ou "capturar", os ecrãs de anfitrião utilizados para uma tarefa específica, definir o fluxo da navegação para essa tarefa por meio de seu aplicativo de mainframe e definir o métodos com parâmetros de entrada e saídos para essa tarefa. A ferramenta de design converte essa informação em metadados que o conector de 3270 utiliza quando chamar uma ação que representa essa tarefa da sua aplicação lógica.

Depois de gerar o ficheiro de metadados a partir da ferramenta de design, adicionar esse arquivo para uma conta de integração no Azure. Dessa forma, a aplicação lógica pode acessar os metadados da sua aplicação ao adicionar uma ação de conector 3270. O conector lê o ficheiro de metadados da sua conta de integração, processa a navegação por meio de ecrãs de 3270 e dinamicamente apresenta os parâmetros para a ação de conector 3270. Em seguida, pode fornecer os seus dados para o aplicativo host e o conector devolve os resultados à sua aplicação lógica. Dessa forma, pode integrar as suas aplicações legadas com o Azure, Microsoft e outras aplicações, serviços e sistemas que suporte o Azure Logic Apps.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Recomendado: Um [o ambiente de serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  Pode selecionar esse ambiente como a localização para criar e executar a aplicação lógica. Um ISE fornece acesso da sua aplicação lógica para recursos que estão protegidos dentro das redes virtuais do Azure.

* A aplicação de lógica para utilizar para automatizar e executar a aplicação de baseada no ecrã de 3270

  O conector do IBM 3270 não tem acionadores, use outro acionador para iniciar a sua aplicação lógica, como o **periodicidade** acionador. Em seguida, pode adicionar 3270 ações de conector. Para começar, [criar uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Se usar um ISE, selecione esse ISE como localização da sua aplicação lógica.

* [Transferir e instalar a ferramenta de Design 3270](https://aka.ms/3270-design-tool-download).
É o único pré-requisito [Microsoft .NET Framework 4.6.1](https://aka.ms/net-framework-download).

  Isto ajuda a ferramenta gravar os ecrãs, os caminhos de navegação, métodos e parâmetros para as tarefas na sua aplicação que adicionar e executar como 3270 ações de conector. A ferramenta gera um ficheiro XML de Designer de integração de Host (HIDX) que fornece os metadados necessários para o conector para utilizar para orientar a sua aplicação de mainframe.
  
  Após baixar e instalar esta ferramenta, siga estes passos para ligar ao seu anfitrião:

  1. Abra a ferramenta de Design 3270. Partir do **sessão** menu, selecione **anfitrião de sessões**.
  
  1. Forneça o seu anfitrião TN3270 informações do servidor.

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), que é o local onde guardou o ficheiro HIDX como um mapa para que a aplicação lógica pode acessar as definições de metadados e o método nesse ficheiro. 

  Certifique-se de que a sua conta de integração está ligada à aplicação lógica que está a utilizar. Além disso, se usar um ISE, certifique-se de que localização da sua conta de integração é o ISE do mesmo que a aplicação lógica utiliza.

* Acesso ao servidor TN3270 que aloja a aplicação de mainframe

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Criar descrição geral de metadados

Num aplicativo controlado por tela 3270 direcionado, os campos de dados e telas são exclusivos para seus cenários, por isso, o conector de 3270 tem estas informações sobre a sua aplicação, que pode fornecer como metadados. Estes metadados descrevem informações que ajuda a sua aplicação lógica, identificar e reconhecer os ecrãs, descreve como navegar entre ecrãs, onde os dados de entrada e onde esperar resultados. Para especificar e gerar estes metadados, use a ferramenta de Design de 3270, que explica como eles específico *modos*, ou fases, conforme descrito posteriormente em mais detalhes:

* **Capturar**: Neste modo, gravar os ecrãs seguintes necessários para concluir uma tarefa específica com a sua aplicação de mainframe, por exemplo, obter um equilíbrio banco.

* **Navegação**: Neste modo, especifique o plano ou o caminho para saber como navegar pelas telas de seu aplicativo de mainframe para a tarefa específica.

* **métodos**: Neste modo, define o método, por exemplo, `GetBalance`, que descreve o caminho de navegação do ecrã. Também é escolher os campos em cada ecrã que tornam-se a entrada do método e parâmetros de saída.

### <a name="unsupported-elements"></a>Elementos não suportados

A ferramenta de design não suporta estes elementos:

* Mapas de suporte de mapeamento básica IBM (BMS) parcial: Se importar um mapa BMS, a ferramenta de design ignora as definições de ecrã parcial.
* Entrada/saída parâmetros: Não é possível definir parâmetros de entrada/saída.
* Processamento de menu: Não é suportada durante a pré-visualização
* Processamento de matriz: Não é suportada durante a pré-visualização

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Captura de telas

Neste modo, marcar um item em cada ecrã 3270 que identifica exclusivamente essa tela. Por exemplo, poderá especificar uma linha de texto ou um conjunto mais complexo de condições, como texto específico e um campo de não vazios. Pode registar estes ecrãs através de uma ligação em direto para o servidor de anfitrião ou importar essas informações a partir de um mapa de suporte de mapeamento básica IBM (BMS). A ligação em direto utiliza um emulador TN3270 para ligar ao anfitrião. Cada ação de conector tem de mapear a uma única tarefa que começa com a ligação à sua sessão e termine com desligar da sua sessão.

1. Se ainda não o tiver feito, abra a ferramenta de Design de 3270. Na barra de ferramentas, escolha **capturar** para que entrar no modo de captura.

1. Para iniciar a gravação, pressione a tecla F5, ou a partir da **gravação** menu, selecione **Iniciar gravação**. 

1. Partir do **sessão** menu, selecione **Connect**.

1. Na **capturar** painel, a partir do primeiro ecrã na sua aplicação, o passo através da aplicação para a tarefa específica que está a gravação.

1. Depois de concluir a tarefa, termine sessão da sua aplicação como geralmente faz.

1. Partir do **sessão** menu, selecione **desligar**.

1. Pretende parar a gravação, pressione a Shift + F5 chaves, ou a partir da **gravação** menu, selecione **parar gravação**.

   Depois de capturar os ecrãs de uma tarefa, a ferramenta mostra miniaturas que representam essas telas. Algumas observações a respeito dessas miniaturas:

   * Incluído com telas capturadas, tem uma tela com o nome "Vazio".

     Quando primeiro estabeleça ligação ao [CICS](https://www.ibm.com/it-infrastructure/z/cics), terá de enviar a chave "Clear" antes de introduzir o nome para a transação que pretende executar. A tela onde envia a chave "Clear" não tem nenhuma *atributos de reconhecimento*, por exemplo, um título do ecrã, que pode adicionar com o editor de reconhecimento de tela. Para representar neste ecrã, as miniaturas inclui uma tela com o nome "Vazia". Mais tarde pode utilizar este ecrã para representar a tela onde introduzir o nome da transação.

   * Por predefinição, o nome de uma tela capturada utiliza a primeira palavra na tela. Se esse nome já existir, a ferramenta de design acrescenta o nome com um caráter de sublinhado e um número, por exemplo, "WBGB" e "WBGB_1".

1. Para dar um nome mais significativo para um ecrã capturado, siga estes passos:

   1. Na **telas de anfitrião** painel, selecione o ecrã que pretende mudar o nome.

   1. No painel do mesmo, junto à parte inferior no mesmo painel, localize a **pseudónimo** propriedade.

   1. Altere o nome do ecrã atual para um nome mais descritivo.

1. Agora, especifique os campos para identificar cada ecrã.

   Com o fluxo de dados 3270 direcionado, telas não tem identificadores de padrão, por isso terá de selecionar o texto exclusivo em cada ecrã. Para cenários complexos, pode especificar várias condições, por exemplo, texto exclusivo e um campo com uma condição específica.

Depois de acabar de selecionar os campos de reconhecimento, mova para o modo seguinte.

### <a name="conditions-for-identifying-repeated-screens"></a>Condições para identificar os ecrãs repetidos

Para o conector navegar e diferenciar entre ecrãs, normalmente encontramos texto exclusivo numa tela que pode utilizar como um identificador de entre as telas capturadas. Para telas repetidas, poderá precisar de mais métodos de identificação. Por exemplo, suponhamos que tenha dois ecrãs que a mesma, exceto uma única tela retorna um valor válido, enquanto outro ecrã devolve uma mensagem de erro.

Na ferramenta de design, pode adicionar *atributos de reconhecimento*, por exemplo, um título do ecrã, como "Obter o saldo da conta", com o editor de reconhecimento de tela. Se tiver um caminho bifurcado e as duas ramificações devolvem o mesmo ecrã, mas com resultados diferentes, terá de outros atributos de reconhecimento. Tempo de execução, o conector utiliza estes atributos para determinar o ramo atual e o fork. Aqui estão as condições que pode utilizar:

* Valor específico: Este valor corresponde à cadeia especificada na localização especificada.
* Não é um valor específico: Este valor não corresponde a cadeia especificada na localização especificada.
* Vazio: Este campo está vazio.
* NÃO pode estar vazio: Este campo não está vazio.

Para obter mais informações, consulte a [plano de navegação de exemplo](#example-plan) mais adiante neste tópico.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Definir os planos de navegação

Neste modo, define o flow ou os passos para navegar através de telas de seu aplicativo de mainframe para a sua tarefa específica. Por exemplo, por vezes, pode ter mais do que um caminho que a aplicação pode efetuar em que um caminho produz o resultado correto, enquanto o outro caminho produz um erro. Para cada ecrã, especifique os pressionamentos de tecla necessários para mover para o ecrã seguinte, tais como `CICSPROD <enter>`.

> [!TIP]
> Se estiver a automatizar várias tarefas que utilizam o mesmo ligar e desligar telas, a ferramenta de design fornece tipos especiais de planos de ligar e desligar. Quando define estes planos, pode adicioná-los para o início e no fim do seu plano de navegação.

### <a name="guidelines-for-plan-definitions"></a>Diretrizes para definições de plano

* Inclua todas as telas, começando com a ligar e terminando a desligar.

* Pode criar um plano autónomo ou utilizar os planos ligar e desligar, que lhe permite reutilizar uma série de telas comuns a todas as suas transações.

  * O último ecrã do seu plano do Connect tem de ser o mesmo ecrã como a primeira tela no seu plano de navegação.

  * A primeira tela do seu plano de desligar tem de ser mesmo ecrã como último ecrã do seu plano de navegação.

* Telas capturadas podem conter várias telas repetidas, por isso, selecione e utilizar apenas uma instância de qualquer telas repetidas no seu plano. Aqui estão alguns exemplos de telas repetidos:

  * O início de sessão na tela, por exemplo, o **MSG 10** ecrã
  * A tela de boas-vindas de CICS
  * "Limpar" ou **vazio** ecrã

<a name="create-plans"></a>

### <a name="create-plans"></a>Criar planos

1. Na barra de ferramentas da ferramenta de Design 3270, escolha **navegação** para que entrar no modo de navegação.

1. Para iniciar o seu plano, na **navegação** painel, escolha **novo plano**.

1. Sob **escolha o nome do novo plano**, introduza um nome para o seu plano. Partir do **tipo** , selecione o tipo de plano:

   | Tipo de plano | Descrição |
   |-----------|-------------|
   | **Processo** | Para autónoma ou planos combinados |
   | **Ligar** | Para ligar os planos |
   | **Desligar** | Para os planos de desligar |
   |||

1. Do **telas de anfitrião** painel, arraste as miniaturas capturadas para o plano de navegação à tona no **navegação** painel.

   Para representar a tela em branco, onde introduzir o nome da transação, utilize o ecrã "Vazio".

1. Dispor os ecrãs na ordem em que descreve a tarefa que está a definir.

1. Para definir o caminho de fluxo entre ecrãs, incluindo bifurcações e associações, na barra de ferramentas da ferramenta de design, escolha **fluxo**.

1. Escolha a primeira tela no fluxo. Arraste e desenhar uma ligação para a próxima tela no fluxo.

1. Para cada ecrã, forneça os valores para o **material de chave** propriedade (identificador de atenção) e para o **fixo de texto** propriedade, que move o fluxo para a próxima tela.

   Pode ter apenas a chave de auxílio, ou a ajuda chave e fixo de texto.

Depois de concluir o seu plano de navegação, poderá [definir métodos no modo de seguinte](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Exemplo

Neste exemplo, suponha que executa uma transação de CICS com o nome "WBGB" com estes passos: 

* No primeiro ecrã, introduza um nome e uma conta.
* No segundo ecrã, obtém o saldo da conta.
* Saia na tela "Vazia".
* Terminar sessão CICS na tela "MSG-10".

Suponha também que repetir essas etapas, mas inserir dados incorretos para que pode capturar o ecrã que mostra o erro. Aqui estão os ecrãs que capturar:

* MSG-10
* Bem-vindo do CICS
* Vazio
* WBGB_1 (input)
* WBGB_2 (erro)
* Empty_1
* MSG-10_1

Embora muitas telas aqui obtém nomes exclusivos, algumas telas estiverem mesmo ecrã, por exemplo, "MSG-10" e "Vazia". Para uma tela repetida, utilize apenas uma instância para essa tela no seu plano. Seguem-se exemplos que mostram como podem parecer um plano autónomo, plano de ligar, desligar plano e um plano combinado:

* Plano autónomo

  ![Plano de navegação autónomo](./media/connectors-create-api-3270/standalone-plan.png)

* Ligar o plano

  ![Ligar o plano](./media/connectors-create-api-3270/connect-plan.png)

* Desligar o plano

  ![Desligar o plano](./media/connectors-create-api-3270/disconnect-plan.png)

* Plano combinado

  ![Plano combinado](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Exemplo: Identificar telas repetidas

Para o conector navegar e diferenciar os ecrãs, normalmente encontramos texto exclusivo numa tela que pode utilizar como um identificador de entre as telas capturadas. Para telas repetidas, poderá precisar de mais métodos de identificação. O plano de exemplo tem um fork onde pode obter telas que ter um aspeto semelhantes. Um ecrã devolve um saldo de conta, enquanto outro ecrã devolve uma mensagem de erro.

A ferramenta de design permite-lhe adicionar atributos de reconhecimento, por exemplo, um título do ecrã com o nome "Obter o saldo da conta", ao utilizar o reconhecimento de tela editor. No caso com telas semelhante, terá de outros atributos. Tempo de execução, o conector utiliza estes atributos para determinar o ramo e o fork.

* No ramo que retorna uma entrada válida, que é a tela com o saldo da conta, pode adicionar um campo que tem uma condição "não vazia".

* No ramo que retorna um erro, pode adicionar um campo que tem uma condição "vazia".

<a name="define-method"></a>

## <a name="define-methods"></a>Definir métodos

Neste modo, define um método que está associada com o seu plano de navegação. Para cada parâmetro de método, especifique o tipo de dados, como uma cadeia de caracteres, número inteiro, data ou hora e assim por diante. Quando tiver terminado, pode testar seu método no anfitrião em direto e confirme que o método funciona conforme esperado. Em seguida, gerar o ficheiro de metadados, ou o ficheiro XML de Designer de integração de Host (HIDX), que tem agora as definições de método a utilizar para criar e executar uma ação para o conector do IBM 3270.

1. Na barra de ferramentas da ferramenta de Design 3270, escolha **métodos** para que entrar no modo de métodos. 

1. Na **navegação** painel, selecione o ecrã que tem os campos de entrada que pretende.

1. Para adicionar o primeiro parâmetro de entrada para o seu método, siga estes passos:

   1. Na **capturar** painel, no ecrã de emulador 3270, selecione o campo de inteiro, não apenas texto dentro do campo que pretende que como a primeira entrada.

      > [!TIP]
      > Para apresentar todos os campos e certifique-se de que seleciona o campo completo, na **View** menu, selecione **todos os campos**.

   1. Na barra de ferramentas da ferramenta de design, escolha **campo de entrada**. 

   Para adicionar mais parâmetros de entrada, repita os passos anteriores para cada parâmetro.

1. Para adicionar o primeiro parâmetro de saída para o seu método, siga estes passos:

   1. Na **capturar** painel, no ecrã de emulador 3270, selecione o campo de inteiro, não apenas texto dentro do campo que pretende que como o primeiro resultado.

      > [!TIP]
      > Para apresentar todos os campos e certifique-se de que seleciona o campo completo, na **View** menu, selecione **todos os campos**.

   1. Na barra de ferramentas da ferramenta de design, escolha **campo de saída**.

   Para adicionar mais parâmetros de saída, repita os passos anteriores para cada parâmetro.

1. Depois de adicionar todos os seus parâmetros do método, defina estas propriedades para cada parâmetro:

   | Nome da propriedade | Valores possíveis | 
   |---------------|-----------------|
   | **Tipo de dados** | Bytes, data hora, número Decimal, Int, há muito tempo, em Resumo, cadeia |
   | **Técnica de preenchimento de campo** | Os parâmetros suportam esses tipos de preenchimento, preenchendo com espaços em branco, se necessário: <p><p>- **Tipo de**: Introduza carateres sequencialmente no campo. <p>- **Preencher**: Substitua o conteúdo do campo com caracteres, preenchendo com espaços em branco se necessário. <p>- **EraseEofType**: Desmarque o campo e, em seguida, introduza sequencialmente carateres no campo. |
   | **Cadeia de formato** | Alguns tipos de dados do parâmetro utilizam uma cadeia de formato, que informa o conector 3270 como converter o texto a partir do ecrã num tipo de dados do .NET: <p><p>- **DateTime**: A cadeia de formato de DateTime segue a [cadeias de caracteres de formato de datas personalizadas do .NET e tempo](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Por exemplo, a data `06/30/2019` utiliza a cadeia de formato `MM/dd/yyyy`. <p>- **Decimal**: A cadeia de formato decimal utiliza a [cláusula de imagem de COBOL](https://www.ibm.com/support/knowledgecenter/SS6SG3_5.2.0/com.ibm.cobol52.ent.doc/PGandLR/ref/rlddepic.html). Por exemplo, o número `100.35` utiliza a cadeia de formato `999V99`. |
   |||

## <a name="save-and-view-metadata"></a>Guardar e ver os metadados

Depois de definir seu método, mas antes de testar seu método, guarde todas as informações que tiver definido até ao momento num arquivo RAP (.rap).
Pode salvar a este ficheiro RAP em qualquer altura durante qualquer modo. A ferramenta de design também inclui um ficheiro de RAP de exemplo que pode abrir e rever ao navegar para a pasta de instalação da ferramenta de design nesta localização e abrir o ficheiro de "WoodgroveBank.rap":

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

No entanto, se tentar salvar as alterações para o ficheiro RAP de exemplo ou gerar um arquivo HIDX do ficheiro RAP de exemplo, enquanto o arquivo permanece na pasta de instalação da ferramenta de design, poderá receber um erro "acesso negado". Por predefinição, a ferramenta de design é instalada na sua pasta Program Files sem permissões elevadas. Se obtiver um erro, experimente uma destas soluções:

* Copie o ficheiro de exemplo para uma localização diferente.
* Execute a ferramenta de design como um administrador.
* Tornar-se o proprietário para a pasta SDK.

## <a name="test-your-method"></a>Seu método de teste

1. Para executar o seu método no anfitrião em direto, ainda no modo de métodos, prima a tecla F5 ou, na barra de ferramentas da ferramenta de design, escolha **executar**.

   > [!TIP]
   > Pode alterar os modos em qualquer altura. Sobre o **arquivo** menu, selecione **modo**e, em seguida, selecione o modo de que pretende.

1. Introduza os valores dos seus parâmetros e escolha **OK**.

1. Para continuar para a próxima tela, selecione **seguinte**.

1. Quando tiver terminado, escolha **feito**, que mostra os valores de parâmetro de saída.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>Gerar e carregar o ficheiro HIDX

Quando estiver pronto, gere o ficheiro HIDX para que pode carregar para a conta de integração. A ferramenta de Design 3270 cria o arquivo HIDX numa nova subpasta onde guardou o ficheiro RAP.

1. Na barra de ferramentas da ferramenta de Design 3270, escolha **gerar código**.

1. Vá para a pasta que contém o ficheiro RAP e abrir a subpasta que a ferramenta criada depois de gerar o ficheiro HIDX. Confirme que a ferramenta criou o ficheiro HIDX.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e localize a conta de integração.

1. Adicionar o ficheiro HIDX como um mapa para a sua conta de integração por [siga estes passos semelhantes para adicionar mapas](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md), mas quando seleciona o tipo de mapa, selecione **HIDX**.

Mais tarde deste tópico, ao adicionar uma ação de IBM 3270 à sua aplicação lógica pela primeira vez, lhe for pedido para criar uma ligação entre a aplicação lógica e o servidor de anfitrião, fornecendo informações de ligação, como os nomes do seu servidor de conta e o anfitrião de integração . Depois de criar a ligação, pode selecionar o ficheiro HIDX adicionado anteriormente, o método para executar e os parâmetros a utilizar.

Quando terminar de todos estes passos, pode utilizar a ação que criar na sua aplicação lógica para ligar à sua mainframe IBM, telas de unidade para a sua aplicação, inserir dados, devolver resultados e assim por diante. Também pode continuar a adicionar outras ações à sua aplicação lógica para a integração com outros sistemas, serviços e aplicações.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>Executar ações de IBM 3270

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. Na última etapa em que pretende adicionar uma ação, escolha **novo passo**e selecione **adicionar uma ação**. 

1. Na caixa de pesquisa, escolha **Enterprise**. Na caixa de pesquisa, introduza "3270" como o filtro. Na lista de ações, selecione a ação: **Executa um programa de mainframe através de uma ligação de TN3270**

   ![Selecione a ação de 3270](./media/connectors-create-api-3270/select-3270-action.png)

   Para adicionar uma ação entre passos, mova o ponteiro do mouse sobre a seta entre passos. 
   Selecione o sinal de adição (**+**) que é apresentada e, em seguida, selecione **adicionar uma ação**.

1. Se não existem ligações ainda, indique as informações necessárias para a sua ligação e escolha **criar**.

   | Propriedade | Necessário | Value | Descrição |
   |----------|----------|-------|-------------|
   | **Nome da Ligação** | Sim | <*connection-name*> | O nome para a sua ligação |
   | **ID da conta de integração** | Sim | <*integration-account-name*> | Nome da sua conta de integração |
   | **URL de SAS de conta de integração** | Sim | <*integration-account-SAS-URL*> | URL assinatura de acesso partilhado (SAS) de sua conta de integração, que pode gerar a partir de definições da sua conta de integração no portal do Azure. <p>1. No sua integração de conta de menu, em **configurações**, selecione **URL de retorno de chamada**. <br>2. No painel da direita, copie os **URL de chamada de retorno gerado** valor. |
   | **Servidor** | Sim | <*TN3270-server-name*> | O nome do servidor para o seu serviço TN3270 |
   | **Porta** | Não | <*TN3270-server-port*> | A porta utilizada pelo seu servidor TN3270. Se deixado em branco, o conector utiliza `23` como valor predefinido. |
   | **Tipo de dispositivo** | Não | <*IBM-terminal-model*> | O nome do modelo ou o número para o terminal da IBM para emular. Se deixado em branco, o conector utiliza valores predefinidos. |
   | **Página de código** | Não | <*code-page-number*> | O número de página de código para o anfitrião. Se deixado em branco, o conector utiliza `37` como valor predefinido. |
   | **Nome da unidade lógica** | Não | <*logical-unit-name*> | O nome de unidade lógica específica para pedir a partir do anfitrião |
   | **Ativar SSL?** | Não | Ativada ou desativada | Ative ou desative a encriptação SSL. |
   | **Validar o certificado de ssl do anfitrião?** | Não | Ativada ou desativada | Ativar ou desativar a validação para o certificado do servidor. |
   ||||

   Por exemplo:

   ![Propriedades de ligação](./media/connectors-create-api-3270/connection-properties.png)

1. Fornece as informações necessárias para a ação:

   | Propriedade | Necessário | Value | Descrição |
   |----------|----------|-------|-------------|
   | **Nome de Hidx** | Sim | <*HIDX-file-name*> | Selecione o ficheiro HIDX 3270 que pretende utilizar. |
   | **Nome do método** | Sim | <*method-name*> | Selecione o método no arquivo HIDX que pretende utilizar. Depois de selecionar um método, o **adicione o novo parâmetro** é apresentada uma lista pelo que pode selecionar os parâmetros a utilizar com esse método. |
   ||||

   Por exemplo:

   **Selecione o ficheiro HIDX**

   ![Selecione o ficheiro HIDX](./media/connectors-create-api-3270/select-hidx-file.png)

   **Selecione o método**

   ![Selecionar método](./media/connectors-create-api-3270/select-method.png)

   **Selecione os parâmetros**

   ![Parâmetros de seleção](./media/connectors-create-api-3270/add-parameters.png)

1. Quando tiver terminado, guarde e executar a aplicação lógica.

   Após a sua lógica de aplicação termina em execução, os passos da execução aparecem. 
   Passos bem-sucedida mostram marcas de verificação, enquanto passos sem êxito mostram a letra "X".

1. Para rever as entradas e saídas de cada passo, expanda esse passo.

1. Para rever os resultados, escolha **ver saídas em bruto**.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre o limite, ações e acionadores, que é descrito através OpenAPI do conector (anteriormente Swagger) descrição, reveja a página de referência do conector: [ https://docs.microsoft.com/connectors/<replace-with-api-topic-file-name> ](/connectors/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)
