---
title: Personalize as tarefas de análise de códigos de segurança da Microsoft
titleSuffix: Azure
description: Este artigo descreve a personalização das tarefas na extensão de análise de código de segurança da Microsoft
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
ms.openlocfilehash: 6cdf892651407defc21f359a8e3b326b4af63b62
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77499992"
---
# <a name="configure-and-customize-the-build-tasks"></a>Configure e personalize as tarefas de construção

Este artigo descreve detalhadamente as opções de configuração disponíveis em cada uma das tarefas de construção. O artigo começa com as tarefas para ferramentas de análise de códigode segurança. Termina com as tarefas pós-processamento.

## <a name="anti-malware-scanner-task"></a>Tarefa anti-Malware Scanner

>[!NOTE]
> A tarefa de construção do Scanner Anti-Malware requer um agente de construção com o Windows Defender ativado. Acolheu o Visual Studio 2017 e mais tarde fornece tal agente. A tarefa de construção não será executada no Visual Studio 2015.
>
> Embora as assinaturas não possam ser atualizadas sobre estes agentes, as assinaturas devem ter sempre menos de três horas de idade.

Os detalhes da configuração da tarefa são mostrados na seguinte imagem e texto.

![Configurar a tarefa de construção do Scanner Anti-Malware](./media/security-tools/5-add-anti-malware-task600.png)

Na caixa da lista **tipo** da imagem, o **Basic** é selecionado. Selecione **Custom** para fornecer argumentos de linha de comando que personalizem a digitalização.

O Windows Defender utiliza o cliente Windows Update para descarregar e instalar assinaturas. Se a atualização de assinatura falhar no seu agente de construção, o código de erro **HRESULT** está provavelmente a vir do Windows Update.

Para obter mais informações sobre os erros do Windows Update e a sua mitigação, consulte [os códigos](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) de erro do Windows Update por componente e o artigo TechNet Windows Update Agent - Error [Codes](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx).

Para obter informações sobre a configuração yAML para esta tarefa, consulte as nossas [opções Anti-Malware YAML](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>Tarefa BinSkim

> [!NOTE]
> Antes de poder executar a tarefa BinSkim, a sua construção deve satisfazer uma destas condições:
>  - A sua construção produz artefactos binários a partir de código gerido.
>  - Tens artefactos binários comprometidos que queres analisar com o BinSkim.

Os detalhes da configuração da tarefa são mostrados na seguinte imagem e lista.

![Configurar a tarefa de construção binSkim](./media/security-tools/7-binskim-task-details.png)

- Defina a configuração de construção para Debug de modo a que os ficheiros de depuração .pdb sejam produzidos. BinSkim usa estes ficheiros para mapear problemas nos binários de saída de volta ao código fonte.
- Para evitar pesquisar e criar a sua própria linha de comando:
     - Na lista **tipo,** selecione **Basic**.
     - Na lista **'Função',** **selecione Analisar**.
- No **Target,** introduza um ou mais especificadores para um padrão de ficheiro, diretório ou filtro. Estes especificadores resolvem um ou mais binários a analisar:
    - Vários alvos especificados devem ser separados por um ponto evígulo (;).
    - Um especificador pode ser um único ficheiro ou conter wildcards.
    - As especificações do diretório devem terminar sempre com \\*.
    - Exemplos:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;

- Se selecionar a **Linha de Comando** na lista **tipo,** tem de executar binskim.exe:
     - Certifique-se de que os primeiros argumentos para binskim.exe são o verbo **analisado** seguido por uma ou mais especificações de caminho. Cada caminho pode ser um caminho completo ou um caminho em relação ao diretório de origem.
     - Vários caminhos-alvo devem ser separados por um espaço.
     - Pode omitindo a opção **/o** ou **/saída.** O valor de saída é acrescentado para si ou substituído.
     - As configurações padrão da linha de comando são mostradas da seguinte forma.

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose

          > [!NOTE]
          > O \\* é importante se especificar os diretórios para o alvo.

Para obter mais informações sobre os argumentos da linha de comando BinSkim, regras por ID ou códigos de saída, consulte o Guia de [Utilizador BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md).

Para obter informações sobre a configuração yAML para esta tarefa, consulte as nossas [opções BinSkim YAML](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>Tarefa do Scanner credencial

Os detalhes da configuração da tarefa são mostrados na seguinte imagem e lista.

![Configurar a tarefa de construção do Scanner Credencial](./media/security-tools/3-taskdetails.png)

As opções disponíveis incluem:

  - **Formato de saída**: Os valores disponíveis incluem **TSV,** **CSV,** **SARIF**e **PREfast**.
  - **Versão da ferramenta**: Recomendamos que selecione **Mais recente**.
  - **Pasta de digitalização**: A pasta de reprodução a digitalizar.
  - Tipo de **ficheiro dos pesquisadores**: As opções para localizar o ficheiro dos pesquisadores que é utilizado para digitalizar.
  - **Ficheiro de Supressões**: Um ficheiro [JSON](https://json.org/) pode suprimir problemas no registo de saída. Para obter mais informações sobre cenários de supressão, consulte a secção DE PERGUNTAS FAQ deste artigo.
  - **Saída verbosa**: Autoexplicativo.
  - **Tamanho do lote**: O número de fios simultâneos utilizados para executar o Scanner Credencial. O valor predefinido é 20. Os valores possíveis variam entre 1 e 2.147.483.647.
  - **Tempo de jogo**: O tempo em segundos para passar a tentar uma partida de pesquisa antes de abandonar o cheque.
  - **Ficheiro Scan Ler Tamanho do tampão**: O tamanho dos bytes do tampão utilizado durante a leitura do conteúdo. O valor padrão é de 524.288.  
  - **Varredura máxima de ficheiroS Bytes**: O número máximo de bytes para ler a partir de um ficheiro durante a análise de conteúdo. O valor padrão é de 104.857.600.
  - **Opções** de controlo > **Executar esta tarefa**: Especifica quando a tarefa será executada. Selecione **condições personalizadas** para especificar condições mais complexas.
  - **Versão**: A versão de tarefa de construção dentro do Azure DevOps. Esta opção não é frequentemente usada.

Para obter informações sobre a configuração yAML para esta tarefa, consulte as nossas [opções De YAML](yaml-configuration.md#credential-scanner-task) do Scanner Credencial

## <a name="microsoft-security-risk-detection-task"></a>Tarefa de deteção de risco de segurança da Microsoft

> [!NOTE]
> Deve criar e configurar uma conta com o serviço de Deteção de Riscos de Segurança da Microsoft (MSRD) antes de utilizar a tarefa MSRD. Este serviço requer um processo de embarque separado. Ao contrário da maioria das outras tarefas nesta extensão, esta tarefa requer uma subscrição separada com a MSRD.
>
> Consulte a [Deteção](https://aka.ms/msrddocs) de Riscos de Segurança da Microsoft e [a Deteção de Riscos de Segurança da Microsoft: Como](https://docs.microsoft.com/security-risk-detection/how-to/) obter instruções.

Os detalhes para configurar esta tarefa são mostrados na seguinte lista. Para qualquer elemento da UI, pode pairar sobre esse elemento para obter ajuda.

   - Nome final de **serviço Azure DevOps para MSRD**: Um tipo genérico de ponto final de serviço Azure DevOps armazena o URL de instância MSRD a bordo e o seu token de acesso API REST. Se criou tal ponto final, pode especificá-lo aqui. Caso contrário, selecione o link **'Gerir'** para criar e configurar um novo ponto final de serviço para esta tarefa MSRD.
   - **ID da conta**: Um GUID que pode ser recuperado do URL da conta MSRD.
   - **URLs para Binários**: Uma lista semi-delimitada de URLs publicamente disponíveis. A máquina de difusão utiliza estes URLs para descarregar os binários.
   - **URLs dos Ficheiros**de Sementes : Uma lista semi-delimitada de URLs publicamente disponíveis. A máquina de difusão utiliza estes URLs para descarregar as sementes. Especificar este valor é opcional se os ficheiros de sementes forem descarregados juntamente com os binários.
   - **Tipo de plataforma osso**: A plataforma de máquinas do sistema operativo (OS) que executa o trabalho de difusão. Os valores disponíveis são **Windows** e **Linux.**
   - **Edição do Windows / Edição Linux**: A edição OS das máquinas que executam o trabalho de difusão. Pode substituir o valor predefinido se as suas máquinas tiverem uma edição de Os diferente.
   - **Script de instalação de pacote**: O seu script a ser executado numa máquina de teste. Este script instala o programa de alvo de teste e as suas dependências antes da apresentação do trabalho de difusão.
   - **Parâmetros de submissão de emprego:**
       - **Diretório de Sementes**: O caminho para o diretório na máquina de difusão que contém as sementes.
       - **Extensão de semente**: A extensão do nome de ficheiro das sementes.
       - **Executável**do controlador de ensaio : O caminho para o ficheiro executável do alvo na máquina de difusão.
       - **Test Driver Executable Architecture**: A arquitetura do ficheiro executável alvo. Os valores disponíveis são **x86** e **amd64**.
       - **Argumentos**do condutor de teste : Os argumentos da linha de comando passaram para o ficheiro executável do teste. O argumento "%testfile%", incluindo as aspas, é automaticamente substituído pelo caminho completo para o ficheiro-alvo. Este ficheiro é analisado pelo controlador de teste e é necessário.
       - **Test Driver Process Exits Upon Test Test Test Completion**: Selecione esta caixa de verificação se o controlador de teste for encerrado após a conclusão. Limpe-o se o condutor do teste precisar de ser fechado à força.
       - **Duração máxima (em segundos)** : Uma estimativa do tempo razoavelmente esperado mais longo que o programa-alvo requer para analisar um ficheiro de entrada. Quanto mais precisa for a estimativa, mais eficientemente a aplicação de difusão corre.
       - **Test Driver Pode ser executado repetidamente**: Selecione esta caixa de verificação se o controlador de teste pode funcionar repetidamente sem depender de um estado global persistente ou partilhado.
       - Controlador de **teste Pode ser renomeado**: Selecione esta caixa de verificação se o ficheiro executável do controlador de teste puder ser renomeado e ainda funcionar corretamente.
       - **A aplicação Fuzzing funciona como um processo único de OS:** Selecione esta caixa de verificação se o controlador de teste for executado num único processo de Os. Limpe-o se o condutor de teste der origem a processos adicionais.

Para obter informações sobre a configuração yAML para esta tarefa, consulte as nossas [opções yAML](yaml-configuration.md#microsoft-security-risk-detection-task) de deteção de risco de segurança da Microsoft

## <a name="roslyn-analyzers-task"></a>Tarefa de Roslyn Analisers

> [!NOTE]
> Antes de poder executar a tarefa roslyn Analisers, a sua construção precisa de satisfazer estas condições:
>
> - A sua definição de construção inclui a tarefa de C# construção de MSBuild ou VSBuild incorporada para compilar ou código Básico Visual. A tarefa dos analisadores baseia-se na entrada e saída da tarefa incorporada para executar a compilação MSBuild com analisadores Roslyn habilitados.
> - O agente de construção que executa esta tarefa de construção tem visual Studio 2017 versão 15.5 ou posteriormente instalado, de modo que usa a versão compilada 2.6 ou posterior.

Os detalhes da configuração da tarefa são mostrados na lista e nota seguintes.

As opções disponíveis incluem:

- **Regra definido**: Os valores são Exigidos pelo **SDL,** **Recomendado sDL,** ou o seu próprio conjunto de regras personalizado.
- **Versão dos Analisadores**: Recomendamos que selecione **Mais recente**.
- **Ficheiro de supressões**de avisos de compilador : Um ficheiro de texto com uma lista de iDs de avisos que são suprimidos.
- **Opções** de controlo > **Executar esta tarefa**: Especifica quando a tarefa será executada. Escolha **condições personalizadas** para especificar condições mais complexas.

> [!NOTE]
>
> - Os Analisadores Roslyn estão integrados com o compilador e só podem ser executados como parte da compilação csc.exe. Por isso, esta tarefa requer que o comando compilador que funcionou mais cedo na construção seja repetido ou executado novamente. Esta repetição ou execução é feita consultando visual studio team services (VSTS) para os registos de tarefas de construção MSBuild.
>
>   Não há outra forma de a tarefa obter de forma fiável a linha de comando de compilação MSBuild a partir da definição de construção. Considerámos adicionar uma caixa de texto freeform para permitir que os utilizadores introduzam as suas linhas de comando. Mas então seria difícil manter essas linhas de comando atualizadas e sincronizadas com a construção principal.
>
>   As construções personalizadas requerem a reprodução de todo o conjunto de comandos, e não apenas comandos de compilador. Nestes casos, permitir que a Roslyn Analisers não seja trivial ou fiável.
>
> - Os Analisadores Roslyn estão integrados com o compilador. Para ser invocado, roslyn Analisers exige compilação.
>
>   Esta nova tarefa de construção C# é implementada através da recompilação de projetos que já foram construídos. A nova tarefa utiliza apenas as tarefas de construção mSBuild e VSBuild na mesma definição de construção ou construção que a tarefa original. No entanto, neste caso, a nova tarefa usa-os com roslyn Analisers habilitados.
>
>   Se a nova tarefa for no mesmo agente que a tarefa original, a saída da nova tarefa substitui a saída da tarefa original na pasta de fontes *da s.* Embora a saída de construção seja a mesma, aconselhamos que execute a MSBuild, copie a saída para o diretório de encenação de artefactos, e, em seguida, executar Roslyn Analisers.

Para obter recursos adicionais para a tarefa roslyn analisers, consulte [os Analisadores baseados em Roslyn](https://docs.microsoft.com/dotnet/standard/analyzers/) no Microsoft Docs.

Pode encontrar o pacote de analisador instalado e utilizado por esta tarefa de construção na página NuGet [Microsoft.CodeAnalysis.FxCopAnalyzeers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers).

Para obter informações sobre a configuração yAML para esta tarefa, consulte as nossas [opções De YAML de Análises Roslyn](yaml-configuration.md#roslyn-analyzers-task)

## <a name="tslint-task"></a>Tarefa TSLint

Para mais informações sobre a TSLint, vá ao [repo TSLint GitHub](https://github.com/palantir/tslint).

>[!NOTE] 
>Como devem saber, a página inicial do [TSLint GitHub](https://github.com/palantir/tslint) diz que a TSLint será depreciada algures em 2019. A Microsoft está a investigar a [ESLint](https://github.com/eslint/eslint) como uma tarefa alternativa.

Para obter informações sobre a configuração yAML para esta tarefa, consulte as nossas [opções De YAML TSLint](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>Publicar tarefa de registos de análise de segurança

Os detalhes da configuração da tarefa são mostrados na seguinte imagem e lista.

![Configurar a tarefa de construção de registos de análise de segurança](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Nome do artefacto**: Qualquer identificador de cordas.
- **Tipo de Artefacto**: Dependendo da sua seleção, pode publicar registos no seu Servidor Azure DevOps ou num ficheiro partilhado que seja acessível ao agente de construção.
- **Ferramentas**: Pode optar por preservar registos para ferramentas específicas, ou pode selecionar **Todas as Ferramentas** para preservar todos os registos.

Para obter informações sobre a configuração yAML para esta tarefa, consulte as [opções YAML](yaml-configuration.md#publish-security-analysis-logs-task) de Registos de Segurança da Publicação

## <a name="security-report-task"></a>Tarefa do Relatório de Segurança

Os detalhes da configuração do Relatório de Segurança são mostrados na seguinte imagem e lista.

![Configurar a tarefa de construção do Relatório de Segurança](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Relatórios**: Selecione qualquer uma das formatos de Consola de **Pipeline,** **Ficheiro TSV**e **Ficheiro Html.** Um ficheiro de relatório é criado para cada formato selecionado.
- **Ferramentas**: Selecione as ferramentas na sua definição de construção para as quais pretende um resumo dos problemas detetados. Para cada ferramenta selecionada, pode haver uma opção para selecionar se vê apenas erros ou se vê erros e avisos no relatório sumário.
- **Opções Avançadas**: Se não houver registos para uma das ferramentas selecionadas, pode optar por registar um aviso ou um erro. Se registar um erro, a tarefa falha.
- Pasta de **Registos base**: Pode personalizar a pasta de registos base onde os registos são encontrados. Mas esta opção normalmente não é usada.

Para obter informações sobre a configuração yAML para esta tarefa, consulte as [opções yAML](yaml-configuration.md#security-report-task) do nosso relatório de segurança

## <a name="post-analysis-task"></a>Tarefa de pós-análise

Os detalhes da configuração da tarefa são mostrados na seguinte imagem e lista.

![Configurar a tarefa de construção pós-análise](./media/security-tools/a-post-analysis600.png)

- **Ferramentas**: Selecione as ferramentas na sua definição de construção para as quais pretende injetar condicionalmente uma rutura de construção. Para cada ferramenta selecionada, pode haver uma opção para selecionar se pretende quebrar apenas erros ou em ambos os erros e advertências.
- **Relatório**: Pode escrever opcionalmente os resultados que estão a causar a rutura da construção. Os resultados estão escritos na janela da consola Azure DevOps e no ficheiro de registo.
- **Opções Avançadas**: Se não houver registos para uma das ferramentas selecionadas, pode optar por registar um aviso ou um erro. Se registar um erro, a tarefa falha.

Para obter informações sobre a configuração yAML para esta tarefa, consulte as nossas [opções de YAML](yaml-configuration.md#post-analysis-task) de Análise Pós

## <a name="next-steps"></a>Passos Seguintes

Para obter informações sobre a configuração baseada em YAML, consulte o nosso guia de [configuração YAML](yaml-configuration.md).

Se tiver mais dúvidas sobre a extensão de Análise de Código de Segurança e as ferramentas oferecidas, consulte a [nossa página de PERGUNTAS FAQ](security-code-analysis-faq.md).
