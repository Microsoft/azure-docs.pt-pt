---
title: Solucionando problemas de status degradado no Gerenciador de tráfego do Azure
description: Como solucionar problemas de perfis do Gerenciador de tráfego quando ele aparece como status degradado.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: dcscontentpm
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: rohink
ms.openlocfilehash: 8f043b11c9319d61c4413d01b008b324103ca6c3
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155207"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Solucionando problemas de estado degradado no Gerenciador de tráfego do Azure

Este artigo descreve como solucionar problemas de um perfil do Gerenciador de tráfego do Azure que está mostrando um status degradado. Como uma primeira etapa na solução de problemas de um estado degradado do Gerenciador de tráfego do Azure é habilitar o log de diagnóstico.  Consulte [Habilitar logs de diagnóstico](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-diagnostic-logs) para obter mais informações. Para este cenário, considere que você configurou um perfil do Gerenciador de tráfego apontando para alguns dos seus serviços hospedados do cloudapp.net. Se a integridade do seu Gerenciador de tráfego exibir um status **degradado** , o status de um ou mais pontos de extremidade poderá ser **degradado**:

![status de ponto de extremidade degradado](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Se a integridade do seu Gerenciador de tráfego exibir um status **inativo** , ambos os pontos de extremidade poderão ser **desabilitados**:

![Status do Gerenciador de tráfego inativo](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Entendendo as investigações do Gerenciador de tráfego

* O Gerenciador de tráfego considera um ponto de extremidade como ONLINE somente quando a investigação recebe uma resposta HTTP 200 do caminho de investigação. Se seu aplicativo retornar qualquer outro código de resposta HTTP, você deverá adicionar esse código de resposta aos [intervalos de código de status esperados](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) do seu perfil do Gerenciador de tráfego.
* Uma resposta de redirecionamento de 30 vezes é tratada como falha, a menos que você tenha especificado isso como um código de resposta válido em [intervalos de códigos de status esperados](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) do seu perfil do Gerenciador de tráfego. O Gerenciador de tráfego não investiga o destino de redirecionamento.
* Para investigações HTTPs, os erros de certificado são ignorados.
* O conteúdo real do caminho de investigação não importa, desde que um 200 seja retornado. Investigar uma URL para algum conteúdo estático como "/favicon.ico" é uma técnica comum. O conteúdo dinâmico, como as páginas ASP, nem sempre pode retornar 200, mesmo quando o aplicativo está íntegro.
* Uma prática recomendada é definir o caminho de investigação para algo que tenha lógica suficiente para determinar se o site está ativo ou inativo. No exemplo anterior, ao definir o caminho para "/favicon.ico", você está testando apenas que w3wp. exe está respondendo. Essa investigação não pode indicar que seu aplicativo Web está íntegro. Uma opção melhor seria definir um caminho para algo como "/Probe.aspx" que tem lógica para determinar a integridade do site. Por exemplo, você pode usar contadores de desempenho para utilização da CPU ou medir o número de solicitações com falha. Ou você pode tentar acessar os recursos do banco de dados ou o estado da sessão para certificar-se de que o aplicativo Web está funcionando.
* Se todos os pontos de extremidade em um perfil estiverem degradados, o Gerenciador de tráfego tratará todos os pontos de extremidade como íntegros e roteará o tráfego para todos os pontos de extremidade. Esse comportamento garante que os problemas com o mecanismo de investigação não resultem em uma interrupção completa do seu serviço.

## <a name="troubleshooting"></a>Resolução de problemas

Para solucionar uma falha de investigação, você precisa de uma ferramenta que mostra o retorno do código de status HTTP da URL de investigação. Há muitas ferramentas disponíveis que mostram a resposta HTTP bruta.

* [Fiddler](https://www.telerik.com/fiddler)
* [curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Além disso, você pode usar a guia rede das ferramentas de depuração F12 no Internet Explorer para exibir as respostas HTTP.

Para este exemplo, queremos ver a resposta de nossa URL de investigação: http:\//watestsdp2008r2.cloudapp.net:80/Probe. O exemplo do PowerShell a seguir ilustra o problema.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Exemplo de saída:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Observe que recebemos uma resposta de redirecionamento. Como mencionado anteriormente, qualquer StatusCode diferente de 200 é considerado uma falha. O Gerenciador de tráfego altera o status do ponto de extremidade para offline. Para resolver o problema, verifique a configuração do site para garantir que o StatusCode apropriado possa ser retornado do caminho de investigação. Reconfigure a investigação do Traffic Manager para apontar para um caminho que retorna um 200.

Se sua investigação estiver usando o protocolo HTTPS, talvez seja necessário desabilitar a verificação de certificado para evitar erros de SSL/TLS durante o teste. As instruções do PowerShell a seguir desabilitam a validação de certificado para a sessão atual do PowerShell:

```powershell
add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
    ServicePoint srvPoint, X509Certificate certificate,
    WebRequest request, int certificateProblem) {
    return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Próximos Passos

[Sobre métodos de roteamento de tráfego do Traffic Manager](traffic-manager-routing-methods.md)

[O que é o Gerenciador de tráfego](traffic-manager-overview.md)

[Serviços Cloud](https://go.microsoft.com/fwlink/?LinkId=314074)

[Serviço de Aplicações do Azure](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operações do Gestor de Tráfego (Referência da API REST)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets do Gerenciador de tráfego do Azure][1]

[1]: https://docs.microsoft.com/powershell/module/az.trafficmanager
