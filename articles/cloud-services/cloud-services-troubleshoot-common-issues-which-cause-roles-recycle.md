---
title: Causas comuns da reciclagem de funções do Serviço de Nuvem Microsoft Docs
description: Um papel de serviço na nuvem que recicla repentinamente pode causar um tempo de inatividade significativo. Aqui estão algumas questões comuns que fazem com que os papéis sejam reciclados, o que pode ajudá-lo a reduzir o tempo de inatividade.
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
ms.openlocfilehash: 0484eb919a9de11b64dcc3334c5a9a942d875ca6
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92075132"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Problemas comuns que fazem com que as funções reciclem
Este artigo discute algumas das causas comuns dos problemas de implantação e fornece dicas de resolução de problemas para ajudá-lo a resolver estes problemas. Uma indicação de que existe um problema com uma aplicação é quando a instância de função não começa, ou ciclos entre os estados de inicialização, ocupados e de paragem.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Dependências de tempo em falta
Se um papel na sua candidatura depender de qualquer montagem que não faça parte do Quadro .NET ou da biblioteca gerida pelo Azure, deve incluir explicitamente essa montagem no pacote de candidaturas. Tenha em mente que outros quadros da Microsoft não estão disponíveis no Azure por padrão. Se o seu papel depender de tal enquadramento, deve adicionar esses conjuntos ao pacote de candidaturas.

Antes de construir e embalar a sua aplicação, verifique o seguinte:

* Se utilizar o estúdio Visual, certifique-se de que a propriedade **Copy Local** está definida para **True** para cada conjunto referenciado no seu projeto que não faz parte do Azure SDK ou do Quadro .NET.
* Certifique-se de que o ficheiro web.config não faz referência a conjuntos não reutilizados no elemento de compilação.
* A **Build Action** de cada ficheiro .cshtml está definida como **Conteúdo**. Isto garante que os ficheiros aparecerão corretamente na embalagem e permite que outros ficheiros referenciados apareçam na embalagem.

## <a name="assembly-targets-wrong-platform"></a>Montagem visa plataforma errada
Azure é um ambiente de 64 bits. Portanto, os conjuntos .NET compilados para um alvo de 32 bits não funcionarão no Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Role lança exceções não manipuladas ao rubricar ou parar
Quaisquer exceções que sejam lançadas pelos métodos da classe [RoleEntryPoint,] que inclui os métodos [OnStart,] [OnStop]e [Run,] são exceções sem manipulação. Se ocorrer uma exceção não manipulada num destes métodos, o papel reciclará. Se a função for reciclada repetidamente, pode estar a lançar uma exceção desafortaçada cada vez que tenta arrancar.

## <a name="role-returns-from-run-method"></a>Retornos de função do método Run
O método [Executar] destina-se a funcionar indefinidamente. Se o seu código substituir o método [Executar,] deve dormir indefinidamente. Se o método [Executar] voltar, a função recicla.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Definição incorreta de DiagnósticoConnectionString
Se a aplicação utilizar o Azure Diagnostics, o seu ficheiro de configuração de serviço deve especificar a definição de `DiagnosticsConnectionString` configuração. Esta definição deve especificar uma ligação HTTPS à sua conta de armazenamento em Azure.

Para garantir que a `DiagnosticsConnectionString` sua definição está correta antes de colocar o seu pacote de candidaturas no Azure, verifique o seguinte:  

* A `DiagnosticsConnectionString` definição aponta para uma conta de armazenamento válida em Azure.  
  Por predefinição, esta definição aponta para a conta de armazenamento emulsionada, pelo que tem de alterar explicitamente esta definição antes de implementar o seu pacote de aplicações. Se não alterar esta definição, é lançada uma exceção quando a instância de função tenta iniciar o monitor de diagnóstico. Isto pode fazer com que a instância de função recicle indefinidamente.
* A cadeia de ligação é especificada no seguinte [formato](../storage/common/storage-configure-connection-string.md). (O protocolo deve ser especificado como HTTPS.) Substitua *o MyAccountName* pelo nome da sua conta de armazenamento e *myAccountKey* com a sua chave de acesso:    

```console
DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey
```

  Se estiver a desenvolver a sua aplicação utilizando ferramentas Azure para o Microsoft Visual Studio, pode utilizar as páginas da propriedade para definir este valor.

## <a name="exported-certificate-does-not-include-private-key"></a>O certificado exportado não inclui chave privada
Para desempenhar uma função web no âmbito do TLS, deve certificar-se de que o seu certificado de gestão exportado inclui a chave privada. Se utilizar o *Gestor de Certificados* do Windows para exportar o certificado, certifique-se de selecionar **Sim** para **exportar a opção chave privada.** O certificado deve ser exportado no formato PFX, que é o único formato atualmente suportado.

## <a name="next-steps"></a>Passos seguintes
Veja mais [artigos de resolução de problemas](../index.yml?product=cloud-services&tag=top-support-issue) para serviços na nuvem.

Veja mais cenários de reciclagem de papéis na [série de blogs de Kevin Williamson.](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)

[RoleEntryPoint]: /previous-versions/azure/reference/ee758619(v=azure.100)
[OnStart]: /previous-versions/azure/reference/ee772851(v=azure.100)
[OnStop]: /previous-versions/azure/reference/ee772844(v=azure.100)
[Executar]: /previous-versions/azure/reference/ee772746(v=azure.100)