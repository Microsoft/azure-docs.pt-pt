---
title: Causas comuns de reciclagem de funções de serviço de nuvem | Microsoft Docs
description: Uma função de serviço de nuvem que recicla repentinamente pode causar um tempo de inatividade significativo. Aqui estão alguns problemas comuns que fazem com que as funções sejam recicladas, o que pode ajudá-lo a reduzir o tempo de inatividade.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 533930d1-8035-4402-b16a-cf887b2c4f85
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 554508b1bf784e306cd12a4a601f908e06320933
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154983"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Problemas comuns que fazem com que as funções reciclem
Este artigo aborda algumas das causas comuns de problemas de implantação e fornece dicas de solução de problemas para ajudá-lo a resolver esses problemas. Uma indicação de que um problema existe com um aplicativo é quando a instância de função não é iniciada ou é alternada entre os Estados inicializando, ocupado e parando.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Dependências de tempo de execução ausentes
Se uma função em seu aplicativo depender de qualquer assembly que não faça parte do .NET Framework ou da biblioteca gerenciada do Azure, você deverá incluir explicitamente esse assembly no pacote de aplicativos. Tenha em mente que outras estruturas da Microsoft não estão disponíveis no Azure por padrão. Se sua função depender de tal estrutura, você deverá adicionar esses assemblies ao pacote de aplicativos.

Antes de criar e empacotar seu aplicativo, verifique o seguinte:

* Se estiver usando o Visual Studio, verifique se a propriedade **Copy local** está definida como **true** para cada assembly referenciado em seu projeto que não faz parte do SDK do Azure ou o .NET Framework.
* Verifique se o arquivo Web. config não faz referência a nenhum assembly não utilizado no elemento Compilation.
* A **ação de compilação** de cada arquivo. cshtml é definida como **Content**. Isso garante que os arquivos serão exibidos corretamente no pacote e permite que outros arquivos referenciados apareçam no pacote.

## <a name="assembly-targets-wrong-platform"></a>O assembly tem como destino uma plataforma incorreta
O Azure é um ambiente de 64 bits. Portanto, os assemblies .NET compilados para um destino de 32 bits não funcionarão no Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>A função gera exceções sem tratamento durante a inicialização ou a interrupção
Todas as exceções geradas pelos métodos da classe [RoleEntryPoint] , que inclui os métodos [OnStart], [OnStop]e [Executar] , são exceções sem tratamento. Se ocorrer uma exceção sem tratamento em um desses métodos, a função será reciclada. Se a função for reciclando repetidamente, ela poderá estar lançando uma exceção sem tratamento toda vez que tentar iniciar.

## <a name="role-returns-from-run-method"></a>A função retorna do método Run
O método [Executar] deve ser executado indefinidamente. Se o seu código substituir o método [Executar] , ele deverá dormir indefinidamente. Se o método [Executar] retornar, a função é reciclada.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Configuração incorreta de DiagnosticsConnectionString
Se o aplicativo usar diagnóstico do Azure, o arquivo de configuração de serviço `DiagnosticsConnectionString` deverá especificar a definição de configuração. Essa configuração deve especificar uma conexão HTTPS para sua conta de armazenamento no Azure.

Para garantir que sua `DiagnosticsConnectionString` configuração esteja correta antes de implantar o pacote de aplicativos no Azure, verifique o seguinte:  

* A `DiagnosticsConnectionString` configuração aponta para uma conta de armazenamento válida no Azure.  
  Por padrão, essa configuração aponta para a conta de armazenamento emulada, portanto, você deve alterar explicitamente essa configuração antes de implantar o pacote de aplicativos. Se você não alterar essa configuração, uma exceção será lançada quando a instância de função tentar iniciar o monitor de diagnóstico. Isso pode fazer com que a instância de função seja reciclada indefinidamente.
* A cadeia de conexão é especificada no [formato](../storage/common/storage-configure-connection-string.md)a seguir. (O protocolo deve ser especificado como HTTPS.) Substitua *AccountName* pelo nome da sua conta de armazenamento e *myAccountKey* pela sua chave de acesso:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Se você estiver desenvolvendo seu aplicativo usando as ferramentas do Azure para Microsoft Visual Studio, poderá usar as páginas de propriedades para definir esse valor.

## <a name="exported-certificate-does-not-include-private-key"></a>O certificado exportado não inclui a chave privada
Para executar uma função Web em SSL, você deve garantir que o certificado de gerenciamento exportado inclua a chave privada. Se você usar o *Gerenciador de certificados do Windows* para exportar o certificado, certifique-se de selecionar **Sim** para a opção **exportar a chave privada** . O certificado deve ser exportado no formato PFX, que é o único formato com suporte no momento.

## <a name="next-steps"></a>Passos seguintes
Veja mais [artigos de solução de problemas](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) para serviços de nuvem.

Veja mais cenários de reciclagem de função na [série de Blogs de Kevin Williamson](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Executar]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
