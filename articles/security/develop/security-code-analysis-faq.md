---
title: Documentação de análise de código de segurança da Microsoft FAQ
description: Este artigo contém uma FAQ sobre a extensão da Análise de Código de Segurança da Microsoft
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: cb04a8e5a6d8c982a35cb5c448e4b6d93825bf73
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460227"
---
# <a name="frequently-asked-questions"></a>Perguntas mais frequentes
Tem perguntas? Consulte as seguintes FAQ para obter mais informações.

## <a name="general-faq"></a>FaQ Geral

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>Posso instalar a extensão no meu caso visual studio team server em vez de em um caso Azure DevOps?

Não. A extensão não está disponível para download e instalação para Visual Studio Team Foundation Server.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>Tenho de fazer análises de código de segurança da Microsoft com a minha construção? 

Talvez sim. Depende do tipo de ferramenta de análise. O código fonte pode ser a única coisa necessária, ou a saída de construção pode ser necessária.

Por exemplo, o Credential Scanner (CredScan) analisa ficheiros dentro da estrutura da pasta do repositório de códigos. Devido a esta análise, pode executar os Registos de Análise de Segurança do CredScan e publicar a construção de tarefas numa construção autónoma para obter resultados.

Para outras ferramentas como binSkim que analisam artefactos pós-construção, a construção é necessária primeiro.

### <a name="can-i-break-my-build-when-results-are-found"></a>Posso quebrar a minha construção quando os resultados forem encontrados?

Sim. Pode introduzir uma rutura de construção quando qualquer ferramenta reportar um problema ou problema no seu ficheiro de registo. Basta adicionar a tarefa de construção pós-análise e selecionar a caixa de verificação para qualquer ferramenta para a qual queira quebrar a construção.

Na UI da tarefa de Pós-Análise, pode optar por quebrar a construção quando qualquer ferramenta reporta apenas erros ou erros e avisos.

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Como é que os argumentos da linha de comando em Azure DevOps diferem desses argumentos nas ferramentas autónomas de ambiente de trabalho? 

Na maior parte das vezes, os Azure DevOps constroem tarefas são invólucros diretos em torno dos argumentos da linha de comando das ferramentas de segurança. Pode passar como argumento para uma tarefa de construção qualquer coisa que normalmente passe para uma ferramenta de linha de comando.

Diferenças visíveis:

- As ferramentas funcionam a partir da pasta fonte do agente $(Build.SourcesDirectory) ou a partir de %BUILD_SOURCESDIRECTORY%. Um exemplo é C:\agent\_work\1\s.
- Os caminhos nos argumentos podem ser relativos à raiz do diretório de origem previamente listado. Os caminhos também podem ser absolutos. Obtém caminhos absolutos usando As Variáveis de Construção de Azure DevOps ou executando um agente no local com locais de implantação conhecidos de recursos locais.
- As ferramentas fornecem automaticamente um caminho ou pasta de ficheirode saída. Se fornecer um local de saída para uma tarefa de construção, essa localização é substituída por um caminho para a nossa conhecida localização de registos no agente de construção
- Alguns argumentos adicionais da linha de comando são alterados para algumas ferramentas. Um exemplo é a adição ou remoção de opções que garantem que não é lançado gui.

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Posso executar uma tarefa de construção como o Credential Scanner através de múltiplos repositórios numa Construção Azure DevOps?

Não. Executar as ferramentas de desenvolvimento seguras através de múltiplos repositórios num único oleoduto não é suportado.

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>O ficheiro de saída que especificei não está a ser criado, ou não consigo encontrar o ficheiro de saída que especificei.

As tarefas de construção filtram alguma entrada do utilizador. Para esta pergunta especificamente, eles atualizam a localização do ficheiro de saída gerado para ser uma localização comum no agente de construção. Para mais informações sobre este local, consulte as seguintes questões.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Onde estão os ficheiros de saída gerados pelas ferramentas guardadas? 

As tarefas de construção adicionam automaticamente caminhos de saída a esta localização conhecida\_no agente de construção: $(Agent.BuildDirectory) sdt\logs. Como padronizamos neste local, todas as equipas que produzem ou consomem registos de análise de código têm acesso à saída.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Posso fazer fila para executar estas tarefas num agente de construção hospedado? 

Sim. Todas as tarefas e ferramentas na extensão podem ser executadas num agente de construção hospedado.

>[!NOTE]
> A tarefa de construção do Scanner Anti-Malware requer um agente de construção com o Windows Defender ativado. Acolheu o Visual Studio 2017 e mais tarde fornece tal agente. A tarefa de construção não será executada no Visual Studio 2015.
>
> Embora as assinaturas não possam ser atualizadas sobre estes agentes, as assinaturas devem ter sempre menos de três horas de idade.

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Posso executar estas tarefas de construção como parte de um oleoduto de libertação em oposição a um oleoduto de construção?

Na maioria dos casos, sim.

No entanto, a Azure DevOps não suporta tarefas de execução dentro dos oleodutos de lançamento quando essas tarefas publicam artefactos. Esta falta de suporte impede que a tarefa de Registos de Análise de Segurança da Publicação seja com sucesso num pipeline de libertação. Em vez disso, a tarefa falha com uma mensagem de erro descritiva.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>De onde as tarefas de construção descarregam as ferramentas?

As tarefas de construção podem descarregar os pacotes NuGet das ferramentas a partir do feed de gestão de [pacotes Azure DevOps](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json). As tarefas de construção também podem usar o Gestor de Pacotes do Nó, que deve ser pré-instalado no agente de construção. Um exemplo de tal instalação é o comando **npm instalar tslint**.

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>Que efeito tem a instalação da extensão na minha organização Azure DevOps? 

Após a sua instalação, as tarefas de construção de segurança fornecidas pela extensão ficam disponíveis para todos os utilizadores da sua organização. Quando cria ou edita um Pipeline Azure, estas tarefas estão disponíveis na lista de recolha de tarefas de construção. Caso contrário, a instalação da extensão na sua organização Azure DevOps não tem qualquer efeito. A instalação não modifica quaisquer definições de conta, configurações do projeto ou oleodutos.

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>A instalação da extensão modifica os meus oleodutos Azure existentes? 

Não. A instalação da extensão disponibiliza as tarefas de construção de segurança para além dos seus oleodutos. Ainda é necessário adicionar ou atualizar definições de construção, para que as ferramentas possam funcionar com o seu processo de construção.

## <a name="task-specific-faq"></a>FAQ específico de tarefa

Questões específicas para construir tarefas estão listadas nesta secção.

### <a name="credential-scanner"></a>Scanner credencial

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>Quais são os cenários e exemplos comuns de supressão?

Aqui estão os detalhes de dois dos cenários de supressão mais comuns.

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Para suprimir todas as ocorrências de um dado segredo dentro do caminho especificado

A chave hash do segredo do ficheiro de saída credScan é necessária como mostrado na amostra seguinte.

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
                "_justification": "Secret used by MSDN sample, it is fake."
            }
          ]
        }

>[!WARNING]
> A chave hash é gerada por uma parte do valor correspondente ou conteúdo de ficheiro. Qualquer revisão do código fonte pode alterar a chave hash e desativar a regra de supressão.

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Para suprimir todos os segredos num ficheiro especificado ou para suprimir os segredos em si

A expressão do ficheiro pode ser um nome de ficheiro. Também pode ser a parte base de um caminho de arquivo completo ou um nome de ficheiro. Não são suportados carateres universais.

Os exemplos seguintes mostram \<como suprimir o ficheiro InputPath>\src\JS\lib\angular.js

Exemplos de regras de supressão válidas:

- \<InputPath>\src\JS\lib\angular.js - suprime o ficheiro no caminho especificado
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angular.js - suprime qualquer ficheiro com o mesmo nome

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "file": "\\files\\AdditonalSearcher.xml", 
                "_justification": "Additional CredScan searcher specific to my team"
            },
            {
                "file": "\\files\\unittest.pfx", 
                "_justification": "Legitimate UT certificate file with private key"
            }
          ]
        }      

>[!WARNING] 
> Todos os segredos futuros adicionados ao ficheiro também serão suprimidos automaticamente.

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>Quais são as orientações recomendadas para a gestão de segredos?

Os seguintes recursos ajudam-no a gerir de forma segura segredos e a aceder a informações sensíveis a partir das suas aplicações:

 - [Azure Key Vault](../../key-vault/index.yml)
 - [Diretório Ativo Azure (Azure AD)](../../sql-database/sql-database-aad-authentication.md)
 - [Identidade de serviço gerido pela Azure AD (MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Identidades geridas para os recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Identidades geridas no Azure App Service e funções azure](../../app-service/overview-managed-identity.md)
 - [Biblioteca appAuthentication](../../key-vault/general/service-to-service-authentication.md)


Para mais informações, consulte o post do blog [Managing Secrets Securely in the Cloud](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/).

#### <a name="can-i-write-my-own-custom-searchers"></a>Posso escrever os meus pesquisadores personalizados?

O Credential Scanner baseia-se num conjunto de pesquisadores de conteúdo que são geralmente definidos no ficheiro buildsearchers.xml. O ficheiro contém uma série de objetos serializados XML que representam um objeto **ContentSearcher.** O programa é distribuído com um conjunto de pesquisadores bem testados. Mas também pode implementar os seus próprios pesquisadores personalizados.

Um pesquisador de conteúdo é definido da seguinte forma:

- **Nome**: O nome do pesquisador descritivo a utilizar nos ficheiros de saída do Scanner Credencial. Recomendamos que use a convenção de nomeação de caso de camelo para nomes de pesquisadores.
- **RegraId**: A identificação opaca estável do pesquisador:
    - Um pesquisador predefinido do Scanner Credencial é atribuído a um valor **RuleId** como CSCAN0010, CSCAN0020 ou CSCAN0030. O último dígito é reservado para potencialmente fundir ou dividir grupos de pesquisadores através de expressões regulares (regex).
    - O valor **RuleId** para um pesquisador personalizado deve ter o seu próprio espaço de nome. Exemplos\<incluem CSCAN- Namespace\>0010, CSCAN-\<Namespace\>0020 e CSCAN-\<Namespace\>0030.
    - Um nome de pesquisador totalmente qualificado é a combinação de um valor **RuleId** e um nome de pesquisa. Exemplos incluem CSCAN0010. KeyStoreFiles e CSCAN0020. Certificado de código Base64Encoded.
- **ResourceMatchPattern**: Regex de extensões de ficheiros para verificar contra o searchdor.
- **ContentSearchPatterns**: Um conjunto de cordas contendo declarações regex para combinar. Se não forem definidos padrões de pesquisa, todos os ficheiros que correspondam ao valor **ResourceMatchPattern** são devolvidos.
- **ContentSearchFilters**: Um conjunto de cordas que contêm declarações regex para filtrar falsos positivos específicos do pesquisador.
- **MatchDetails**: Uma mensagem descritiva, instruções de mitigação ou ambas a adicionar para cada partida do pesquisador.
- **Recomendação**: O conteúdo do campo de sugestões para uma correspondência utilizando o formato prefast.
- **Gravidade**: Um inteiro que reflete o nível de gravidade de um problema. O nível de severidade mais elevado tem o valor 1.

  ![XML mostrando configuração do Scanner Credencial](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Analisadores Roslyn

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>Quais são os erros comuns ao usar a tarefa dos Analisadores Roslyn?

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>O projeto foi restaurado usando uma versão errada da Microsoft.NETCore.App

A mensagem de erro completa:

"Error: O projeto foi restaurado utilizando a versão Microsoft.NETCore.App *x.x.x*, mas com as definições atuais, a versão *y.y.y* seria usada em vez disso. Para resolver este problema, certifique-se de que as mesmas configurações são utilizadas para restaurar e para operações subsequentes, tais como construir ou publicar. Normalmente, este problema pode ocorrer se a propriedade RuntimeIdentifier for definida durante a construção ou publicação, mas não durante a restauração."

Como as tarefas da Roslyn Analisers funcionam como parte da compilação, a árvore-fonte na máquina de construção precisa de estar num estado construível.

Um passo entre a sua construção principal e os passos de Roslyn Analisers pode ter colocado a árvore de origem num estado que impede a construção. Este passo extra é provavelmente **dotnet.exe publicar**. Tente duplicar o passo que faz uma restauração NuGet antes do passo roslyn Analisers. Este passo duplicado pode colocar a árvore de origem de volta num estado construível.

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc.exe não pode criar um analisador instância

A mensagem de erro completa:

"'csc.exe' saiu com o código de erro 1 -- Um exemplo de\\analisador *AAAA* não pode ser criado a partir de C:*BBBB*.dll : Não conseguiu carregar ficheiro si ou montagem 'Microsoft.CodeAnalysis, Version=*X.X.X,* Culture=neutral, PublicKeyToken=31bf3856ad364e35' ou uma das suas dependências. O sistema não pode encontrar o ficheiro especificado."

Certifique-se de que o seu compilador apoia a Roslyn Analisers. A execução do comando **csc.exe /versão** deve reportar um valor de versão de 2.6 ou posterior.

Por vezes, um ficheiro .csproj pode sobrepor-se à instalação do Estúdio Visual da máquina de construção, referindo-se a um pacote da Microsoft.Net.Compilers. Se não pretender utilizar uma versão específica do compilador, remova referências ao Microsoft.Net.Compilers. Caso contrário, certifique-se de que a versão do pacote referenciado também é de 2.6 ou mais tarde.

Tente obter o caminho de registo de erros, especificado na opção **csc.exe/errorlog.** A opção e o caminho aparecem no registo para os Analisadores Roslyn construir tarefa. Podem parecer algo como **/errorlog:F:\ts-services-123\_trabalho\456\s\Projeto\Code\Code.csproj.sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>A versão do compilador C# não é suficientemente recente.

Para obter as versões mais recentes do compilador C#, vá a [Microsoft.Net.Compilers](https://www.nuget.org/packages/Microsoft.Net.Compilers). Para obter a sua versão instalada, execute **csc.exe /versão** a uma solicitação de comando. Certifique-se de que faz referência a um pacote Microsoft.Net.Compilers NuGet que é a versão 2.6 ou posterior.

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>Não são encontrados registos MSBuild e VSBuild

A tarefa de construção de Análises Roslyn precisa de consultar azure DevOps para o log MSBuild da tarefa de construção MSBuild. Se a tarefa do analisador for imediatamente após a tarefa MSBuild, o registo ainda não estará disponível. Coloque outras tarefas entre a tarefa MSBuild e a tarefa roslyn Analisadores. Exemplos de outras tarefas incluem BinSkim e Anti-Malware Scanner.

## <a name="next-steps"></a>Passos seguintes

Se precisar de assistência adicional, o Suporte de Análise do Código de Segurança da Microsoft está disponível de segunda a sexta-feira das 9:00 às 17:00 hora padrão do Pacífico.

- Embarque: Consulte a nossa [documentação de embarque](security-code-analysis-onboard.md)
  
- Suporte: Envie um e-mail para a nossa equipa no [Suporte de Análise de Código](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request) de Segurança da Microsoft