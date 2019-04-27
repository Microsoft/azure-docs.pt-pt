---
title: Causas comuns da Reciclagem de funções do serviço em nuvem | Documentos da Microsoft
description: Uma função de serviço na nuvem que, de repente, recicla pode provocar períodos de indisponibilidade significativos. Seguem-se alguns problemas comuns que fazem com que funções ser reciclado, que podem ajudar a reduzir o tempo de inatividade.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 533930d1-8035-4402-b16a-cf887b2c4f85
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 2a9214b918883e493ebe5c93fc7f56e7ce9c77ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60652218"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Problemas comuns que fazem com que as funções reciclem
Este artigo discute algumas das causas comuns dos problemas de implementação e fornece sugestões de resolução de problemas para ajudar a resolver esses problemas. Uma indicação de que existe um problema com um aplicativo é quando a instância de função não consegue iniciar ou ciclos de entre os Estados de inicializar, ocupados e interromper.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Dependências de tempo de execução em falta
Se uma função na sua aplicação baseia-se em qualquer biblioteca gerida do assembly que não faz parte do .NET Framework ou do Azure, tem de incluir explicitamente esse assembly no pacote de aplicação. Tenha em atenção que outras estruturas da Microsoft não estão disponíveis no Azure por predefinição. Se a sua função se baseia numa estrutura desse tipo, tem de adicionar esses assemblies ao pacote de aplicações.

Antes de criar e empacotar a sua aplicação, verifique o seguinte:

* Se utilizar o Visual studio, certifique-se de que o **Copy Local** estiver definida como **verdadeiro** para cada assembly referenciado no seu projeto que não faz parte do SDK do Azure ou o .NET Framework.
* Certifique-se de que o ficheiro Web. config não referencia qualquer Assembly não utilizados no elemento de compilação.
* O **Build Action** de cada. cshtml arquivo está definido como **conteúdo**. Isto garante que os ficheiros será apresentado corretamente no pacote e permite que outros arquivos referenciados a aparecer no pacote.

## <a name="assembly-targets-wrong-platform"></a>Plataforma de errado de destinos de assemblagem
O Azure é um ambiente de 64 bits. Por conseguinte, os assemblies do .NET compiladas para um destino de 32 bits não funcionarão no Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Função emite exceções não processadas ao inicializar ou parar
Quaisquer exceções que são geradas pelos métodos dos [RoleEntryPoint] classe, que inclui a [OnStart], [OnStop], e [executar] os métodos, são exceções sem tratamento. Se ocorrer uma exceção não processada em um dos seguintes métodos, haverá reciclagem de função. Se a função está a reciclar repetidamente, pode gerar uma exceção não processada sempre que tenta iniciar.

## <a name="role-returns-from-run-method"></a>A função é devolvida do método Run
O [executar] método destina-se para executar indefinidamente. Se o seu código substitui o [executar] método, ele deve suspensão indefinidamente. Se o [executar] método retorna, a função reciclar.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Definição de DiagnosticsConnectionString incorreta
Se o aplicativo usa diagnóstico do Azure, o ficheiro de configuração de serviço tem de especificar o `DiagnosticsConnectionString` definição de configuração. Esta definição deve especificar uma ligação HTTPS para a sua conta de armazenamento no Azure.

Para garantir que seu `DiagnosticsConnectionString` definição está correta antes de implementar o pacote de aplicação para o Azure, verifique o seguinte:  

* O `DiagnosticsConnectionString` na definição de pontos de uma conta de armazenamento válida no Azure.  
  Por predefinição, esta definição aponta para a conta de armazenamento emulado, para que explicitamente tem de alterar esta definição antes de implementar o pacote de aplicação. Se não altere esta definição, é emitida uma exceção quando a instância de função tenta iniciar o monitor de diagnóstico. Isso pode fazer com que a instância de função reciclar indefinidamente.
* A cadeia de ligação é especificada a seguir [formato](../storage/common/storage-configure-connection-string.md). (O protocolo tem de ser especificado como HTTPS.) Substitua *MyAccountName* com o nome da sua conta de armazenamento, e *MyAccountKey* com a sua chave de acesso:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Se estiver a desenvolver seu aplicativo, utilizando ferramentas do Azure para Microsoft Visual Studio, pode utilizar as páginas de propriedades para definir este valor.

## <a name="exported-certificate-does-not-include-private-key"></a>Certificado exportado não inclui a chave privada
Para executar uma função da web em SSL, tem de se certificar de que o seu certificado de gestão exportado inclui a chave privada. Se utilizar o *Gestor de certificados do Windows* para exportar o certificado, certifique-se de que selecionar **Sim** para o **exportar a chave privada** opção. O certificado tem de ser exportado no formato PFX, que é o único formato suportado atualmente.

## <a name="next-steps"></a>Passos Seguintes
Ver mais [artigos de resolução de problemas](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) para serviços em nuvem.

Ver a função mais cenários em de reciclagem [série de blogue de Kevin Williamson](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Executar]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
