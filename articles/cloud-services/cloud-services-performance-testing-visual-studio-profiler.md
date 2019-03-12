---
title: A criação de perfis de um serviço em nuvem localmente no emulador de computação | Documentos da Microsoft
services: cloud-services
description: Investigar problemas de desempenho nos serviços cloud com o criador de perfil do Visual Studio
documentationcenter: ''
author: mikejo
manager: douge
editor: ''
tags: ''
ms.assetid: 25e40bf3-eea0-4b0b-9f4a-91ffe797f6c3
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/18/2016
ms.author: mikejo
ms.openlocfilehash: 40ba5814bce08037b9e4d0787defbab4d02e58df
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57546265"
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Testar o desempenho de um serviço Cloud localmente no emulador de computação do Azure com o Visual Studio Profiler
Uma variedade de ferramentas e técnicas estão disponíveis para testar o desempenho dos serviços cloud.
Quando publica um serviço em nuvem para o Azure, pode ter o Visual Studio, recolher dados de criação de perfis e, em seguida, analisá-lo localmente, conforme descrito em [uma aplicação do Azure de criação de perfis][1].
Também pode utilizar diagnósticos para controlar uma variedade de contadores de desempenho, conforme descrito em [com os contadores de desempenho no Azure][2].
Pode também querer analisar seu aplicativo localmente no emulador de computação antes de o implementar para a cloud.

Este artigo aborda o método de criação de perfis da Amostragem de CPU, que pode ser levada a cabo localmente no emulador. Amostragem de CPU é um método de criação de perfis que não é muito INVASIVO. Num intervalo de amostragem designado, o criador de perfil tira um instantâneo de pilha de chamadas. Os dados são recolhidos ao longo de um período de tempo e apresentados num relatório. Este método de criação de perfis tende a indicar onde num aplicativo intensivo a nível computacional maior parte do trabalho de CPU está a ser feito.  Isso lhe dá a oportunidade de se concentrar em "hot path" em que seu aplicativo está gastando mais tempo.

## <a name="1-configure-visual-studio-for-profiling"></a>1: Configurar o Visual Studio para criação de perfis
Em primeiro lugar, existem algumas opções de configuração do Visual Studio que podem ser útil durante a criação de perfis. Dar sentido os relatórios de criação de perfis, precisará símbolos (arquivos. pdb) para a aplicação e também os símbolos para bibliotecas do sistema. Vai querer Certifique-se de que mencione o servidor de símbolos disponíveis. Para tal, no **ferramentas** menu no Visual Studio, escolha **opções**, em seguida, escolha **Debugging**, em seguida, **símbolos**. Certifique-se de que o servidor de símbolos da Microsoft está listado em **símbolo localizações de ficheiros (. pdb)**.  Também pode fazer referência https://referencesource.microsoft.com/symbols, que poderá ter os arquivos de símbolo adicionais.

![Opções de símbolo][4]

Se assim o desejar, pode simplificar os relatórios que o criador de perfil gera definindo o Just My Code. Com o Just My Code ativada, as pilhas de chamadas de função são simplificadas para que as chamadas inteiramente internas para o .NET Framework e bibliotecas estão ocultos dos relatórios. Sobre o **ferramentas** menu, escolha **opções**. Em seguida, expanda o **ferramentas de desempenho** nó e escolha **geral**. Selecione a caixa de verificação **habilitar Just My Code para relatórios do criador de perfil**.

![Apenas as opções de meu código][17]

Pode utilizar estas instruções com um projeto existente ou com um novo projeto.  Se criar um novo projeto para experimentar as técnicas descritas abaixo, escolha uma linguagem c# **serviço Cloud do Azure** do projeto e selecione um **função da Web** e um **função de trabalho**.

![Funções do projeto de serviço em nuvem do Azure][5]

Por exemplo fins, adicionar algum código ao seu projeto que demora muito tempo e demonstra a algum problema de desempenho óbvia. Por exemplo, adicione o seguinte código para um projeto de função de trabalho:

```csharp
public class Concatenator
{
    public static string Concatenate(int number)
    {
        int count;
        string s = "";
        for (count = 0; count < number; count++)
        {
            s += "\n" + count.ToString();
        }
        return s;
    }
}
```

Chame esse código do método RunAsync na classe derivada de RoleEntryPoint da função de trabalho. (Ignorar o aviso sobre o método de execução de forma síncrona).

```csharp
private async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.
    while (!cancellationToken.IsCancellationRequested)
    {
        Trace.TraceInformation("Working");
        Concatenator.Concatenate(10000);
    }
}
```

Criar e executar seu serviço cloud localmente sem depuração (CTRL+F5), com a configuração da solução definida como **versão**. Isto garante que todos os ficheiros e pastas são criadas para executar a aplicação localmente e garante que todos os emuladores são iniciados. Inicie a IU do emulador de computação da barra de tarefas para verificar que a função de trabalho está em execução.

## <a name="2-attach-to-a-process"></a>2: Anexar a um processo
Em vez de criação de perfis da aplicação ao iniciá-la a partir do IDE do Visual Studio 2010, tem de anexar o criador de perfil um processo em execução. 

Para anexar o criador de perfil para um processo, sobre o **Analyze** menu, escolha **Profiler** e **anexar/desanexar**.

![Anexar a opção de perfil][6]

Para uma função de trabalho, encontre o processo de WaWorkerHost.exe.

![Processo de WaWorkerHost][7]

Se a pasta do projeto é numa unidade de rede, o criador de perfil irá pedir-lhe para fornecer outra localização para guardar os relatórios de criação de perfis.

 Também pode anexar a uma função da web ao anexar a WaIISHost.exe.
Se existirem vários processos de função de trabalho na sua aplicação, terá de utilizar o processID para distingui-los. Pode consultar o processID programaticamente acessando o objeto de processo. Por exemplo, se adicionar este código para o método Run da classe derivada de RoleEntryPoint numa função, pode ver o registo na IU do emulador de computação de saber que processo para ligar a.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

Para ver o registo, inicie o IU do emulador de computação.

![Inicie o emulador de computação da interface do Usuário][8]

Abra a janela de consola de registo de função de trabalho na IU do emulador de computação ao clicar na barra de título da janela de consola. Pode ver o ID de processo no registo.

![ID de processo do Vista][9]

Um que tenha ligado, execute os passos na interface do Usuário de seu aplicativo (se necessário) para reproduzir o cenário.

Quando deseja parar a criação de perfis, escolha o **Stop Profiling** ligação.

![Parar a opção de criação de perfis][10]

## <a name="3-view-performance-reports"></a>3: Ver relatórios de desempenho
O relatório de desempenho para a sua aplicação é apresentado.

Neste momento, o criador de perfil interrompe a execução, guarda dados num arquivo de Vsp e exibe um relatório que mostra uma análise de dados.

![Relatório do Profiler][11]

Se vir String.wstrcpy no caminho de acesso frequente, clique em Just My Code para alterar a vista para mostrar apenas o código de utilizador.  Se vir concat, tente pressionar o botão Mostrar todo o código.

Deverá ver o método Concatenate e concat ocupando uma grande parte do tempo de execução.

![Análise do relatório][12]

Se tiver adicionado o código de concatenação de cadeia de caracteres neste artigo, deverá ver um aviso na lista de tarefas para isso. Também poderá ver um aviso de que existe uma quantidade excessiva de coleta de lixo, que é devido ao número de cadeias de caracteres que são criados e descartado.

![Aviso de desempenho][14]

## <a name="4-make-changes-and-compare-performance"></a>4: Fazer alterações e comparar o desempenho
Também pode comparar o desempenho antes e depois uma alteração de código.  Parar o processo em execução e editar o código para substituir a operação de concatenação de cadeia de caracteres com o uso de StringBuilder:

```csharp
public static string Concatenate(int number)
{
    int count;
    System.Text.StringBuilder builder = new System.Text.StringBuilder("");
    for (count = 0; count < number; count++)
    {
        builder.Append("\n" + count.ToString());
    }
    return builder.ToString();
}
```

Fazer outra execução de desempenho e, em seguida, compare o desempenho. No Explorador de desempenho, se as execuções são na mesma sessão, pode simplesmente selecionar ambos os relatórios, abrir o menu de atalho e escolha **Compare relatórios de desempenho**. Se deseja comparar com uma execução em outra sessão de desempenho, abra a **Analyze** menu e escolha **comparar relatórios de desempenho**. Especifique ambos os ficheiros na caixa de diálogo que aparece.

![Comparar a opção de relatórios de desempenho][15]

Os relatórios de destacam as diferenças entre as duas execuções.

![Relatório de comparação][16]

Parabéns! Que tiver iniciado com o criador de perfil.

## <a name="troubleshooting"></a>Resolução de problemas
* Certifique-se de que está criando uma compilação de versão e iniciar sem depuração.
* Se a opção de anexar/desanexar não estiver ativada no menu do Profiler, execute o Assistente de desempenho.
* Utilize a IU de emulador de computação para ver o estado da sua aplicação. 
* Se tiver problemas a partir de aplicativos no emulador, ou anexar o criador de perfil, encerrar pendente o emulador de computação e reiniciá-lo. Se o que não resolve o problema, tente reiniciar. Este problema pode ocorrer se usar o emulador de computação para suspender e remover implementações em execução.
* Se tiver utilizado qualquer um dos comandos da linha de comando, especialmente as definições globais, criação de perfis, certifique-se de que foi chamado VSPerfClrEnv /globaloff e que VsPerfMon.exe foi encerrado.
* Se quando a amostragem, verá a mensagem "PRF0025: Não existem dados coletados,"Verifique se o processo que anexou à tem atividade de CPU. Aplicativos que não estão a fazer qualquer trabalho computacional podem não produzir qualquer dado de amostragem.  Também é possível que o processo foi encerrado antes de qualquer amostragem foi feita. Certifique-se de que o método Run para uma função que se está criando não encerra.

## <a name="next-steps"></a>Próximos Passos
Instrumentar binários do Azure no emulador não é suportada no Criador de perfil do Visual Studio, mas se quiser testar a alocação de memória, pode escolher essa opção quando a criação de perfis. Também é possível criação de perfis de simultaneidade, que ajuda a determinar se os threads são desperdiçar tempo competindo por bloqueios ou criação de perfil de interação, de camada que ajuda a rastrear problemas de desempenho ao interagir entre camadas de um aplicativo, mais com frequência entre a camada de dados e uma função de trabalho.  Pode ver as consultas de base de dados que gera a sua aplicação e utilizar os dados de criação de perfis para melhorar a utilização da base de dados. Para obter informações sobre a criação de perfil de interação de camada, consulte a mensagem de blogue [passo a passo: Usando o Profiler de interação de camada no Visual Studio Team System 2010][3].

[1]: https://docs.microsoft.com/azure/application-insights/app-insights-profiler
[2]: https://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: https://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
