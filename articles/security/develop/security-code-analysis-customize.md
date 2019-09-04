---
title: Guia de personalização de tarefas de análise de código de segurança Microsoft Azure
description: Este artigo descreve como personalizar as tarefas na extensão de análise de código de segurança da Microsoft
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
ms.openlocfilehash: c0d49c3ce06f6fa72daf7aff466ef65e09ced09a
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241804"
---
# <a name="configure-and-customize-the-build-tasks"></a>Configurar e personalizar as tarefas de compilação

Este artigo descreve detalhadamente as opções de configuração disponíveis em cada uma das tarefas de compilação. O artigo começa com as tarefas de ferramentas de análise de código de segurança. Ele termina com as tarefas de pós-processamento.

## <a name="anti-malware-scanner-task"></a>Tarefa de scanner antimalware

>[!NOTE]
> A tarefa de compilação de scanner antimalware requer um agente de compilação com o Windows Defender habilitado. O Visual Studio 2017 hospedado e posterior fornece um agente desse tipo. A tarefa de compilação não será executada no agente hospedado do Visual Studio 2015.
>
> Embora as assinaturas não possam ser atualizadas nesses agentes, as assinaturas devem ter sempre menos de três horas de idade.

Os detalhes da configuração de tarefa são mostrados na captura de tela e no texto a seguir.

![Configurando a tarefa de compilação do scanner antimalware](./media/security-tools/5-add-anti-malware-task600.png)

Na caixa de listagem **tipo** da captura de tela, **básico** é selecionado. Selecione **personalizado** para fornecer argumentos de linha de comando que personalizam a verificação.

O Windows Defender usa o cliente Windows Update para baixar e instalar assinaturas. Se a atualização de assinatura falhar em seu agente de compilação, o código de erro **HRESULT** provavelmente será proveniente de Windows Update.

Para obter mais informações sobre erros de Windows Update e sua mitigação, consulte [Windows Update códigos de erro por componente](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) e o artigo do TechNet [Windows Update códigos de erro de agente](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx).

## <a name="binskim-task"></a>Tarefa BinSkim

> [!NOTE]
> Antes que você possa executar a tarefa BinSkim, sua compilação deve atender a uma destas condições:
>    - Sua compilação produz artefatos binários do código gerenciado.
>    - Você tem artefatos binários confirmados que deseja analisar com BinSkim.

Os detalhes da configuração de tarefa são mostrados na seguinte captura de tela e lista.

![Configurando a tarefa de compilação BinSkim](./media/security-tools/7-binskim-task-details.png)

- Defina a configuração de compilação a ser depurada para que os arquivos de depuração. pdb sejam produzidos. O BinSkim usa esses arquivos para mapear problemas nos binários de saída de volta para o código-fonte.
- Para evitar a pesquisa e a criação da sua própria linha de comando:
     - Na lista **tipo** , selecione **básico**.
     - Na lista de **funções** , selecione **analisar**.
- Em **destino**, insira um ou mais especificadores para um arquivo, diretório ou padrão de filtro. Esses especificadores são resolvidos para um ou mais binários a serem analisados:
    - Vários destinos especificados devem ser separados por um ponto-e-vírgula (;).
    - Um especificador pode ser um único arquivo ou conter curingas.
    - As especificações de diretório sempre devem \\terminar com *.
    - Exemplos:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;

- Se você selecionar **linha de comando** na lista **tipo** , será necessário executar o binskim. exe:
     - Verifique se os primeiros argumentos para binskim. exe são a **análise** de verbo seguida por uma ou mais especificações de caminho. Cada caminho pode ser um caminho completo ou um caminho relativo ao diretório de origem.
     - Vários caminhos de destino devem ser separados por um espaço.
     - Você pode omitir a opção **/o** ou **/output** . O valor de saída é adicionado ou substituído.
     - As configurações de linha de comando padrão são mostradas a seguir.

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose

          > [!NOTE]
          > A trilha \\* é importante se você especificar os diretórios para o destino.

Para obter mais informações sobre argumentos de linha de comando BinSkim, regras por ID ou códigos de saída, consulte o [Guia do usuário do BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md).

## <a name="credential-scanner-task"></a>Tarefa do verificador de credenciais

Os detalhes da configuração de tarefa são mostrados na seguinte captura de tela e lista.

![Configurando a tarefa de compilação do verificador de credenciais](./media/security-tools/3-taskdetails.png)

As opções disponíveis incluem:

  - **Formato de saída**: Os valores disponíveis incluem **TSV**, **CSV**, **SARIF**e **PREfast**.
  - **Versão da ferramenta**: Recomendamos que você selecione **mais recente**.
  - **Pasta de verificação**: A pasta do repositório a ser examinada.
  - **Tipo de arquivo Searchers**: As opções para localizar o arquivo de pesquisador que é usado para verificação.
  - **Arquivo de supressão**: Um arquivo [JSON](https://json.org/) pode suprimir problemas no log de saída. Para obter mais informações sobre cenários de supressão, consulte a seção de perguntas frequentes deste artigo.
  - **Saída detalhada**: Auto-explicativo.
  - **Tamanho do lote**: O número de threads simultâneos usados para executar o verificador de credenciais. O valor predefinido é 20. Os valores possíveis variam de 1 a 2.147.483.647.
  - **Tempo limite de correspondência**: A quantidade de tempo em segundos a ser gasto tentando uma correspondência de pesquisador antes de abandonar a verificação.
  - **Tamanho do buffer de leitura de verificação de arquivo**: O tamanho em bytes do buffer usado enquanto o conteúdo é lido. O valor padrão é 524.288.  
  - **Máximo de bytes lidos da verificação de arquivo**: O número máximo de bytes a serem lidos de um arquivo durante a análise de conteúdo. O valor padrão é 104.857.600.
  - **As opções** > de controle**executam esta tarefa**: Especifica quando a tarefa será executada. Selecione **condições personalizadas** para especificar condições mais complexas.
  - **Versão**: A versão da tarefa de compilação no Azure DevOps. Essa opção não é usada com frequência.

## <a name="microsoft-security-risk-detection-task"></a>Tarefa de detecção de riscos de segurança da Microsoft

> [!NOTE]
> Você deve criar e configurar uma conta com o serviço de detecção de riscos de segurança da Microsoft (MSRD) antes de usar a tarefa MSRD. Esse serviço requer um processo de integração separado. Ao contrário da maioria das outras tarefas nesta extensão, essa tarefa requer uma assinatura separada com MSRD.
>
> Veja detecção de [riscos de segurança](https://aka.ms/msrddocs) da Microsoft [e detecção de riscos de segurança da Microsoft: ](https://docs.microsoft.com/security-risk-detection/how-to/) Instruções.

Os detalhes para configurar essa tarefa são mostrados na lista a seguir. Para qualquer elemento de interface do usuário, você pode passar o mouse sobre esse elemento para obter ajuda.

   - **Nome do ponto de extremidade do serviço DevOps do Azure para MSRD**: Um tipo genérico de ponto de extremidade do serviço DevOps do Azure armazena sua URL de instância MSRD integrada e seu token de acesso da API REST. Se você tiver criado um ponto de extremidade desse tipo, poderá especificá-lo aqui. Caso contrário, selecione o link **gerenciar** para criar e configurar um novo ponto de extremidade de serviço para esta tarefa MSRD.
   - **ID da conta**: Um GUID que pode ser recuperado da URL da conta do MSRD.
   - **URLs para binários**: Uma lista delimitada por ponto-e-vírgula de URLs disponíveis publicamente. A máquina difusa usa essas URLs para baixar os binários.
   - **URLs dos arquivos de semente**: Uma lista delimitada por ponto-e-vírgula de URLs disponíveis publicamente. A máquina difusa usa essas URLs para baixar as sementes. A especificação desse valor será opcional se os arquivos de semente forem baixados junto com os binários.
   - **Tipo de plataforma do so**: A plataforma do sistema operacional (SO) de computadores que executam o trabalho de difusão. Os valores disponíveis são **Windows** e **Linux**.
   - Edição do **Windows/Linux**: A edição do sistema operacional de computadores que executam o trabalho de difusão. Você poderá substituir o valor padrão se seus computadores tiverem uma edição de sistema operacional diferente.
   - **Script de instalação do pacote**: O script a ser executado em um computador de teste. Esse script instala o programa de destino de teste e suas dependências antes de o trabalho de fuzzing ser enviado.
   - **Parâmetros de envio de trabalho**:
       - **Diretório de semente**: O caminho para o diretório na máquina difusa que contém as sementes.
       - **Extensão de semente**: A extensão de nome de arquivo das sementes.
       - **Executável do driver de teste**: O caminho para o arquivo executável de destino no computador de difusão.
       - **Arquitetura executável do driver de teste**: A arquitetura do arquivo executável de destino. Os valores disponíveis são **x86** e **AMD64**.
       - **Argumentos do driver de teste**: Os argumentos de linha de comando passados para o arquivo executável de teste. O argumento "% Testfile%", incluindo as aspas, é substituído automaticamente pelo caminho completo para o arquivo de destino. Esse arquivo é analisado pelo driver de teste e é necessário.
       - O **processo do driver de teste é encerrado após a conclusão do teste**: Marque esta caixa de seleção se o driver de teste deve ser encerrado após a conclusão. Desmarque-a se o driver de teste precisar ser fechado à força.
       - **Duração máxima (em segundos)** : Uma estimativa do tempo razoavelmente esperado mais longo que o programa de destino requer para analisar um arquivo de entrada. Quanto mais precisa for a estimativa, mais eficiente será a execução do aplicativo difuso.
       - O **Driver de teste pode ser executado repetidamente**: Marque esta caixa de seleção se o driver de teste puder ser executado repetidamente sem depender de um estado global persistente ou compartilhado.
       - O **Driver de teste pode ser**renomeado: Marque esta caixa de seleção se o arquivo executável do driver de teste puder ser renomeado e ainda funcionar corretamente.
       - **O aplicativo de fuzzing é executado como um único processo de sistema operacional**: Marque esta caixa de seleção se o driver de teste for executado em um único processo do sistema operacional. Desmarque-a se o driver de teste gerar processos adicionais.

## <a name="roslyn-analyzers-task"></a>Tarefa de analisadores de Roslyn

> [!NOTE]
> Antes de executar a tarefa analisadores de Roslyn, sua compilação precisa atender a estas condições:
> - Sua definição de compilação inclui o MSBuild interno ou a tarefa de compilação VSBuild para C# compilar ou Visual Basic código. A tarefa de analisadores depende da entrada e da saída da tarefa interna para executar a compilação do MSBuild com analisadores Roslyn habilitados.
> - O agente de compilação que executa essa tarefa de compilação tem o Visual Studio 2017 versão 15,5 ou posterior instalado, para que ele use o compilador versão 2,6 ou posterior.

Os detalhes da configuração de tarefa são mostrados na lista e observação a seguir.

As opções disponíveis incluem:

- Conjunto de **regras**: Os valores são o **SDL necessário**, o **SDL é recomendado**ou seu próprio conjunto de regras personalizado.
- **Versão**dos analisadores: Recomendamos que você selecione **mais recente**.
- **Arquivo**de supressões de avisos do compilador: Um arquivo de texto com uma lista de IDs de avisos que são suprimidos.
- **As opções** > de controle**executam esta tarefa**: Especifica quando a tarefa será executada. Escolha **condições personalizadas** para especificar condições mais complexas.

> [!NOTE]
> - Os analisadores Roslyn são integrados ao compilador e podem ser executados somente como parte da compilação CSC. exe. Portanto, essa tarefa requer que o comando do compilador executado anteriormente na compilação seja reproduzido ou executado novamente. Essa reprodução ou execução é feita consultando Visual Studio Team Services (VSTS) para os logs de tarefa de compilação do MSBuild.
>
>   Não há outra maneira para a tarefa obter de forma confiável a linha de comando de compilação do MSBuild da definição de compilação. Consideramos a adição de uma caixa de texto de forma livre para permitir que os usuários insiram suas linhas de comando. Mas seria difícil manter essas linhas de comando atualizadas e sincronizadas com a compilação principal.
>
>   As compilações personalizadas exigem a repetição de todo o conjunto de comandos, não apenas os comandos do compilador. Nesses casos, a habilitação de analisadores Roslyn não é trivial ou confiável.
>
> - Os analisadores Roslyn são integrados ao compilador. Para ser invocado, os analisadores Roslyn exigem compilação.
>
>   Essa nova tarefa de compilação é implementada por C# meio da recompilação de projetos que já foram criados. A nova tarefa usa apenas as tarefas de compilação MSBuild e VSBuild na mesma compilação ou definição de compilação que a tarefa original. No entanto, nesse caso, a nova tarefa as usa com os analisadores de Roslyn habilitados.
>
>   Se a nova tarefa for executada no mesmo agente que a tarefa original, a saída da nova tarefa substituirá a saída da tarefa original na pasta *s* sources. Embora a saída da compilação seja a mesma, aconselhamos que você execute o MSBuild, copie a saída para o diretório de preparo dos artefatos e, em seguida, execute os analisadores do Roslyn.

Para obter recursos adicionais para a tarefa analisadores de Roslyn, confira [os analisadores baseados em Roslyn](https://docs.microsoft.com/dotnet/standard/analyzers/) no Microsoft docs.

Você pode encontrar o pacote do analisador instalado e usado por essa tarefa de compilação na página do NuGet [Microsoft. CodeAnalysis. FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers).

## <a name="tslint-task"></a>Tarefa TSLint

Para obter mais informações sobre TSLint, acesse o [repositório GitHub TSLint](https://github.com/palantir/tslint).

>[!NOTE] 
>Como você deve estar ciente, o [repositório GitHub TSLint](https://github.com/palantir/tslint) Home Page diz que o TSLint será preterido em algum momento em 2019. A Microsoft está investigando o [ESLint](https://github.com/eslint/eslint) como uma tarefa alternativa.

## <a name="publish-security-analysis-logs-task"></a>Tarefa publicar logs de análise de segurança

Os detalhes da configuração de tarefa são mostrados na seguinte captura de tela e lista.

![Configurando a tarefa de compilação de logs de análise de segurança de publicação](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Nome do artefato**: Qualquer identificador de cadeia de caracteres.
- **Tipo de artefato**: Dependendo da sua seleção, você pode publicar logs no servidor DevOps do Azure ou em um arquivo compartilhado que seja acessível ao agente de compilação.
- **Ferramentas**: Você pode optar por preservar os logs para ferramentas específicas ou pode selecionar **todas as ferramentas** para preservar todos os logs.

## <a name="security-report-task"></a>Tarefa de relatório de segurança

Os detalhes da configuração do relatório de segurança são mostrados na seguinte captura de tela e lista.

![Configurando a tarefa de compilação do relatório de segurança](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Relatórios**: Selecione qualquer um dos formatos do **console do pipeline**, do **arquivo TSV**e do **arquivo HTML** . Um arquivo de relatório é criado para cada formato selecionado.
- **Ferramentas**: Selecione as ferramentas em sua definição de compilação para as quais você deseja um resumo dos problemas detectados. Para cada ferramenta selecionada, pode haver uma opção para selecionar se você vê somente erros ou vê erros e avisos no relatório de resumo.
- **Opções avançadas**: Se não houver nenhum log para uma das ferramentas selecionadas, você poderá optar por registrar um aviso ou um erro. Se você registrar um erro, a tarefa falhará.
- **Pasta de logs de base**: Você pode personalizar a pasta de logs base onde os logs devem ser encontrados. Mas essa opção normalmente não é usada.

## <a name="post-analysis-task"></a>Tarefa de pós-análise

Os detalhes da configuração de tarefa são mostrados na seguinte captura de tela e lista.

![Configurando a tarefa de compilação pós-análise](./media/security-tools/a-post-analysis600.png)

- **Ferramentas**: Selecione as ferramentas em sua definição de compilação para as quais você deseja injetar condicionalmente uma quebra de compilação. Para cada ferramenta selecionada, pode haver uma opção para selecionar se deseja interromper somente erros ou se há erros e avisos.
- **Relatório**: Opcionalmente, você pode gravar os resultados que estão causando a quebra de compilação. Os resultados são gravados na janela do console DevOps do Azure e no arquivo de log.
- **Opções avançadas**: Se não houver nenhum log para uma das ferramentas selecionadas, você poderá optar por registrar um aviso ou um erro. Se você registrar um erro, a tarefa falhará.

## <a name="next-steps"></a>Passos seguintes

Se você tiver mais dúvidas sobre a extensão de análise de código de segurança e as ferramentas oferecidas, confira [nossa página de perguntas frequentes](security-code-analysis-faq.md).
