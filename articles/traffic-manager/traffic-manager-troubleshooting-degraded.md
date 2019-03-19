---
title: Resolução de problemas de degradação de estado no Gestor de tráfego do Azure
description: Como resolver problemas de perfis do Gestor de tráfego quando ele mostra como degradado estado.
services: traffic-manager
documentationcenter: ''
author: chadmath
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: genli
ms.openlocfilehash: 2cb70c67bc4719eb43c4fcb70edf574c57362f26
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013286"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Resolução de problemas do Estado no Gestor de tráfego do Azure degradado

Este artigo descreve como resolver problemas de um perfil do Gestor de tráfego do Azure que está a mostrar o estado degradado. Para este cenário, considere que tiver configurado um perfil do Gestor de tráfego que aponta para alguns dos seus serviços cloudapp.net alojada. Se o estado de funcionamento do Gestor de tráfego apresenta um **Degraded** Estado, em seguida, o estado de um ou mais pontos de extremidade pode ser **Degraded**:

![Estado do ponto final degradado](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Se o estado de funcionamento do Gestor de tráfego apresenta uma **Inactive** Estado, em seguida, os dois pontos de extremidade pode ser **desativado**:

![Estado inativo do Gestor de tráfego](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Sondas de noções básicas sobre o Gestor de tráfego

* O Gestor de tráfego considera um ponto final para estar ONLINE, apenas quando a sonda recebe uma resposta HTTP 200 volta do caminho de sonda. Qualquer resposta outra que não 200 é uma falha.
* Um redirecionamento de 30 x falha, mesmo se o URL redirecionado retorna um 200.
* Para sondas de HTTPs, erros de certificado são ignorados.
* O conteúdo real do caminho da sonda não importa, enquanto um 200 é retornado. Pesquisa um URL para algum conteúdo estático, como "/ favicon.ico" é uma técnica comum. Conteúdo dinâmico, como as páginas ASP, pode não sempre retornar 200, mesmo quando a aplicação está em bom estada.
* Uma melhor prática é definir o caminho de sonda para algo que tem suficiente lógica para determinar se o site é ou reduzir verticalmente. No exemplo anterior, definindo o caminho como "/ favicon.ico", são apenas testes que w3wp.exe está a responder. Esta pesquisa não pode indicar que a sua aplicação web está em bom estada. Uma opção melhor seria definir um caminho como uma algo como "/ Probe.aspx" que tem lógica para determinar o estado de funcionamento do site. Por exemplo, pode utilizar contadores de desempenho para a utilização da CPU ou medir o número de pedidos falhados. Ou poderia tentar aceder a recursos de base de dados ou estado da sessão para se certificar de que a aplicação web está a funcionar.
* Se todos os pontos finais num perfil estão degradados, em seguida, o Gestor de tráfego trata todos os pontos de extremidade como bom estado de funcionamento e encaminha o tráfego para todos os pontos finais. Esse comportamento garante que os problemas com o mecanismo de pesquisa não resultar numa falha completa do seu serviço.

## <a name="troubleshooting"></a>Resolução de problemas

Para resolver uma falha de pesquisa, precisa de uma ferramenta que mostra o código de estado HTTP devolvido do URL da sonda. Existem muitas ferramentas disponíveis que lhe mostram a resposta HTTP não processada.

* [Fiddler](https://www.telerik.com/fiddler)
* [curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Além disso, pode utilizar o separador rede as ferramentas de depuração de F12 no Internet Explorer para ver as respostas HTTP.

Neste exemplo, queremos ver a resposta do nosso URL de sonda: http://watestsdp2008r2.cloudapp.net:80/Probe. O exemplo de PowerShell seguinte ilustra o problema.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Exemplo de saída:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Tenha em atenção que recebemos uma resposta de redirecionamento. Conforme indicado anteriormente, qualquer StatusCode diferente de 200 é considerado uma falha. O Gestor de tráfego altera o estado de ponto final para Offline. Para resolver o problema, verifique a configuração do Web site para se certificar de que o StatusCode adequado pode ser retornado do caminho de sonda. Reconfigure a sonda de Gestor de tráfego para apontar para um caminho que retorna um 200.

Se a sua pesquisa está a utilizar o protocolo HTTPS, terá de desativar a verificação para evitar erros SSL/TLS durante o teste de certificado. As seguintes declarações de PowerShell desativar a validação de certificado para a sessão atual do PowerShell:

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

[Sobre métodos de encaminhamento de tráfego do Gestor de tráfego](traffic-manager-routing-methods.md)

[O que é o Gestor de tráfego](traffic-manager-overview.md)

[Serviços Cloud](https://go.microsoft.com/fwlink/?LinkId=314074)

[Serviço de Aplicações do Azure](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operações do Gestor de Tráfego (Referência da API REST)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets do Gestor de tráfego do Azure][1]

[1]: https://docs.microsoft.com/powershell/module/azurerm.trafficmanager
