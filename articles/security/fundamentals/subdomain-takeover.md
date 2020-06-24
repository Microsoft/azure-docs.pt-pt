---
title: Evitar aquisições de subdomínio com registos de pseudónimos Azure DNS e verificação de domínio personalizado do Azure App Service
description: Saiba como evitar a ameaça comum de alta gravidade da aquisição de subdomínio
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2020
ms.author: memildin
ms.openlocfilehash: 4e5969b4c3a42fc8a2c4b1cd537c22a4422ca131
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85269030"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>Evitar entradas de DNS pendentes e evitar a aquisição de subdomínios

Este artigo descreve a ameaça comum de segurança da aquisição de subdomínios e os passos que pode tomar para mitigar.


## <a name="what-is-subdomain-takeover"></a>O que é a aquisição de subdomínios?

As aquisições de subdomínios são uma ameaça comum e de alta gravidade para organizações que criam regularmente e apagam muitos recursos. Uma aquisição de subdomínio pode ocorrer quando se tem um registo DNS que aponta para um recurso Azure deprovisionado. Estes registos DNS também são conhecidos como entradas "dNS pendentes". Os registos da CNAME são especialmente vulneráveis a esta ameaça.

Um cenário comum para uma aquisição de subdomínio:

1. Um site é criado. 

    Neste exemplo, `app-contogreat-dev-001.azurewebsites.net` .

1. Uma entrada CNAME é adicionada ao DNS apontando para o site. 

    Neste exemplo, foi criado o seguinte nome amigável: `greatapp.contoso.com` .

1. Após alguns meses, o site já não é necessário, pelo que é apagado **sem** eliminar a entrada de DNS correspondente. 

    A entrada do DNS CNAME está agora "pendurada".

1. Quase imediatamente após a apagar o site, um ator ameaça descobre o site desaparecido e cria o seu próprio website em `app-contogreat-dev-001.azurewebsites.net` .

    Agora, o tráfego destinado `greatapp.contoso.com` vai para o site Azure do ator ameaça, e o ator ameaça está no controlo do conteúdo que é exibido. 

    O DNS pendente foi explorado e o subdomínio de Contoso "GreatApp" tem sido vítima da aquisição de subdomínios. 

![Aquisição de subdomínio a partir de um site desprovisionado](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-dangling-dns-records"></a>Os riscos de pendurar os registos do DNS

Quando um registo DNS aponta para um recurso que não está disponível, o registo em si deveria ter sido removido da sua zona de DNS. Se não foi apagado, é um registo de DNS pendente e um risco de segurança.

O risco para a organização é que permite que um ator ameaça assuma o controlo do nome DNS associado para hospedar um site ou serviço malicioso. Este site malicioso no subdomínio da organização pode resultar em:

- **Perda de controlo sobre o conteúdo do subdomínio** - Imprensa negativa sobre a incapacidade da sua organização em garantir o seu conteúdo, bem como os danos e perda de confiança da marca.

- **Colheita de cookies de visitantes insuspeitos** - É comum que as aplicações web exponham cookies de sessão a subdomínios (*.contoso.com), consequentemente qualquer subdomínio pode aceder a eles. Os atores de ameaça podem usar a aquisição de subdomínio para construir uma página autêntica de aparência, enganar utilizadores insuspeitos para visitá-la e colher os seus cookies (até mesmo cookies seguros). Um equívoco comum é que a utilização de certificados SSL protege o seu site, e os cookies dos seus utilizadores, de uma aquisição. No entanto, um ator ameaça pode usar o subdomínio sequestrado para solicitar e receber um certificado SSL válido. Isto então lhes dá acesso a cookies seguros e pode aumentar ainda mais a legitimidade percebida do site malicioso.

- **Campanhas de phishing** - Subdomínios de aparência autêntica podem ser usados em campanhas de phishing. Isto é verdade para sites maliciosos e também para registos MX que permitiriam que o ator ameaçasse receber e-mails dirigidos a um subdomínio legítimo de uma marca conhecida e segura.

- **Riscos adicionais** - Aumente para outros ataques clássicos como XSS, CSRF, BYPASS CORS, e muito mais.



## <a name="preventing-dangling-dns-entries"></a>Prevenção de entradas pendentes de DNS

Garantir que a sua organização implementou processos para evitar entradas de DNS pendentes e as aquisições de subdomínios resultantes é uma parte crucial do seu programa de segurança.

As medidas preventivas de que hoje dispõem estão listadas abaixo.


### <a name="use-azure-dns-alias-records"></a>Use registos de pseudónimos Azure DNS

Ao acoplar firmemente o ciclo de vida de um registo DNS com um recurso Azure, a funcionalidade [de registos de pseudónimos](https://docs.microsoft.com/azure/dns/dns-alias#scenarios) do Azure DNS pode impedir referências pendentes. Por exemplo, considere um registo DNS que seja qualificado como um registo de pseudónimo para apontar para um endereço IP público ou um perfil de Gestor de Tráfego. Se eliminar os recursos subjacentes, o registo de pseudónimos DNS torna-se um recorde vazio. Já não faz referência ao recurso eliminado. É importante notar que há limites para o que se pode proteger com registos de pseudónimos. Hoje, a lista está limitada a:

- Azure Front Door
- Perfis do Gestor de Tráfego
- Pontos finais da Rede de Entrega de Conteúdos Azure (CDN)
- IPs públicos

Se você tem recursos que podem ser protegidos contra a aquisição de subdomínio com registos de pseudónimos, recomendamos fazê-lo apesar das ofertas limitadas de serviços hoje em dia.

[Saiba mais](https://docs.microsoft.com/azure/dns/dns-alias#capabilities) sobre as capacidades dos registos de pseudónimos do Azure DNS.



### <a name="use-azure-app-services-custom-domain-verification"></a>Utilize a verificação de domínio personalizada do Azure App Service

Ao criar entradas DNS para o Azure App Service, crie um asuid. {subdomínio} Registo TXT com o ID de Verificação de Domínio. Quando tal registo TXT existe, nenhuma outra Subscrição Azure pode validar o Domínio Personalizado que é, assumir. 

Estes registos não impedem que alguém crie o Serviço de Aplicações Azure com o mesmo nome que está na sua entrada CNAME, mas não poderão receber tráfego ou controlar o conteúdo, porque não podem provar a propriedade do nome de domínio.

[Saiba mais](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain) sobre como mapear um nome DNS personalizado existente para o Azure App Service.



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Construir e automatizar processos para mitigar a ameaça

Muitas vezes cabe aos desenvolvedores e equipas de operações executar processos de limpeza para evitar ameaças pendentes de DNS. As práticas abaixo ajudarão a garantir que a sua organização evite sofrer com esta ameaça. 

- **Criar procedimentos de prevenção:**

    - Educar os desenvolvedores de aplicações para redirecionar endereços sempre que apagarem recursos.

    - Coloque a "Remover a entrada de DNS" na lista de verificações necessárias ao desativar um serviço.

    - Coloque [as fechaduras de apagar](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) quaisquer recursos que tenham uma entrada personalizada de DNS. Isto deve servir como um indicador de que o mapeamento deve ser removido antes de o recurso ser desprovisionado. Medidas como esta só podem funcionar quando combinadas com programas de educação interna.

- **Criar procedimentos para a descoberta:**

    - Reveja regularmente os seus registos DNS para garantir que os seus subdomínios estão todos mapeados para recursos da Azure que:

        - **Exista** - Consultar as suas zonas DNS para recursos que apontam para subdomínios Azure tais como *.azurewebsites.net ou *.cloudapp.azure.com (consulte [esta lista de referência).](azure-domains.md)
        - **Possui** - Confirme que possui todos os recursos que os seus subdomínios DNS estão a ser alvo.

    - Mantenha um catálogo de serviços do seu nome de domínio Azure totalmente qualificado (FQDN) e dos proprietários da aplicação. Para construir o seu catálogo de serviços, execute a seguinte consulta de Gráfico de Recursos Azure com os parâmetros da tabela abaixo:
    
        >[!IMPORTANT]
        > **Permissões** - Executar a consulta como utilizador com acesso a todas as suas subscrições Azure. 
        >
        > **Limitações** - O Azure Resource Graph tem limites de estrangulamento e paging que deve considerar se tem um grande ambiente Azure. [Saiba mais](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) sobre trabalhar com grandes conjuntos de dados de recursos Azure.  

        ```
        Search-AzGraph -Query "resources | where type == '[ResourceType]' | project tenantId, subscriptionId, type, resourceGroup, name, endpoint = [FQDNproperty]"
        ``` 
        
        Por exemplo, esta consulta devolve os recursos do Azure App Service:

        ```
        Search-AzGraph -Query "resources | where type == 'microsoft.web/sites' | project tenantId, subscriptionId, type, resourceGroup, name, endpoint = properties.defaultHostName"
        ```
        
        Também pode combinar vários tipos de recursos. Este exemplo de consulta devolve os recursos do Azure App Service **e** do Azure App Service - Slots:

        ```
        Search-AzGraph -Query "resources | where type in ('microsoft.web/sites', 'microsoft.web/sites/slots') | project tenantId, subscriptionId, type, resourceGroup, name, endpoint = properties.defaultHostName"
        ```

        |Nome do recurso  |[ResourceType]  | [FQDNproperty]  |
        |---------|---------|---------|
        |Azure Front Door|microsoft.network/frontdoors|propriedades.cName|
        |Armazenamento de Blobs do Azure|microsoft.storage/storageaccounts|propriedades.primaryEndpoints.blob|
        |CDN do Azure|microsoft.cdn/perfis/pontos finais|propriedades.hostName|
        |Endereços IP públicos|microsoft.network/publicipaddresses|properties.dnsSettings.fqdn|
        |Traffic Manager do Azure|microsoft.network/trafficmanagerprofils|properties.dnsConfig.fqdn|
        |Instância de Contentor do Azure|microsoft.containerinstance/containergroups|propriedades.ipAddress.fqdn|
        |API Management do Azure|microsoft.apimanagement/service|propriedades.hostnameConfigurations.hostName|
        |Serviço de Aplicações do Azure|microsoft.web/sites|propriedades.defaultName|
        |Serviço de Aplicações Azure - Slots|microsoft.web/sites/slots|propriedades.defaultName|


- **Criar procedimentos de reparação:**
    - Quando forem encontradas entradas de DNS pendentes, a sua equipa precisa de investigar se houve algum compromisso.
    - Investigue por que o endereço não foi reencaminhado quando o recurso foi desativado.
    - Elimine o registo DNS se já não estiver em uso, ou aponte-o para o recurso Azure (FQDN) de propriedade da sua organização.
 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre serviços relacionados e funcionalidades Azure que pode usar para se defender contra a aquisição de subdomínios, consulte as páginas seguintes.

- [Azure DNS suporta usar registos de pseudónimos para domínios personalizados](https://docs.microsoft.com/azure/dns/dns-alias#prevent-dangling-dns-records)

- [Utilize o ID de Verificação de Domínio ao adicionar domínios personalizados no Serviço de Aplicações Azure](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#get-domain-verification-id) 

-    [Quickstart: Executar a sua primeira consulta de Gráfico de Recurso usando a Azure PowerShell](https://docs.microsoft.com/azure/governance/resource-graph/first-query-powershell)