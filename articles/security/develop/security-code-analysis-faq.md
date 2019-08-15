---
title: Perguntas frequentes sobre documentação de análise de código de segurança Microsoft Azure
description: Este artigo contém perguntas frequentes sobre a extensão de análise de código de segurança
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
ms.openlocfilehash: 8038b7bd60ac771c798a1a8645022b0bf9e142a9
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934851"
---
# <a name="frequently-asked-questions"></a>Perguntas mais frequentes
Tem perguntas? Confira as perguntas frequentes abaixo para obter mais informações.

## <a name="general-faqs"></a>Perguntas frequentes gerais

### <a name="can-i-install-the-extension-on-my-tfs-not-azure-devops-server"></a>Posso instalar a extensão no meu servidor TFS (não Azure DevOps)? 

Não, a extensão não está disponível para download e instalação para o TFS.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>É necessário executar a análise de código de segurança da Microsoft com minha compilação? 

Sim e não. Dependendo do tipo de ferramenta de análise, o próprio código-fonte pode ser a única coisa necessária ou a saída da compilação pode ser necessária. Por exemplo, como o verificador de credenciais analisa arquivos dentro da estrutura de pastas do repositório de código, você pode executar o verificador de credenciais e publicar logs de análise de segurança tarefas de compilação em uma compilação autônoma para recuperar resultados.
Para outras ferramentas que analisam artefatos de compilação posterior, como BinSkim, a compilação será necessária primeiro.

### <a name="can-i-break-my-build-when-results-are-found"></a>Posso interromper minha compilação quando os resultados são encontrados? 
Sim, você pode introduzir uma quebra de compilação quando qualquer ferramenta relata um problema, uma localização, em seu arquivo de log. Basta adicionar a tarefa de compilação pós-análise e marcar a caixa de seleção de qualquer ferramenta para a qual você gostaria de interromper a compilação. Você pode optar por interromper a compilação quando qualquer ferramenta relata erros ou avisos e erros na interface do usuário da tarefa de pós-análise.

### <a name="how-are-the-command-line-arguments-different-in-azure-devops-than-they-are-in-the-standalone-desktop-tools"></a>Como os argumentos de linha de comando são diferentes no Azure DevOps do que estão nas ferramentas de área de trabalho autônoma? 

Para a maior parte, as tarefas de compilação do Azure DevOps são wrappers diretos em torno dos argumentos de linha de comando das ferramentas de segurança. Tudo o que você normalmente passaria para a ferramenta na linha de comando de sua área de trabalho, você pode passar para a entrada de argumentos da tarefa de compilação.
Aqui está uma lista de diferenças perceptíveis:
 - A ferramenta será executada na pasta de origem do agente $ (Build. SourcesDirectory) ou% BUILD_SOURCESDIRECTORY%. Exemplo: C:\agent\_work\1\s 
 - Os caminhos nos argumentos podem ser relativos à raiz do diretório de origem listado acima ou absoluto, seja executando um agente local com locais de implantação conhecidos de recursos locais ou usando variáveis de compilação DevOps do Azure
 - As ferramentas fornecerão automaticamente um caminho de arquivo de saída ou uma pasta se um caminho de saída for fornecido, ele será removido e substituído por um caminho para nosso local de logs conhecidos no agente de compilação
 - Alguns parâmetros de linha de comando adicionais são limpos e removidos em algumas ferramentas, como a adição ou remoção de opções para garantir que nenhuma GUI seja iniciada.

### <a name="can-i-run-a-build-task-for-example-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Posso executar uma tarefa de compilação (por exemplo, o verificador de credenciais) em vários repositórios em uma compilação de DevOps do Azure? 

Não, não há suporte atualmente para a execução de ferramentas de desenvolvimento seguro em vários repositórios em um único pipeline.

###  <a name="the-output-file-i-specified-is-not-being-created--i-cant-find-the-output-file-i-specified"></a>O arquivo de saída especificado não está sendo criado/não consigo encontrar o arquivo de saída que especifiquei 

As tarefas de compilação atualmente limpam a entrada do usuário e atualizam o local do arquivo de saída gerado para um local comum no agente de compilação. Para obter mais informações sobre esse local, consulte as perguntas a seguir.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Onde os arquivos de saída são gerados pelas ferramentas salvas? 

As tarefas de compilação adicionam automaticamente caminhos de saída ao seguinte local conhecido no agente de compilação $ (Agent. BuildDirectory)\_sdt\logs. Ao padronizar esse local, podemos garantir que outras equipes que produzem logs de análise de código ou consumi-las terão acesso.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Posso colocar uma compilação em fila para executar essas tarefas em um agente de compilação hospedado? 

Sim, todas as tarefas e ferramentas na extensão podem ser executadas em um agente de compilação hospedado.

>[!NOTE]
> A tarefa de compilação Antimalware requer um agente de compilação com o Windows Defender habilitado, que é verdadeiro em "Hosted VS2017" ou agentes de compilação posteriores. (Ele não será executado no agente herdado/VS2015 "hospedado".) As assinaturas não podem ser atualizadas nesses agentes, mas a assinatura deve ser sempre relativamente atual, com menos de 3 horas de idade.
>

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Posso executar essas tarefas de compilação como parte de um pipeline de lançamento (em vez de um pipeline de compilação)? 
Na maioria dos casos, sim. No entanto, as tarefas que publicam artefatos não têm suporte do Azure DevOps para serem executadas de dentro de pipelines de versão: "A única categoria de tarefas que não eram esperadas para trabalhar com a versão são aquelas que publicam artefatos. Isso ocorre porque, a partir de agora, não temos suporte para publicar artefatos na versão ".
Isso impede que a tarefa "publicar logs de análise de segurança" seja executada com êxito de um pipeline de liberação; Ele falhará, com uma mensagem de erro descritiva.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>De onde as tarefas de compilação baixam as ferramentas? 
As tarefas de compilação a) baixam pacotes NuGet para as ferramentas do [feed de gerenciamento de pacotes do Azure DevOps](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json) a seguir ou usando o Gerenciador de pacotes do nó, que deve ser pré-instalado no agente de compilação (exemplo: "NPM install tslint").

### <a name="what-effect-will-installing-the-extension-have-on-my-azure-devops-organization"></a>Qual efeito será a instalação da extensão na minha organização do Azure DevOps? 

Após a instalação, as tarefas de compilação de segurança fornecidas pela extensão ficarão disponíveis para uso por todos os usuários em sua organização. Ao criar ou editar um pipeline do Azure, essas tarefas estarão disponíveis para adição na lista coleção de tarefas de compilação. Caso contrário, instalar a extensão em sua organização DevOps do Azure não terá nenhum impacto. Ele não modifica nenhuma conta ou pipelines ou configurações de projeto.

### <a name="will-installing-the-extension-modify-my-existing-azure-pipelines"></a>A instalação da extensão modificará o Azure Pipelines existente? 

Não. A instalação da extensão disponibilizará as tarefas de compilação de segurança para serem adicionadas ao seu Azure Pipelines. Os usuários ainda precisam adicionar ou atualizar definições de compilação para integrar as ferramentas ao seu processo de compilação.

## <a name="task-specific-faqs"></a>Perguntas frequentes específicas da tarefa

As perguntas frequentes específicas para tarefas de compilação serão listadas nesta seção.

### <a name="credential-scanner-faqs"></a>Perguntas frequentes do verificador de credenciais

#### <a name="what-are-common-suppressions-scenarios-and-examples"></a>O que são cenários e exemplos de supressões comuns? 
Dois dos cenários de supressão mais comuns são detalhados abaixo:
##### <a name="suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Suprimir todas as ocorrências de um determinado segredo dentro do caminho especificado 
A chave de hash do segredo do arquivo de saída do verificador de credenciais é necessária, conforme mostrado no exemplo abaixo
   
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
> A chave de hash é gerada por uma parte do valor correspondente ou do conteúdo do arquivo. Qualquer revisão de código-fonte pode alterar a chave de hash e desabilitar a regra de supressão. 

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Para suprimir todos os segredos em um arquivo especificado (ou para suprimir o próprio arquivo de segredos) 
A expressão de arquivo pode ser um nome de arquivo ou qualquer parte de sufixo do caminho/nome completo do arquivo. Não são suportados carateres universais. 

**Exemplo** 

Arquivo a ser suprimido: [InputPath] \src\JS\lib\angular.js 

Regras de supressão válidas: 
- [InputPath] \src\JS\lib\angular.js--suprimir o arquivo no caminho especificado
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angular. js--suprimir qualquer arquivo com o mesmo nome

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
> Todos os segredos futuros adicionados ao arquivo também serão suprimidos automaticamente. 

#### <a name="what-are-recommended-secrets-management-guidelines"></a>Quais são as diretrizes de gerenciamento de segredos recomendadas? 
Embora a detecção de segredos embutidos em código seja oportuna e a mitigação dos riscos seja útil, é ainda melhor se alguém pudesse impedir que os segredos sejam verificados completamente. Nesse sentido, a Microsoft lançou o CredScan Code Analyzer como parte da [extensão do Microsoft DevLabs](https://marketplace.visualstudio.com/items?itemName=VSIDEDevOpsMSFT.ContinuousDeliveryToolsforVisualStudio) para Visual Studio. Na visualização antecipada, ele fornece aos desenvolvedores uma experiência embutida para detectar possíveis segredos em seu código, dando-lhes a oportunidade de corrigir esses problemas em tempo real. Para obter mais informações, consulte [este](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/) blog sobre como gerenciar segredos com segurança na nuvem. Abaixo estão alguns recursos adicionais para ajudá-lo a gerenciar segredos e acessar informações confidenciais de dentro de seus aplicativos de maneira segura: 
 - [Cofre de Chaves do Azure](../../key-vault/index.yml)
 - [Azure Active Directory](../../sql-database/sql-database-aad-authentication.md)
 - [Identidade de Serviço Gerenciada do Azure AD](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Identidade de Serviço Gerida (MSI) para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Identidade de Serviço Gerenciada do Azure](../../app-service/overview-managed-identity.md)
 - [Biblioteca AppAuthentication](../../key-vault/service-to-service-authentication.md)

#### <a name="can-i-write-my-own-custom-searchers"></a>Posso escrever meus próprios pesquisadores personalizados?

O verificador de credenciais depende de um conjunto de pesquisa de conteúdo comumente definido no arquivo **buildsearchers. xml** . O arquivo contém uma matriz de objetos serializados XML que representam um objeto ContentSearcher. O programa é distribuído com um conjunto de pesquisa que foram bem testadas, mas também permite que você implemente seus próprios pesquisadores personalizados também. 

Um pesquisador de conteúdo é definido da seguinte maneira: 

- **Nome** – o nome de pesquisador descritivo a ser usado no arquivo de saída do verificador de credenciais. É recomendável usar a Convenção de nomenclatura do camel case para nomes de pesquisador. 
- **RuleId** – a ID opaca estável do pesquisador. 
    - Os pesquisadores padrão do verificador de credenciais são atribuídos com RuleIds como CSCAN0010, CSCAN0020, CSCAN0030, etc. O último dígito é reservado para mesclagem ou divisão em potencial do grupo de Regex do pesquisador.
    - A RuleId para pesquisadores personalizados deve ter seu próprio namespace no formato de: CSCAN-{namespace} 0010, CSCAN-{namespace} 0020, CSCAN-{namespace} 0030, etc.
    - O nome do pesquisador totalmente qualificado é a combinação de RuleId e o nome do pesquisador. Exemplo de CSCAN0010. KeyStoreFiles, CSCAN0020. Base64EncodedCertificate, etc.
- **ResourceMatchPattern** – Regex de extensões de arquivo para verificar no Pesquisador
- **ContentSearchPatterns** – matriz de cadeias de caracteres contendo instruções Regex para correspondência. Se nenhum padrão de pesquisa for definido, todos os arquivos correspondentes ao padrão de correspondência de recursos serão retornados.
- **ContentSearchFilters** – matriz de cadeias de caracteres contendo instruções Regex para filtrar falsos positivos específicos do pesquisador.
- **Matchdetails** – uma mensagem descritiva e/ou instruções de mitigação a serem adicionadas para cada correspondência do pesquisador.
- **Recomendação** – fornece o conteúdo do campo de sugestões para uma correspondência usando o formato de relatório PREfast.
- **Severidade** – um número inteiro para refletir a severidade do problema (mais alta = 1).
![Instalação do verificador de credenciais](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers-faqs"></a>Perguntas frequentes sobre analisadores de Roslyn

#### <a name="what-are-the-most-common-errors-when-using-the-roslyn-analyzers-task"></a>Quais são os erros mais comuns ao usar a tarefa analisadores de Roslyn?

**Erro: O projeto foi restaurado usando Microsoft. NetCore. app versão x. x. x, mas com as configurações atuais, a versão y. y seria usada em seu lugar. Para resolver esse problema, verifique se as mesmas configurações são usadas para restauração e para operações subsequentes, como compilar ou publicar. Normalmente, esse problema pode ocorrer se a propriedade RuntimeIdentifier for definida durante a compilação ou publicação, mas não durante a restauração:**

Os analisadores Roslyn são executados como parte da compilação, portanto, a árvore de origem no computador de compilação precisa estar em um estado compilável. Uma etapa (provavelmente "dotnet. exe Publish") entre a compilação principal e os analisadores Roslyn podem colocar a árvore de origem em um estado não compilável. Talvez a duplicação da etapa que faz uma restauração do NuGet, logo antes da etapa dos analisadores de Roslyn, colocará a árvore de origem de volta em um estado compilável.

**"CSC. exe" saiu com o código de erro 1--uma instância do Analyzer AAAA não pode ser criada a partir de C:\BBBB.dll: Não foi possível carregar o arquivo ou o assembly ' Microsoft. CodeAnalysis, Version = X. X.x.x. X. x, Culture = neutral, PublicKeyToken = 31bf3856ad364e35 ' ou uma de suas dependências. O sistema não pode localizar o arquivo especificado.**

Verifique se o compilador dá suporte a analisadores Roslyn. "CSC. exe/Version" deve relatar pelo menos a v 2.6. x. Em alguns casos, os arquivos. csproj individuais podem substituir a instalação do Visual Studio da máquina de compilação, fazendo referência a um pacote por meio do Microsoft.Net. compilers. Se o uso de uma versão específica do compilador não tiver sido intencional, remova as referências a Microsoft.Net. compilers. Caso contrário, verifique se o pacote referenciado também é pelo menos v 2.6. x. Tente obter o log de erros, que pode ser encontrado no parâmetro/ErrorLog: da linha de comando CSC. exe (encontrada no log da tarefa de compilação do Roslyn). Ele pode ser algo parecido com:/ErrorLog: f:\ts-Services-\_123 work\456\s\Some\Project\Code\Code.csproj.Sarif

**O C# compilador não é suficientemente recente (deve ser > = 2,6)**

As versões mais recentes do C# compilador são lançadas aqui https://www.nuget.org/packages/Microsoft.Net.Compilers:. Para obter a versão instalada, você está usando `C:\>csc.exe /version` executar do prompt de comando. Certifique-se de que você não tem nenhuma referência a um pacote NuGet do Microsoft.Net. compilers < v 2.6.

**Logs do MSBuild/VSBuild não encontrados**

Devido a como a tarefa funciona, essa tarefa precisa consultar o Azure DevOps para o log do MSBuild da tarefa de compilação do MSBuild. Se essa tarefa for executada imediatamente após a tarefa de compilação do MSBuild, o log ainda não estará disponível; Coloque outras tarefas de compilação, incluindo tarefas de compilação SecDevTools, como Binskim, varredura antimalware e outros), entre a tarefa de compilação do MSBuild e a tarefa de compilação dos analisadores de Roslyn. 

## <a name="next-steps"></a>Passos seguintes

Se você precisar de assistência adicional, o suporte à análise de código de segurança da Microsoft estará disponível de segunda a sexta-feira da 9:00-5:00 hora oficial do Pacífico

  - Integração-entre em contato com seus gerentes de contas técnicas para começar. 
  >[!NOTE] 
  >Se você ainda não tiver uma relação de suporte pago com a Microsoft ou se tiver uma oferta de suporte que não permita a compra de serviços do catálogo Phoenix, visite nossos [serviços de suporte Home Page](https://www.microsoft.com/enterprise/services/support) para obter mais informações.

  - Suporte-envie a equipe por email com [o suporte à análise de código de segurança da Microsoft](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)