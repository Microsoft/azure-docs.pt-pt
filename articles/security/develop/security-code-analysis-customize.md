---
title: Guia de personalização de tarefas de análise de código de segurança Microsoft Azure
description: Este artigo trata da personalização das tarefas na extensão de análise de código de segurança
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
ms.openlocfilehash: ab219b71eb8cd6f6172b7d02a639301c67811b49
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718356"
---
# <a name="how-to-configure--customize-the-build-tasks"></a>Como: Configurar & personalizar as tarefas de compilação

Esta página descreve detalhadamente as opções de configuração disponíveis em cada uma das tarefas de compilação, começando com as tarefas para ferramentas de análise de código de segurança, seguidas pelas tarefas de pós-processamento

## <a name="anti-malware-scanner-task"></a>Tarefa de scanner antimalware

> [!NOTE]
> A tarefa de compilação Antimalware requer um agente de compilação com o Windows Defender habilitado, que é verdadeiro em "Hosted VS2017" e agentes de compilação posteriores. (Ele não será executado no agente herdado/VS2015 "hospedado") As assinaturas não podem ser atualizadas nesses agentes, mas a assinatura deve ser sempre relativamente atual, com menos de 3 horas de idade.

Captura de tela e detalhes sobre a configuração abaixo.

![Personalizando a tarefa de compilação de scanner antimalware](./media/security-tools/5-add-anti-malware-task600.png) 

- Configurações para Type = **Basic**
- Com tipos = **personalizados**, argumentos de linha de comando podem ser fornecidos para personalizar a verificação.

O Windows Defender usa o cliente Windows Update para baixar e instalar assinaturas. Se a atualização de assinatura falhar em seu agente de compilação, o código de erro HRESULT provavelmente será proveniente de Windows Update. Para obter mais informações sobre erros e mitigação do Windows Update, visite [esta página](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) e esta [página do TechNet](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)

## <a name="binskim-task"></a>Tarefa BinSkim

> [!NOTE]
> Como um pré-requisito para executar a tarefa BinSkim, sua compilação deve atender a uma das condições abaixo.
>    - Sua compilação produz artefatos binários do código gerenciado.
>    - Você tem artefatos binários confirmados que gostaria de analisar com BinSkim.

Captura de tela e detalhes sobre a configuração abaixo. 

![Configuração do BinSkim](./media/security-tools/7-binskim-task-details.png)  
1. Defina a configuração de compilação a ser depurada para produzir arquivos de depuração * **. pdb** . Eles são usados pelo BinSkim para mapear problemas encontrados no binário de saída de volta para o código-fonte. 
2. Escolha tipo = **básico** & função = **analisar** para evitar a pesquisa e criação da sua própria linha de comando. 
3. **Target** -um ou mais especificadores para um arquivo, diretório ou padrão de filtro que é resolvido para um ou mais binários a serem analisados. 
    - Vários destinos devem ser separados por um **ponto-e-vírgula (;)** . 
    - Pode ser um único arquivo ou conter curingas.
    - Os diretórios sempre devem terminar com\*
    - Exemplos:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
4. Se você selecionar tipo = **linha de comando**, 
     - Verifique se o primeiro argumento para **BinSkim. exe** é o verbo **analisar** usando caminhos completos ou caminhos relativos ao diretório de origem.
     - Para entrada de **linha de comando** , vários destinos devem ser separados por um espaço.
     - Você pode omitir o parâmetro de arquivo **/o** ou **/output** ; Ele será adicionado para você ou substituído. 
     - **Configuração de linha de comando padrão** 

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
          > [!NOTE]
          > A trilha \* é muito importante ao especificar um diretório ou diretórios para o destino. 

Para obter mais detalhes sobre o BinSkim sobre argumentos de linha de comando, regras por ID ou códigos de saída, visite o [Guia do usuário do BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)

## <a name="credential-scanner-task"></a>Tarefa do verificador de credenciais
Captura de tela e detalhes sobre a configuração abaixo.
 
![Personalização do verificador de credenciais](./media/security-tools/3-taskdetails.png)

As opções disponíveis incluem 
  - **Formato de saída** – TSV/CSV/SARIF/PREfast
  - **Versão da ferramenta** Aconselhável Mais recente
  - **Pasta de verificação** – a pasta em seu repositório para verificação
  - **Tipo de arquivo de pesquisa** -opções para localizar o arquivo de pesquisa usado para verificação.
  - **Arquivo de supressão** – um arquivo [JSON](https://json.org/) pode ser usado para suprimir problemas no log de saída (mais detalhes na seção de recursos). 
  - **Saída detalhada** -auto-explicação 
  - **Tamanho do lote** -o número de threads simultâneos usados para executar os verificadores de credenciais em paralelo. O padrão é 20 (o valor deve estar no intervalo de 1 a 2147483647).
  - **Corresponder tempo limite** -a quantidade de tempo a ser gasto tentando uma correspondência de pesquisador antes de abandonar a verificação. 
  - **Tamanho do buffer de leitura de verificação de arquivo** -tamanho do buffer ao ler o conteúdo em bytes. (O padrão é 524288) 
  - **Máximo de bytes de leitura da verificação de arquivo** -número máximo de bytes a serem lidos de um determinado arquivo durante a análise de conteúdo. (O padrão é 104857600) 
  - **Executar esta tarefa** (em **Opções de controle**) – especifica quando a tarefa deve ser executada. Escolha "condições personalizadas" para especificar condições mais complexas. 
  - **Versão** -versão da tarefa de compilação no Azure DevOps. Não é usado com frequência. 

## <a name="microsoft-security-risk-detection-task"></a>Tarefa de detecção de riscos de segurança da Microsoft
> [!NOTE]
> Você precisa criar e configurar uma conta com o serviço de detecção de risco como um pré-requisito para poder usar essa tarefa. Esse serviço requer um processo de integração separado; Ele não é ' plug-and-Play ' como a maioria das outras tarefas nesta extensão. Veja detecção de [riscos de segurança](https://aka.ms/msrddocs) da Microsoft [e detecção de riscos de segurança da Microsoft: ](https://docs.microsoft.com/security-risk-detection/how-to/) Instruções.

Detalhes sobre a configuração abaixo.

Insira os dados necessários; cada opção tem a ajuda de texto em foco.
   - **Nome do ponto de extremidade do serviço DevOps do Azure para MSRD**: Se você tiver criado um tipo genérico de ponto de extremidade do serviço DevOps do Azure para armazenar a URL da instância do MSRD (integrada ao) e o token de acesso da API REST, poderá escolher esse ponto de extremidade de serviço. Caso contrário, clique no link Gerenciar para criar e configurar um novo ponto de extremidade de serviço para esta tarefa MSRD. 
   - **ID da conta**: É um GUID que pode ser recuperado da URL da conta do MSRD.
   - **URLs para binários**: Uma lista delimitada por ponto e vírgula de URLs disponíveis publicamente (a ser usada pelo computador de difusão para baixar os binários).
   - **URLs dos arquivos de semente**: Uma lista delimitada por ponto e vírgula de URLs disponíveis publicamente (a ser usada pelo computador de difusão para baixar as sementes). Esse campo será opcional se os arquivos de semente forem baixados junto com os binários.
   - **Tipo de plataforma do so**: O tipo de plataforma do sistema operacional (Windows ou Linux) de computadores nos quais executar o trabalho de difusão.
   - Edição do **Windows/Linux**: A edição do sistema operacional dos computadores nos quais executar o trabalho de difusão. Você poderá substituir o valor padrão se seus computadores tiverem uma edição de sistema operacional diferente.
   - **Script de instalação do pacote**: Forneça o script a ser executado em um computador de teste para instalar o programa de destino de teste e suas dependências antes do envio do trabalho de fuzzing.
   - **Parâmetros de envio de trabalho**:
       - **Diretório de semente**: Caminho para o diretório na máquina difusa que contém as sementes.
       - **Extensão de semente**: A extensão de arquivo das sementes
       - **Executável do driver de teste**: Caminho para o executável de destino no computador de difusão.
       - **Arquitetura executável do driver de teste**: A arquitetura de arquivo executável de destino (x86 ou AMD64).
       - **Argumentos do driver de teste**: Os argumentos de linha de comando passados para o executável de destino de teste. Observe que o símbolo "% Testfile%", incluindo as aspas duplas, será substituído automaticamente pelo caminho completo do arquivo de destino que o driver de teste deve analisar e é obrigatório.
       - O **processo do driver de teste é encerrado após a conclusão do teste**: Marque para encerrar o test driver após a conclusão; Desmarque se o driver de teste precisa ser fechado à força.
       - **Duração máxima (em segundos)** : Forneça uma estimativa do tempo esperado razoavelmente mais longo necessário para que o programa de destino analise um arquivo de entrada. Quanto mais precisa for a estimativa, mais eficiente é a execução difusa.
       - O **Driver de teste pode ser executado repetidamente**: Verifique se o driver de teste pode ser executado repetidamente sem depender de um estado global persistente/compartilhado.
       - O **Driver de teste pode ser**renomeado: Verifique se o executável do driver de teste pode ser renomeado e ainda pode funcionar corretamente.
       - **O aplicativo de fuzzing é executado como um único processo de sistema operacional**: Verificar se o driver de teste é executado em um único processo de sistema operacional; Desmarque se o driver de teste gerar processos adicionais.


## <a name="roslyn-analyzers-task"></a>Tarefa de analisadores de Roslyn
> [!NOTE]
> Como um pré-requisito para executar a tarefa do analisador do Roslyn, sua compilação deve atender às seguintes condições.
>  - Sua definição de compilação inclui o MSBuild interno ou a tarefa de compilação VSBuild para C# compilar (ou VB) o código. Essa tarefa depende da entrada e da saída dessa tarefa de compilação específica para executar novamente a compilação do MSBuild com analisadores Roslyn habilitados.
>  - O agente de compilação que executa essa tarefa de compilação tem o Visual Studio 2017 v 15.5 ou posterior instalado (compilador versão 2.6. x).
>

Detalhes sobre a configuração abaixo.

As opções disponíveis incluem 
- **RuleSet** -SDL necessário, o SDL é recomendado ou você pode usar um conjunto de regras personalizado por conta própria.
- **Versão** dos analisadores Aconselhável Mais recente
- **Arquivo** de supressões de avisos do compilador-um arquivo de texto com uma lista de IDs de avisos que devem ser suprimidos. 
- **Executar esta tarefa** (em **Opções de controle**) – especifica quando a tarefa deve ser executada. Escolha "**condições personalizadas**" para especificar condições mais complexas. 

> [!NOTE]
> - Os analisadores Roslyn são integrados ao compilador e só podem ser executados como parte da compilação CSC. exe. Portanto, essa tarefa requer a repetição/reexecução do comando do compilador executado anteriormente na compilação. Isso é feito consultando o VSTS para os logs de tarefa de compilação do MSBuild (não há outra maneira para a tarefa obter de forma confiável a linha de comando de compilação do MSBuild da definição de compilação; fizemos uma boa adição de uma caixa de texto de forma livre para permitir que os usuários insiram suas linhas de comando , mas seria difícil manter esses dados atualizados e sincronizados com a compilação principal). As compilações personalizadas exigem a repetição de todo o conjunto de comandos, não apenas os comandos do compilador, e não é trivial/confiável habilitar os analisadores Roslyn nesses casos. 
> - Os analisadores Roslyn são integrados ao compilador e exigem a compilação a ser invocada. Essa tarefa de compilação é implementada por C# meio da recompilação de projetos que já foram criados usando apenas a tarefa de compilação MSBuild/VSBuild, na mesma definição de compilação/compilação, mas, nesse caso, com os analisadores habilitados. Se essa tarefa de compilação for executada no mesmo agente que a tarefa de compilação original, a saída da tarefa de compilação original do MSBuild/VSBuild será substituída na pasta de códigos-fonte do ' s, pela saída dessa tarefa de compilação. A saída da compilação será a mesma, mas é recomendável que você execute o MSBuild, copie a saída para o diretório de preparo de artefatos e execute Roslyn.
>

Para obter recursos adicionais para verificação de tarefa do Roslyn analisadores [Roslyn analisadores no Microsoft docs](https://docs.microsoft.com/dotnet/standard/analyzers/)

O pacote do analisador instalado e usado por essa tarefa de compilação pode ser encontrado [aqui](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers) 

## <a name="tslint-task"></a>Tarefa TSLint

Para obter mais informações sobre TSLint, visite o [repositório GitHub TSLint](https://github.com/palantir/tslint)
>[!NOTE] 
>Como você deve estar ciente, o TSLint será preterido algum tempo em 2019 (fonte: [Repositório GitHub TSLint](https://github.com/palantir/tslint)) Atualmente, a equipe está investigando [ESLint](https://github.com/eslint/eslint) como alternativa e a criação de uma nova tarefa para ESLint está no roteiro.

## <a name="publish-security-analysis-logs-task"></a>Tarefa publicar logs de análise de segurança
Captura de tela e detalhes sobre a configuração abaixo.

![Personalizando a análise de segurança de publicação](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Nome do artefato** – pode ser qualquer identificador de cadeia de caracteres
- **Tipo de artefato** – você pode publicar logs no servidor Azure-DevOps ou em um compartilhamento de arquivos que é acessível para o agente de compilação. 
- **Ferramentas** – você pode optar por preservar os logs de ferramentas individuais/específicas ou selecionar **todas as ferramentas** para preservar todos os logs. 

## <a name="security-report-task"></a>Tarefa de relatório de segurança
Captura de tela e detalhes sobre a configuração abaixo.  
![Configurar pós-análise](./media/security-tools/4-createsecurityanalysisreport600.png) 
- **Relatórios** -escolha os arquivos de relatório a serem criados; uma será criada em cada **console**do formato, **TSV**e/ou **HTML** 
- **Ferramentas** – selecione as ferramentas em sua definição de compilação para as quais você deseja um resumo dos problemas detectados. Para cada ferramenta selecionada, pode haver uma opção para selecionar se você deseja ver somente erros ou erros e avisos no relatório. 
- **Opções avançadas** – você pode optar por registrar um aviso ou um erro (e fazer a tarefa falhar) caso não haja nenhum log para uma das ferramentas selecionadas.
Você pode personalizar a pasta de logs base onde os logs devem ser encontrados, mas esse não é um cenário típico. 

## <a name="post-analysis-task"></a>Tarefa de pós-análise
Captura de tela e detalhes sobre a configuração abaixo.

![Personalizando após a análise](./media/security-tools/a-post-analysis600.png) 
- **Ferramentas** – selecione as ferramentas em sua definição de compilação para as quais você gostaria de injetar uma quebra de compilação com base em suas descobertas. Para cada ferramenta selecionada, pode haver uma opção para selecionar se você deseja interromper somente erros ou erros e avisos. 
- **Relatório** -você pode, opcionalmente, escrever os resultados que são encontrados e causar a quebra de compilação para a janela do console DevOps do Azure e o arquivo de log. 
- **Opções avançadas** – você pode optar por registrar um aviso ou um erro (e fazer a tarefa falhar) caso não haja nenhum log para uma das ferramentas selecionadas.

## <a name="next-steps"></a>Passos seguintes

Se você tiver mais dúvidas sobre a extensão e as ferramentas oferecidas, [Consulte nossa página perguntas frequentes.](security-code-analysis-faq.md)


