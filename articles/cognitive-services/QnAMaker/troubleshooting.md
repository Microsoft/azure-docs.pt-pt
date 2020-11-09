---
title: Resolução de problemas - QnA Maker
description: A lista com curadoria das perguntas mais frequentes sobre o serviço QnA Maker irá ajudá-lo a adotar o serviço de forma mais rápida e com melhores resultados.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: troubleshooting
ms.date: 11/09/2020
ms.openlocfilehash: 6bf9eb5fbb8604bca67279f5f41c8aed19a510c0
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376881"
---
# <a name="troubleshooting-for-qna-maker"></a>Resolução de problemas para o Criador QnA

A lista com curadoria das perguntas mais frequentes sobre o serviço QnA Maker irá ajudá-lo a adotar o serviço de forma mais rápida e com melhores resultados.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>Gerir previsões

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

<details>
<summary><b>Como posso melhorar o desempenho da produção para previsões de consulta?</b></summary>

**Resposta** : Os problemas de desempenho de produção indicam que precisa de aumentar tanto para o seu serviço de Aplicação como para a sua Pesquisa Cognitiva. Considere adicionar uma réplica à sua Pesquisa Cognitiva para melhorar o desempenho.

Saiba mais sobre [os níveis de preços.](Concepts/azure-resources.md)
</details>

<details>
<summary><b>Como obter o ponto final do serviço QnAMaker</b></summary>

**Resposta** : O ponto final do serviço QnAMaker é útil para depurar propósitos quando contacte o Suporte QnAMaker ou o UserVoice. O ponto final é um URL neste formulário: `https://your-resource-name.azurewebsites.net` .

1. Aceda ao seu serviço QnAMaker (grupo de recursos) no [portal Azure](https://portal.azure.com)

    ![Grupo de recursos QnAMaker Azure no portal Azure](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecione o Serviço de Aplicações associado ao recurso QnA Maker. Normalmente, os nomes são os mesmos.

     ![Selecione O Serviço de Aplicações QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. O URL de ponto final está disponível na secção Visão Geral

    ![Ponto final do QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

<details>
<summary><b>Como posso melhorar o desempenho da produção para previsões de consulta?</b></summary>

**Resposta** : Os problemas de desempenho de produção indicam que precisa de aumentar a sua Pesquisa Cognitiva. Considere adicionar uma réplica à sua Pesquisa Cognitiva para melhorar o desempenho.

Saiba mais sobre [os níveis de preços.](Concepts/azure-resources.md)
</details>

---

## <a name="manage-the-knowledge-base"></a>Gerir a base de conhecimentos

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

<details>
<summary><b>Apaguei acidentalmente uma parte do meu Fabricante QnA, o que devo fazer?</b></summary>

**Resposta** : Não elimine nenhum dos serviços Azure criados juntamente com o recurso QnA Maker, como Search ou Web App. Estes são necessários para que o QnA Maker funcione, se eliminar um, o QnA Maker deixará de funcionar corretamente.

Todos os deletes são permanentes, incluindo pares de perguntas e respostas, ficheiros, URLs, perguntas e respostas personalizadas, bases de conhecimento ou recursos Azure. Certifique-se de que exporta a sua base de conhecimento a partir da página **Definições** antes de eliminar qualquer parte da sua base de conhecimento.

</details>

<details>
<summary><b>Porque é que o meu URL/ficheiro(s) não está a extrair pares de perguntas-respostas?</b></summary>

**Resposta** : É possível que o QnA Maker não possa extrair automaticamente algum conteúdo de perguntas e respostas (QnA) a partir de URLs FAQ válidos. Nesses casos, pode colar o conteúdo QnA num ficheiro .txt e ver se a ferramenta pode ingerir. Em alternativa, pode adicionar conteúdo editorialmente à sua base de conhecimento através do [portal QnA Maker](https://qnamaker.ai).

</details>

<details>
<summary><b>Quão grande é a base de conhecimento que posso criar?</b></summary>

**Resposta** : O tamanho da base de conhecimento depende da pesquisa SKU of Azure que escolher ao criar o serviço QnA Maker. Leia [aqui](./Tutorials/choosing-capacity-qnamaker-deployment.md) para mais detalhes.

</details>

<details>
<summary><b>Por que não consigo ver nada quando tento criar uma nova base de conhecimento?</b></summary>

**Resposta** : Ainda não criou nenhum serviço da QnA Maker em Azure. Leia [aqui](./How-To/set-up-qnamaker-service-azure.md) para aprender a fazer isso.

</details>

<details>
<summary><b>Como partilho uma base de conhecimento com os outros?</b></summary>

**Resposta** : A partilha funciona ao nível de um serviço QnA Maker, ou seja, todas as bases de conhecimento do serviço serão partilhadas. Leia [aqui](./How-To/collaborate-knowledge-base.md) como colaborar numa base de conhecimento.

</details>

<details>
<summary><b>Pode partilhar uma base de conhecimento com um contribuinte que não esteja no mesmo inquilino da AAD, para modificar uma base de conhecimento?</b></summary>

**Resposta** : A partilha baseia-se no controlo de acesso baseado em funções Azure (Azure RBAC). Se puder partilhar _qualquer_ recurso em Azure com outro utilizador, também pode partilhar o QnA Maker.

</details>

<details>
<summary><b>Se tiver um Plano de Serviço de Aplicações com 5 bases de conhecimento QnAMaker. Pode atribuir direitos de leitura/escrita a 5 utilizadores diferentes para que cada um deles possa aceder apenas a 1 base de conhecimentos QnAMaker?</b></summary>

**Resposta** : Pode partilhar um serviço QnAMaker inteiro, não bases de conhecimento individuais.

</details>

<details>
<summary><b>Como posso alterar a mensagem padrão quando não se encontra uma boa correspondência?</b></summary>

**Resposta** : A mensagem predefinida faz parte das definições do seu serviço de Aplicação.
- Aceda ao seu recurso de serviço app no portal Azure

![serviço de app do qnamaker](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Clique na opção **Definições**

![qnamaker configurações de serviço de aplicativos](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Alterar o valor da definição **defaultAnswer**
- Reinicie o seu serviço de Aplicação

![qnamaker appservice reiniciar](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>Porque é que o meu link SharePoint não está a ser extraído?</b></summary>

**Resposta** : Consulte [as localizações da fonte de dados](./Concepts/knowledge-base.md#data-source-locations) para obter mais informações.

</details>

<details>
<summary><b>As atualizações que fiz à minha base de conhecimento não se refletem na publicação. Porque não?</b></summary>

**Resposta** : Todas as operações de edição, seja numa atualização, teste ou definição de tabela, têm de ser guardadas antes de poderem ser publicadas. Certifique-se de que **clica** no botão Guardar e treinar após cada operação de edição.

</details>

<details>
<summary><b>A base de conhecimento suporta dados ricos ou multimédia?</b></summary>

**Resposta** :

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Extração automática multimédia para ficheiros e URLs

* URLS - capacidade limitada de conversão HTML-para-Markdown.
* Ficheiros - não suportados

#### <a name="answer-text-in-markdown"></a>Texto de resposta em marcação
Uma vez que os pares QnA estejam na base de conhecimento, você pode editar o texto de marcação de uma resposta para incluir links para os meios de comunicação disponíveis a partir de URLs públicos.


</details>

<details>
<summary><b>O QnA Maker suporta línguas não inglesas?</b></summary>

**Resposta** : Ver mais detalhes sobre [línguas apoiadas.](./Overview/languages-supported.md)

Se tiver conteúdo de vários idiomas, certifique-se de criar um serviço separado para cada idioma.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

<details>
<summary><b>Porque é que o meu URL/ficheiro(s) não está a extrair pares de perguntas-respostas?</b></summary>

**Resposta** : É possível que o QnA Maker não possa extrair automaticamente algum conteúdo de perguntas e respostas (QnA) a partir de URLs FAQ válidos. Nesses casos, pode colar o conteúdo QnA num ficheiro .txt e ver se a ferramenta pode ingerir. Em alternativa, pode adicionar conteúdo editorialmente à sua base de conhecimento através do [portal QnA Maker](https://qnamaker.ai).

</details>

<details>
<summary><b>Quão grande é a base de conhecimento que posso criar?</b></summary>

**Resposta** : O tamanho da base de conhecimento depende da pesquisa SKU of Azure que escolher ao criar o serviço QnA Maker. Leia [aqui](./Tutorials/choosing-capacity-qnamaker-deployment.md) para mais detalhes.

</details>

<details>
<summary><b>Por que não consigo ver nada quando tento criar uma nova base de conhecimento?</b></summary>

**Resposta** : Ainda não criou nenhum serviço da QnA Maker em Azure. Leia [aqui](./How-To/set-up-qnamaker-service-azure.md) para aprender a fazer isso.

</details>

<details>
<summary><b>Como partilho uma base de conhecimento com os outros?</b></summary>

**Resposta** : A partilha funciona ao nível de um serviço QnA Maker, ou seja, todas as bases de conhecimento do serviço serão partilhadas. Leia [aqui](./How-To/collaborate-knowledge-base.md) como colaborar numa base de conhecimento.

</details>

<details>
<summary><b>Pode partilhar uma base de conhecimento com um contribuinte que não esteja no mesmo inquilino do Azure Ative Directory, para modificar uma base de conhecimento?</b></summary>

**Resposta** : A partilha baseia-se no controlo de acesso baseado em funções Azure (Azure RBAC). Se puder partilhar _qualquer_ recurso em Azure com outro utilizador, também pode partilhar o QnA Maker.

</details>

<details>
<summary><b>Pode atribuir direitos de leitura/escrita a 5 utilizadores diferentes para que cada um deles possa aceder apenas a 1 base de conhecimentos QnAMaker?</b></summary>

**Resposta** : Pode partilhar um serviço QnAMaker inteiro, não bases de conhecimento individuais.

</details>

<details>
<summary><b>Porque é que o meu link SharePoint não está a ser extraído?</b></summary>

**Resposta** : Consulte [as localizações da fonte de dados](./Concepts/knowledge-base.md#data-source-locations) para obter mais informações.

</details>

<details>
<summary><b>As atualizações que fiz à minha base de conhecimento não se refletem na publicação. Porque não?</b></summary>

**Resposta** : Todas as operações de edição, seja numa atualização, teste ou definição de tabela, têm de ser guardadas antes de poderem ser publicadas. Certifique-se de que **clica** no botão Guardar e treinar após cada operação de edição.

</details>

<details>
<summary><b>A base de conhecimento suporta dados ricos ou multimédia?</b></summary>

**Resposta** :

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Extração automática multimédia para ficheiros e URLs

* URLS - capacidade limitada de conversão HTML-para-Markdown.
* Ficheiros - não suportados

#### <a name="answer-text-in-markdown"></a>Texto de resposta em marcação
Uma vez que os pares QnA estejam na base de conhecimento, você pode editar o texto de marcação de uma resposta para incluir links para os meios de comunicação disponíveis a partir de URLs públicos.


</details>

<details>
<summary><b>O QnA Maker suporta línguas não inglesas?</b></summary>

**Resposta** : Ver mais detalhes sobre [línguas apoiadas.](./Overview/languages-supported.md)

Se tiver conteúdo de vários idiomas, certifique-se de criar um serviço separado para cada idioma.

</details>

---

## <a name="manage-service"></a>Gerir serviço

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

<details>
<summary><b>Quando devo reiniciar o meu serviço de aplicações?</b></summary>

**Resposta** : Refresque o seu serviço de aplicações quando o ícone de precaução estiver ao lado do valor da versão para a base de conhecimento na tabela **de teclas Endpoint** na [página](https://www.qnamaker.ai/UserSettings) **Definições** do Utilizador .

</details>

<details>
<summary><b>Apaguei o meu serviço de pesquisa existente. Como posso resolver isto?</b></summary>

**Resposta** : Se eliminar um índice de Pesquisa Cognitiva Azure, a operação é final e o índice não pode ser recuperado.

</details>

<details>
<summary><b>Apaguei o meu `testkb` índice no meu serviço de Pesquisa. Como posso resolver isto?</b></summary>

**Resposta:** Os seus dados antigos não podem ser recuperados. Crie um novo recurso QnA Maker e crie novamente a sua base de conhecimento.

</details>

<details>
<summary><b>Quando devo refrescar as minhas chaves do ponto final?</b></summary>

**Resposta:** Refresque as chaves do ponto final se suspeitar que foram comprometidas.

</details>

<details>
<summary><b>Posso usar o mesmo recurso de Pesquisa Cognitiva Azure para bases de conhecimento usando várias línguas?</b></summary>

**Resposta** : Para utilizar várias bases linguísticas e múltiplas bases de conhecimento, o utilizador tem de criar um recurso QnA Maker para cada idioma. Isto criará um serviço de pesquisa Azure separado por idioma. Misturar diferentes bases de conhecimento de linguagem num único serviço de pesquisa Azure resultará numa relevância degradada dos resultados.

</details>

<details>
<summary><b>Como posso alterar o nome do recurso Azure Cognitive Search usado pela QnA Maker?</b></summary>

**Resposta** : O nome do recurso Azure Cognitive Search é o nome de recurso do QnA Maker com algumas letras aleatórias anexadas no final. Isto torna difícil distinguir entre múltiplos recursos de pesquisa para o QnA Maker. Crie um serviço de pesquisa separado (nomeando-o como gostaria) e conecte-o ao seu Serviço QnA. Os passos são semelhantes aos passos que precisa de fazer para [atualizar uma pesquisa do Azure.](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)

</details>

<details>
<summary><b>Quando a QnA Maker `Runtime core is not initialized,` voltar, como é que o arranjo?</b></summary>

**Resposta** : O espaço do disco para o seu serviço de aplicações pode estar cheio. Passos para corrigir o seu espaço em disco:

1. No [portal Azure,](https://portal.azure.com)selecione o seu serviço de App do Criador QnA e, em seguida, pare o serviço.
1. Enquanto ainda está no serviço app, selecione **Ferramentas de Desenvolvimento,** em seguida, **Ferramentas Avançadas,** em seguida, **Vá**. Isto abre uma nova janela do navegador.
1. Selecione **a consola Debug** e, em seguida, **CMD** para abrir uma ferramenta de linha de comando.
1. Navegue para o _site/wwwroot/Data/QnAMaker/diretório._
1. Remova todas as pastas cujo nome começa `rd` por .

    **Não elimine** o seguinte:

    * KbIdToRankerMappings.txt arquivo
    * EndpointSettings.jsno arquivo
    * Pasta EndpointKeys

1. Inicie o serviço app.
1. Aceda à sua base de conhecimentos para verificar se funciona agora.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)


<details>
<summary><b>Apaguei o meu serviço de pesquisa existente. Como posso resolver isto?</b></summary>

**Resposta** : Se eliminar um índice de Pesquisa Cognitiva Azure, a operação é final e o índice não pode ser recuperado.

</details>

<details>
<summary><b>Apaguei o meu `testkb` índice no meu serviço de Pesquisa. Como posso resolver isto?</b></summary>

**Resposta:** Os seus dados antigos não podem ser recuperados. Crie um novo recurso QnA Maker e crie novamente a sua base de conhecimento.

</details>

<details>
<summary><b>Posso usar o mesmo recurso de Pesquisa Cognitiva Azure para bases de conhecimento usando várias línguas?</b></summary>

**Resposta** : Para utilizar várias bases linguísticas e múltiplas bases de conhecimento, o utilizador tem de criar um recurso QnA Maker para cada idioma. Isto criará um serviço de pesquisa Azure separado por idioma. Misturar diferentes bases de conhecimento de linguagem num único serviço de pesquisa Azure resultará numa relevância degradada dos resultados.

</details>

<details>
<summary><b>Como posso alterar o nome do recurso Azure Cognitive Search usado pela QnA Maker?</b></summary>

**Resposta** : O nome do recurso Azure Cognitive Search é o nome de recurso do QnA Maker com algumas letras aleatórias anexadas no final. Isto torna difícil distinguir entre múltiplos recursos de pesquisa para o QnA Maker. Crie um serviço de pesquisa separado (nomeando-o como gostaria) e conecte-o ao seu Serviço QnA. Os passos são semelhantes aos passos que precisa de fazer para [atualizar uma pesquisa do Azure.](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)

</details>

---

## <a name="integrate-with-other-services-including-bots"></a>Integre outros serviços, incluindo bots

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

<details>
<summary><b>Preciso de utilizar o Bot Framework para utilizar o QnA Maker?</b></summary>

**Resposta** : Não, não precisa de utilizar a [Estrutura bot](https://github.com/Microsoft/botbuilder-dotnet) com o Fabricante QnA. No entanto, o QnA Maker é oferecido como um dos vários modelos no [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0&preserve-view=true). O Bot Service permite o rápido desenvolvimento inteligente de bots através do Microsoft Bot Framework, e funciona num ambiente sem servidor.

</details>

<details>
<summary><b>Como posso criar um novo bot com a QnA Maker?</b></summary>

**Resposta** : Siga as instruções [desta](./Quickstarts/create-publish-knowledge-base.md) documentação para criar o seu Bot com o Serviço Azure Bot.

</details>

<details>
<summary><b>Como uso uma base de conhecimento diferente com um serviço de bots Azure existente?</b></summary>

**Resposta** : Precisa de ter as seguintes informações sobre a sua base de conhecimentos:

* Identificação da base de conhecimento.
* O nome de subdomínio personalizado da base de conhecimento publicado, conhecido como `host` , encontrado na página De **Definições** após a sua publicação.
* A chave de ponto final publicada da base de conhecimento - encontrada na página **Definições** após a sua publicação.

Com esta informação, aceda ao serviço de aplicações do seu bot no portal Azure. Em **Definições -> configurações de configuração -> configurações de aplicação** , altere esses valores.

A chave final da base de conhecimento está rotulada `QnAAuthkey` no serviço ABS.

</details>

<details>
<summary><b>Duas ou mais aplicações de clientes podem partilhar uma base de conhecimento?</b></summary>

**Resposta** : Sim, a base de conhecimento pode ser questionada a partir de qualquer número de clientes. Se a resposta da base de conhecimento parecer lenta ou de tempo, considere a atualização do nível de serviço para o serviço de aplicações associado à base de conhecimento.

</details>

<details>
<summary><b>Como posso incorporar o serviço QnA Maker no meu site?</b></summary>

**Resposta** : Siga estes passos para incorporar o serviço QnA Maker como um controlo de chat web no seu website:

1. Crie o seu bot FAQ seguindo as instruções [aqui.](./Quickstarts/create-publish-knowledge-base.md)
2. Ativar o chat web seguindo os passos [aqui](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)


<details>
<summary><b>Preciso de utilizar o Bot Framework para utilizar o QnA Maker?</b></summary>

**Resposta** : Não, não precisa de utilizar a [Estrutura bot](https://github.com/Microsoft/botbuilder-dotnet) com o Fabricante QnA. No entanto, o QnA Maker é oferecido como um dos vários modelos no [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0&preserve-view=true). O Bot Service permite o rápido desenvolvimento inteligente de bots através do Microsoft Bot Framework, e funciona num ambiente sem servidor.

</details>

<details>
<summary><b>Como posso criar um novo bot com a QnA Maker?</b></summary>

**Resposta** : Siga as instruções [desta](./Quickstarts/create-publish-knowledge-base.md) documentação para criar o seu Bot com o Serviço Azure Bot.

</details>

<details>
<summary><b>Como uso uma base de conhecimento diferente com um serviço de bots Azure existente?</b></summary>

**Resposta** : Precisa de ter as seguintes informações sobre a sua base de conhecimentos:

* Identificação da base de conhecimento.
* O nome de subdomínio personalizado da base de conhecimento publicado, conhecido como `host` , encontrado na página De **Definições** após a sua publicação.
* A chave de ponto final publicada da base de conhecimento - encontrada na página **Definições** após a sua publicação.

Com esta informação, aceda ao serviço de aplicações do seu bot no portal Azure. Em **Definições -> configurações de configuração -> configurações de aplicação** , altere esses valores.

A chave final da base de conhecimento está rotulada `QnAAuthkey` no serviço ABS.

</details>

<details>
<summary><b>Duas ou mais aplicações de clientes podem partilhar uma base de conhecimento?</b></summary>

**Resposta** : Sim, a base de conhecimento pode ser questionada a partir de qualquer número de clientes. Se a resposta da base de conhecimento parecer lenta ou de tempo, considere a atualização do nível de serviço para o serviço de aplicações associado à base de conhecimento.

</details>

<details>
<summary><b>Como posso incorporar o serviço QnA Maker no meu site?</b></summary>

**Resposta** : Siga estes passos para incorporar o serviço QnA Maker como um controlo de chat web no seu website:

1. Crie o seu bot FAQ seguindo as instruções [aqui.](./Quickstarts/create-publish-knowledge-base.md)
2. Ativar o chat web seguindo os passos [aqui](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)

---

## <a name="data-storage"></a>Armazenamento de dados

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

<details>
<summary><b>Que dados são armazenados e onde é armazenado?</b></summary>

**Resposta** :

Ao criar o seu serviço QnA Maker, selecionou uma região Azure. As suas bases de conhecimento e ficheiros de registo estão armazenados nesta região.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

<details>
<summary><b>Que dados são armazenados e onde é armazenado?</b></summary>

**Resposta** :

Ao criar o seu serviço QnA Maker, selecionou uma região Azure. As suas bases de conhecimento e ficheiros de registo estão armazenados nesta região.

</details>

---
