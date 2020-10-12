---
title: Funções de resolução de problemas que não começam Microsoft Docs
description: Aqui estão algumas razões comuns para que um papel de Serviço cloud pode não começar. São também fornecidas soluções para estes problemas.
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
ms.openlocfilehash: 75603593e8c290b6bcb059207858d663ab5c96d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89460121"
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Funções de Serviço de Nuvem de Resolução de Problemas que não começam
Eis alguns problemas e soluções comuns relacionados com as funções dos Azure Cloud Services que não começam.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>DLLs em falta ou dependências
Papéis e papéis sem resposta que estão a pedalar entre estados **de inicialização,** **ocupados**e **de paragem** podem ser causados pela falta de DLLs ou conjuntos.

Os sintomas de DLLs ou conjuntos em falta podem ser:

* O seu exemplo de papel é andar de bicicleta através de Estados **de Inicialização,** **Ocupado**e **Paragem.**
* A sua instância de função mudou-se para **Ready** mas se navegar para a sua aplicação web, a página não aparece.

Existem vários métodos recomendados para investigar estas questões.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnosticar problemas de DLL em falta numa função web
Quando navega para um website que é implantado numa função web, e o navegador apresenta um erro do servidor semelhante ao seguinte, pode indicar que falta um DLL.

![Erro do servidor na aplicação '/' .](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnosticar problemas desligando erros personalizados
Informações de erro mais completas podem ser visualizadas configurando o web.config para a função web definir o modo de erro personalizado para Desligar e redistribuir o serviço.

Para ver erros mais completos sem utilizar o Ambiente de Trabalho Remoto:

1. Abra a solução no Microsoft Visual Studio.
2. No **Solution Explorer,** localize o ficheiro web.config e abra-o.
3. No ficheiro web.config, localize a secção system.web e adicione a seguinte linha:

    ```xml
    <customErrors mode="Off" />
    ```
4. Guarde o ficheiro.
5. Reembalar e recolocar o serviço.

Uma vez que o serviço seja reenviado, verá uma mensagem de erro com o nome do conjunto em falta ou DLL.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnosticar problemas visualizando o erro remotamente
Pode utilizar o Remote Desktop para aceder à função e visualizar informações de erro mais completas remotamente. Utilize os seguintes passos para visualizar os erros utilizando o Ambiente de Trabalho Remoto:

1. Certifique-se de que o Azure SDK 1.3 ou posteriormente está instalado.
2. Durante a implementação da solução utilizando o Visual Studio, ative o Remote Desktop. Para obter mais informações, consulte [Enable Remote Desktop Connection for a Role in Azure Cloud Services using Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).
3. No portal Microsoft Azure, uma vez que o caso mostra um estado de **Ready**, remotamente no caso. Para obter mais informações sobre a utilização do ambiente de trabalho remoto com [serviços cloud, consulte Remote em instâncias de função](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Inscreva-se na máquina virtual utilizando as credenciais especificadas durante a configuração do Ambiente de Trabalho Remoto.
6. Abra uma janela de comando.
7. Escreva `IPconfig`.
8. Note o valor do endereço IPV4.
9. Abra o Internet Explorer.
10. Digite o endereço e o nome da aplicação web. Por exemplo, `http://<IPV4 Address>/default.aspx`.

Navegar no site irá agora devolver mensagens de erro mais explícitas:

* Erro do servidor na aplicação '/' .
* Descrição: Ocorreu uma exceção não manipulada durante a execução do pedido web atual. Por favor, reveja o rastreio da pilha para obter mais informações sobre o erro e de onde foi originado no código.
* Detalhes da exceção: System.IO.FIleNotFoundException: Não foi possível carregar ficheiro ou montagem 'Microsoft.WindowsAzure.StorageClient, Versão=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35' ou uma das suas dependências. O sistema não consegue encontrar o ficheiro especificado.

Por exemplo:

![Erro explícito do servidor na aplicação '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnosticar problemas usando o emulador computacional
Pode utilizar o emulador de computação Microsoft Azure para diagnosticar e resolver problemas de dependências em falta e erros de web.config.

Para obter os melhores resultados na utilização deste método de diagnóstico, deve utilizar um computador ou máquina virtual que tenha uma instalação limpa do Windows. Para melhor simular o ambiente Azure, utilize o Windows Server 2008 R2 x64.

1. Instale a versão autónoma do [Azure SDK](https://azure.microsoft.com/downloads/).
2. Na máquina de desenvolvimento, construa o projeto de serviço em nuvem.
3. No Windows Explorer, navegue para a pasta bin\debug do projeto de serviço de nuvem.
4. Copie a pasta .csx e o ficheiro .cscfg para o computador que está a usar para depurar os problemas.
5. Na máquina limpa, abra uma janela e tipo de aviso de comando Azure SDK `csrun.exe /devstore:start` .
6. Na solicitação de comando, escreva `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser` .
7. Quando a função começar, verá informações detalhadas de erro no Internet Explorer. Também pode utilizar ferramentas padrão de resolução de problemas do Windows para diagnosticar o problema.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnosticar problemas usando IntelliTrace
Para funções de trabalhadores e web que utilizem .NET Framework 4, pode utilizar [o IntelliTrace,](/visualstudio/debugger/intellitrace)que está disponível na Microsoft Visual Studio Enterprise.

Siga estes passos para implementar o serviço com o IntelliTrace ativado:

1. Confirme se o Azure SDK 1.3 ou posteriormente está instalado.
2. Implemente a solução utilizando o Visual Studio. Durante a implementação, verifique a caixa de verificação **enable IntelliTrace para .NET 4.**
3. Assim que a instância começar, abra o Explorador do **Servidor**.
4. Expanda o nó **Azure \\ Cloud Services** e localize a implantação.
5. Expanda a implementação até ver os exemplos de papéis. Clique com o direito numa das ocorrências.
6. Escolha **ver os registos IntelliTrace**. O **Resumo IntelliTrace** será aberto.
7. Localize a secção de exceções do resumo. Se houver exceções, a secção será rotulada como **Dados de Exceção**.
8. Expandir os **dados de exceção** e procurar por erros **system.iO.FileNotFoundException** semelhantes aos seguintes:

![Dados de exceção, ficheiro em falta ou montagem](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Endereço desaparecido DLLs e conjuntos
Para resolver os erros de DLL e de montagem em falta, siga estes passos:

1. Abra a solução no Visual Studio.
2. No **Solution Explorer,** abra a pasta **Referências.**
3. Clique no conjunto identificado no erro.
4. No painel **propriedades,** localize **a propriedade Copy Local** e desaceia o valor para **True**.
5. Reimplantar o serviço de nuvem.

Uma vez verificado que todos os erros foram corrigidos, pode implementar o serviço sem verificar a caixa de verificação **de funções Enable IntelliTrace para .NET 4.**

## <a name="next-steps"></a>Passos seguintes
Veja mais [artigos de resolução de problemas](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) para serviços na nuvem.

Para aprender a resolver problemas de problemas de funções de serviço em nuvem utilizando dados de diagnóstico de computador Azure PaaS, consulte a [série de blogs de Kevin Williamson](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
