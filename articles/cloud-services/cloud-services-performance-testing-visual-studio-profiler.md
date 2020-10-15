---
title: Perfilamento de um Serviço de Nuvem Localmente no Emulator Compute Microsoft Docs
services: cloud-services
description: Investigue problemas de desempenho em serviços na nuvem com o profiler do Estúdio Visual
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
ms.openlocfilehash: 6b5707405879c462a1d919e04730d368332ba68c
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92077160"
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Testar o desempenho de um serviço de nuvem localmente no Emulador Azure Compute usando o Perfil de Estúdio Visual
Uma variedade de ferramentas e técnicas estão disponíveis para testar o desempenho dos serviços na nuvem.
Quando publica um serviço em nuvem para o Azure, pode pedir ao Visual Studio que recolha dados de perfis e, em seguida, analise-os localmente, como descrito no [Profiling a Azure Application][1].
Também pode utilizar diagnósticos para rastrear uma variedade de contadores de desempenho, conforme descrito na [Utilização de contadores de desempenho em Azure][2].
Também pode querer perfilar a sua aplicação localmente no emulador compute antes de a implementar na nuvem.

Este artigo abrange o método de amostragem do CPU de perfis, que pode ser feito localmente no emulador. A amostragem do CPU é um método de perfis que não é muito intrusivo. Num intervalo de amostragem designado, o perfil tira uma imagem da pilha de chamadas. Os dados são recolhidos ao longo de um período de tempo, e apresentados num relatório. Este método de perfis tende a indicar onde, numa aplicação computacionalmente intensiva, a maior parte do trabalho da CPU está a ser realizado.  Isto dá-lhe a oportunidade de se concentrar no "caminho quente" onde a sua aplicação está a passar mais tempo.

## <a name="1-configure-visual-studio-for-profiling"></a>1: Configurar o Estúdio Visual para perfis
Em primeiro lugar, existem algumas opções de configuração do Estúdio Visual que podem ser úteis no perfil. Para dar sentido aos relatórios de perfis, vai precisar de símbolos (ficheiros.pdb) para a sua aplicação e também de símbolos para bibliotecas do sistema. Deverá certificar-se de que faz referência aos servidores de símbolos disponíveis. Para isso, no menu **Ferramentas** no Estúdio Visual, escolha **Opções,** em seguida, escolha **Debugging,** em **seguida, Símbolos**. Certifique-se de que os Servidores de Símbolos do Microsoft estão listados nas **localizações do ficheiro Symbol (.pdb).**  Também pode fazer referência https://referencesource.microsoft.com/symbols , que pode ter ficheiros de símbolos adicionais.

![Opções de símbolo][4]

Se desejar, pode simplificar os relatórios que o perfil gera definindo Just My Code. Com o Just My Code ativado, as pilhas de chamadas de função são simplificadas de modo a que as chamadas inteiramente internas para bibliotecas e o Quadro .NET sejam ocultados dos relatórios. No menu **Ferramentas,** escolha **Opções.** Em seguida, expanda o nó **ferramentas** de desempenho e escolha **o General**. Selecione a caixa de verificação para **ativar apenas o meu código para relatórios de perfis**.

![Apenas as opções do Meu Código][17]

Pode utilizar estas instruções com um projeto existente ou com um novo projeto.  Se criar um novo projeto para experimentar as técnicas descritas abaixo, escolha um projeto C# **Azure Cloud Service** e selecione um **Web Role** e um Papel **do Trabalhador**.

![Funções de projeto do Azure Cloud Service][5]

Por exemplo, adicione algum código ao seu projeto que leva muito tempo e demonstra algum problema de desempenho óbvio. Por exemplo, adicione o seguinte código a um projeto de função do trabalhador:

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

Chame este código do método RunAsync na classe derivada do RoleEntryPoint do papel operário. (Ignore o aviso sobre o método que funciona sincronizadamente.)

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

Construa e execute o seu serviço em nuvem localmente sem depuração (Ctrl+F5), com a configuração da solução definida para **release**. Isto garante que todos os ficheiros e pastas são criados para executar a aplicação localmente, e garante que todos os emuladores são iniciados. Inicie a UI do Emulador Compute a partir da barra de tarefas para verificar se o seu papel de trabalhador está a desempenhar.

## <a name="2-attach-to-a-process"></a>2: Anexar a um processo
Em vez de perfilar a aplicação a partir do IDE do Visual Studio 2010, deve anexar o perfil a um processo de execução. 

Para anexar o perfil a um processo, no menu **Analisar,** escolha **Profiler** e **Attach/Despremie**.

![Adiem a opção de perfil][6]

Para um papel de trabalhador, encontre o processo WaWorkerHost.exe.

![Processo WaWorkerHost][7]

Se a sua pasta de projeto estiver numa unidade de rede, o perfis pedir-lhe-á que forneça outro local para guardar os relatórios de perfis.

 Também pode anexar uma função web fixando-se a WaIISHost.exe.
Se houver vários processos de função do trabalhador na sua aplicação, precisa de utilizar o processoID para os distinguir. Pode consultar o processoID programáticamente acedendo ao objeto Processo. Por exemplo, se adicionar este código ao método Executar da classe derivada roleEntryPoint numa função, pode olhar para o registo no UI do Emulador Compute para saber a que processo se ligar.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

Para visualizar o registo, inicie a UI do Emulador Compute.

![Inicie a UI do Emulador Compute][8]

Abra a janela da consola de registo de funções do trabalhador na UI do Emulador Compute clicando na barra de título da janela da consola. Pode ver a identificação do processo no registo.

![Ver ID do processo][9]

Um que tenha anexado, execute os passos na UI da sua aplicação (se necessário) para reproduzir o cenário.

Quando quiser parar de fazer perfis, escolha o link **Stop Profiling.**

![Parar a opção de perfis][10]

## <a name="3-view-performance-reports"></a>3: Ver relatórios de desempenho
O relatório de desempenho da sua aplicação é apresentado.

Neste ponto, o profiler para de executar, guarda dados num ficheiro .vsp e apresenta um relatório que mostra uma análise destes dados.

![Relatório do profiler][11]

Se vir String.wstrcpy no Caminho Quente, clique em Just My Code para alterar a vista apenas para mostrar o código do utilizador.  Se vir String.Concat, tente premir o botão Mostrar Todos códigos.

Você deve ver o método Concatenate e String.Concat ocupando uma grande parte do tempo de execução.

![Análise do relatório][12]

Se adicionar o código de concatenação de cordas neste artigo, deverá ver um aviso na Lista de Tarefas para este facto. Pode também ver um aviso de que há uma quantidade excessiva de recolha de lixo, que se deve ao número de cordas que são criadas e eliminadas.

![Avisos de desempenho][14]

## <a name="4-make-changes-and-compare-performance"></a>4: Fazer alterações e comparar desempenho
Também pode comparar o desempenho antes e depois de uma alteração de código.  Pare o processo de execução e edite o código para substituir a operação de concatenação de cordas com a utilização do StringBuilder:

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

Faça outra performance e, em seguida, compare o desempenho. No Performance Explorer, se as execuções estiverem na mesma sessão, basta selecionar ambos os relatórios, abrir o menu de atalho e escolher **Compare Performance Reports**. Se quiser comparar com uma corrida em outra sessão de desempenho, abra o menu **'Analisar'** e escolha **Compare Performance Reports**. Especifique ambos os ficheiros na caixa de diálogo que aparece.

![Compare a opção de relatórios de desempenho][15]

Os relatórios destacam as diferenças entre as duas corridas.

![Relatório de comparação][16]

Parabéns! Começaste com o perfis.

## <a name="troubleshooting"></a>Resolução de problemas
* Certifique-se de que está a perfilar uma construção de Lançamento e comece sem depurar.
* Se a opção Anexar/Desprendimento não estiver ativada no menu Profiler, execute o Assistente de Desempenho.
* Utilize o UI do Emulador Compute para visualizar o estado da sua aplicação. 
* Se tiver problemas em iniciar aplicações no emulador ou em ligar o perfil, desligue o emulador computacional e reinicie-o. Se isso não resolver o problema, tente reiniciar. Este problema pode ocorrer se utilizar o Emulador Compute para suspender e remover as implementações de funcionamento.
* Se tiver utilizado algum dos comandos de perfis da linha de comando, especialmente as definições globais, certifique-se de que o VSPerfClrEnv /globaloff foi chamado e que VsPerfMon.exe foi desligado.
* Se ao recolher amostragem, consulte a mensagem "PRF0025: Não foram recolhidos dados", verifique se o processo a que se juntou tem atividade de CPU. As aplicações que não estão a fazer qualquer trabalho computacional podem não produzir quaisquer dados de amostragem.  Também é possível que o processo tenha saído antes de qualquer amostragem ser feita. Verifique se o método 'Executar' para uma função que está a fazer perfis não termina.

## <a name="next-steps"></a>Passos Seguintes
Instrumentar binários Azure no emulador não é suportado no perfilador do Estúdio Visual, mas se quiser testar a atribuição de memória, pode escolher essa opção ao fazer perfis. Também pode escolher o perfil de concurrency, o que o ajuda a determinar se os fios estão a perder tempo a competir por bloqueios, ou perfis de interação de nível, o que o ajuda a rastrear problemas de desempenho ao interagir entre níveis de uma aplicação, mais frequentemente entre o nível de dados e um papel de trabalhador.  Pode ver as consultas de base de dados que a sua aplicação gera e utilizar os dados de perfis para melhorar a sua utilização da base de dados. Para obter informações sobre perfis de interação de nível, consulte o blog post [Walkthrough: Using the Tier Interaction Profiler in Visual Studio Team System 2010][3].

[1]: ../azure-monitor/app/profiler.md
[2]: /previous-versions/azure/hh411542(v=azure.100)
[3]: /archive/blogs/habibh/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010
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