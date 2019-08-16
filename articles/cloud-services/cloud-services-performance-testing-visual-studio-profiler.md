---
title: Criando o perfil de um serviço de nuvem localmente no emulador de computação | Microsoft Docs
services: cloud-services
description: Investigar problemas de desempenho em serviços de nuvem com o criador de perfil do Visual Studio
documentationcenter: ''
author: mikejo
manager: jillfra
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
ms.openlocfilehash: 4844e07b83f7e529d7e3de2c5bac1dadb5414391
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515946"
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Testando o desempenho de um serviço de nuvem localmente no emulador de computação do Azure usando o criador de perfil do Visual Studio
Uma variedade de ferramentas e técnicas estão disponíveis para testar o desempenho dos serviços de nuvem.
Quando você publica um serviço de nuvem no Azure, pode fazer com que o Visual Studio colete dados de criação de perfil e, em seguida, analise-os localmente, conforme descrito em [criação de perfil de um aplicativo Azure][1].
Você também pode usar o diagnóstico para acompanhar uma variedade de contadores de desempenho, conforme descrito em [usando contadores de desempenho no Azure][2].
Você também pode querer criar o perfil do seu aplicativo localmente no emulador de computação antes de implantá-lo na nuvem.

Este artigo aborda o método de criação de perfis da Amostragem de CPU, que pode ser levada a cabo localmente no emulador. A amostragem de CPU é um método de criação de perfil que não é muito invasivo. Em um intervalo de amostragem designado, o criador de perfil tira um instantâneo da pilha de chamadas. Os dados são coletados durante um período de tempo e mostrados em um relatório. Esse método de criação de perfil tende a indicar onde em um aplicativo de computação intensiva a maior parte do trabalho de CPU está sendo feito.  Isso lhe dá a oportunidade de se concentrar no "Hot Path" onde seu aplicativo está gastando mais tempo.

## <a name="1-configure-visual-studio-for-profiling"></a>1: Configurar o Visual Studio para criação de perfil
Primeiro, há algumas opções de configuração do Visual Studio que podem ser úteis durante a criação de perfil. Para fazer sentido dos relatórios de criação de perfil, você precisará de símbolos (arquivos. pdb) para seu aplicativo e também de símbolos para bibliotecas do sistema. Você deve se certificar de fazer referência aos servidores de símbolo disponíveis. Para fazer isso, no menu **ferramentas** no Visual Studio, escolha **Opções**e, em seguida, escolha **depuração**e **símbolos**. Verifique se os servidores de símbolos da Microsoft estão listados em **locais de arquivo de símbolo (. pdb)** .  Você também pode referenciar https://referencesource.microsoft.com/symbols, que pode ter arquivos de símbolo adicionais.

![Opções de símbolo][4]

Se desejar, você pode simplificar os relatórios que o criador de perfil gera Configurando Apenas Meu Código. Com o Apenas Meu Código habilitado, as pilhas de chamadas de função são simplificadas para que as chamadas totalmente internas às bibliotecas e as .NET Framework fiquem ocultas dos relatórios. No menu **ferramentas** , escolha **Opções**. Em seguida, expanda o nó **ferramentas de desempenho** e escolha **geral**. Marque a caixa de seleção **habilitar apenas meu código para relatórios do**Profiler.

![Opções de Apenas Meu Código][17]

Você pode usar essas instruções com um projeto existente ou com um novo projeto.  Se você criar um novo projeto para experimentar as técnicas descritas abaixo, escolha C# um projeto de **serviço de nuvem do Azure** e selecione uma **função Web** e uma **função de trabalho**.

![Funções de projeto de serviço de nuvem do Azure][5]

Para fins de exemplo, adicione um código ao seu projeto que demore muito tempo e demonstre algum problema de desempenho óbvio. Por exemplo, adicione o seguinte código a um projeto de função de trabalho:

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

Chame esse código do método RunAsync na classe derivada de RoleEntryPoint da função de trabalho. (Ignore o aviso sobre o método executado de forma síncrona.)

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

Crie e execute seu serviço de nuvem localmente sem depuração (Ctrl + F5), com a configuração da solução definida como **liberar**. Isso garante que todos os arquivos e pastas sejam criados para executar o aplicativo localmente e garante que todos os emuladores sejam iniciados. Inicie a interface do usuário do emulador de computação na barra de tarefas para verificar se sua função de trabalho está em execução.

## <a name="2-attach-to-a-process"></a>2: Anexar a um processo
Em vez de fazer o perfil do aplicativo iniciando-o no IDE do Visual Studio 2010, você deve anexar o criador de perfil a um processo em execução. 

Para anexar o criador de perfil a um processo, no menu **analisar** , escolha Profiler e **anexar/desanexar**.

![Opção anexar perfil][6]

Para uma função de trabalho, localize o processo WaWorkerHost. exe.

![Processo de WaWorkerHost][7]

Se a pasta do projeto estiver em uma unidade de rede, o criador de perfil solicitará que você forneça outro local para salvar os relatórios de criação de perfil.

 Você também pode anexar a uma função Web anexando a WaIISHost. exe.
Se houver vários processos de função de trabalho em seu aplicativo, você precisará usar o processID para distingui-los. Você pode consultar o processID programaticamente acessando o objeto de processo. Por exemplo, se você adicionar esse código ao método Run da classe derivada de RoleEntryPoint em uma função, poderá examinar o log na interface do usuário do emulador de computação para saber a qual processo se conectar.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

Para exibir o log, inicie a interface do usuário do emulador de computação.

![Iniciar a interface do usuário do emulador de computação][8]

Abra a janela do console de log de função de trabalho na interface do usuário do emulador de computação clicando na barra de título da janela do console. Você pode ver a ID do processo no log.

![Exibir ID do processo][9]

Uma que você tenha anexado, execute as etapas na interface do usuário do aplicativo (se necessário) para reproduzir o cenário.

Quando você quiser parar a criação de perfil, escolha o link **parar criação de perfil** .

![Opção parar criação de perfil][10]

## <a name="3-view-performance-reports"></a>3: Exibir relatórios de desempenho
O relatório de desempenho para seu aplicativo é exibido.

Neste ponto, o criador de perfil interrompe a execução, salva dados em um arquivo. vsp e exibe um relatório que mostra uma análise desses dados.

![Relatório do criador de perfil][11]

Se você vir String. wstrcpy no Hot Path, clique em Apenas Meu Código para alterar a exibição para mostrar apenas o código do usuário.  Se você vir String. Concat, tente pressionar o botão Mostrar todo o código.

Você deve ver o método concatenar e String. Concat que ocupa uma grande parte do tempo de execução.

![Análise de relatório][12]

Se você adicionou o código de concatenação de cadeia de caracteres neste artigo, você deverá ver um aviso na Lista de Tarefas para isso. Você também pode ver um aviso de que há uma quantidade excessiva de coleta de lixo, que é devido ao número de cadeias de caracteres que são criadas e descartadas.

![Avisos de desempenho][14]

## <a name="4-make-changes-and-compare-performance"></a>4: Fazer alterações e comparar o desempenho
Você também pode comparar o desempenho antes e depois de uma alteração de código.  Pare o processo em execução e edite o código para substituir a operação de concatenação de cadeia de caracteres pelo uso de StringBuilder:

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

Execute outra execução de desempenho e compare o desempenho. No Gerenciador de Desempenho, se as execuções estiverem na mesma sessão, basta selecionar ambos os relatórios, abrir o menu de atalho e escolher **comparar relatórios de desempenho**. Se você quiser comparar com uma execução em outra sessão de desempenho, abra o menu **analisar** e escolha **comparar relatórios de desempenho**. Especifique os dois arquivos na caixa de diálogo que aparece.

![Opção comparar relatórios de desempenho][15]

Os relatórios realçam as diferenças entre as duas execuções.

![Relatório de comparação][16]

Parabéns! Você começou com o criador de perfil.

## <a name="troubleshooting"></a>Resolução de problemas
* Verifique se você está criando o perfil de uma compilação de versão e inicie sem depuração.
* Se a opção anexar/desanexar não estiver habilitada no menu criador de perfil, execute o assistente de desempenho.
* Use a interface do usuário do emulador de computação para exibir o status do seu aplicativo. 
* Se você tiver problemas para iniciar aplicativos no emulador ou anexar o criador de perfil, desligue o emulador de computação e reinicie-o. Se isso não resolver o problema, tente reinicializar. Esse problema pode ocorrer se você usar o emulador de computação para suspender e remover implantações em execução.
* Se você tiver usado qualquer um dos comandos de criação de perfil da linha de comando, especialmente as configurações globais, certifique-se de que VSPerfClrEnv/globaloff foi chamado e que VsPerfMon. exe foi desligado.
* Se, durante a amostragem, você vir a mensagem "PRF0025: Nenhum dado foi coletado "Verifique se o processo que você anexou tem atividade de CPU. Os aplicativos que não estão fazendo nenhum trabalho computacional podem não produzir nenhum dado de amostragem.  Também é possível que o processo tenha sido encerrado antes de qualquer amostragem ser feita. Verifique se o método Run de uma função para a qual você está criando a criação de perfil não é encerrado.

## <a name="next-steps"></a>Próximos Passos
A instrumentação de binários do Azure no emulador não tem suporte no criador de perfil do Visual Studio, mas se você quiser testar a alocação de memória, poderá escolher essa opção ao fazer a criação de perfil. Você também pode escolher a criação de perfil de simultaneidade, que ajuda a determinar se os threads estão desperdiçando tempo competindo por bloqueios ou criação de perfil de interação de camada, o que ajuda a rastrear problemas de desempenho ao interagir entre as camadas de um aplicativo, a maioria frequentemente entre a camada de dados e uma função de trabalho.  Você pode exibir as consultas de banco de dados geradas por seu aplicativo e usar a criação de perfis para melhorar o uso do banco de dado. Para obter informações sobre a criação de perfil de interação de camada [, consulte a postagem do blog passo a passos: Usando o criador de perfil de interação de camada no Visual][3]Studio Team System 2010.

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
