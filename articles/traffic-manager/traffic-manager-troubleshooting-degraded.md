---
title: Resolução de problemas degradado em Azure Traffic Manager
description: Como resolver os perfis do Traffic Manager quando mostra como estado degradado.
services: traffic-manager
documentationcenter: ''
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: duau
ms.openlocfilehash: b76eab5771d724e4f0ec56b7d5acd5cf5f91edc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98183460"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Resolução de problemas do estado degradado no Gestor de Tráfego do Azure

Este artigo descreve como resolver problemas num perfil do Gestor de Tráfego Azure que está a mostrar um estado degradado. Como primeiro passo para resolver problemas, um estado degradado do Azure Traffic Manager é permitir a exploração madeireira.  Consulte para [Ativar registos de recursos](./traffic-manager-diagnostic-logs.md) para obter mais informações. Para este cenário, considere que configuraste um perfil do Gestor de Tráfego que aponta para alguns dos seus serviços cloudapp.net hospedados. Se a saúde do seu Gestor de Tráfego apresentar um estado **degradado,** então o estado de um ou mais pontos finais pode ser **degradado:**

![estado de ponto final degradado](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Se a saúde do seu Gestor de Tráfego apresentar um estado **inativo,** então ambos os pontos finais podem ser **desactivos:**

![Estado do Gestor de Tráfego Inativo](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Compreender as sondas do Gestor de Tráfego

* O Gestor de Tráfego considera que um ponto final é online apenas quando a sonda recebe uma resposta HTTP 200 de volta do caminho da sonda. Se a sua aplicação devolver qualquer outro código de resposta HTTP, deve adicionar esse código de resposta às gamas de códigos de [estado esperados](./traffic-manager-monitoring.md#configure-endpoint-monitoring) do seu perfil de Gestor de Tráfego.
* Uma resposta de redirecionamento de 30x é tratada como falha, a menos que tenha especificado isto como um código de resposta válido nas [gamas](./traffic-manager-monitoring.md#configure-endpoint-monitoring) de código de estado esperado do seu perfil de Gestor de Tráfego. O Gestor de Tráfego não sonda o alvo de reorientação.
* Para as sondas HTTPs, os erros de certificado são ignorados.
* O conteúdo real do caminho da sonda não importa, desde que um 200 seja devolvido. Sondar um URL para algum conteúdo estático como "/favicon.ico" é uma técnica comum. O conteúdo dinâmico, tal como as páginas ASP, nem sempre pode devolver 200, mesmo quando a aplicação é saudável.
* Uma boa prática é definir o caminho da sonda para algo que tenha lógica suficiente para determinar que o site está para cima ou para baixo. No exemplo anterior, ao definir o caminho para "/favicon.ico", só está a testar que w3wp.exe está a responder. Esta sonda pode não indicar que a sua aplicação web é saudável. Uma melhor opção seria definir um caminho para algo como "/Sonda.aspx" que tenha lógica para determinar a saúde do site. Por exemplo, pode utilizar contadores de desempenho para utilizar o CPU ou medir o número de pedidos falhados. Ou pode tentar aceder a recursos de base de dados ou estado de sessão para se certificar de que a aplicação web está a funcionar.
* Se todos os pontos finais de um perfil estiverem degradados, então o Traffic Manager trata todos os pontos finais como saudáveis e encaminha o tráfego para todos os pontos finais. Este comportamento garante que os problemas com o mecanismo de sondagem não resultam numa interrupção completa do seu serviço.

## <a name="troubleshooting"></a>Resolução de problemas

Para resolver uma falha da sonda, precisa de uma ferramenta que mostre o retorno do código de estado HTTP a partir do URL da sonda. Existem muitas ferramentas disponíveis que mostram a resposta HTTP crua.

* [Fiddler](https://www.telerik.com/fiddler)
* [curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Além disso, pode utilizar o separador Rede das Ferramentas de Depuragem F12 no Internet Explorer para visualizar as respostas HTTP.

Para este exemplo queremos ver a resposta da nossa URL da sonda: http: \/ /watestsdp2008r2.cloudapp.net:80/Probe. O exemplo powerShell que se segue ilustra o problema.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Exemplo de saída:

```output
StatusCode StatusDescription
---------- -----------------
        301 Moved Permanently
```

Note que recebemos uma resposta de redirecionamento. Como indicado anteriormente, qualquer StatusCode que não seja 200 é considerado uma falha. O Gestor de Tráfego altera o estado do ponto final para Offline. Para resolver o problema, consulte a configuração do site para garantir que o StatusCode adequado pode ser devolvido do caminho da sonda. Reconfigure a sonda Do Gestor de Tráfego para apontar para um caminho que retorne um 200.

Se a sua sonda estiver a utilizar o protocolo HTTPS, poderá ter de desativar a verificação de certificados para evitar erros de SSL/TLS durante o teste. As seguintes declarações powerShell desativam a validação do certificado para a sessão atual do PowerShell:

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

[Sobre os métodos de encaminhamento de tráfego do Traffic Manager](traffic-manager-routing-methods.md)

[O que é Gestor de Tráfego](traffic-manager-overview.md)

[Serviços em Nuvem](/previous-versions/azure/jj155995(v=azure.100))

[Serviço de Aplicações do Azure](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operações do Traffic Manager (Referência da API REST)](/previous-versions/azure/reference/hh758255(v=azure.100))

[Comandantes do Gestor de Tráfego Azure][1]

[1]: /powershell/module/az.trafficmanager