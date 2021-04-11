---
title: Personalize as tarefas de Análise de Código de Segurança do Microsoft
titleSuffix: Azure
description: Este artigo descreve a personalização das tarefas na extensão de Análise de Código de Segurança da Microsoft
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 03/22/2021
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: ad395e1b782edb28845bb7db0607d2bab5b5697c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802017"
---
# <a name="configure-and-customize-the-build-tasks"></a>Configurar e personalizar as tarefas de construção

> [!Note]
> A partir de 1 de março de 2022, a extensão do Código de Segurança da Microsoft (MSCA) será retirada. Os clientes MSCA existentes manterão o seu acesso à MSCA até 1 de março de 2022. Consulte as [Ferramentas de Análise de Código Fonte OWASP](https://owasp.org/www-community/Source_Code_Analysis_Tools) para obter opções alternativas em Azure DevOps. Para clientes que planeiam migrar para o GitHub, você pode verificar [GitHub Advanced Security](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security).

Este artigo descreve em detalhe as opções de configuração disponíveis em cada uma das tarefas de construção. O artigo começa com as tarefas para ferramentas de análise de código de segurança. Termina com as tarefas pós-processamento.

## <a name="anti-malware-scanner-task"></a>Tarefa de scanner anti-malware

>[!NOTE]
> A tarefa de construção do Scanner Anti-Malware requer um agente de construção com o Windows Defender ativado. Acolheu o Visual Studio 2017 e mais tarde forneceu tal agente. A tarefa de construção não será executada no Visual Studio 2015 agente hospedado.
>
> Embora as assinaturas não possam ser atualizadas sobre estes agentes, as assinaturas devem ter sempre menos de três horas de idade.

Os detalhes da configuração da tarefa são apresentados na imagem e no texto seguintes.

![Configurar a tarefa de construção do Scanner Anti-Malware](./media/security-tools/5-add-anti-malware-task600.png)

Na caixa de lista **tipo** da imagem, **o Basic** é selecionado. Selecione **Custom** para fornecer argumentos de linha de comando que personalizem a digitalização.

O Windows Defender utiliza o cliente Windows Update para descarregar e instalar assinaturas. Se a atualização de assinatura falhar no seu agente de construção, o código de erro **HRESULT** provavelmente virá do Windows Update.

Para obter mais informações sobre os erros do Windows Update e a sua mitigação, consulte [os códigos de erro do Windows Update por componente](/windows/deployment/update/windows-update-error-reference) e o artigo TechNet Windows Update Agent - Error [Codes](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx).

Para obter informações sobre a configuração YAML para esta tarefa, consulte as nossas [opções de YAML anti-malware](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>Tarefa BinSkim

> [!NOTE]
> Antes de poder executar a tarefa BinSkim, a sua construção deve satisfazer uma destas condições:
>  - A sua construção produz artefactos binários a partir de código gerido.
>  - Tens artefactos binários comprometidos que queres analisar com o BinSkim.

Os detalhes da configuração da tarefa são apresentados na imagem e lista a seguir.

![Configurar a tarefa de construção binSkim](./media/security-tools/7-binskim-task-details.png)

- Desave a configuração de construção para Debug de modo a que sejam produzidos ficheiros de depurar .pdb. BinSkim usa estes ficheiros para mapear problemas nos binários de saída de volta ao código fonte.
- Para evitar pesquisar e criar a sua própria linha de comando:
     - Na lista **'Tipo',** selecione **Basic**.
     - Na lista **'Função',** selecione **Analisar**.
- No **Target,** introduza um ou mais especificadores para um ficheiro, diretório ou padrão de filtro. Estes especificadores resolvem uma ou mais binários a analisar:
    - Vários alvos especificados devem ser separados por um ponto e vírgula (;).
    - Um especificador pode ser um único ficheiro ou conter wildcards.
    - As especificações do diretório devem sempre terminar com \\ *.
    - Exemplos:

```binskim-targets
           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
```

- Se selecionar **a Linha de Comando** na lista **tipo,** tem de executar binskim.exe:
     - Certifique-se de que os primeiros argumentos a binskim.exe são a **análise** do verbo seguida de uma ou mais especificações de caminho. Cada caminho pode ser um caminho completo ou um caminho em relação ao diretório de origem.
     - Vários caminhos-alvo devem ser separados por um espaço.
     - Pode omitir a opção **/o** **ou/output.** O valor de saída é adicionado para si ou substituído.
     - As configurações padrão da linha de comando são mostradas da seguinte forma.

```binskim-line-args
           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
```

> [!NOTE]
> O rasto \\ * é importante se especificar os diretórios para o alvo.

Para obter mais informações sobre os argumentos da linha de comando BinSkim, regras por ID ou códigos de saída, consulte o Manual do [Utilizador BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md).

Para obter informações sobre a configuração YAML para esta tarefa, consulte as nossas [opções BinSkim YAML](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>Tarefa do Scanner Credencial

Os detalhes da configuração da tarefa são apresentados na imagem e lista a seguir.

![Configurar a tarefa de construção do Scanner Credencial](./media/security-tools/3-taskdetails.png)

As opções disponíveis incluem:
  - **Nome do visor**: Nome da tarefa Azure DevOps. O valor predefinido é Executar Scanner Credencial
  - **Versão Principal da Ferramenta**: Os valores disponíveis incluem **CredScan V2,** **CredScan V1**. Recomendamos que os clientes utilizem a versão **CredScan V2.**
  - **Formato de saída**: Os valores disponíveis incluem **TSV,** **CSV,** **SARIF** e **PREfast**.
  - **Versão da ferramenta**: Recomendamos que selecione **mais recentemente**.
  - **Digitalizar :** A pasta do repositório a digitalizar.
  - **Tipo de ficheiro dos pesquisadores**: As opções para localizar o ficheiro dos pesquisadores que é utilizado para digitalização.
  - **Supressões Ficheiro**: Um ficheiro [JSON](https://json.org/) pode suprimir problemas no registo de saída. Para obter mais informações sobre cenários de supressão, consulte a secção faq deste artigo.
  - **Saída verbosa**: Autoexplicativo.
  - **Tamanho do lote**: O número de fios simultâneos utilizados para executar o Scanner Credencial. O valor predefinido é de 20. Os valores possíveis variam entre 1 e 2.147.483.647.
  - **Match Timeout**: A quantidade de tempo em segundos para gastar a tentar uma partida de pesquisador antes de abandonar o cheque.
  - **Scan De arquivo Leia o tamanho do tampão**: O tamanho dos bytes do tampão utilizado durante a leitura do conteúdo. O valor predefinido é de 524.288.  
  - **Scan Máximo Leia Bytes**: O número máximo de bytes a ler a partir de um ficheiro durante a análise do conteúdo. O valor predefinido é de 104.857.600.
  - Opções de **Controlo**  >  **Executar esta tarefa**: Especifica quando a tarefa será executada. Selecione **condições personalizadas** para especificar condições mais complexas.
  - **Versão**: A versão de tarefa de construção dentro de Azure DevOps. Esta opção não é frequentemente usada.

Para obter informações sobre a configuração YAML para esta tarefa, consulte as [nossas opções de TiML do Scanner Credencial](yaml-configuration.md#credential-scanner-task)

## <a name="roslyn-analyzers-task"></a>Tarefa roslyn Analisadores

> [!NOTE]
> Antes de poder executar a tarefa Roslyn Analyzers, a sua construção precisa de satisfazer estas condições:
>
> - A sua definição de construção inclui a tarefa de construção de MSBuild ou VSBuild incorporada para compilar o código C# ou Visual Basic. A tarefa dos analisadores baseia-se na entrada e saída da tarefa incorporada para executar a compilação MSBuild com os analisadores Roslyn ativados.
> - O agente de construção que executa esta tarefa de construção tem a versão 15.5 do Visual Studio 2017 ou posteriormente instalada, de modo a utilizar a versão 2.6 ou posterior do compilador.

Os detalhes da configuração da tarefa são apresentados na lista seguinte e nota.

As opções disponíveis incluem:

- **Regras**: Os valores são **SDL Required,** **SDL Recommended,** ou o seu próprio conjunto de regras personalizadas.
- **Versão dos Analisadores**: Recomendamos que selecione **o mais recente**.
- **Ficheiro de supressões de avisos do compilador**: um ficheiro de texto com uma lista de IDs de avisos que são suprimidos.
- Opções de **Controlo**  >  **Executar esta tarefa**: Especifica quando a tarefa será executada. Escolha **condições personalizadas** para especificar condições mais complexas.

> [!NOTE]
>
> - Roslyn Analyzers são integrados com o compilador e podem ser executados apenas como parte da compilação csc.exe. Assim, esta tarefa requer que o comando do compilador que correu mais cedo na construção seja reproduzido ou executado novamente. Esta repetição ou execução é feita consultando Azure DevOps (anteriormente Visual Studio Team Services) para os registos de tarefas de construção MSBuild.
>
>   Não há outra maneira de a tarefa obter de forma fiável a linha de comando de compilação MSBuild a partir da definição de construção. Considerámos adicionar uma caixa de texto de forma gratuita para permitir que os utilizadores entrassem nas suas linhas de comando. Mas então seria difícil manter essas linhas de comando atualizadas e em sintonia com a construção principal.
>
>   As construções personalizadas requerem a repetição de todo o conjunto de comandos, e não apenas comandos de compilador. Nestes casos, permitir roslyn Analyzers não é trivial ou fiável.
>
> - Roslyn Analyzers estão integrados com o compilador. Para ser invocado, Roslyn Analyzers requer compilação.
>
>   Esta nova tarefa de construção é implementada através da recompilagem de projetos C# que já foram construídos. A nova tarefa utiliza apenas as tarefas de construção msBuild e VSBuild na mesma definição de construção ou construção que a tarefa original. No entanto, neste caso, a nova tarefa utiliza-os com roslyn Analyzers ativados.
>
>   Se a nova tarefa for executado no mesmo agente que a tarefa original, a saída da nova tarefa substitui a saída da tarefa original na pasta *de fontes.* Embora a produção de construção seja a mesma, aconselhamos que você execute MSBuild, copie a saída para o diretório de encenação de artefactos, e depois executar Roslyn Analyzers.

Para obter recursos adicionais para a tarefa roslyn Analyzers, confira [os Analisadores baseados em Roslyn](/dotnet/standard/analyzers/api-analyzer) no Microsoft Docs.

Pode encontrar o pacote de analisador instalado e utilizado por esta tarefa de construção na página NuGet [Microsoft.CodeAnalysis.FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers).

Para obter informações sobre a configuração YAML para esta tarefa, consulte as [nossas opções de Roslyn Analyzers YAML](yaml-configuration.md#roslyn-analyzers-task)

## <a name="tslint-task"></a>Tarefa TSLint

Para mais informações sobre a TSLint, vá ao [TSLint GitHub repo](https://github.com/palantir/tslint).

>[!NOTE] 
>Como devem saber, a página inicial do [TSLint GitHub](https://github.com/palantir/tslint) diz que a TSLint será depreciada algures em 2019. A Microsoft está a investigar o [ESLint](https://github.com/eslint/eslint) como uma tarefa alternativa.

Para obter informações sobre a configuração YAML para esta tarefa, consulte as [nossas opções de TSLint YAML](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>Publicar tarefa de Registos de Análise de Segurança

Os detalhes da configuração da tarefa são apresentados na imagem e lista a seguir.

![Configurar a tarefa de construção de registos de análise de segurança de publicação](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Nome do artefacto**: Qualquer identificador de cordas.
- **Tipo artefacto**: Dependendo da sua seleção, pode publicar registos no seu Azure DevOps Server ou num ficheiro partilhado acessível ao agente de construção.
- **Ferramentas**: Pode optar por preservar registos para ferramentas específicas, ou pode selecionar **Todas as Ferramentas** para preservar todos os registos.

Para obter informações sobre a configuração YAML para esta tarefa, consulte as [nossas opções de IOSML de Registos de Segurança de Publicação](yaml-configuration.md#publish-security-analysis-logs-task)

## <a name="security-report-task"></a>Tarefa do Relatório de Segurança

Os detalhes da configuração do Relatório de Segurança são apresentados na seguinte imagem e lista.

![Configurar a tarefa de construção do Relatório de Segurança](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Relatórios**: Selecione qualquer uma das **consolas de pipeline,** **ficheiro TSV** e formatos **de ficheiro html.** Um ficheiro de relatório é criado para cada formato selecionado.
- **Ferramentas**: Selecione as ferramentas na definição de construção para as quais pretende um resumo dos problemas detetados. Para cada ferramenta selecionada, pode haver uma opção para selecionar se vê apenas erros ou vê erros e avisos no relatório resumo.
- **Opções Avançadas**: Se não houver registos para uma das ferramentas selecionadas, pode optar por registar um aviso ou um erro. Se registar um erro, a tarefa falha.
- **Pasta de registos base**: Pode personalizar a pasta de registos base onde os registos são encontrados. Mas esta opção normalmente não é usada.

Para obter informações sobre a configuração YAML para esta tarefa, consulte as opções do nosso [relatório de segurança YAML](yaml-configuration.md#security-report-task)

## <a name="post-analysis-task"></a>Tarefa pós-análise

Os detalhes da configuração da tarefa são apresentados na imagem e lista a seguir.

![Configurar a tarefa de construção pós-análise](./media/security-tools/a-post-analysis600.png)

- **Ferramentas**: Selecione as ferramentas na definição de construção para as quais pretende injetar uma rutura de construção condicional. Para cada ferramenta selecionada, pode haver uma opção para selecionar se deseja quebrar apenas erros ou em erros e avisos.
- **Relatório**: Pode escrever opcionalmente os resultados que estão a causar a quebra de construção. Os resultados são escritos na janela da consola E registo do Azure DevOps.
- **Opções Avançadas**: Se não houver registos para uma das ferramentas selecionadas, pode optar por registar um aviso ou um erro. Se registar um erro, a tarefa falha.

Para obter informações sobre a configuração YAML para esta tarefa, consulte as nossas [opções DE PÓS-Análise YAML](yaml-configuration.md#post-analysis-task)

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre a configuração baseada em YAML, consulte o nosso [guia de configuração YAML](yaml-configuration.md).

Se tiver mais perguntas sobre a extensão de Análise de Código de Segurança e as ferramentas oferecidas, consulte a [nossa página de PERGUNTAS Frequentes.](security-code-analysis-faq.md)