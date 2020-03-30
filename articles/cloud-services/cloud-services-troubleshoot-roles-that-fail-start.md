---
title: Papéis de troubleshoot que não começam Microsoft Docs
description: Aqui estão algumas razões comuns para que um papel de Serviço de Nuvem possa não começar. São também fornecidas soluções para estes problemas.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 674b2faf-26d7-4f54-99ea-a9e02ef0eb2f
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 869453d92f536a62aacc2be52598223158566ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71122734"
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Funções de Troubleshoot Cloud Service que não conseguem começar
Aqui estão alguns problemas e soluções comuns relacionados com as funções da Azure Cloud Services que não conseguem arrancar.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>DLLs ou dependências desaparecidas
Papéis e funções sem resposta que estão a andar de bicicleta entre os estados de **Inicialização,** **Ocupados**e **Deter** podem ser causados por DLLs ou assembleias em falta.

Os sintomas de DLLs ou conjuntos em falta podem ser:

* A sua instância de papel é andar de bicicleta pelos estados de **Inicialização,** **Ocupados**e **Deter.**
* A sua instância de papel mudou-se para **Ready** mas se navegar para a sua aplicação web, a página não aparece.

Existem vários métodos recomendados para investigar estas questões.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnosticar problemas de DLL em falta num papel web
Quando navega para um site que é implementado numa função web, e o navegador apresenta um erro de servidor semelhante ao seguinte, pode indicar que um DLL está em falta.

![Erro do servidor na aplicação '/'.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnosticar problemas desligando erros personalizados
Informações de erro mais completas podem ser visualizadas configurando o web.config para a função web para definir o modo de erro personalizado para Desligar e recolocar o serviço.

Para ver erros mais completos sem utilizar o Ambiente de Trabalho Remoto:

1. Abra a solução no Microsoft Visual Studio.
2. No **Solution Explorer,** localize o ficheiro web.config e abra-o.
3. No ficheiro web.config, localize a secção system.web e adicione a seguinte linha:

    ```xml
    <customErrors mode="Off" />
    ```
4. Guarde o ficheiro.
5. Reembala e reimplante o serviço.

Assim que o serviço for redistribuído, verá uma mensagem de erro com o nome do conjunto em falta ou dLL.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnosticar problemas visualizando o erro remotamente
Pode utilizar o Remote Desktop para aceder à função e visualizar informações de erro mais completas remotamente. Utilize os seguintes passos para ver os erros utilizando o Ambiente de Trabalho Remoto:

1. Certifique-se de que o Azure SDK 1.3 ou mais tarde está instalado.
2. Durante a implementação da solução utilizando o Estúdio Visual, ative o Ambiente de Trabalho Remoto. Para mais informações, consulte [Enable Remote Desktop Connection for a Role in Azure Cloud Services using Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).
3. No portal Microsoft Azure, uma vez que a instância mostra um estado de **Ready**, remota mente na instância. Para obter mais informações sobre a utilização do ambiente de trabalho remoto com os Serviços cloud, consulte [o Remote em instâncias](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances)de funções .
5. Inscreva-se na máquina virtual utilizando as credenciais especificadas durante a configuração do Ambiente de Trabalho Remoto.
6. Abra uma janela de comando.
7. Digite `IPconfig`.
8. Note o valor do endereço IPV4.
9. Abra o Internet Explorer.
10. Digite o endereço e o nome da aplicação web. Por exemplo, `http://<IPV4 Address>/default.aspx`.

Navegar para o site irá agora devolver mensagens de erro mais explícitas:

* Erro do servidor na aplicação '/'.
* Descrição: Ocorreu uma exceção não tratada durante a execução do pedido web atual. Por favor, reveja o rastreio da pilha para obter mais informações sobre o erro e de onde se originou no código.
* Detalhes de exceção: System.IO.FIleNotFoundException: Não conseguiu carregar ficheiro sinuoso ou montagem 'Microsoft.WindowsAzure.StorageClient, Version=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35' ou uma das suas dependências. O sistema não consegue encontrar o ficheiro especificado.

Por exemplo:

![Erro explícito do servidor na aplicação '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnosticar problemas utilizando o emulador de cálculo
Pode utilizar o emulador computacional do Microsoft Azure para diagnosticar e resolver problemas de dependências desaparecidas e erros web.config.

Para obter os melhores resultados na utilização deste método de diagnóstico, deve utilizar um computador ou uma máquina virtual que tenha uma instalação limpa do Windows. Para simular melhor o ambiente Azure, utilize o Windows Server 2008 R2 x64.

1. Instale a versão autónoma do [Azure SDK](https://azure.microsoft.com/downloads/).
2. Na máquina de desenvolvimento, construa o projeto de serviço em nuvem.
3. No Windows Explorer, navegue para a pasta bin\debug do projeto de serviço na nuvem.
4. Copie a pasta .csx e o ficheiro .cscfg para o computador que está a usar para depurar os problemas.
5. Na máquina limpa, abra uma janela e tipo `csrun.exe /devstore:start`de solicitação de comando SDK Azure .
6. No pedido de `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`comando, escreva .
7. Quando o papel começar, verá informações detalhadas de erro no Internet Explorer. Também pode utilizar ferramentas padrão de resolução de problemas do Windows para diagnosticar o problema.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnosticar problemas usando intelliTrace
Para funções de trabalhador e web que utilizem .NET Framework 4, pode utilizar o [IntelliTrace,](/visualstudio/debugger/intellitrace)que está disponível na Microsoft Visual Studio Enterprise.

Siga estes passos para implementar o serviço com o IntelliTrace ativado:

1. Confirme se o Azure SDK 1.3 ou posteriormente está instalado.
2. Implementar a solução utilizando o Visual Studio. Durante a implementação, verifique a caixa de verificação de **funções .NET 4.**
3. Assim que a instância começar, abra o **Server Explorer**.
4. Expanda o nó **azure\\Cloud Services** e localize a implantação.
5. Expanda a implantação até ver as instâncias de papel. Clique à direita numa das instâncias.
6. Escolha **ver registos IntelliTrace**. O **Resumo IntelliTrace** será aberto.
7. Localize a secção de exceções do resumo. Se houver exceções, a secção será rotulada de Dados de **Exceção**.
8. Expandir os Dados de Exceção e procurar erros de **exceção** do **System.IO.FileNotFoundException** similares aos seguintes:

![Dados de exceção, ficheiros em falta ou montagem](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Endereço desaparecido DLLs e conjuntos
Para resolver os erros de DLL em falta e de montagem, siga estes passos:

1. Abra a solução no Estúdio Visual.
2. No **Solution Explorer,** abra a pasta **Referências.**
3. Clique na montagem identificada no erro.
4. No painel **Propriedades,** localize a **propriedade Copy Local** e detetete o valor para **True**.
5. Reimplante o serviço de nuvem.

Depois de ter verificado que todos os erros foram corrigidos, pode implementar o serviço sem verificar a caixa de verificação de **funções .NET 4.**

## <a name="next-steps"></a>Passos seguintes
Veja mais artigos de [resolução de problemas](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) para serviços na nuvem.

Para aprender a resolver problemas de papel de serviço na nuvem utilizando dados de diagnóstico de computador Azure PaaS, consulte a [série de blogs de Kevin Williamson.](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)
