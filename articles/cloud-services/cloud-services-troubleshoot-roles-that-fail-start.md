---
title: Solucionar problemas de funções que falham ao iniciar | Microsoft Docs
description: Aqui estão alguns motivos comuns pelos quais uma função de serviço de nuvem pode falhar ao iniciar. Também são fornecidas soluções para esses problemas.
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
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122734"
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Solucionar problemas de funções de serviço de nuvem que falham ao iniciar
Aqui estão alguns problemas comuns e soluções relacionadas a funções de serviços de nuvem do Azure que falham ao iniciar.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>DLLs ou dependências ausentes
Funções e funções sem resposta que são alternadas entre os Estados **inicializando**, **ocupado**e **parando** podem ser causadas por DLLs ou assemblies ausentes.

Os sintomas de DLLs ou assemblies ausentes podem ser:

* Sua instância de função está alternando os Estados **inicializando**, **ocupado**e **parando** .
* Sua instância de função foi movida para **pronto** , mas se você navegar para seu aplicativo Web, a página não será exibida.

Há vários métodos recomendados para investigar esses problemas.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnosticar problemas de DLL ausentes em uma função Web
Quando você navega para um site que é implantado em uma função Web e o navegador exibe um erro de servidor semelhante ao seguinte, isso pode indicar que uma DLL está ausente.

![Erro de servidor no aplicativo '/'.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnosticar problemas desativando erros personalizados
Informações de erro mais completas podem ser exibidas Configurando o Web. config para a função Web a fim de definir o modo de erro personalizado como desativado e reimplantar o serviço.

Para exibir erros mais completos sem usar Área de Trabalho Remota:

1. Abra a solução no Microsoft Visual Studio.
2. No **Gerenciador de soluções**, localize o arquivo Web. config e abra-o.
3. No arquivo Web. config, localize a seção System. Web e adicione a seguinte linha:

    ```xml
    <customErrors mode="Off" />
    ```
4. Guarde o ficheiro.
5. Reempacotar e reimplantar o serviço.

Depois que o serviço for reimplantado, você verá uma mensagem de erro com o nome do assembly ou DLL ausente.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnosticar problemas exibindo o erro remotamente
Você pode usar Área de Trabalho Remota para acessar a função e exibir informações de erro mais completas remotamente. Use as etapas a seguir para exibir os erros usando Área de Trabalho Remota:

1. Verifique se o SDK do Azure 1,3 ou posterior está instalado.
2. Durante a implantação da solução usando o Visual Studio, habilite Área de Trabalho Remota. Para obter mais informações, consulte [habilitar conexão de área de trabalho remota para uma função nos serviços de nuvem do Azure usando o Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).
3. No portal do Microsoft Azure, uma vez que a instância mostra um status de **pronto**, remoto na instância. Para obter mais informações sobre como usar a área de trabalho remota com serviços de nuvem, consulte [remoto em instâncias de função](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Entre na máquina virtual usando as credenciais que foram especificadas durante a configuração de Área de Trabalho Remota.
6. Abra uma janela de comandos.
7. Digite `IPconfig`.
8. Observe o valor do endereço IPV4.
9. Abra o Internet Explorer.
10. Digite o endereço e o nome do aplicativo Web. Por exemplo, `http://<IPV4 Address>/default.aspx`.

Navegar até o site agora retornará mensagens de erro mais explícitas:

* Erro de servidor no aplicativo '/'.
* Descrição: Ocorreu uma exceção sem tratamento durante a execução da solicitação da Web atual. Examine o rastreamento de pilha para obter mais informações sobre o erro e onde ele foi originado no código.
* Detalhes da exceção: System.IO.FIleNotFoundException: Não foi possível carregar o arquivo ou assembly ' Microsoft. WindowsAzure. StorageClient, Version = 1.1.0.0, Culture = neutral, PublicKeyToken = 31bf856ad364e35 ' ou uma de suas dependências. O sistema não pode localizar o arquivo especificado.

Por exemplo:

![Erro de servidor explícito no aplicativo '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnosticar problemas usando o emulador de computação
Você pode usar o emulador de computação Microsoft Azure para diagnosticar e solucionar problemas de dependências ausentes e erros de Web. config.

Para obter melhores resultados ao usar esse método de diagnóstico, você deve usar um computador ou uma máquina virtual que tenha uma instalação limpa do Windows. Para simular melhor o ambiente do Azure, use o Windows Server 2008 R2 x64.

1. Instale a versão autônoma do [SDK do Azure](https://azure.microsoft.com/downloads/).
2. No computador de desenvolvimento, compile o projeto de serviço de nuvem.
3. No Windows Explorer, navegue até a pasta bin\debug do projeto de serviço de nuvem.
4. Copie a pasta. CSX e o arquivo. cscfg para o computador que você está usando para depurar os problemas.
5. No computador limpo, abra uma janela do prompt de comando do SDK do `csrun.exe /devstore:start`Azure e digite.
6. No prompt de comando, digite `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.
7. Quando a função for iniciada, você verá informações detalhadas sobre o erro no Internet Explorer. Você também pode usar ferramentas padrão de solução de problemas do Windows para diagnosticar o problema.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnosticar problemas usando o IntelliTrace
Para funções de trabalho e Web que usam o .NET Framework 4, você pode usar o [IntelliTrace](/visualstudio/debugger/intellitrace), que está disponível no Microsoft Visual Studio Enterprise.

Siga estas etapas para implantar o serviço com o IntelliTrace habilitado:

1. Confirme se o SDK do Azure 1,3 ou posterior está instalado.
2. Implante a solução usando o Visual Studio. Durante a implantação, marque a caixa de seleção **Habilitar IntelliTrace para funções do .NET 4** .
3. Depois que a instância for iniciada, abra o **Gerenciador de servidores**.
4. Expanda o nó **serviços de\\nuvem do Azure** e localize a implantação.
5. Expanda a implantação até ver as instâncias de função. Clique com o botão direito do mouse em uma das instâncias.
6. Escolha **Exibir logs do IntelliTrace**. O **Resumo do IntelliTrace** será aberto.
7. Localize a seção de exceções do resumo. Se houver exceções, a seção será rotulada como **dados de exceção**.
8. Expanda os **dados de exceção** e procure erros **System. IO. FileNotFoundException** semelhantes ao seguinte:

![Dados de exceção, arquivo ausente ou assembly](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Endereçar DLLs e assemblies ausentes
Para resolver erros de DLL e assembly ausentes, siga estas etapas:

1. Abra a solução no Visual Studio.
2. Em **Gerenciador de soluções**, abra a pasta **referências** .
3. Clique no assembly identificado no erro.
4. No painel **Propriedades** , localize a **Propriedade Copy local** e defina o valor como **true**.
5. Reimplante o serviço de nuvem.

Depois de verificar se todos os erros foram corrigidos, você pode implantar o serviço sem marcar a caixa de seleção **Habilitar IntelliTrace para funções do .NET 4** .

## <a name="next-steps"></a>Passos seguintes
Veja mais [artigos de solução de problemas](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) para serviços de nuvem.

Para saber como solucionar problemas de função de serviço de nuvem usando dados de diagnóstico de computador de PaaS do Azure, consulte a [série de Blogs de Kevin Williamson](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
