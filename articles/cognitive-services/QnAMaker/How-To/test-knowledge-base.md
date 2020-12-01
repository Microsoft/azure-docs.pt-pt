---
title: Como testar uma base de conhecimento - QnA Maker
description: Testar a sua base de conhecimento QnA Maker é uma parte importante de um processo iterativo para melhorar a precisão das respostas que estão a ser devolvidas. Pode testar a base de conhecimento através de uma interface de chat melhorada que também lhe permite fazer edições.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 273548ec095ce04772438a2d732b914d80d976cc
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353158"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Teste a sua base de conhecimentos no QnA Maker

Testar a sua base de conhecimento QnA Maker é uma parte importante de um processo iterativo para melhorar a precisão das respostas que estão a ser devolvidas. Pode testar a base de conhecimento através de uma interface de chat melhorada que também lhe permite fazer edições.

## <a name="interactively-test-in-qna-maker-portal"></a>Teste interativo no portal QnA Maker

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

1. Aceda à sua base de conhecimento selecionando o seu nome na página **'Bases de conhecimento'** Do meu conhecimento.
1. Para aceder ao painel de desapoia do Teste, selecione **Teste** no painel superior da sua aplicação.
1. Introduza uma consulta na caixa de texto e selecione Enter.
1. A resposta mais bem acompanhada da base de conhecimento é devolvida como resposta.

### <a name="clear-test-panel"></a>Painel de teste claro

Para limpar todas as consultas de teste inseridas e os seus resultados a partir da consola de teste, selecione **Comece de novo** no canto superior esquerdo do painel de teste.

### <a name="close-test-panel"></a>Painel de teste de fecho

Para fechar o painel de Teste, selecione novamente o botão **Teste.** Enquanto o painel de Teste estiver aberto, não é possível editar o conteúdo da Base de Conhecimento.

### <a name="inspect-score"></a>Inspecionar pontuação

Inspecione os detalhes do resultado do teste no painel De inspecione.

1.  Com o painel de deslizamento de teste aberto, **selecione Inspecione** para obter mais detalhes sobre esta resposta.

    ![Inspecione respostas](../media/qnamaker-how-to-test-knowledge-bases/inspect.png)

2.  O painel de inspeção aparece. O painel inclui a intenção de pontuação superior, bem como quaisquer entidades identificadas. O painel mostra o resultado da expressão selecionada.

### <a name="correct-the-top-scoring-answer"></a>Corrija a resposta de pontuação superior

Se a resposta de pontuação superior estiver incorreta, selecione a resposta correta da lista e selecione **Save and Train**.

![Corrija a resposta de pontuação superior](../media/qnamaker-how-to-test-knowledge-bases/choose-answer.png)

### <a name="add-alternate-questions"></a>Adicionar perguntas alternativas

Pode adicionar formas alternativas de uma pergunta a uma determinada resposta. Digite as respostas alternativas na caixa de texto e selecione insira para adicioná-las. **Selecione Guardar e Treinar** para armazenar as atualizações.

![Adicionar perguntas alternativas](../media/qnamaker-how-to-test-knowledge-bases/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Adicione uma nova resposta

Pode adicionar uma nova resposta se alguma das respostas existentes que foram correspondidas estiver incorreta ou se a resposta não existir na base de conhecimento (nenhuma boa correspondência encontrada no KB).

Na parte inferior da lista de respostas, utilize a caixa de texto para introduzir uma nova resposta e prima para a adicionar.

**Selecione Save and Train** para persistir esta resposta. Um novo par de perguntas-respostas foi agora adicionado à sua base de conhecimento.

> [!NOTE]
> Todas as edições para a sua base de conhecimento só são guardadas quando premir o botão **Salvar e Treinar.**

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

1. Aceda à sua base de conhecimento selecionando o seu nome na página **'Bases de conhecimento'** Do meu conhecimento.
1. Para aceder ao painel de desapoia do Teste, selecione **Teste** no painel superior da sua aplicação. 
1. Verá uma caixa de verificação na **resposta curta** do Ecrã superior, selecionada por predefinição. Esta opção é utilizada para permitir a deteção de extensão de resposta baseada em MRC no seu painel de teste. 
1. Introduza uma consulta na caixa de texto e selecione Enter. 
1. Para cada consulta, se houver uma resposta precisa/resposta curta presente na passagem de resposta, então juntamente com a passagem de resposta mais bem-partida, que está presente na base de conhecimento, você também terá uma resposta curta para a sua consulta.
    ![Painel de teste gerido](../media/qnamaker-how-to-test-knowledge-bases/test-pane-with-managed-detail.png)
1. Se desmarcar a **resposta curta do Display,** apenas a passagem de resposta mais bem acompanhada da base de conhecimento é devolvida como resposta.

### <a name="clear-test-panel"></a>Painel de teste claro

Para limpar todas as consultas de teste inseridas e os seus resultados a partir da consola de teste, selecione **Comece de novo** no canto superior esquerdo do painel de teste.

### <a name="close-test-panel"></a>Painel de teste de fecho

Para fechar o painel de Teste, selecione novamente o botão **Teste.** Enquanto o painel de Teste estiver aberto, não é possível editar o conteúdo da Base de Conhecimento.

### <a name="inspect-score"></a>Inspecionar pontuação

Inspecione os detalhes do resultado do teste no painel De inspecione.

1.  Com o painel de deslizamento de teste aberto, **selecione Inspecione** para obter mais detalhes sobre esta resposta.

    ![Inspecione a pré-visualização das respostas](../media/qnamaker-how-to-test-knowledge-bases/inspect-with-managed.png)

2.  O painel de inspeção aparece. O painel inclui a intenção de pontuação superior, bem como quaisquer entidades identificadas. O painel mostra o resultado da expressão selecionada.
3. O painel mostra a pontuação de confiança da passagem de resposta juntamente com a pontuação do intervalo de resposta detetada.

### <a name="correct-the-top-scoring-answer"></a>Corrija a resposta de pontuação superior

Se a resposta de pontuação superior estiver incorreta, selecione a resposta correta da lista e selecione **Save and Train**.

![Corrija a pré-visualização da resposta de pontuação superior](../media/qnamaker-how-to-test-knowledge-bases/choose-answer-managed.png)

### <a name="add-alternate-questions"></a>Adicionar perguntas alternativas

Pode adicionar formas alternativas de uma pergunta a uma determinada resposta. Digite as respostas alternativas na caixa de texto e selecione insira para adicioná-las. **Selecione Guardar e Treinar** para armazenar as atualizações.

![Adicionar pré-visualização de perguntas alternativas](../media/qnamaker-how-to-test-knowledge-bases/add-alternate-question-with-managed.png)

### <a name="add-a-new-answer"></a>Adicione uma nova resposta

Pode adicionar uma nova resposta se alguma das respostas existentes que foram correspondidas estiver incorreta ou se a resposta não existir na base de conhecimento (nenhuma boa correspondência encontrada no KB).

Na parte inferior da lista de respostas, utilize a caixa de texto para introduzir uma nova resposta e prima para a adicionar.

**Selecione Save and Train** para persistir esta resposta. Um novo par de perguntas-respostas foi agora adicionado à sua base de conhecimento.

---

### <a name="test-the-published-knowledge-base"></a>Testar a base de conhecimentos publicada

Pode testar a versão publicada da base de conhecimento no painel de teste. Depois de ter publicado o KB, selecione a caixa **KB publicada** e envie uma consulta para obter resultados do KB publicado.

![Teste contra um KB publicado](../media/qnamaker-how-to-test-knowledge-bases/test-against-published-knowledge-base.png)

## <a name="batch-test-with-tool"></a>Teste de lote com ferramenta

Utilize a ferramenta de teste do lote quando quiser:

* determinar a resposta superior e pontuação para um conjunto de perguntas
* validar a resposta esperada para conjunto de perguntas

### <a name="prerequisites"></a>Pré-requisitos

* Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Ou [cria um serviço QnA Maker](../Quickstarts/create-publish-knowledge-base.md) ou utiliza um serviço existente, que utiliza a língua inglesa.
* Descarregue o [ `.docx` ficheiro de amostra de várias voltas](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)
* Descarregue a [ferramenta de teste de lote,](https://aka.ms/qnamakerbatchtestingtool)extrate o ficheiro executável do `.zip` ficheiro.

### <a name="sign-into-qna-maker-portal"></a>Inscreva-se no portal QnA Maker

[Inscreva-se no](https://www.qnamaker.ai/) portal QnA Maker.

### <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>Criar uma nova base de conhecimento a partir do ficheiro de sample.docx multi-volta

1. **Selecione Criar uma base** de conhecimento a partir da barra de ferramentas.
1. Salte **o Passo 1** porque já deve ter um recurso QnA Maker, passando para o **Passo 2** para selecionar as informações de recursos existentes:
    * ID do Diretório Ativo Azure
    * Nome de assinatura Azure
    * Nome de serviço Azure QnA
    * Língua - a língua inglesa
1. Insira o nome `Multi-turn batch test quickstart` como o nome da sua base de conhecimento.

1. No **passo 4**, configurar as definições com a seguinte tabela:

    |Definição|Valor|
    |--|--|
    |**Ativar a extração em várias voltas a partir de ficheiros URLs, .pdf ou .docx.**|Marcado|
    |**Texto de resposta predefinido**| `Batch test - default answer not found.`|
    |**+ Adicionar Arquivo**|Selecione a listagem de ficheiros descarregada `.docx` nos pré-requisitos.|
    |**Conversa**|Selecione **Profissional**|

1. No **passo 5,** selecione **Criar o seu KB**.

    Quando o processo de criação termina, o portal exibe a base de conhecimento editável.

### <a name="save-train-and-publish-knowledge-base"></a>Salvar, treinar e publicar base de conhecimento

1. **Selecione Guardar e treinar** a partir da barra de ferramentas para salvar a base de conhecimentos.
1. Selecione **Publicar** na barra de ferramentas e, em seguida, selecione **Publicar** novamente para publicar a base de conhecimentos. A publicação disponibiliza a base de conhecimentos para consultas a partir de um ponto final de URL público. Quando a publicação estiver concluída, guarde as informações de URL e ponto final apresentadas na página **Publicar.**

    |Dados necessários| Exemplo|
    |--|--|
    |Anfitrião publicado|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |Chave publicada|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` (32 caracteres de corda mostrados após `Endpoint` )|
    |ID da Aplicação|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (36 caracteres de corda mostrados como parte de `POST` ) |

### <a name="create-batch-test-file-with-question-ids"></a>Criar ficheiro de teste de lote com IDs de pergunta

Para utilizar a ferramenta de teste de lote, crie um ficheiro nomeado `batch-test-data-1.tsv` com um editor de texto. O ficheiro deve estar no formato UTF-8 e tem de ter as seguintes colunas separadas por um separador.

|Campos de ficheiros de entrada de TSV|Notas|Exemplo|
|--|--|--|
|ID da base de conhecimento|A sua identificação da base de conhecimento encontrada na página publicar. Teste várias bases de conhecimento no mesmo serviço de uma só vez num único ficheiro utilizando diferentes IDs de base de conhecimento num único ficheiro.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (36 caracteres de corda mostrados como parte de `POST` ) |
|Pergunta|O texto de pergunta que um utilizador entraria. 1.000 caracteres no máximo.|`How do I sign out?`|
|Etiquetas de metadados|opcional|`topic:power` usa o `key:value` formato|
|Parâmetro superior|opcional|`25`|
|ID de resposta esperada|opcional|`13`|

Para esta base de conhecimento, adicione três linhas de apenas as duas colunas necessárias ao ficheiro. A primeira coluna é o ID da sua base de conhecimento e a segunda coluna deve ser a seguinte lista de perguntas:

|Coluna 2 - perguntas|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

Estas questões são a redação exata da base de conhecimento e devem devolver 100 como ponto de confiança.

Em seguida, adicione algumas perguntas, semelhantes a estas questões, mas não exatamente as mesmas em mais três linhas, usando o mesmo ID base de conhecimento:

|Coluna 2 - perguntas|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> Certifique-se de que cada coluna é separada apenas por um delimiter de separador. Os espaços de liderança ou de fuga são adicionados aos dados da coluna e farão com que o programa atire exceções quando o tipo ou o tamanho estiverem incorretos.

O ficheiro de teste do lote, quando aberto no Excel, parece a seguinte imagem. A identificação da base de conhecimento foi substituída `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` por segurança. Para o seu próprio teste de lote, certifique-se de que a coluna exibe o seu ID da base de conhecimento.

> [!div class="mx-imgBorder"]
> ![Inserir primeira versão do ficheiro .tsv a partir do teste de lote](../media/batch-test/batch-test-1-input.png)

### <a name="test-the-batch-file"></a>Teste o ficheiro do lote

Executar o programa de teste de lote utilizando o seguinte formato CLI na linha de comando.

Substitua `YOUR-RESOURCE-NAME` e `ENDPOINT-KEY` pelos seus próprios valores para o nome de serviço e tecla de ponto final. Estes valores encontram-se na página **Definições** no portal QnA Maker.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
O teste completa e gera o `out.tsv` ficheiro:

> [!div class="mx-imgBorder"]
> ![Primeira versão de saída do ficheiro .tsv do teste de lote](../media/batch-test/batch-test-1-output.png)

A identificação da base de conhecimento foi substituída `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` por segurança. Para o seu próprio teste de lote, a coluna apresenta o seu ID de base de conhecimento.

A produção de teste da pontuação de confiança, na quarta coluna, mostra que as três primeiras perguntas devolveram uma pontuação de 100 como esperado porque cada pergunta é exatamente a mesma que aparece na base de conhecimento. As últimas três perguntas, com nova formulação da pergunta, não devolvem 100 como ponto de confiança. Para aumentar a pontuação tanto para o teste como para os seus utilizadores, precisa de adicionar perguntas mais alternativas à base de conhecimento.

### <a name="testing-with-the-optional-fields"></a>Teste com os campos opcionais

Assim que compreender o formato e o processo, pode gerar um ficheiro de teste, para correr contra a sua base de conhecimento a partir de uma fonte de dados, como a partir de registos de chats.

Como a fonte de dados e o processo são automatizados, o ficheiro de teste pode ser executado muitas vezes com diferentes configurações para determinar os valores corretos.

Por exemplo, se tiver um registo de chat e pretender determinar a que texto de registo de chat se aplica a que campos de metadados, criar um ficheiro de teste e definir os campos de metadados para cada linha. Executar o teste e, em seguida, rever as linhas que correspondem aos metadados. Geralmente, os jogos devem ser positivos, mas deve rever os resultados para falsos positivos. Um falso positivo é uma linha que combina com os metadados mas com base no texto, não deve coincidir.

### <a name="using-optional-fields-in-the-input-batch-test-file"></a>Utilização de campos opcionais no ficheiro de teste do lote de entrada

Utilize o gráfico seguinte para entender como encontrar os valores de campo para dados opcionais.

|Número da coluna|Coluna opcional|Localização dos dados|
|--|--|--|
|3|do IdP|Exportar a base de conhecimento existente para `key:value` os pares existentes.|
|4|top|Recomenda-se o valor `25` predefinido.|
|5|ID do conjunto de perguntas e respostas|Exportar a base de conhecimento existente para valores de ID. Note também que os IDs foram devolvidos no ficheiro de saída.|

### <a name="add-metadata-to-the-knowledge-base"></a>Adicionar metadados à base de conhecimento

1. No portal QnA, na página **Editar,** adicione metadados às `topic:power` seguintes perguntas:

    |Perguntas|
    |--|
    |Carregue o Surface Pro 4|
    |Verifique o nível da bateria|

    Dois pares QnA têm o conjunto de metadados.

    > [!TIP]
    > Para ver os metadados e os IDs QnA de cada conjunto, exporte a base de conhecimento. Selecione a página **Definições** e, em seguida, **selecione Export** como um `.xls` ficheiro. Encontre este ficheiro descarregado e abra com o Excel a rever metadados e ID.

1. **Selecione Guardar e treinar,** em seguida, selecione a página **Publicar** e, em seguida, selecione o botão **Publicar.** Estas ações disponibilizam a alteração ao teste do lote. Descarregue a base de conhecimentos a partir da página **Definições.**

    O ficheiro descarregado tem o formato correto para os metadados e o ID de definição de pergunta e resposta correto. Use estes campos na secção seguinte

    > [!div class="mx-imgBorder"]
    > ![Base de conhecimento exportada com metadados](../media/batch-test/exported-knowledge-base-with-metadata.png)

### <a name="create-a-second-batch-test"></a>Criar um segundo teste de lote

Existem dois cenários principais para o teste do lote:
* **Processa ficheiros de registo de chat** - Determine a resposta principal para uma pergunta nunca antes vista - a situação mais comum é quando precisa de processar são ficheiros de registo de consultas, como as perguntas do utilizador de um chat bot. Crie um teste de ficheiro de lote, apenas com as colunas necessárias. O teste devolve a resposta principal para cada pergunta. Não quer dizer que a resposta principal seja a resposta correta. Assim que completar este teste, passe para o teste de validação.
* **Teste de validação** - Valide a resposta esperada. Este teste requer que todas as perguntas e respostas esperadas correspondentes no teste do lote tenham sido validadas. Isto pode requerer algum processo manual.

O procedimento seguinte pressupõe que o cenário é processar registos de chat com

1. Crie um novo ficheiro de teste de lote para incluir dados opcionais, `batch-test-data-2.tsv` . Adicione as seis linhas do ficheiro de entrada original do teste do lote e, em seguida, adicione os metadados, topo e ID do par QnA para cada linha.

    Para simular o processo automatizado de verificação de novos textos a partir de registos de chat contra a base de conhecimento, deslote os metadados para cada coluna com o mesmo valor: `topic:power` .

    > [!div class="mx-imgBorder"]
    > ![Inserir segunda versão do ficheiro .tsv do teste de lote](../media/batch-test/batch-test-2-input.png)

1. Volte a fazer o teste, alterando os nomes dos ficheiros de entrada e saída para indicar que é o segundo teste.

    > [!div class="mx-imgBorder"]
    > ![Segunda versão de saída do ficheiro .tsv do teste de lote](../media/batch-test/batch-test-2-output.png)

### <a name="test-results-and-an-automated-test-system"></a>Resultados dos testes e um sistema de teste automatizado

Este ficheiro de saída de teste pode ser analisado como parte de um pipeline de teste contínuo automatizado.

Esta saída específica de teste deve ser lida como: cada linha foi filtrada com metadados, e porque cada linha não corresponde aos metadados na base de conhecimento, a resposta padrão para as linhas não correspondentes devolvidas ("nenhuma boa correspondência encontrada no kb"). Das filas que combinaram, o QnA ID e o resultado foram devolvidos.

Todas as linhas devolveram o rótulo de incorreto porque nenhuma linha correspondia à identificação de resposta esperada.

Você deve ser capaz de ver com estes resultados que você pode pegar um log de chat e usar o texto como consulta de cada linha. Sem saber nada sobre os dados, os resultados dizem-lhe muito sobre os dados que pode usar para avançar:

* meta-dados
* QnA ID
* classificação

Filtrar com meta-dados foi uma boa ideia para o teste? Sim e não. O sistema de teste deve criar ficheiros de teste para cada par de meta-dados, bem como um teste sem pares de meta-dados.

### <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a testar a base de conhecimentos, elimine a ferramenta de ficheiro de lote e os ficheiros de teste.

Se não continuar a utilizar esta base de conhecimento, elimine a base de conhecimento com os seguintes passos:

1. No portal QnA Maker, selecione **as bases my knowledge** a partir do menu superior.
1. Na lista de bases de conhecimento, selecione o ícone **Eliminar** na linha da base de conhecimento deste arranque rápido.

[A documentação de referência sobre a ferramenta](../reference-tsv-format-batch-testing.md) inclui:

* o exemplo da linha de comando da ferramenta
* o formato para ficheiros de entrada e preenchimento de TSV

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Publicar uma base de dados de conhecimento](../quickstarts/create-publish-knowledge-base.md)