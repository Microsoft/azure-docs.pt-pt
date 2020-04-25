---
title: Situação de resolução de problemas degradou-se no Gestor de Tráfego do Azure
description: Como resolver os perfis do Traffic Manager quando mostra o estado degradado.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: rohink
ms.openlocfilehash: 6d720067b619b0d871899f2ac9025a9d8ab24d95
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82130756"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Resolução de problemas do estado degradado no Gestor de Tráfego do Azure

Este artigo descreve como resolver um perfil do Gestor de Tráfego Azure que mostra um estado degradado. Como primeiro passo na resolução de problemas, um Estado dedegradado do Gestor de Tráfego Azure é permitir a exploração madeireira.  Consulte os [registos](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-diagnostic-logs) de recursos enable para obter mais informações. Para este cenário, considere que configura um perfil de Gestor de Tráfego apontando para alguns dos seus serviços cloudapp.net hospedados. Se a saúde do seu Gestor de Tráfego apresentar um estado **degradado,** então o estado de um ou mais pontos finais pode ser **degradado:**

![estatuto de ponto final degradado](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Se a saúde do seu Gestor de Tráfego apresentar um estado **inativo,** então ambos os pontos finais podem ser **desativados:**

![Estado do Gestor de Tráfego Inativo](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Compreender as sondas do Gestor de Tráfego

* O Traffic Manager considera que um ponto final só está online quando a sonda recebe uma resposta HTTP 200 de volta do caminho da sonda. Se a aplicação devolver qualquer outro código de resposta HTTP, deve adicionar esse código de resposta aos intervalos de código de [estado esperados](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) do seu perfil de Gestor de Tráfego.
* Uma resposta de redirecionamento de 30x é tratada como falha, a menos que tenha especificado isto como um código de resposta válido nas gamas de [código de estado esperados](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) do seu perfil de Gestor de Tráfego. O Gestor de Tráfego não sonda o alvo de redirecionamento.
* Para as sondas HTTPs, os erros de certificado são ignorados.
* O conteúdo real do caminho da sonda não importa, desde que um 200 seja devolvido. Sondar um URL a algum conteúdo estático como "/favicon.ico" é uma técnica comum. O conteúdo dinâmico, tal como as páginas ASP, nem sempre pode devolver 200, mesmo quando a aplicação é saudável.
* Uma boa prática é definir o caminho da sonda para algo que tem lógica suficiente para determinar que o site está para cima ou para baixo. No exemplo anterior, definindo o caminho para "/favicon.ico", está apenas a testar que w3wp.exe está a responder. Esta sonda pode não indicar que a sua aplicação web é saudável. Uma melhor opção seria definir um caminho para algo como "/Probe.aspx" que tem lógica para determinar a saúde do site. Por exemplo, pode utilizar contadores de desempenho para a utilização do CPU ou medir o número de pedidos falhados. Ou pode tentar aceder aos recursos da base de dados ou ao estado de sessão para se certificar de que a aplicação web está a funcionar.
* Se todos os pontos finais de um perfil estiverem degradados, então o Traffic Manager trata todos os pontos finais como saudáveis e encaminha o tráfego para todos os pontos finais. Este comportamento garante que os problemas com o mecanismo de sondagem não resultam numa interrupção completa do seu serviço.

## <a name="troubleshooting"></a>Resolução de problemas

Para resolver uma falha na sonda, precisa de uma ferramenta que mostre o retorno do código de estado HTTP do URL da sonda. Existem muitas ferramentas disponíveis que mostram a resposta http crua.

* [Fiddler](https://www.telerik.com/fiddler)
* [caracol](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Além disso, pode utilizar o separador Rede das Ferramentas de Depuração F12 no Internet Explorer para visualizar as respostas HTTP.

Para este exemplo queremos ver a resposta do\/nosso URL da sonda: http: /watestsdp2008r2.cloudapp.net:80/Probe. O exemplo da PowerShell ilustra o problema.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Exemplo de saída:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Reparem que recebemos uma resposta redireccional. Como indicado anteriormente, qualquer StatusCode que não seja 200 é considerado uma falha. O Gestor de Tráfego altera o estado do ponto final para Offline. Para resolver o problema, verifique a configuração do site para garantir que o StatusCode adequado pode ser devolvido do caminho da sonda. Reconfigure a sonda do Gestor de Tráfego para apontar para um caminho que retorne um 200.

Se a sua sonda estiver a utilizar o protocolo HTTPS, poderá ter de desativar a verificação de certificados para evitar erros SSL/TLS durante o seu teste. As seguintes declarações powerShell desativam a validação do certificado para a sessão atual da PowerShell:

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

## <a name="next-steps"></a>Passos Seguintes

[Sobre os métodos de encaminhamento de tráfego do Gestor de Tráfego](traffic-manager-routing-methods.md)

[O que é Gestor de Tráfego](traffic-manager-overview.md)

[Serviços em Nuvem](https://go.microsoft.com/fwlink/?LinkId=314074)

[Serviço de Aplicações do Azure](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operações do Traffic Manager (Referência da API REST)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets do Gestor de Tráfego Azure][1]

[1]: https://docs.microsoft.com/powershell/module/az.trafficmanager
