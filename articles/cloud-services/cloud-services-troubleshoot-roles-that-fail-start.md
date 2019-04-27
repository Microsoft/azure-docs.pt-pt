---
title: Resolver problemas de funções que falharem ao iniciar | Documentos da Microsoft
description: Aqui estão algumas razões comuns, por que uma função de serviço em nuvem pode falhar ao iniciar. Também são fornecidas soluções para esses problemas.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 674b2faf-26d7-4f54-99ea-a9e02ef0eb2f
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: d2daae2a3317d3b48748262d87ab8d7f7e13f2b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60653394"
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Resolver problemas de funções do serviço Cloud que falharem ao iniciar
Seguem-se alguns problemas comuns e soluções relacionadas para serviços Cloud do Azure, funções que falharem ao iniciar.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>DLLs em falta ou dependências
Funções sem resposta e funções que são andar de bicicleta entre **a inicializar**, **ocupado**, e **parar** Estados podem ser causados por falta de DLLs ou assemblies.

Os sintomas de DLLs ou assemblies em falta podem ser:

* A instância de função está passando por toda **a inicializar**, **ocupado**, e **parar** Estados.
* A instância de função foi movido para **pronto** , mas se navega para a sua aplicação web, a página não aparece.

Existem vários métodos recomendados para investigar esses problemas.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnosticar problemas DLL em falta numa função da web
Ao navegar para um Web site que é implementado numa web função e o browser apresenta um erro de servidor semelhante ao seguinte, poderá indicar que está faltando uma DLL.

![Erro de servidor na aplicação '/'.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnosticar problemas ao desativar erros personalizados
Informações de erro mais completas podem ser visualizadas ao configurar o Web. config para a função da web definir o modo de erro personalizadas para desativado e voltar a implementar o serviço.

Para ver os erros mais completos sem utilizar o ambiente de trabalho remoto:

1. Abra a solução no Microsoft Visual Studio.
2. Na **Explorador de soluções**, localize o ficheiro Web. config e abra-o.
3. No ficheiro Web. config, localize a seção System. Web e adicione a seguinte linha:

    ```xml
    <customErrors mode="Off" />
    ```
4. Guarde o ficheiro.
5. Reempacotar e reimplemente o serviço.

Assim que o serviço está a ser reimplementado, verá uma mensagem de erro com o nome do assembly ou DLL em falta.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnosticar problemas visualizando o erro remotamente
Pode utilizar o ambiente de trabalho remoto para aceder à função e ver informações de erro mais completas remotamente. Utilize os seguintes passos para ver os erros com o ambiente de trabalho remoto:

1. Certifique-se de que o Azure SDK 1.3 ou posterior está instalado.
2. Durante a implantação da solução com o Visual Studio, ative o ambiente de trabalho remoto. Para obter mais informações, consulte [ativar a ligação de ambiente de trabalho remoto para uma função nos serviços Cloud do Azure com o Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).
3. No portal do Microsoft Azure, uma vez que a instância apresentem o estado **pronto**remoto para a instância. Para obter mais informações sobre como utilizar o ambiente de trabalho remoto com os serviços Cloud, consulte [remotamente a instâncias de função](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Entrar para a máquina virtual com as credenciais que foram especificadas durante a configuração do ambiente de trabalho remoto.
6. Abra uma janela de comando.
7. Digite `IPconfig`.
8. Tenha em atenção o valor de endereço IPV4.
9. Abra o Internet Explorer.
10. Escreva o endereço e o nome da aplicação web. Por exemplo, `http://<IPV4 Address>/default.aspx`.

Navegar para o Web site agora irá devolver mensagens de erro mais explícitas:

* Erro de servidor na aplicação '/'.
* Descrição: Ocorreu uma exceção não processada durante a execução da solicitação da web atual. Consulte o rastreio de pilha para obter mais informações sobre o erro e em que foi gerado no código.
* Detalhes da exceção: System.IO.FIleNotFoundException: Não foi possível carregar o ficheiro ou a assemblagem ' Microsoft.WindowsAzure.StorageClient, versão = 1.1.0.0, Culture = neutral, PublicKeyToken = 31bf856ad364e35' ou uma de suas dependências. Não é possível ao sistema localizar o ficheiro especificado.

Por exemplo:

![Erro de servidor explícito na aplicação '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnosticar problemas ao utilizar o emulador de computação
Pode utilizar o emulador de computação do Microsoft Azure para diagnosticar e resolver problemas de falta de dependências e erros de Web. config.

Para obter melhores resultados na através deste método de diagnóstico, deve usar um computador ou máquina virtual que tenha uma instalação limpa do Windows. Para simular melhor o ambiente do Azure, utilize o Windows Server 2008 R2 x64.

1. Instalar a versão autônoma dos [SDK do Azure](https://azure.microsoft.com/downloads/).
2. Na máquina de desenvolvimento, crie o projeto de serviço em nuvem.
3. No Explorador do Windows, navegue para a pasta de bin\debug do projeto de serviço em nuvem.
4. Copie o ficheiro de pasta e. cscfg. csx para o computador que está a utilizar para depurar os problemas.
5. Na máquina limpa, abra uma janela de linha de comandos do Azure SDK e o tipo `csrun.exe /devstore:start`.
6. No prompt de comando, digite `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.
7. Quando a função for iniciada, irá ver informações de erro detalhadas no Internet Explorer. Também pode utilizar o Windows padrão, ferramentas de resolução de problemas para diagnosticar melhor o problema.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnosticar problemas com o IntelliTrace
Para a função de trabalho e funções da web que utilizam o .NET Framework 4, pode usar [IntelliTrace](/visualstudio/debugger/intellitrace), que está disponível no Microsoft Visual Studio Enterprise.

Siga estes passos para implementar o serviço com o IntelliTrace habilitado:

1. Confirme que o Azure SDK 1.3 ou posterior está instalado.
2. Implante a solução usando o Visual Studio. Durante a implementação, consulte a **ativar o IntelliTrace para funções de .NET 4** caixa de verificação.
3. Assim que a instância for iniciada, abra a **Explorador de servidores**.
4. Expanda a **Azure\\serviços Cloud** nó e localize a implementação.
5. Expanda a implantação até ver as instâncias de função. Clique com o botão direito em uma das instâncias.
6. Escolher **protokoly IntelliTrace vista**. O **resumo de IntelliTrace** será aberto.
7. Localize a secção de exceções do resumo. Se existirem exceções, a seção serão identificados como **dados de exceção**.
8. Expanda a **dados de exceção** e procure **FileNotFoundException** erros semelhantes ao seguinte:

![Dados de exceção, ficheiro em falta ou assemblagem](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>DLLs em falta e os assemblies de endereços
Para resolver a DLL em falta e erros de assembly, siga estes passos:

1. Abra a solução no Visual Studio.
2. Na **Explorador de soluções**, abra o **referências** pasta.
3. Clique em assembly identificado no erro.
4. Na **propriedades** painel, localize **propriedade Copy Local** e defina o valor como **verdadeiro**.
5. Reimplemente o serviço de nuvem.

Assim que tiver verificado que todos os erros foram corrigidos, pode implementar o serviço sem verificar o **ativar o IntelliTrace para funções de .NET 4** caixa de verificação.

## <a name="next-steps"></a>Passos Seguintes
Ver mais [artigos de resolução de problemas](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) para serviços em nuvem.

Para saber como resolver problemas de função do serviço cloud, utilizando dados de diagnóstico do computador de PaaS do Azure, veja [série de blogue de Kevin Williamson](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
