---
title: 'Início rápido: testar a base de dados de conhecimento com perguntas do lote'
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/19/2019
ms.author: diberry
ms.openlocfilehash: 9483db2187c05fe8e0f4fa2d41c17b8748ba3db7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474040"
---
# <a name="quickstart-test-knowledge-base-with-batch-questions-and-expected-answers"></a>Início rápido: testar a base de dados de conhecimento com perguntas do lote e respostas esperadas

Use a ferramenta de teste do QnA Maker batch para testar as bases de dados de conhecimento em seu recurso de QnA Maker para respostas esperadas, pontuações de confiança e prompts de múltipla ativação.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Crie um serviço de QnA Maker](create-publish-knowledge-base.md#create-a-new-qna-maker-knowledge-base) ou use um serviço existente, que usa o idioma Inglês para o documento de exemplo usado neste guia de início rápido.
* Baixar o [arquivo de `.docx` de exemplo de várias desativações](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)
* Baixe a [ferramenta de teste do lote](https://aka.ms/qnamakerbatchtestingtool), extraia o arquivo executável do arquivo de `.zip`.

## <a name="sign-into-qna-maker-portal"></a>Entrar no portal de QnA Maker

[Entre](https://www.qnamaker.ai/) no portal de QnA Maker.

## <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>Criar uma nova base de dados de conhecimento por meio do arquivo. docx de amostra de múltipla

1. Selecione **criar uma base de dados de conhecimento** na barra de ferramentas.
1. Pule a **etapa 1** porque você já deve ter um recurso de QnA Maker, passando para a **etapa 2** para selecionar as informações de recurso existentes:
    * ID de Azure Active Directory
    * Nome da assinatura do Azure
    * Nome do serviço QnA do Azure
    * Idioma-o idioma inglês
1. Insira o nome `Multi-turn batch test quickstart` como o nome da sua base de dados de conhecimento.
1. Na **etapa 4**, marque **habilitar a extração múltipla de URLs, arquivos. pdf ou. docx**.
1. Insira o **texto de resposta padrão** de `Quickstart - can't find answer`. Em uma base de dados de conhecimento de produção, essas informações devem ser mais instrutivas ao usuário, mas para este guia de início rápido, uma resposta simples funciona.
1. Ainda na **etapa 4**, selecione **+ Adicionar arquivo** e, em seguida, selecione a lista de arquivos baixados `.docx` nos pré-requisitos.
1. Na **etapa 5**, selecione **criar seu KB**.

    Quando o processo de criação for concluído, o portal exibirá a base de dados de conhecimento editável.

## <a name="save-train-and-publish-knowledge-base"></a>Salvar, treinar e publicar a base de dados de conhecimento

1. Selecione **salvar e treinar** na barra de ferramentas para salvar a base de dados de conhecimento.
1. Selecione **publicar** na barra de ferramentas e, em seguida, selecione **publicar** novamente para publicar a base de dados de conhecimento. A publicação disponibiliza a base de dados de conhecimento para consultas de um ponto de extremidade de URL pública. Quando a publicação for concluída, salve as informações de URL do host e chave do ponto de extremidade mostradas na página **publicar** .

    |Dados necessários| Exemplo|
    |--|--|
    |Host publicado|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |Chave publicada|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` (cadeia de caracteres 32 mostrada após `Endpoint`)|
    |ID de Aplicação|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (cadeia de caracteres 36 mostrada como parte do `POST`) |

## <a name="create-batch-test-file-with-question-ids"></a>Criar arquivo de teste em lote com IDs de pergunta

Para usar a ferramenta de teste do lote, crie um arquivo chamado `batch-test-data-1.tsv` com um editor de texto. O arquivo precisa ter as colunas a seguir separadas por uma tabulação.

|Campos do arquivo de entrada TSV|Notas|Exemplo|
|--|--|--|
|ID da base de dados de conhecimento|Sua ID da base de dados de conhecimento foi encontrada na página de publicação. Teste várias bases de dados de conhecimento no mesmo serviço de uma só vez em um único arquivo usando IDs de base de dados de conhecimento diferentes em um único arquivo.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (cadeia de caracteres 36 mostrada como parte do `POST`) |
|Pergunta|O texto da pergunta que um usuário digitaria. máximo de 1.000 caracteres.|`How do I sign out?`|
|Etiquetas de metadados|opcional|`topic:power` usa o formato _chave: valor_|
|Parâmetro superior|opcional|`25`|
|ID de resposta esperada|opcional|`13`|

Para essa base de dados de conhecimento, adicione três linhas apenas das duas colunas necessárias ao arquivo. A primeira coluna é sua ID da base de dados de conhecimento e a segunda coluna deve ser a seguinte lista de perguntas:

|Coluna 2-perguntas|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

Essas perguntas são as palavras exatas da base de dados de conhecimento e devem retornar 100 como a pontuação de confiança.

Em seguida, adicione algumas perguntas, semelhantes a essas perguntas, mas não exatamente as mesmas em mais três linhas, usando a mesma ID da base de dados de conhecimento:

|Coluna 2-perguntas|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> Certifique-se de que cada coluna seja separada apenas por um delimitador de tabulação. Espaços à esquerda ou à direita são adicionados aos dados da coluna e farão com que o programa gere exceções quando o tipo ou o tamanho estiver incorreto.

O arquivo de teste do lote, quando aberto no Excel, é semelhante à imagem a seguir. A ID da base de dados de conhecimento foi substituída por `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` para segurança. Para seu próprio teste em lotes, verifique se a coluna exibe sua ID da base de dados de conhecimento.

> [!div class="mx-imgBorder"]
> ![entrada da primeira versão do arquivo. tsv do teste em lote](../media/batch-test/batch-test-1-input.png)

## <a name="test-the-batch-file"></a>Testar o arquivo em lotes

Execute o programa de teste de lote usando o seguinte formato de CLI na linha de comando.

Substitua `YOUR-RESOURCE-NAME` e `ENDPOINT-KEY` pelos seus próprios valores para nome do serviço e chave do ponto de extremidade. Esses valores são encontrados na página **configurações** no portal de QnA Maker.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
O teste é concluído e gera o arquivo de `out.tsv`:

> [!div class="mx-imgBorder"]
> ![saída da primeira versão do arquivo. tsv do teste do lote](../media/batch-test/batch-test-1-output.png)

A ID da base de dados de conhecimento foi substituída por `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` para segurança. Para seu próprio teste em lotes, a coluna exibe sua ID da base de dados de conhecimento.

A saída de teste de Pontuação de confiança, na 4ª coluna, mostra as três principais perguntas retornadas de 100 como esperado, pois cada pergunta é exatamente igual à que aparece na base de dados de conhecimento. As três últimas perguntas, com novas palavras da pergunta, não retornam 100 como a pontuação de confiança. Para aumentar a pontuação para o teste e seus usuários, você precisa adicionar mais perguntas alternativas à base de dados de conhecimento.

## <a name="testing-with-the-optional-fields"></a>Testando com os campos opcionais

Depois de entender o formato e o processo, você pode gerar um arquivo de teste, para executar em sua base de dados de conhecimento a partir de uma fonte de dado, como de logs de chats.

Como a fonte de dados e o processo são automatizados, o arquivo de teste pode ser executado muitas vezes com configurações diferentes para determinar os valores corretos.

Por exemplo, se você tiver um log de chat e desejar determinar qual texto de log de chat aplica-se a quais campos de metadados, crie um arquivo de teste e defina os campos de metadados para cada linha. Execute o teste e examine as linhas que correspondem aos metadados. Em geral, as correspondências devem ser positivas, mas você deve revisar os resultados para falsos positivos. Um falso positivo é uma linha que corresponde aos metadados, mas com base no texto, ele não deve corresponder.

## <a name="using-optional-fields-in-the-input-batch-test-file"></a>Usando campos opcionais no arquivo de teste do lote de entrada

Use o gráfico a seguir para entender como localizar os valores de campo para dados opcionais.

|Número da coluna|Coluna opcional|Localização dos dados|
|--|--|--|
|3|do IdP|Exportar a base de dados de conhecimento existente para pares de _chave: valor_ existentes.|
|4|superior|O valor padrão de `25` é recomendado.|
|5|ID do conjunto de perguntas e respostas|Exportar base de dados de conhecimento existente para valores de ID. Observe também que as IDs foram retornadas no arquivo de saída.|

## <a name="add-metadata-to-the-knowledge-base"></a>Adicionar metadados à base de dados de conhecimento

1. No portal do QnA, na página **Editar** , adicione metadados de `topic:power` às seguintes perguntas:

    |Dúvidas|
    |--|
    |Cobrar sua superfície Pro 4|
    |Verificar o nível da bateria|

    Dois conjuntos de QnA têm os metadados definidos.

    > [!TIP]
    > Para ver os metadados e as IDs de QnA de cada conjunto, exporte a base de dados de conhecimento. Selecione a página **configurações** e, em seguida, selecione **Exportar** como um arquivo de `.xls`. Localize esse arquivo baixado e abra com o Excel revisando os metadados e a ID.

1. Selecione **salvar e treinar**, em seguida, selecione a página **publicar** e, em seguida, selecione o botão **publicar** . Essas ações tornam a alteração disponível para o teste em lotes. Baixe a base de dados de conhecimento na página **configurações** .

    O arquivo baixado tem o formato correto para os metadados e a pergunta e a ID corretas do conjunto de respostas. Use esses campos na próxima seção

    > [!div class="mx-imgBorder"]
    > ![exportação da base de dados de conhecimento com metadados](../media/batch-test/exported-knowledge-base-with-metadata.png)

## <a name="create-a-second-batch-test"></a>Criar um segundo teste em lote

Há dois cenários principais para testes em lotes:
* **Processar arquivos de log de chat** -determinar a resposta principal para uma pergunta anteriormente não vista-a situação mais comum é quando você precisa processar o arquivo de log de consultas, como perguntas de usuário de um bate-papo. Crie um teste de arquivo em lotes, com apenas as colunas necessárias. O teste retorna a resposta principal para cada pergunta. Isso não significa que a resposta principal é a resposta correta. Depois de concluir este teste, vá para o teste de validação.
* **Teste de validação** – valide a resposta esperada. Esse teste exige que todas as perguntas e correspondências de respostas esperadas no teste em lotes foram validadas. Isso pode exigir algum processo manual.

O procedimento a seguir pressupõe que o cenário é processar os logs de chat com 

1. Crie um novo arquivo de teste do lote para incluir dados opcionais, `batch-test-data-2.tsv`. Adicione as 6 linhas do arquivo de entrada de teste do lote original e, em seguida, adicione a ID de conjunto de metadados, superior e QnA para cada linha.

    Para simular o processo automatizado de verificar o novo texto de logs de chat na base de dados de conhecimento, defina os metadados para cada coluna com o mesmo valor: `topic:power`.

    > [!div class="mx-imgBorder"]
    > ![entrada segunda versão do arquivo. tsv do teste do lote](../media/batch-test/batch-test-2-input.png)

1. Execute o teste novamente, alterando os nomes de arquivo de entrada e saída para indicar que ele é o segundo teste.

    > [!div class="mx-imgBorder"]
    > ![saída da segunda versão do arquivo. tsv do teste do lote](../media/batch-test/batch-test-2-output.png)

## <a name="test-results-and-an-automated-test-system"></a>Resultados de teste e um sistema de teste automatizado

Esse arquivo de saída de teste pode ser analisado como parte de um pipeline de teste contínuo automatizado.

Esta saída de teste específica deve ser lida como: cada linha foi filtrada com metadados e, como cada linha não correspondeu aos metadados na base de dados de conhecimento, a resposta padrão para as linhas não correspondentes retorna ("nenhuma correspondência adequada encontrada em KB"). Das linhas que corresponderam, a ID de QnA e a pontuação foram retornadas.

Todas as linhas retornaram o rótulo de incorreto porque nenhuma linha correspondeu à ID de resposta esperada.

Você deve ser capaz de ver com esses resultados que você pode fazer um log de chat e usar o texto como a consulta de cada linha. Sem saber nada sobre os dados, os resultados informam muito sobre os dados que você pode usar avançando:

* metadados
* ID do QnA
* pontuação

Estava filtrando com meta-dados uma boa ideia para o teste? Sim e não. O sistema de teste deve criar arquivos de teste para cada par de metadados, bem como um teste sem pares de metadados.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não vai continuar testando a base de dados de conhecimento, exclua a ferramenta arquivo em lotes e os arquivos de teste.

Se você não pretende usar essa base de dados de conhecimento, exclua a base de dados de conhecimento com as seguintes etapas:

1. No portal de QnA Maker, selecione **minhas bases de dados de conhecimento** no menu superior.
1. Na lista de bases de dados de conhecimento, selecione o ícone **excluir** na linha da base de dados de conhecimento deste guia de início rápido.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)
