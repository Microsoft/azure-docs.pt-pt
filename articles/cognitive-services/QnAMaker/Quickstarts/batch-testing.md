---
title: 'Quickstart: Teste a base de conhecimento com questões de lote'
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: 3bc095d8949f177ccb6c4cc111ba4b272027904e
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756711"
---
# <a name="quickstart-test-knowledge-base-with-batch-questions-and-expected-answers"></a>Quickstart: Teste a base de conhecimento com perguntas de lote e respostas esperadas

Utilize a ferramenta de teste de lote qnA Maker para testar as bases de conhecimento no seu recurso QnA Maker para respostas esperadas, pontuações de confiança e solicitações de várias voltas.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Ou [cria um serviço QnA Maker](create-publish-knowledge-base.md) ou usa um serviço existente, que utiliza a língua inglesa.
* Descarregue o [ficheiro de amostra `.docx` seleção de várias voltas](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)
* Descarregue a ferramenta de teste do `.zip` [lote,](https://aka.ms/qnamakerbatchtestingtool)extraia o ficheiro executável do ficheiro.

## <a name="sign-into-qna-maker-portal"></a>Assine no portal QnA Maker

[Inscreva-se](https://www.qnamaker.ai/) no portal QnA Maker.

## <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>Criar uma nova base de conhecimento a partir do ficheiro multi-turn sample.docx

1. Selecione **Criar uma base** de conhecimento a partir da barra de ferramentas.
1. Skip **Step 1** porque já deve ter um recurso QnA Maker, passando para o Passo **2** para selecionar as informações de recursos existentes:
    * ID do Diretório Ativo Azure
    * Nome de subscrição do Azure
    * Nome de serviço Azure QnA
    * Língua - a língua inglesa
1. Insira `Multi-turn batch test quickstart` o nome como o nome da sua base de conhecimento.

1. No **passo 4,** configure as definições com a tabela seguinte:

    |Definição|Valor|
    |--|--|
    |**Ativar a extração de várias voltas a partir de URLs, ficheiros .pdf ou .docx.**|Assinalado|
    |**Texto de resposta padrão**| `Batch test - default answer not found.`|
    |**+ Adicionar Ficheiro**|Selecione `.docx` a listagem de ficheiros descarregadonos pré-requisitos.|
    |**Chit-chat**|Selecionar **Profissional**|

1. No **passo 5,** selecione **Criar o seu KB**.

    Quando o processo de criação termina, o portal exibe a base de conhecimento editável.

## <a name="save-train-and-publish-knowledge-base"></a>Salvar, treinar e publicar base de conhecimento

1. Selecione **Guardar e treinar** a partir da barra de ferramentas para salvar a base de conhecimentos.
1. **Selecione Publicar** a partir da barra de ferramentas e, em seguida, selecione **Publicar** novamente para publicar a base de conhecimentos. A publicação disponibiliza a base de conhecimentos para consultas a partir de um ponto final de URL público. Quando a publicação estiver concluída, guarde o URL do anfitrião e as informações de chave de ponto final mostradas na página **Publicar.**

    |Dados necessários| Exemplo|
    |--|--|
    |Anfitrião publicado|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |Chave Publicada|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX`(32 caracteres de `Endpoint` cadeia mostradas depois)|
    |ID da Aplicação|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`(36 caracteres de cadeia `POST`mostradas como parte de) |

## <a name="create-batch-test-file-with-question-ids"></a>Criar ficheiro de teste de lote com iDs de pergunta

Para utilizar a ferramenta de teste do `batch-test-data-1.tsv` lote, crie um ficheiro nomeado com um editor de texto. O ficheiro tem de ter as seguintes colunas separadas por um separador.

|Campos de ficheiros de entrada TSV|Notas|Exemplo|
|--|--|--|
|ID base de conhecimento|O seu ID da base de conhecimento encontrado na página De publicação. Teste várias bases de conhecimento no mesmo serviço de uma só vez num único ficheiro, utilizando diferentes IDs de base de conhecimento num único ficheiro.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`(36 caracteres de cadeia `POST`mostradas como parte de) |
|Pergunta|O texto de pergunta que um utilizador introduziria. 1.000 caracteres no máximo.|`How do I sign out?`|
|Etiquetas de metadados|opcional|`topic:power`usa a _chave:formato de valor_|
|Parâmetro superior|opcional|`25`|
|ID de resposta esperada|opcional|`13`|

Para esta base de conhecimento, adicione 3 linhas de apenas as 2 colunas necessárias ao ficheiro. A primeira coluna é o seu ID base de conhecimento e a segunda coluna deve ser a seguinte lista de perguntas:

|Coluna 2 - perguntas|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

Estas questões são a redação exata da base de conhecimento e devem devolver 100 como a pontuação de confiança.

Em seguida, adicione algumas perguntas, semelhantes a estas questões, mas não exatamente as mesmas em mais 3 linhas, usando o mesmo ID base de conhecimento:

|Coluna 2 - perguntas|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> Certifique-se de que cada coluna está separada apenas por um delimitador de separador. Os espaços de liderança ou de desfilar são adicionados aos dados da coluna e farão com que o programa lance exceções quando o tipo ou tamanho estiver incorreto.

O ficheiro de teste do lote, quando aberto no Excel, parece a seguinte imagem. A identificação da base `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` de conhecimento foi substituída por segurança. Para o seu próprio teste de lote, certifique-se de que a coluna exibe o seu ID base de conhecimento.

> [!div class="mx-imgBorder"]
> ![Entrada primeira versão do ficheiro .tsv do teste do lote](../media/batch-test/batch-test-1-input.png)

## <a name="test-the-batch-file"></a>Testar o ficheiro do lote

Executar o programa de teste do lote utilizando o seguinte formato CLI na linha de comando.

`YOUR-RESOURCE-NAME` Substitua `ENDPOINT-KEY` e com os seus próprios valores para nome de serviço e chave de ponto final. Estes valores encontram-se na página **Definições** do portal QnA Maker.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
O teste completa e `out.tsv` gera o ficheiro:

> [!div class="mx-imgBorder"]
> ![Primeira versão de .tsv do teste de lote](../media/batch-test/batch-test-1-output.png)

A identificação da base `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` de conhecimento foi substituída por segurança. Para o seu próprio teste de lote, a coluna exibe o seu ID base de conhecimento.

A produção de teste da pontuação de confiança, na 4ª coluna, mostra que as 3 principais perguntas devolveram uma pontuação de 100 como esperado, porque cada pergunta é exatamente a mesma que aparece na base de conhecimento. As últimas 3 perguntas, com nova redação da pergunta, não devolvem 100 como a pontuação de confiança. Para aumentar a pontuação tanto para o teste como para os seus utilizadores, é necessário adicionar mais perguntas alternativas à base de conhecimentos.

## <a name="testing-with-the-optional-fields"></a>Teste com os campos opcionais

Assim que compreender o formato e o processo, pode gerar um ficheiro de teste, para correr contra a sua base de conhecimento a partir de uma fonte de dados, como a partir de registos de chats.

Como a fonte de dados e o processo são automatizados, o ficheiro de teste pode ser executado muitas vezes com configurações diferentes para determinar os valores corretos.

Por exemplo, se tiver um registo de chat e quiser determinar qual o texto de registo de chat que se aplica aos campos de metadados, crie um ficheiro de teste e defina os campos de metadados para cada linha. Executar o teste e, em seguida, rever as linhas que correspondem aos metadados. Geralmente, os jogos devem ser positivos, mas deve rever os resultados por falsos positivos. Um falso positivo é uma linha que corresponde aos metadados, mas com base no texto, não deve coincidir.

## <a name="using-optional-fields-in-the-input-batch-test-file"></a>Utilização de campos opcionais no ficheiro de teste do lote de entrada

Utilize o gráfico seguinte para entender como encontrar os valores de campo para dados opcionais.

|Número da coluna|Coluna opcional|Localização dos dados|
|--|--|--|
|3|do IdP|Exportar base de conhecimentos existente para os pares de _chaves existentes:valores._|
|4|superior|Recomenda-se `25` o valor predefinido.|
|5|Id conjunto de perguntas e respostas|Exportar base de conhecimento existente para valores de identificação. Note também que os IDs foram devolvidos no ficheiro de saída.|

## <a name="add-metadata-to-the-knowledge-base"></a>Adicione metadados à base de conhecimentos

1. No portal QnA, na página **Edit,** `topic:power` adicione metadados das seguintes questões:

    |Perguntas|
    |--|
    |Carregue o seu Surface Pro 4|
    |Verifique o nível da bateria|

    Dois pares QnA têm o conjunto de metadados.

    > [!TIP]
    > Para ver os metadados e os IDs QnA de cada conjunto, exporte a base de conhecimentos. Selecione a página **Definições** e, em seguida, selecione **Exportar** como ficheiro. `.xls` Encontre este ficheiro descarregado e abra com o Excel a rever para metadados e ID.

1. Selecione **Guardar e treinar,** e, em seguida, selecione a página **Publicar** e, em seguida, selecione o botão **Publicar.** Estas ações disponibilizam a alteração ao teste do lote. Descarregue a base de conhecimentos a partir da página **Definições.**

    O ficheiro descarregado tem o formato correto para os metadados e o ID correto de perguntas e respostas. Use estes campos na secção seguinte

    > [!div class="mx-imgBorder"]
    > ![Base de conhecimento exportada com metadados](../media/batch-test/exported-knowledge-base-with-metadata.png)

## <a name="create-a-second-batch-test"></a>Criar um segundo teste de lote

Existem dois cenários principais para o teste do lote:
* **Processo ficheiros** de registo de chat - Determine a resposta máxima para uma pergunta anteriormente invisível - a situação mais comum é quando precisa processar são ficheiros de consultas, como as perguntas de utilizador de um chat bot. Crie um teste de ficheiro de lote, com apenas as colunas necessárias. O teste devolve a resposta superior para cada pergunta. Não quer dizer que a resposta máxima seja a resposta correta. Assim que completar este teste, passe para o teste de validação.
* Teste de **validação** - Validar a resposta esperada. Este teste requer que todas as perguntas e respostas esperadas correspondentes no teste do lote tenham sido validadas. Isto pode requerer algum processo manual.

O procedimento seguinte pressupõe que o cenário é processar registos de chat com

1. Crie um novo ficheiro de `batch-test-data-2.tsv`teste de lote para incluir dados opcionais, . Adicione as 6 linhas do ficheiro de entrada de teste original do lote e, em seguida, adicione os metadados, a parte superior e o id do par QnA para cada linha.

    Para simular o processo automatizado de verificação de novos textos a partir de registos `topic:power`de chat contra a base de conhecimentos, detete temetaos para cada coluna com o mesmo valor: .

    > [!div class="mx-imgBorder"]
    > ![Entrada segunda versão do ficheiro .tsv do teste do lote](../media/batch-test/batch-test-2-input.png)

1. Volte a fazer o teste, alterando os nomes dos ficheiros de entrada e de saída para indicar que é o segundo teste.

    > [!div class="mx-imgBorder"]
    > ![Segunda versão de .tsv de .tsv do teste do lote](../media/batch-test/batch-test-2-output.png)

## <a name="test-results-and-an-automated-test-system"></a>Resultados dos testes e um sistema de teste automatizado

Este ficheiro de saída de teste pode ser analisado como parte de um gasoduto de teste contínuo automatizado.

Esta saída específica de teste deve ser lida como: cada linha foi filtrada com metadados, e porque cada linha não corresponde aos metadados na base de conhecimento, a resposta padrão para as linhas não correspondentes devolvidas ("não há uma boa correspondência encontrada em kb"). Das filas que coincidiram, o ID da QnA e a pontuação foram devolvidos.

Todas as linhas devolveram o rótulo de incorreto porque nenhuma linha correspondia ao ID de resposta esperado.

Você deve ser capaz de ver com estes resultados que você pode pegar um log de chat e usar o texto como a consulta de cada linha. Sem saber nada sobre os dados, os resultados dizem-lhe muito sobre os dados que pode usar para avançar:

* meta-dados
* QnA ID
* classificação

Filtrar com metadados foi uma boa ideia para o teste? Sim e não. O sistema de teste deve criar ficheiros de teste para cada par de metadados, bem como um teste sem pares de metadados.

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a testar a base de conhecimentos, elimine a ferramenta de ficheiro de lote e os ficheiros de teste.

Se não vai continuar a usar esta base de conhecimento, elimine a base de conhecimentos com os seguintes passos:

1. No portal QnA Maker, selecione **As bases My Knowledge** do menu superior.
1. Na lista de bases de conhecimento, selecione o ícone **Delete** na linha da base de conhecimento deste arranque rápido.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)
