---
title: Causas comuns das funções de serviço na nuvem reciclagem de papéis de reciclagem Microsoft Docs
description: Uma função de serviço na nuvem que de repente recicla pode causar tempo de inatividade significativo. Aqui estão algumas questões comuns que fazem com que as funções sejam recicladas, o que pode ajudá-lo a reduzir o tempo de inatividade.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71154983"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Problemas comuns que fazem com que as funções reciclem
Este artigo discute algumas das causas comuns dos problemas de implantação e fornece dicas de resolução de problemas para ajudá-lo a resolver estes problemas. Uma indicação de que existe um problema com uma aplicação é quando a instância de funções não começa, ou se circula entre os estados inicializadores, ocupados e de paragem.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Dependências de tempo em falta
Se uma função na sua aplicação depender de qualquer conjunto que não faça parte do .NET Framework ou da biblioteca gerida pelo Azure, deve incluir explicitamente esse conjunto no pacote de candidatura. Tenha em mente que outros quadros da Microsoft não estão disponíveis no Azure por padrão. Se o seu papel se basear em tal enquadramento, deve adicionar esses conjuntos ao pacote de candidatura.

Antes de construir e embalar a sua aplicação, verifique o seguinte:

* Se utilizar o estúdio Visual, certifique-se de que a propriedade **Copy Local** está definida para **True** para cada conjunto referenciado no seu projeto que não faz parte do Azure SDK ou do .NET Framework.
* Certifique-se de que o ficheiro web.config não faz referência a quaisquer conjuntos não utilizados no elemento de compilação.
* A **Ação de Construção** de cada ficheiro .cshtml está definida para **Conteúdo**. Isto garante que os ficheiros aparecerão corretamente na embalagem e permite que outros ficheiros referenciados apareçam na embalagem.

## <a name="assembly-targets-wrong-platform"></a>Plataforma de montagem alvos errados
Azure é um ambiente de 64 bits. Portanto, os conjuntos .NET compilados para um alvo de 32 bits não funcionarão no Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>O papel lança exceções não manipuladas ao mesmo tempo que inicializa ou para
Quaisquer exceções que sejam lançadas pelos métodos da classe [RoleEntryPoint,] que inclui os métodos [OnStart], [OnStop]e [Run,] são exceções não tratadas. Se ocorrer uma exceção não tratada num destes métodos, o papel reciclará. Se o papel for reciclar repetidamente, pode ser uma exceção não tratada cada vez que tenta começar.

## <a name="role-returns-from-run-method"></a>Retornos de funções do método Run
O método [Executar] destina-se a funcionar indefinidamente. Se o seu código se sobrepor ao método [Executar,] deve dormir indefinidamente. Se o método [Executar] voltar, o papel recicla.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Definição de DiagnosticsConnectionString incorreta
Se a aplicação utilizar o Azure Diagnostics, o ficheiro de configuração do seu serviço deve especificar a `DiagnosticsConnectionString` definição de configuração. Esta definição deve especificar uma ligação HTTPS à sua conta de armazenamento no Azure.

Para garantir `DiagnosticsConnectionString` que a sua definição está correta antes de enviar o seu pacote de aplicação para o Azure, verifique o seguinte:  

* A `DiagnosticsConnectionString` definição aponta para uma conta de armazenamento válida em Azure.  
  Por predefinição, esta definição aponta para a conta de armazenamento emulada, pelo que deve alterar explicitamente esta definição antes de implementar o seu pacote de aplicação. Se não alterar esta definição, abre-se uma exceção quando a instância de funções tenta iniciar o monitor de diagnóstico. Isto pode fazer com que a instância de funções recicle indefinidamente.
* A cadeia de ligação é especificada no [seguinte formato](../storage/common/storage-configure-connection-string.md). (O protocolo deve ser especificado como HTTPS.) Substitua o *MyAccountName* pelo nome da sua conta de armazenamento e *o MyAccountKey* pela sua chave de acesso:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Se estiver a desenvolver a sua aplicação utilizando ferramentas Azure para o Microsoft Visual Studio, pode utilizar as páginas de propriedade para definir este valor.

## <a name="exported-certificate-does-not-include-private-key"></a>Certificado exportado não inclui chave privada
Para desempenhar uma função web no âmbito do SSL, deve garantir que o seu certificado de gestão exportado inclui a chave privada. Se utilizar o *Gestor de Certificados do Windows* para exportar o certificado, **certifique-se** de selecionar Sim para a Exportação a opção **chave privada.** O certificado deve ser exportado no formato PFX, que é o único formato atualmente suportado.

## <a name="next-steps"></a>Passos seguintes
Veja mais artigos de [resolução de problemas](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) para serviços na nuvem.

Veja mais cenários de reciclagem de papéis na [série de blogs de Kevin Williamson.](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[Início]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Executar]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
