---
title: Perfis de um Serviço de Nuvem Localmente no Emulador computacional [ Microsoft Docs
services: cloud-services
description: Investigue problemas de desempenho em serviços na nuvem com o perfilador do Estúdio Visual
documentationcenter: ''
author: mikejo
manager: jillfra
editor: ''
tags: ''
ms.assetid: 25e40bf3-eea0-4b0b-9f4a-91ffe797f6c3
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/18/2016
ms.author: mikejo
ms.openlocfilehash: 21270d3c7143ce063ffe30d939368b9813e9072e
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "70094108"
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Testar o desempenho de um serviço de nuvem localmente no Emulador de Computação Azure Usando o Perfil do Estúdio Visual
Uma variedade de ferramentas e técnicas estão disponíveis para testar o desempenho dos serviços na nuvem.
Quando publica um serviço na nuvem para o Azure, pode pedir ao Visual Studio que recolha dados de perfis e, em seguida, analise-os localmente, como descrito no [Profiling a Azure Application][1].
Também pode utilizar diagnósticos para rastrear uma variedade de contadores de desempenho, como descrito em Utilizar contadores de [desempenho em Azure][2].
Também pode querer perfilar a sua aplicação localmente no emulador de cálculo antes de a colocar na nuvem.

Este artigo abrange o método de amostragem de CPU de perfis, que pode ser feito localmente no emulador. A amostragem de CPU é um método de perfis que não é muito intrusivo. Num intervalo de amostragem designado, o perfilador tira uma foto da pilha de chamadas. Os dados são recolhidos ao longo de um período de tempo e apresentados num relatório. Este método de perfis tende a indicar onde, numa aplicação computacionalmente intensiva, a maior parte do trabalho da CPU está a ser feito.  Isto dá-lhe a oportunidade de se concentrar no "caminho quente" onde a sua aplicação está a passar mais tempo.

## <a name="1-configure-visual-studio-for-profiling"></a>1: Configure Visual Studio para perfis
Primeiro, existem algumas opções de configuração do Estúdio Visual que podem ser úteis ao perfil. Para dar sentido aos relatórios de perfis, vai precisar de símbolos (ficheiros.pdb) para a sua aplicação e também símbolos para bibliotecas do sistema. Deverá certificar-se de que faz referência aos servidores de símbolos disponíveis. Para isso, no menu **Tools** in Visual Studio, escolha **Opções,** em seguida, escolha **Debugging,** em **seguida, Símbolos**. Certifique-se de que os Servidores de Símbolos da Microsoft estão listados sob as localizações do **ficheiro Symbol (.pdb).**  Também pode https://referencesource.microsoft.com/symbolsfazer referência, que pode ter ficheiros de símbolos adicionais.

![Opções de símbolo][4]

Se desejar, pode simplificar os relatórios que o profiler gera definindo Just My Code. Com o Just My Code ativado, as pilhas de chamadas de funções são simplificadas de modo a que as chamadas totalmente internas às bibliotecas e ao Quadro .NET sejam ocultadas dos relatórios. No menu **Ferramentas,** escolha **Opções**. Em seguida, expanda o nó das **Ferramentas** de Desempenho e escolha **o General**. Selecione a caixa de verificação para **ativar apenas**o meu código para relatórios de perfis .

![Apenas opções do Meu Código][17]

Pode utilizar estas instruções com um projeto existente ou com um novo projeto.  Se criar um novo projeto para experimentar as técnicas descritas abaixo, escolha um projeto C# **Azure Cloud Service** e selecione um **Web Role** e um **Funto de Trabalhador**.

![Funções do projeto Azure Cloud Service][5]

Para fins por exemplo, adicione algum código ao seu projeto que leva muito tempo e demonstre algum problema de desempenho óbvio. Por exemplo, adicione o seguinte código a um projeto de função de trabalhador:

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

Ligue para este código a partir do método RunAsync na classe derivada do roleEntryPoint do papel do trabalhador. (Ignore o aviso sobre o método que funciona sincronizadamente.)

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

Construa e execute o seu serviço de nuvem localmente sem depuração (Ctrl+F5), com a configuração de solução definida para **Lançamento**. Isto garante que todos os ficheiros e pastas são criados para executar a aplicação localmente, e garante que todos os emuladores são iniciados. Inicie a UI do Emulador computacional a partir da barra de tarefas para verificar se o seu papel de trabalhador está em execução.

## <a name="2-attach-to-a-process"></a>2: Anexar a um processo
Em vez de perfilar a aplicação iniciando-a a partir do Visual Studio 2010 IDE, deve ligar o perfil a um processo de execução. 

Para fixar o perfil a um processo, no menu **Analisar,** escolha **Profiler** e **Attach/Detach**.

![Anexar a opção de perfil][6]

Para um papel de trabalhador, encontre o processo WaWorkerHost.exe.

![Processo WaWorkerHost][7]

Se a pasta do projeto estiver numa unidade de rede, o profiler pedir-lhe-á que forneça outro local para guardar os relatórios de perfis.

 Também pode anexar-se a uma função web anexando-se a WaIISHost.exe.
Se existem múltiplos processos de função do trabalhador na sua aplicação, precisa de usar o processID para os distinguir. Pode consultar o processID programáticamente acedendo ao objeto Do Processo. Por exemplo, se adicionar este código ao método Executar da classe derivada do RoleEntryPoint numa função, pode olhar para o log no Compute Emulator UI para saber a que processo se conectar.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

Para ver o registo, inicie a UI do Emulador Computacional.

![Inicie a UI do Emulador computacional][8]

Abra a janela da consola de log role do trabalhador no Compute Emulator UI clicando na barra de título da janela da consola. Pode ver a identificação do processo no registo.

![Ver ID do processo][9]

Um que tenha anexado, execute os passos na UI da sua aplicação (se necessário) para reproduzir o cenário.

Quando quiser parar de fazer perfis, escolha o link **Stop Profiling.**

![Parar a opção De perfil][10]

## <a name="3-view-performance-reports"></a>3: Ver relatórios de desempenho
O relatório de desempenho da sua aplicação é apresentado.

Neste ponto, o perfilador deixa de executar, guarda dados num ficheiro .vsp e apresenta um relatório que mostra uma análise destes dados.

![Relatório do profiler][11]

Se vir String.wstrcpy no Caminho Quente, clique em Just My Code para alterar a vista para mostrar apenas o código do utilizador.  Se vir string.Concat, tente premir o botão Show All Code.

Devias ver o método concatenado e a String.Concat a ocupar uma grande parte do tempo de execução.

![Análise do relatório][12]

Se adicionou o código de concatenação de cordas neste artigo, deve ver um aviso na Lista de Tarefas para isso. Pode também ver um aviso de que existe uma quantidade excessiva de recolha de lixo, que se deve ao número de cordas que são criadas e eliminadas.

![Avisos de desempenho][14]

## <a name="4-make-changes-and-compare-performance"></a>4: Fazer alterações e comparar desempenho
Também pode comparar o desempenho antes e depois de uma alteração de código.  Pare o processo de funcionamento e edite o código para substituir a operação de concatenação de cordas com a utilização do StringBuilder:

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

Faça outra performance e compare o desempenho. No Performance Explorer, se as corridas estiverem na mesma sessão, pode selecionar ambos os relatórios, abrir o menu de atalho e escolher **Compare Performance Reports**. Se quiser comparar com uma corrida noutra sessão de desempenho, abra o menu **Analyze** e escolha **Compare Performance Reports**. Especifique ambos os ficheiros na caixa de diálogo que aparece.

![Comparar a opção de relatórios de desempenho][15]

Os relatórios destacam as diferenças entre as duas corridas.

![Relatório de comparação][16]

Parabéns! Começaste com o perfil.

## <a name="troubleshooting"></a>Resolução de problemas
* Certifique-se de que está a fazer perfis de uma construção de Lançamento e comece sem depurar.
* Se a opção Anexar/Deposição não estiver ativada no menu Profiler, execute o Assistente de Desempenho.
* Utilize a UI do Emulador Compute para ver o estado da sua aplicação. 
* Se tiver problemas em iniciar aplicações no emulador, ou anexar o perfil, desligue o emulador do cálculo e reinicie-o. Se isso não resolver o problema, tente reiniciar. Este problema pode ocorrer se utilizar o Emulador Compute para suspender e remover as implementações em execução.
* Se utilizou algum dos comandos de perfis da linha de comando, especialmente as configurações globais, certifique-se de que vsPerfClrEnv /globaloff foi chamado e que VsPerfMon.exe foi desligado.
* Se ao recolher amostragem, vê a mensagem "PRF0025: Não foram recolhidos dados", verifique se o processo a que se anexou tem atividade de CPU. As aplicações que não estão a fazer nenhum trabalho computacional podem não produzir quaisquer dados de amostragem.  Também é possível que o processo tenha saído antes de qualquer amostragem ter sido feita. Verifique se o método Executar para uma função que está a perfilar não termina.

## <a name="next-steps"></a>Passos Seguintes
A instrumentação de binários Azure no emulador não é suportada no perfilador do Estúdio Visual, mas se quiser testar a atribuição de memória, pode escolher essa opção ao perfilar. Também pode escolher perfis de moeda, o que o ajuda a determinar se os fios estão a perder tempo a competir por fechaduras, ou perfis de interação de nível, o que o ajuda a rastrear problemas de desempenho ao interagir entre níveis de uma aplicação, mais frequentemente entre o nível de dados e um papel de trabalhador.  Pode ver as consultas de base de dados que a sua aplicação gera e utilizar os dados de perfis para melhorar a sua utilização da base de dados. Para obter informações sobre perfis de interação a nível, consulte o blog post [Walkthrough: Using the Tier Interaction Profiler in Visual Studio Team System 2010][3].

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
