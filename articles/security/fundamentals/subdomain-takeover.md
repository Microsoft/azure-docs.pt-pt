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
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: c3a821156074727d02ab36cf88f3e998756b8cc4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389455"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>Evitar entradas de DNS pendentes e evitar a aquisição de subdomínios

Este artigo descreve a ameaça comum de segurança da aquisição de subdomínios e os passos que pode tomar para mitigar.


## <a name="what-is-a-subdomain-takeover"></a>O que é uma aquisição de subdomínio?

As aquisições de subdomínios são uma ameaça comum e de alta gravidade para organizações que criam regularmente e apagam muitos recursos. Uma aquisição de subdomínio pode ocorrer quando se tem um [registo DNS](../../dns/dns-zones-records.md#dns-records) que aponta para um recurso Azure deprovisionado. Estes registos DNS também são conhecidos como entradas "dNS pendentes". Os registos da CNAME são especialmente vulneráveis a esta ameaça. As aquisições de subdomínio permitem que atores maliciosos redirecionem o tráfego destinado ao domínio de uma organização para um site que executa atividades maliciosas.

Um cenário comum para uma aquisição de subdomínio:

1. **CRIAÇÃO:**

    1. Você disponibiliza um recurso Azure com um nome de domínio totalmente qualificado (FQDN) de `app-contogreat-dev-001.azurewebsites.net` .

    1. Você atribui um registo CNAME na sua zona de DNS com o subdomínio que encaminha o `greatapp.contoso.com` tráfego para o seu recurso Azure.

1. **DESPROVISIONAMENTO:**

    1. O recurso Azure é desprovisionado ou eliminado depois de já não ser necessário. 
    
        Neste ponto, o registo CNAME `greatapp.contoso.com` *deve* ser removido da sua zona de DNS. Se o registo cname não for removido, é anunciado como um domínio ativo, mas não encaminha o tráfego para um recurso Azure ativo. Esta é a definição de um disco "pendente" de DNS.

    1. O subdomínio `greatapp.contoso.com` pendente, está agora vulnerável e pode ser assumido ao ser atribuído a outro recurso de subscrição da Azure.

1. **AQUISIÇÃO:**

    1. Usando métodos e ferramentas geralmente disponíveis, um ator ameaça descobre o subdomínio pendente.  

    1. O ator ameaça fornece um recurso Azure com o mesmo FQDN do recurso que você previamente controlou. Neste exemplo, `app-contogreat-dev-001.azurewebsites.net` .

    1. O tráfego enviado para o subdomínio `greatapp.contoso.com` é agora encaminhado para o recurso do ator malicioso onde controlam o conteúdo.



![Aquisição de subdomínio a partir de um site desprovisionado](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>Os riscos de aquisição de subdomínio

Quando um registo DNS aponta para um recurso que não está disponível, o registo em si deveria ter sido removido da sua zona de DNS. Se não foi apagado, é um registo "dNS pendente" e cria a possibilidade de aquisição de subdomínio.

As entradas de DNS pendentes tornam possível que os atores de ameaça assumam o controlo do nome DNS associado para hospedar um website ou serviço malicioso. Páginas e serviços maliciosos no subdomínio de uma organização podem resultar em:

- **Perda de controlo sobre o conteúdo do subdomínio** - Imprensa negativa sobre a incapacidade da sua organização em garantir o seu conteúdo, bem como os danos e perda de confiança da marca.

- **Colheita de cookies de visitantes insuspeitos** - É comum que as aplicações web exponham cookies de sessão a subdomínios (*.contoso.com), consequentemente qualquer subdomínio pode aceder a eles. Os atores de ameaça podem usar a aquisição de subdomínio para construir uma página autêntica de aparência, enganar utilizadores insuspeitos para visitá-la e colher os seus cookies (até mesmo cookies seguros). Um equívoco comum é que a utilização de certificados SSL protege o seu site, e os cookies dos seus utilizadores, de uma aquisição. No entanto, um ator ameaça pode usar o subdomínio sequestrado para solicitar e receber um certificado SSL válido. Os certificados SSL válidos concedem-lhes acesso a cookies seguros e podem aumentar ainda mais a legitimidade percebida do site malicioso.

- **Campanhas de phishing** - Subdomínios de aparência autêntica podem ser usados em campanhas de phishing. Isto é verdade para sites maliciosos e para registos MX que permitiriam que o ator ameaçasse receber e-mails dirigidos a um subdomínio legítimo de uma marca conhecida e segura.

- **Riscos adicionais** - Sites maliciosos podem ser usados para escalar em outros ataques clássicos como XSS, CSRF, BYPASS CORS, e muito mais.



## <a name="identify-dangling-dns-entries"></a>Identificar entradas de DNS pendentes

Para identificar as entradas de DNS dentro da sua organização que possam estar pendentes, utilize as ferramentas PowerShell da [Microsoft"Get-DanglingDnsRecords".](https://aka.ms/DanglingDNSDomains)

Esta ferramenta ajuda os clientes Azure a listar todos os domínios com um CNAME associado a um recurso Azure existente que foi criado nas suas subscrições ou inquilinos.

Se as suas CNAMEs estiverem noutros serviços DNS e apontarem para os recursos da Azure, forneça as CNAMEs num ficheiro de entrada para a ferramenta.

A ferramenta suporta os recursos Azure listados na tabela seguinte. A ferramenta extrai, ou toma como entradas, todas as CNAMEs do inquilino.


| Serviço                   | Tipo                                        | FQDNproperty                               | Exemplo                         |
|---------------------------|---------------------------------------------|--------------------------------------------|---------------------------------|
| Azure Front Door          | microsoft.network/frontdoors                | propriedades.cName                           | `abc.azurefd.net`               |
| Armazenamento de Blobs do Azure        | microsoft.storage/storageaccounts           | propriedades.primaryEndpoints.blob           | `abc. blob.core.windows.net`    |
| CDN do Azure                 | microsoft.cdn/perfis/pontos finais            | propriedades.hostName                        | `abc.azureedge.net`             |
| Endereços IP públicos       | microsoft.network/publicipaddresses         | properties.dnsSettings.fqdn                | `abc.EastUs.cloudapp.azure.com` |
| Gestor de Tráfego do Azure     | microsoft.network/trafficmanagerprofils    | properties.dnsConfig.fqdn                  | `abc.trafficmanager.net`        |
| Instância de Contentor do Azure  | microsoft.containerinstance/containergroups | propriedades.ipAddress.fqdn                  | `abc.EastUs.azurecontainer.io`  |
| API Management do Azure      | microsoft.apimanagement/service             | propriedades.hostnameConfigurations.hostName | `abc.azure-api.net`             |
| Serviço de Aplicações do Azure         | microsoft.web/sites                         | propriedades.defaultName                 | `abc.azurewebsites.net`         |
| Serviço de Aplicações Azure - Slots | microsoft.web/sites/slots                   | propriedades.defaultName                 | `abc-def.azurewebsites.net`     |



### <a name="prerequisites"></a>Pré-requisitos

Executar a consulta como um utilizador que tem:

- pelo menos o acesso ao nível do leitor às assinaturas Azure
- ler o acesso ao gráfico de recursos Azure

Se é administrador global do inquilino da sua organização, eleve a sua conta para ter acesso a toda a subscrição da sua organização usando a orientação no [acesso elevate para gerir todas as subscrições e grupos de gestão da Azure.](../../role-based-access-control/elevate-access-global-admin.md)


> [!TIP]
> O Azure Resource Graph tem limites de estrangulamento e paging que deve considerar se tem um grande ambiente Azure. 
> 
> [Saiba mais sobre trabalhar com grandes conjuntos de dados de recursos Azure](../../governance/resource-graph/concepts/work-with-data.md).
> 
> A ferramenta utiliza lotes de subscrição para evitar estas limitações.

### <a name="run-the-script"></a>Executar o script

Saiba mais sobre o script PowerShell, **Get-DanglingDnsRecords.ps1**, e descarregue-o a partir do GitHub: https://aka.ms/Get-DanglingDnsRecords .

## <a name="remediate-dangling-dns-entries"></a>Remediar as entradas de DNS pendentes 

Reveja as suas zonas de DNS e identifique os registos cname que estão pendentes ou foram assumidos. Se se constatar que os subdomínios estão pendurados ou foram tomados, remova as subdomínios vulneráveis e atenue os riscos com as seguintes etapas:

1. Da sua zona de DNS, remova todos os registos CNAME que apontam para FQDNs de recursos que já não são a provisionados.

1. Para permitir que o tráfego seja encaminhado para recursos ao seu controlo, fornecendo recursos adicionais com as FQDNs especificadas nos registos CNAME dos subdomínios pendentes.

1. Reveja o seu código de aplicação para referências a subdomínios específicos e atualize quaisquer referências de subdomínios incorretas ou desatualizadas.

1. Investigue se houve algum compromisso e tome medidas de acordo com os procedimentos de resposta a incidentes da sua organização. Dicas e boas práticas para investigar este assunto podem ser encontrados abaixo.

    Se a sua lógica de aplicação for tal que segredos como credenciais de OAuth foram enviados para o subdomínio pendente, ou informações sensíveis à privacidade foram enviadas para os subdomínios pendentes, esses dados podem ter sido expostos a terceiros.

1. Compreenda porque é que o registo cname não foi removido da sua zona de DNS quando o recurso foi desprovisionado e tome medidas para garantir que os registos dns sejam atualizados adequadamente quando os recursos Azure forem desprovisionados no futuro.


## <a name="prevent-dangling-dns-entries"></a>Evitar entradas penduradas no DNS

Garantir que a sua organização implementou processos para evitar entradas de DNS pendentes e as aquisições de subdomínios resultantes é uma parte crucial do seu programa de segurança.

Alguns serviços da Azure oferecem funcionalidades de ajuda na criação de medidas preventivas e são detalhados abaixo. Outros métodos para prevenir este problema devem ser estabelecidos através das melhores práticas da sua organização ou dos procedimentos operacionais padrão.

### <a name="enable-azure-defender-for-app-service"></a>Ativar o Azure Defender para o Serviço de Aplicações

A plataforma integrada de proteção da carga de trabalho em nuvem (CWPP) do Azure Security Center oferece uma gama de planos para proteger os seus recursos e cargas de trabalho Azure, híbridos e multi-cloud.

O plano **Azure Defender for App Service** inclui a deteção de DNS pendente. Com este plano ativado, receberá alertas de segurança se desativar um website do Serviço de Aplicações, mas não remover o seu domínio personalizado do seu registo DNS.

A proteção de DNS pendente do Azure Defender está disponível se os seus domínios são geridos com DNS ou um registrador de domínio externo e se aplica ao Serviço de Aplicações tanto no Windows como no Linux.

Saiba mais sobre este e outros benefícios deste plano Azure Defender em [Introdução ao Azure Defender para o Serviço de Aplicações.](../../security-center/defender-for-app-service-introduction.md)

### <a name="use-azure-dns-alias-records"></a>Use registos de pseudónimos Azure DNS

[Os registos de pseudónimos](../../dns/dns-alias.md#scenarios) do Azure DNS podem impedir referências pendentes acoplamento do ciclo de vida de um registo DNS com um recurso Azure. Por exemplo, considere um registo DNS que seja qualificado como um registo de pseudónimo para apontar para um endereço IP público ou um perfil de Gestor de Tráfego. Se eliminar os recursos subjacentes, o registo de pseudónimos DNS torna-se um recorde vazio. Já não faz referência ao recurso eliminado. É importante notar que há limites para o que se pode proteger com registos de pseudónimos. Hoje, a lista está limitada a:

- Azure Front Door
- Perfis do Gestor de Tráfego
- Pontos finais da Rede de Entrega de Conteúdos Azure (CDN)
- IPs públicos

Apesar das ofertas de serviços limitadas hoje em dia, recomendamos a utilização de registos de alias para se defender contra a aquisição de subdomínio sempre que possível.

[Saiba mais sobre as capacidades dos registos de pseudónimos do Azure DNS](../../dns/dns-alias.md#capabilities).



### <a name="use-azure-app-services-custom-domain-verification"></a>Utilize a verificação de domínio personalizada do Azure App Service

Ao criar entradas DNS para o Azure App Service, crie um asuid. {subdomínio} Registo TXT com o ID de Verificação de Domínio. Quando tal registo TXT existe, nenhuma outra Subscrição Azure pode validar o Domínio Personalizado que é, assumir. 

Estes registos não impedem que alguém crie o Serviço de Aplicações Azure com o mesmo nome que está na sua entrada CNAME. Sem a capacidade de provar a propriedade do nome de domínio, os atores de ameaça não podem receber tráfego ou controlar o conteúdo.

[Saiba mais sobre como mapear um nome DNS personalizado existente para o Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md).



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Construir e automatizar processos para mitigar a ameaça

Muitas vezes cabe aos desenvolvedores e equipas de operações executar processos de limpeza para evitar ameaças pendentes de DNS. As práticas abaixo ajudarão a garantir que a sua organização evite sofrer com esta ameaça. 

- **Criar procedimentos de prevenção:**

    - Educar os desenvolvedores de aplicações para redirecionar endereços sempre que apagarem recursos.

    - Coloque a "Remover a entrada de DNS" na lista de verificações necessárias ao desativar um serviço.

    - Coloque [as fechaduras de apagar](../../azure-resource-manager/management/lock-resources.md) quaisquer recursos que tenham uma entrada personalizada de DNS. Um bloqueio de eliminação serve como um indicador de que o mapeamento deve ser removido antes de o recurso ser desprovisionado. Medidas como esta só podem funcionar quando combinadas com programas de educação interna.

- **Criar procedimentos para a descoberta:**

    - Reveja regularmente os seus registos DNS para garantir que os seus subdomínios estão todos mapeados para recursos da Azure que:

        - Exista - Consultar as suas zonas DNS para recursos que apontam para subdomínios Azure tais como *.azurewebsites.net ou *.cloudapp.azure.com (consulte a [lista de referência dos domínios Azure).](azure-domains.md)
        - Possui - Confirme que possui todos os recursos que os seus subdomínios DNS estão a ser alvo.

    - Mantenha um catálogo de serviços do seu nome de domínio Azure totalmente qualificado (FQDN) e dos proprietários da aplicação. Para construir o seu catálogo de serviços, execute o seguinte script de consulta de gráfico de recursos Azure. Este script projeta a informação do ponto final da FQDN dos recursos a que tem acesso e produz-os num ficheiro CSV. Se tiver acesso a todas as subscrições do seu inquilino, o script considera todas essas subscrições como mostrado no seguinte script de amostra. Para limitar os resultados a um conjunto específico de subscrições, edite o script como mostrado.


- **Criar procedimentos de reparação:**
    - Quando forem encontradas entradas de DNS pendentes, a sua equipa precisa de investigar se houve algum compromisso.
    - Investigue por que o endereço não foi reencaminhado quando o recurso foi desativado.
    - Elimine o registo DNS se já não estiver em uso, ou aponte-o para o recurso Azure (FQDN) de propriedade da sua organização.
 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre serviços relacionados e funcionalidades Azure que pode usar para se defender contra a aquisição de subdomínios, consulte as páginas seguintes.

- [Enable Azure Defender for App Service](../../security-center/defender-for-app-service-introduction.md) - para receber alertas quando forem detetadas entradas de DNS pendentes

- [Evitar registos de DNS pendentes com DNS Azure](../../dns/dns-alias.md#prevent-dangling-dns-records)

- [Utilize um ID de verificação de domínio ao adicionar domínios personalizados no Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id)

- [Quickstart: Executar a sua primeira consulta de Gráfico de Recurso usando a Azure PowerShell](../../governance/resource-graph/first-query-powershell.md)
