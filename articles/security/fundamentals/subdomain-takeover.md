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
ms.openlocfilehash: e378ffe00be9215c692a832e232fac7e866ab3c9
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88890829"
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



## <a name="the-risks-of-subdomain-takeover"></a>Os riscos de aquisição de subdomínio

Quando um registo DNS aponta para um recurso que não está disponível, o registo em si deveria ter sido removido da sua zona de DNS. Se não foi apagado, é um registo "dNS pendente" e cria a possibilidade de aquisição de subdomínio.

As entradas de DNS pendentes tornam possível que os atores de ameaça assumam o controlo do nome DNS associado para hospedar um website ou serviço malicioso. Páginas e serviços maliciosos no subdomínio de uma organização podem resultar em:

- **Perda de controlo sobre o conteúdo do subdomínio** - Imprensa negativa sobre a incapacidade da sua organização em garantir o seu conteúdo, bem como os danos e perda de confiança da marca.

- **Colheita de cookies de visitantes insuspeitos** - É comum que as aplicações web exponham cookies de sessão a subdomínios (*.contoso.com), consequentemente qualquer subdomínio pode aceder a eles. Os atores de ameaça podem usar a aquisição de subdomínio para construir uma página autêntica de aparência, enganar utilizadores insuspeitos para visitá-la e colher os seus cookies (até mesmo cookies seguros). Um equívoco comum é que a utilização de certificados SSL protege o seu site, e os cookies dos seus utilizadores, de uma aquisição. No entanto, um ator ameaça pode usar o subdomínio sequestrado para solicitar e receber um certificado SSL válido. Os certificados SSL válidos concedem-lhes acesso a cookies seguros e podem aumentar ainda mais a legitimidade percebida do site malicioso.

- **Campanhas de phishing** - Subdomínios de aparência autêntica podem ser usados em campanhas de phishing. Isto é verdade para sites maliciosos e também para registos MX que permitiriam que o ator ameaçasse receber e-mails dirigidos a um subdomínio legítimo de uma marca conhecida e segura.

- **Riscos adicionais** - Sites maliciosos podem ser usados para escalar em outros ataques clássicos como XSS, CSRF, BYPASS CORS, e muito mais.



## <a name="preventing-dangling-dns-entries"></a>Prevenção de entradas pendentes de DNS

Garantir que a sua organização implementou processos para evitar entradas de DNS pendentes e as aquisições de subdomínios resultantes é uma parte crucial do seu programa de segurança.

As medidas preventivas de que hoje dispõem estão listadas abaixo.


### <a name="use-azure-dns-alias-records"></a>Use registos de pseudónimos Azure DNS

[Os registos de pseudónimos](https://docs.microsoft.com/azure/dns/dns-alias#scenarios) do Azure DNS podem impedir referências pendentes acoplamento do ciclo de vida de um registo DNS com um recurso Azure. Por exemplo, considere um registo DNS que seja qualificado como um registo de pseudónimo para apontar para um endereço IP público ou um perfil de Gestor de Tráfego. Se eliminar os recursos subjacentes, o registo de pseudónimos DNS torna-se um recorde vazio. Já não faz referência ao recurso eliminado. É importante notar que há limites para o que se pode proteger com registos de pseudónimos. Hoje, a lista está limitada a:

- Azure Front Door
- Perfis do Gestor de Tráfego
- Pontos finais da Rede de Entrega de Conteúdos Azure (CDN)
- IPs públicos

Apesar das ofertas de serviços limitadas hoje em dia, recomendamos a utilização de registos de alias para se defender contra a aquisição de subdomínio sempre que possível.

[Saiba mais](https://docs.microsoft.com/azure/dns/dns-alias#capabilities) sobre as capacidades dos registos de pseudónimos do Azure DNS.



### <a name="use-azure-app-services-custom-domain-verification"></a>Utilize a verificação de domínio personalizada do Azure App Service

Ao criar entradas DNS para o Azure App Service, crie um asuid. {subdomínio} Registo TXT com o ID de Verificação de Domínio. Quando tal registo TXT existe, nenhuma outra Subscrição Azure pode validar o Domínio Personalizado que é, assumir. 

Estes registos não impedem que alguém crie o Serviço de Aplicações Azure com o mesmo nome que está na sua entrada CNAME. Sem a capacidade de provar a propriedade do nome de domínio, os atores de ameaça não podem receber tráfego ou controlar o conteúdo.

[Saiba mais](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain) sobre como mapear um nome DNS personalizado existente para o Azure App Service.



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Construir e automatizar processos para mitigar a ameaça

Muitas vezes cabe aos desenvolvedores e equipas de operações executar processos de limpeza para evitar ameaças pendentes de DNS. As práticas abaixo ajudarão a garantir que a sua organização evite sofrer com esta ameaça. 

- **Criar procedimentos de prevenção:**

    - Educar os desenvolvedores de aplicações para redirecionar endereços sempre que apagarem recursos.

    - Coloque a "Remover a entrada de DNS" na lista de verificações necessárias ao desativar um serviço.

    - Coloque [as fechaduras de apagar](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) quaisquer recursos que tenham uma entrada personalizada de DNS. Um bloqueio de eliminação serve como um indicador de que o mapeamento deve ser removido antes de o recurso ser desprovisionado. Medidas como esta só podem funcionar quando combinadas com programas de educação interna.

- **Criar procedimentos para a descoberta:**

    - Reveja regularmente os seus registos DNS para garantir que os seus subdomínios estão todos mapeados para recursos da Azure que:

        - Exista - Consultar as suas zonas DNS para recursos que apontam para subdomínios Azure tais como *.azurewebsites.net ou *.cloudapp.azure.com (consulte [esta lista de referência).](azure-domains.md)
        - Possui - Confirme que possui todos os recursos que os seus subdomínios DNS estão a ser alvo.

    - Mantenha um catálogo de serviços do seu nome de domínio Azure totalmente qualificado (FQDN) e dos proprietários da aplicação. Para construir o seu catálogo de serviços, execute o seguinte script de consulta de gráfico de recursos Azure. Este script projeta a informação do ponto final da FQDN dos recursos a que tem acesso e produz-os num ficheiro CSV. Se tiver acesso a todas as subscrições do seu inquilino, o script considera todas essas subscrições como mostrado no seguinte script de amostra. Para limitar os resultados a um conjunto específico de subscrições, edite o script como mostrado.

        >[!IMPORTANT]
        > **Permissões** - Executar a consulta como um utilizador que tenha acesso a todas as suas subscrições Azure. 
        >
        > **Limitações** - O Azure Resource Graph tem limites de estrangulamento e paging que deve considerar se tem um grande ambiente Azure. [Saiba mais](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) sobre trabalhar com grandes conjuntos de dados de recursos Azure. O seguinte script de amostra utiliza o lote de subscrição para evitar estas limitações.

        ```powershell
        
            # Fetch the full array of subscription IDs.
            $subscriptions = Get-AzSubscription

            $subscriptionIds = $subscriptions.Id
                    # Output file path and names
                    $date = get-date
                    $fdate = $date.ToString("MM-dd-yyy hh_mm_ss tt")
                    $fdate #log to console
                    $rpath = [Environment]::GetFolderPath("MyDocuments") + '\' # Feel free to update your path.
                    $rname = 'Tenant_FQDN_Report_' + $fdate + '.csv' # Feel free to update the document name.
                    $fpath = $rpath + $rname
                    $fpath #This is the output file of FQDN report.

            # queries
            $allTypesFqdnsQuery = "where type in ('microsoft.network/frontdoors',
                                    'microsoft.storage/storageaccounts',
                                    'microsoft.cdn/profiles/endpoints',
                                    'microsoft.network/publicipaddresses',
                                    'microsoft.network/trafficmanagerprofiles',
                                    'microsoft.containerinstance/containergroups',
                                    'microsoft.web/sites',
                                    'microsoft.web/sites/slots')
                        | extend FQDN = case(
                            type =~ 'microsoft.network/frontdoors', properties['cName'],
                            type =~ 'microsoft.storage/storageaccounts', parse_url(tostring(properties['primaryEndpoints']['blob'])).Host,
                            type =~ 'microsoft.cdn/profiles/endpoints', properties['hostName'],
                            type =~ 'microsoft.network/publicipaddresses', properties['dnsSettings']['fqdn'],
                            type =~ 'microsoft.network/trafficmanagerprofiles', properties['dnsConfig']['fqdn'],
                            type =~ 'microsoft.containerinstance/containergroups', properties['ipAddress']['fqdn'],
                            type =~ 'microsoft.web/sites', properties['defaultHostName'],
                            type =~ 'microsoft.web/sites/slots', properties['defaultHostName'],
                            '')
                        | project id, type, name, FQDN
                        | where isnotempty(FQDN)";

            $apiManagementFqdnsQuery = "where type =~ 'microsoft.apimanagement/service'
                        | project id, type, name,
                            gatewayUrl=parse_url(tostring(properties['gatewayUrl'])).Host,
                            portalUrl =parse_url(tostring(properties['portalUrl'])).Host,
                            developerPortalUrl = parse_url(tostring(properties['developerPortalUrl'])).Host,
                            managementApiUrl = parse_url(tostring(properties['managementApiUrl'])).Host,
                            gatewayRegionalUrl = parse_url(tostring(properties['gatewayRegionalUrl'])).Host,
                            scmUrl = parse_url(tostring(properties['scmUrl'])).Host,
                            additionaLocs = properties['additionalLocations']
                        | mvexpand additionaLocs
                        | extend additionalPropRegionalUrl = tostring(parse_url(tostring(additionaLocs['gatewayRegionalUrl'])).Host)
                        | project id, type, name, FQDN = pack_array(gatewayUrl, portalUrl, developerPortalUrl, managementApiUrl, gatewayRegionalUrl, scmUrl,             
                            additionalPropRegionalUrl)
                        | mvexpand FQDN
                        | where isnotempty(FQDN)";

            $queries = @($allTypesFqdnsQuery, $apiManagementFqdnsQuery);

            # Paging helper cursor
            $Skip = 0;
            $First = 1000;

            # If you have large number of subscriptions, process them in batches of 2,000.
            $counter = [PSCustomObject] @{ Value = 0 }
            $batchSize = 2000
            $response = @()

            # Group the subscriptions into batches.
            $subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

            foreach($query in $queries)
            {
                # Run the query for each subscription batch with paging.
                foreach ($batch in $subscriptionsBatch)
                { 
                    $Skip = 0; #Reset after each batch.

                    $response += do { Start-Sleep -Milliseconds 500;   if ($Skip -eq 0) {$y = Search-AzGraph -Query $query -First $First -Subscription $batch.Group ; } `
                    else {$y = Search-AzGraph -Query $query -Skip $Skip -First $First -Subscription $batch.Group } `
                    $cont = $y.Count -eq $First; $Skip = $Skip + $First; $y; } while ($cont)
                }
            }

            # View the completed results of the query on all subscriptions
            $response | Export-Csv -Path $fpath -Append  

        ```

        Lista de tipos e seus `FQDNProperty` valores conforme especificado na consulta de gráfico de recurso anterior:

        |Nome do recurso  | `<ResourceType>`  | `<FQDNproperty>`  |
        |---------|---------|---------|
        |Azure Front Door|microsoft.network/frontdoors|propriedades.cName|
        |Armazenamento de Blobs do Azure|microsoft.storage/storageaccounts|propriedades.primaryEndpoints.blob|
        |CDN do Azure|microsoft.cdn/perfis/pontos finais|propriedades.hostName|
        |Endereços IP públicos|microsoft.network/publicipaddresses|properties.dnsSettings.fqdn|
        |Gestor de Tráfego do Azure|microsoft.network/trafficmanagerprofils|properties.dnsConfig.fqdn|
        |Instância de Contentor do Azure|microsoft.containerinstance/containergroups|propriedades.ipAddress.fqdn|
        |Gestão de API do Azure|microsoft.apimanagement/service|propriedades.hostnameConfigurations.hostName|
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

- [Quickstart: Executar a sua primeira consulta de Gráfico de Recurso usando a Azure PowerShell](https://docs.microsoft.com/azure/governance/resource-graph/first-query-powershell)
