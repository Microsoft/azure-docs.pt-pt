---
title: Documentação de análise de código de segurança da Microsoft FAQ
description: Saiba mais sobre a extensão de Análise de Código de Segurança da Microsoft, analisando perguntas frequentes (PERGUNTAS Frequentes).
author: sukhans
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
ms.openlocfilehash: f872159e538dc85121a7a6d4d6503fd18a263628
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87543049"
---
# <a name="frequently-asked-questions"></a>Perguntas mais frequentes
Tem perguntas? Consulte as seguintes FAQ para mais informações.

## <a name="general-faq"></a>FAQs Gerais

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>Posso instalar a extensão no meu servidor da Fundação Visual Studio Team em vez de num caso de Azure DevOps?

N.º A extensão não está disponível para download e instalação para Visual Studio Team Foundation Server.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>Tenho de executar a Microsoft Security Code Analysis com a minha construção? 

Talvez sim. Depende do tipo de ferramenta de análise. O código-fonte pode ser a única coisa que é necessária, ou a saída de construção pode ser necessária.

Por exemplo, o Scanner Credencial (CredScan) analisa ficheiros dentro da estrutura da pasta do repositório de código. Devido a esta análise, pode executar os Registos de Análise de Segurança credScan e Publicação, construir tarefas numa construção autónoma para obter resultados.

Para outras ferramentas como BinSkim que analisam artefactos pós-construção, a construção é necessária primeiro.

### <a name="can-i-break-my-build-when-results-are-found"></a>Posso quebrar a minha construção quando os resultados forem encontrados?

Sim. Pode introduzir uma rutura de construção quando qualquer ferramenta reporta um problema ou problema no seu ficheiro de registo. Basta adicionar a tarefa de construção pós-análise e selecionar a caixa de verificação para qualquer ferramenta para a qual pretende quebrar a construção.

Na UI da tarefa De Pós-Análise, pode optar por quebrar a construção quando qualquer ferramenta reporta erros apenas ou erros e avisos.

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Como é que os argumentos da linha de comando em Azure DevOps diferem dos argumentos nas ferramentas de ambiente de trabalho autónomas? 

Na maior parte das vezes, as tarefas de construção do Azure DevOps são invólucros diretos em torno dos argumentos da linha de comando das ferramentas de segurança. Pode passar como argumentos para uma tarefa de construção qualquer coisa que normalmente passe para uma ferramenta de linha de comando.

Diferenças percetíveis:

- As ferramentas são executadas a partir da pasta de origem do agente $(Build.SourcesDirectory) ou de %BUILD_SOURCESDIRECTORY%. Um exemplo é C:\agente \_ trabalho\1\s.
- Os caminhos dos argumentos podem ser relativos à raiz do diretório de origem anteriormente listado. Os caminhos também podem ser absolutos. Obtém caminhos absolutos, quer utilizando variáveis de construção de devOps Azure DevOps, quer executando um agente no local com locais de implantação conhecidos de recursos locais.
- As ferramentas fornecem automaticamente um caminho ou uma pasta de ficheiro de saída. Se fornecer uma localização de saída para uma tarefa de construção, essa localização é substituída por um caminho para a nossa conhecida localização de registos no agente de construção
- Alguns argumentos adicionais da linha de comando são alterados para algumas ferramentas. Um exemplo é a adição ou remoção de opções que garantem o lançamento de nenhum GUI.

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Posso executar uma tarefa de construção como o Scanner Credencial através de vários repositórios numa Construção de DevOps Azure?

N.º Executar as ferramentas de desenvolvimento seguras através de vários repositórios num único oleoduto não é suportado.

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>O ficheiro de saída que especifiquei não está a ser criado, ou não consigo encontrar o ficheiro de saída que especifiquei.

As tarefas de construção filtram algumas entradas do utilizador. Para esta questão especificamente, eles atualizam a localização do ficheiro de saída gerado para ser uma localização comum no agente de construção. Para mais informações sobre este local, consulte as seguintes questões.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Onde estão os ficheiros de saída gerados pelas ferramentas guardadas? 

As tarefas de construção adicionam automaticamente caminhos de saída a este local bem conhecido no agente de construção: $(Agent.BuildDirectory) \_ sdt\logs. Como nós normalizamos neste local, todas as equipas que produzem ou consomem registos de análise de código têm acesso à saída.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Posso fazer fila para executar estas tarefas num agente de construção hospedado? 

Sim. Todas as tarefas e ferramentas da extensão podem ser executadas num agente de construção hospedado.

>[!NOTE]
> A tarefa de construção do Scanner Anti-Malware requer um agente de construção com o Windows Defender ativado. Acolheu o Visual Studio 2017 e mais tarde forneceu tal agente. A tarefa de construção não será executada no Visual Studio 2015 agente hospedado.
>
> Embora as assinaturas não possam ser atualizadas sobre estes agentes, as assinaturas devem ter sempre menos de três horas de idade.

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Posso executar estas tarefas de construção como parte de um oleoduto de libertação em vez de um oleoduto de construção?

Na maioria dos casos, sim.

No entanto, a Azure DevOps não suporta tarefas de execução dentro dos oleodutos de lançamento quando essas tarefas publicam artefactos. Esta falta de suporte impede que a tarefa de Publish Security Analysis Logs seja feita com sucesso num oleoduto de lançamento. Em vez disso, a tarefa falha com uma mensagem de erro descritiva.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>De onde as tarefas de construção descarregam as ferramentas?

As tarefas de construção podem descarregar os pacotes NuGet das ferramentas a partir do [feed de Gestão de Pacotes Azure DevOps](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json). As tarefas de construção também podem utilizar o Node Package Manager, que deve ser pré-instalado no agente de construção. Um exemplo de tal instalação é o comando **npm instalar tslint**.

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>Que efeito tem a instalação da extensão na minha organização Azure DevOps? 

Após a sua instalação, as tarefas de construção de segurança fornecidas pela extensão ficam disponíveis para todos os utilizadores da sua organização. Quando cria ou edita um Pipeline Azure, estas tarefas estão disponíveis na lista de recolha de tarefas de construção. Caso contrário, a instalação da extensão na sua organização Azure DevOps não tem qualquer efeito. A instalação não modifica quaisquer definições de conta, definições de projeto ou oleodutos.

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>A instalação da extensão modifica os meus gasodutos Azure existentes? 

N.º A instalação da extensão disponibiliza as tarefas de construção de segurança para a adição aos seus oleodutos. Você ainda é obrigado a adicionar ou atualizar definições de construção, para que as ferramentas possam trabalhar com o seu processo de construção.

## <a name="task-specific-faq"></a>FAQ específico de tarefas

Nesta secção estão listadas questões específicas para construir tarefas.

### <a name="credential-scanner"></a>Scanner credencial

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>O que são cenários e exemplos comuns de supressão?

Aqui estão os detalhes de dois dos cenários de supressão mais comuns.

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Para suprimir todas as ocorrências de um dado segredo dentro do caminho especificado

A chave de haxixe do segredo do ficheiro de saída credScan é necessária como mostrado na amostra seguinte.

```js
{
    "tool": "Credential Scanner",
    "suppressions": [
    {
        "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
        "_justification": "Secret used by MSDN sample, it is fake."
    }
  ]
}
```

>[!WARNING]
> A chave hash é gerada por uma parte do valor correspondente ou do conteúdo do ficheiro. Qualquer revisão do código de origem pode alterar a tecla de haxixe e desativar a regra de supressão.

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Para suprimir todos os segredos num ficheiro especificado ou para suprimir os segredos em si

A expressão do ficheiro pode ser um nome de ficheiro. Também pode ser a parte base de um caminho de arquivo completo ou um nome de arquivo. Não são suportados carateres universais.

Os exemplos a seguir mostram como suprimir o ficheiro \<InputPath>\src\JS\lib\angular.js

Exemplos de regras válidas de supressão:

- \<InputPath>\src\JS\lib\angular.js - suprime o ficheiro no caminho especificado
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angular.js - suprime qualquer ficheiro com o mesmo nome

```js
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
```

>[!WARNING] 
> Todos os segredos futuros adicionados ao ficheiro também serão suprimidos automaticamente.

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>Quais são as orientações recomendadas para a gestão de segredos?

Os seguintes recursos ajudam-no a gerir de forma segura segredos e a aceder a informações sensíveis a partir das suas aplicações:

 - [Azure Key Vault](../../key-vault/index.yml)
 - [Azure Active Directory (Azure AD)](../../azure-sql/database/authentication-aad-overview.md)
 - [Identidade de serviço gerido Azure AD (MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Identidades geridas para os recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Identidades geridas no Azure App Service e nas Funções Azure](../../app-service/overview-managed-identity.md)
 - [Biblioteca appAuthentication](../../key-vault/general/service-to-service-authentication.md)


Para mais informações, consulte o post de blog [Managing Secrets Securely in the Cloud](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/).

#### <a name="can-i-write-my-own-custom-searchers"></a>Posso escrever os meus próprios pesquisadores personalizados?

O Scanner Credencial baseia-se num conjunto de pesquisadores de conteúdo que são geralmente definidos no ficheiro buildsearchers.xml. O ficheiro contém uma matriz de objetos serializados XML que representam um objeto **ContentSearcher.** O programa é distribuído com um conjunto de pesquisadores bem testados. Mas também pode implementar os seus próprios pesquisadores personalizados.

Um pesquisador de conteúdos é definido da seguinte forma:

- **Nome**: O nome do pesquisador descritivo a ser utilizado nos ficheiros de saída do Scanner Credencial. Recomendamos que use a convenção de nomeação de camelo para nomes de pesquisadores.
- **RuleId**: O ID opaco estável do pesquisador:
    - Um pesquisador padrão do Scanner Credencial é atribuído a um valor **RuleId** como CSCAN0010, CSCAN0020 ou CSCAN0030. O último dígito é reservado para grupos de pesquisadores potencialmente fundidos ou divididos através de expressões regulares (regex).
    - O valor **RuleId** para um pesquisador personalizado deve ter o seu próprio espaço de nome. Exemplos incluem CSCAN- \<Namespace\> 0010, \<Namespace\> CSCAN-0020 e CSCAN-0030. \<Namespace\>
    - Um nome de pesquisador totalmente qualificado é a combinação de um valor **RuleId** e um nome de pesquisador. Exemplos incluem CSCAN0010. KeyStoreFiles e CSCAN0020. Base64EncodedCertificate.
- **ResourceMatchPattern**: Regex de extensões de ficheiros para verificar contra o pesquisador.
- **ConteúdoSearchPatterns**: Uma variedade de cordas que contêm declarações de regex para combinar. Se não forem definidos padrões de pesquisa, todos os ficheiros correspondentes ao valor **ResourceMatchPattern** são devolvidos.
- **ConteúdoSearchFiltros**: Uma série de cordas que contêm declarações de regex para filtrar falsos positivos específicos do pesquisador.
- **MatchDetails**: Uma mensagem descritiva, instruções de mitigação ou ambos a serem adicionados para cada partida do pesquisador.
- **Recomendação**: O conteúdo do campo de sugestões para uma correspondência utilizando o formato de relatório PREfast.
- **Gravidade**: Um número inteiro que reflete o nível de gravidade de um problema. O nível de severidade mais elevado tem o valor 1.

  ![XML mostrando configuração do Scanner Credencial](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Analisadores Roslyn

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>Quais são os erros comuns ao utilizar a tarefa Roslyn Analyzers?

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>O projeto foi restaurado usando uma versão errada do Microsoft.NETCore.App

A mensagem de erro completa:

"Error: The project was restored using Microsoft.NETCore.App version *x.x.x*, but with current settings, version *y.y.y* would be used in instead. Para resolver este problema, certifique-se de que as mesmas definições são utilizadas para restauro e para operações subsequentes, tais como construção ou publicação. Normalmente, este problema pode ocorrer se a propriedade RuntimeIdentifier for definida durante a construção ou publicação, mas não durante a restauração."

Como as tarefas da Roslyn Analyzers funcionam como parte da compilação, a árvore-fonte da máquina de construção precisa de estar num estado construível.

Um passo entre a sua construção principal e os passos de Roslyn Analyzers pode ter colocado a árvore de origem num estado que impede a construção. Este passo extra é provavelmente **dotnet.exe publicar.** Tente duplicar o passo que faz uma restauração NuGet pouco antes do passo roslyn Analisers. Este passo duplicado pode colocar a árvore de origem de volta em um estado construível.

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc.exe não pode criar um exemplo de analisador

A mensagem de erro completa:

"'csc.exe' saído com código de erro 1 -- Uma instância do analisador *AAAA* não pode ser criada a partir de C: \\ *BBBB*.dll : Não foi possível carregar ficheiro ou montagem 'Microsoft.CodeAnalysis, Versão=*X.X.X,X,* Cultura=neutro, PublicKeyToken=31bf3856ad364e35' ou uma das suas dependências. O sistema não consegue encontrar o ficheiro especificado."

Certifique-se de que o seu compilador suporta Roslyn Analyzers. Executar o comando **csc.exe /versão** deve reportar um valor de versão de 2.6 ou mais tarde.

Por vezes, um ficheiro .csproj pode sobrepor-se à instalação do Estúdio Visual da máquina de construção, fazendo referência a um pacote do Microsoft.Net.Compilers. Se não pretender utilizar uma versão específica do compilador, remova as referências ao Microsoft.Net.Compilers. Caso contrário, certifique-se de que a versão do pacote referenciado também é 2.6 ou mais tarde.

Tente obter o caminho de registo de erros, que é especificado na opção **csc.exe/errorlog.** A opção e o caminho aparecem no log para a tarefa de construção de Roslyn Analyzers. Podem parecer algo como **/errorlog:F:\ts-services-123 \_ work\456\s\Some\Project\Code\Code.csproj.sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>A versão do compilador C# não é recente o suficiente

Para obter as versões mais recentes do compilador C#, vá ao [Microsoft.Net.Compilers](https://www.nuget.org/packages/Microsoft.Net.Compilers). Para obter a sua versão instalada, execute **csc.exe /versão** num pedido de comando. Certifique-se de que faz referência a um pacote NuGet Microsoft.Net.Compilers que é a versão 2.6 ou posterior.

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>Os registos MSBuild e VSBuild não são encontrados

A tarefa de construção de Roslyn Analyzers precisa de consultar a Azure DevOps para o registo MSBuild a partir da tarefa de construção do MSBuild. Se a tarefa do analisador for executado imediatamente após a tarefa MSBuild, o registo ainda não estará disponível. Coloque outras tarefas entre a tarefa MSBuild e a tarefa Roslyn Analyzers. Exemplos de outras tarefas incluem BinSkim e Anti-Malware Scanner.

## <a name="next-steps"></a>Passos seguintes

Se precisar de assistência adicional, o Microsoft Security Code Analysis Support está disponível de segunda a sexta-feira das 9:00 às 17:00 horas do Pacífico.

- Embarque: Consulte a nossa [documentação de embarque](security-code-analysis-onboard.md)
  
- Suporte: Envie um e-mail à nossa equipa no [Microsoft Security Code Analysis Support](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)