---
title: Serviço de porta frontal do Azure-perguntas frequentes
description: Esta página fornece respostas para perguntas frequentes sobre o serviço de porta frontal do Azure
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2019
ms.author: sharadag
ms.openlocfilehash: 39051014e5e474264a44983fb366bc08f02c31e0
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639859"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Perguntas frequentes sobre o serviço de porta frontal do Azure

Este artigo responde a perguntas comuns sobre recursos e funcionalidades do serviço de porta frontal do Azure. Se você não vir a resposta para sua pergunta, poderá entrar em contato conosco por meio dos seguintes canais (em ordem de escalonamento):

1. A seção de comentários deste artigo.
2. [UserVoice do serviço de porta frontal do Azure](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Suporte da Microsoft:** Para criar uma nova solicitação de suporte, na portal do Azure, na guia **ajuda** , selecione o botão **ajuda + suporte** e, em seguida, selecione **nova solicitação de suporte**.

## <a name="general"></a>Geral

### <a name="what-is-azure-front-door-service"></a>O que é o Azure Front Door Service?

O serviço de porta frontal do Azure é um Rede de Distribuição de Aplicativos (ADN) como um serviço, oferecendo vários recursos de balanceamento de carga de camada 7 para seus aplicativos. Ele fornece o DSA (aceleração de site dinâmico) junto com o balanceamento de carga global com failover quase em tempo real. É um serviço altamente disponível e escalonável, que é totalmente gerenciado pelo Azure.

### <a name="what-features-does-azure-front-door-service-support"></a>A quais recursos o serviço de porta frontal do Azure dá suporte?

O serviço de porta frontal do Azure dá suporte a DSA (aceleração de site dinâmico), descarregamento de SSL e SSL de ponta a ponta, firewall de aplicativo Web, afinidade de sessão baseada em cookie, roteamento baseado em caminho de URL, certificados gratuitos e gerenciamento de vários domínios, entre outros. Para obter uma lista completa dos recursos com suporte, consulte [visão geral do serviço de porta frontal do Azure](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>Qual é a diferença entre o serviço de porta frontal do Azure e o gateway de Aplicativo Azure?

Embora tanto a porta de frente quanto o gateway de aplicativo sejam balanceadores de carga de camada 7 (HTTP/HTTPS), a principal diferença é que a porta frontal é um serviço global, enquanto o gateway de aplicativo é um serviço regional. Embora a porta frontal possa balancear a carga entre as diferentes unidades de escala/clusters/unidades de carimbo entre regiões, o gateway de aplicativo permite balancear a carga entre suas VMs/contêineres, etc. que está dentro da unidade de escala.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Quando devemos implantar um gateway de aplicativo por trás da porta frontal?

Os principais cenários pelos quais um deve usar o gateway de aplicativo por trás da porta frontal são:

- A porta frontal pode executar o balanceamento de carga baseado em caminho apenas no nível global, mas se um quiser balancear a carga do tráfego ainda mais dentro de sua rede virtual (VNET), eles deverão usar o gateway de aplicativo.
- Como a porta da frente não funciona em nível de VM/contêiner, isso não pode fazer o descarregamento da conexão. No entanto, o gateway de aplicativo permite que você faça o descarregamento de conexão. 
- Com um gateway de aplicativo por trás de AFD, é possível obter um descarregamento de 100% SSL e rotear apenas solicitações HTTP em sua VNET (rede virtual).
- A porta de frente e o gateway de aplicativo dão suporte à afinidade de sessão. Embora a porta frontal possa direcionar o tráfego subsequente de uma sessão de usuário para o mesmo cluster ou back-end em uma determinada região, o gateway de aplicativo pode direcionar relacionar o tráfego para o mesmo servidor dentro do cluster.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Podemos implantar Azure Load Balancer atrás da porta frontal?

O serviço de porta frontal do Azure precisa de um VIP público ou um nome DNS publicamente disponível para rotear o tráfego para o. Implantar um Azure Load Balancer atrás da porta frontal é um caso de uso comum.

### <a name="what-protocols-does-azure-front-door-service-support"></a>A quais protocolos o serviço de porta frontal do Azure dá suporte?

O serviço de porta frontal do Azure dá suporte a HTTP, HTTPS e HTTP/2.

### <a name="how-does-azure-front-door-service-support-http2"></a>Como o serviço de porta frontal do Azure dá suporte a HTTP/2?

O suporte a protocolo HTTP/2 está disponível para clientes que se conectam somente ao serviço de porta frontal do Azure. A comunicação aos back-ends no pool de back-end é por HTTP/1.1. O suporte a HTTP/2 está habilitado por padrão.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Quais recursos têm suporte hoje como parte do pool de back-end?

Os pools de back-end podem ser compostos de armazenamento, aplicativo Web, instâncias de kubernetes ou qualquer outro nome de host personalizado que tenha conectividade pública. O serviço de porta frontal do Azure requer que os back-ends sejam definidos por meio de um IP público ou um nome de host DNS resolvido publicamente. Os membros de pools de back-end podem estar entre zonas, regiões ou até mesmo fora do Azure, desde que tenham conectividade pública.

### <a name="what-regions-is-the-service-available-in"></a>Em quais regiões o serviço está disponível?

O serviço de porta frontal do Azure é um serviço global e não está vinculado a nenhuma região específica do Azure. O único local que você precisa especificar ao criar uma porta frontal é o local do grupo de recursos, que está basicamente especificando onde os metadados do grupo de recursos serão armazenados. O próprio recurso de porta frontal é criado como um recurso global e a configuração é implantada globalmente em todos os POPs (ponto de presença). 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Quais são os locais POP para o serviço de porta frontal do Azure?

O serviço de porta frontal do Azure tem a mesma lista de locais POP (ponto de presença) que a CDN do Azure da Microsoft. Para obter a lista completa de nossos POPs, consulte os [locais pop de CDN do Azure da Microsoft](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>O serviço de porta frontal do Azure é uma implantação dedicada para meu aplicativo ou é compartilhado entre clientes?

O serviço de porta frontal do Azure é um serviço de vários locatários distribuído globalmente. Portanto, a infraestrutura para a porta frontal é compartilhada entre todos os seus clientes. No entanto, ao criar um perfil de porta frontal, você define a configuração específica necessária para seu aplicativo e nenhuma alteração feita na porta de frente afeta outras configurações de porta frontal.

### <a name="is-http-https-redirection-supported"></a>Há suporte para redirecionamento de HTTP-> HTTPS?

Sim. Na verdade, o serviço de porta frontal do Azure dá suporte ao host, caminho e redirecionamento de cadeia de caracteres de consulta, bem como parte do redirecionamento de URL. Saiba mais sobre o [redirecionamento de URL](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>Em que ordem as regras de roteamento são processadas?

As rotas para sua porta frontal não são ordenadas e uma rota específica é selecionada com base na melhor correspondência. Saiba mais sobre [como a porta frontal corresponde às solicitações para uma regra de roteamento](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Como fazer bloquear o acesso ao meu back-end para apenas a porta frontal do Azure?

Para bloquear seu aplicativo para aceitar o tráfego somente de sua porta de frente específica, você precisará configurar as ACLs de IP para o back-end e, em seguida, restringir o conjunto de valores aceitos para o cabeçalho ' X-Forwarded-host ' enviado pela porta frontal do Azure. Essas etapas são detalhadas como a seguir:

- Configure o IP atuação para seus back-ends para aceitar o tráfego do espaço de endereço IP de back-end da porta do Azure e dos serviços de infraestrutura do Azure apenas. Estamos trabalhando para integrar com [intervalos de IP e marcas de serviço do Azure](https://www.microsoft.com/download/details.aspx?id=56519) , mas, por enquanto, você pode consultar os intervalos de IP como abaixo:
 
    - Espaço IP de back-end **IPv4** da porta frontal: `147.243.0.0/16`
    - Espaço IP de back-end **IPv6** da porta frontal: `2a01:111:2050::/44`
    - Serviços de [infraestrutura básica](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) do Azure por meio de endereços IP de host virtualizados: `168.63.129.16` e `169.254.169.254`

    > [!WARNING]
    > O espaço de IP de back-end da porta frontal pode ser alterado mais tarde. no entanto, garantiremos que, antes disso, isso seria integrado aos [intervalos de IP e às marcas de serviço do Azure](https://www.microsoft.com/download/details.aspx?id=56519). Recomendamos que você assine [intervalos de IP do Azure e marcas de serviço](https://www.microsoft.com/download/details.aspx?id=56519) para quaisquer alterações ou atualizações.

-   Filtre os valores do cabeçalho de entrada '**X-Forwarded-host**' enviado pela porta frontal. Os únicos valores permitidos para o cabeçalho devem ser todos os hosts de front-end, conforme definido em sua configuração de porta frontal. Na verdade, ainda mais especificamente, apenas os nomes de host para os quais você deseja aceitar o tráfego, nesse back-end específico do seu.
    - Exemplo – digamos que sua configuração de porta frontal tenha os seguintes hosts de front-end _`contoso.azurefd.net`_ (A), _`www.contoso.com`_ (B), _ (C) e _`notifications.contoso.com`_ (D). Vamos supor que você tenha dois back-ends X e Y. 
    - O back-end X deve pegar apenas o tráfego dos nomes de host A e B. o back-end Y pode pegar o tráfego de A, C e D.
    - Portanto, no back-end X, você só deve aceitar o tráfego que tem o cabeçalho '**X-Forwarded-host**' definido como _`contoso.azurefd.net`_ ou _`www.contoso.com`_ . Para todo o resto, o back-end X deve rejeitar o tráfego.
    - Da mesma forma, no back-end Y, você só deve aceitar o tráfego que tem o cabeçalho "**X-Forwarded-host**" definido como _`contoso.azurefd.net`_ , _`api.contoso.com`_ ou _`notifications.contoso.com`_ . Para todo o resto, o back-end Y deve rejeitar o tráfego.

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>O IP anycast pode ser alterado durante o tempo de vida da minha porta frontal?

O IP de anycast de front-end para sua porta frontal normalmente não deve ser alterado e pode permanecer estático durante o tempo de vida da porta frontal. No entanto, **não há nenhuma garantia** para o mesmo. Não use nenhuma dependência direta no IP.

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>O serviço de porta frontal do Azure dá suporte a IPs estáticos ou dedicados?

Não, o serviço de porta frontal do Azure atualmente não dá suporte a IPs de anycast de frontend estáticos ou dedicados. 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>O serviço de porta frontal do Azure dá suporte a cabeçalhos x-Forwarded-for?

Sim, o serviço de porta frontal do Azure dá suporte aos cabeçalhos X-Forwardd-for, X-Forwarded-host e-encaminhar-proto. Para X-Forwarded – para se o cabeçalho já estiver presente, a porta frontal acrescentará o IP de soquete do cliente a ele. Caso contrário, ele adiciona o cabeçalho com o IP de soquete do cliente como o valor. Para o X-Forwarded-host e o protocolo X-Forwarded-proto, o valor é substituído.

Saiba mais sobre os [cabeçalhos HTTP com suporte da porta frontal](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>Quanto tempo leva para implantar um serviço de porta frontal do Azure? Minha porta frontal ainda funciona quando está sendo atualizada?

Uma nova criação de porta de front-end ou qualquer atualização de uma porta de frente existente leva cerca de 3 a 5 minutos para a implantação global. Isso significa que, em cerca de 3 a 5 minutos, sua configuração de porta frontal será implantada em todos os nossos POPs globalmente.

Observação-as atualizações personalizadas do certificado SSL levam cerca de 30 minutos para serem implantadas globalmente.

## <a name="configuration"></a>Configuração

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>O balanceamento de carga da porta do Azure pode balancear ou rotear o tráfego em uma rede virtual?

A porta frontal do Azure (AFD) requer um IP público ou um nome DNS que possa ser resolvido publicamente para rotear o tráfego. Portanto, a resposta não é um AFD diretamente não pode ser roteada em uma rede virtual, mas usar um gateway de aplicativo ou Azure Load Balancer no entre resolverá esse cenário.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>Quais são os vários tempos limite e limites para o serviço de porta frontal do Azure?

Saiba mais sobre todos os [tempos limite e limites documentados para o serviço de porta frontal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Desempenho

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Como o serviço de porta frontal do Azure dá suporte à alta disponibilidade e escalabilidade?

O serviço de porta frontal do Azure é uma plataforma multilocatário distribuída globalmente com grandes volumes de capacidade para atender às necessidades de escalabilidade de seu aplicativo. Entregue da borda da rede global da Microsoft, a porta da frente fornece o recurso de balanceamento de carga global que permite que você faça failover de todo o seu aplicativo ou mesmo de microserviços individuais entre regiões ou nuvens diferentes.

## <a name="ssl-configuration"></a>Configuração de SSL

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>Quais versões do TLS têm suporte do serviço de porta frontal do Azure?

Todos os perfis de porta frontal criados depois de setembro de 2019 usam TLS 1,2 como o mínimo padrão.

A porta frontal dá suporte às versões de TLS 1,0, 1,1 e 1,2. O TLS 1,3 ainda não tem suporte.

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Quais certificados têm suporte no serviço de porta frontal do Azure?

Para habilitar o protocolo HTTPS para fornecer conteúdo com segurança em um domínio personalizado de porta frontal, você pode optar por usar um certificado que é gerenciado pelo serviço de porta frontal do Azure ou usar seu próprio certificado.
A opção gerenciada de porta frontal provisiona um certificado SSL padrão via DigiCert e armazenada no Key Vault da porta frontal. Se você optar por usar seu próprio certificado, poderá integrar um certificado de uma AC com suporte e pode ser um SSL padrão, um certificado de validação estendido ou até mesmo um certificado curinga. Não há suporte para certificados autoassinados. Saiba [como habilitar o HTTPS para um domínio personalizado](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="does-front-door-support-autorotation-of-certificates"></a>A porta frontal dá suporte à rotação autocertificada de certificados?

Para a opção de certificado gerenciado de porta frontal, os certificados são girados de acordo com a porta frontal. Se você estiver usando um certificado gerenciado de porta de front-end e ver que a data de expiração do certificado está abaixo de 60 dias de folga, arquivo um tíquete de suporte.
</br>Para seu próprio certificado SSL personalizado, a rotação não tem suporte. Semelhante ao modo como ele foi configurado na primeira vez para um determinado domínio personalizado, você precisará apontar a porta da frente para a versão correta do certificado no seu Key Vault e garantir que a entidade de serviço para a porta frontal ainda tenha acesso ao Key Vault. Essa operação de distribuição de certificado atualizada pela porta frontal é atômica e não causa nenhum impacto de produção desde que o nome da entidade ou a SAN para o certificado não seja alterada.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Quais são os conjuntos de codificação atuais com suporte do serviço de porta frontal do Azure?

A seguir estão os conjuntos de codificação atuais com suporte do serviço de porta frontal do Azure:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>O serviço de porta frontal do Azure também dá suporte à recriptografia de tráfego para o back-end?

Sim, o serviço de porta frontal do Azure dá suporte ao descarregamento de SSL e ao SSL de ponta a ponta, que criptografa novamente o tráfego para o back-end. Na verdade, como as conexões com o back-end acontecem sobre o IP público, é recomendável que você configure sua porta frontal para usar HTTPS como o protocolo de encaminhamento.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Posso configurar a política SSL para controlar as versões do protocolo SSL?

Você pode configurar uma versão mínima do TLS na porta frontal do Azure por meio da [API REST do Azure](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). No momento, você pode escolher entre 1,0 e 1,2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Posso configurar a porta frontal para dar suporte apenas a conjuntos de codificação específicos?

Não, não há suporte para a configuração da porta frontal para conjuntos de codificação específicos. 

## <a name="diagnostics-and-logging"></a>Diagnóstico e registos

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>Quais tipos de métricas e logs estão disponíveis com o serviço de porta frontal do Azure?

Para obter informações sobre logs e outros recursos de diagnóstico, consulte [monitoramento de métricas e logs da porta frontal](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Qual é a política de retenção nos logs de diagnóstico?

Os logs de diagnóstico fluem para a conta de armazenamento dos clientes e os clientes podem definir a política de retenção com base em sua preferência. Os logs de diagnóstico também podem ser enviados para um hub de eventos ou logs de Azure Monitor. Para obter mais informações, consulte [diagnóstico do serviço de porta frontal do Azure](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>Como fazer obter logs de auditoria para o serviço de porta frontal do Azure?

Os logs de auditoria estão disponíveis para o serviço de porta frontal do Azure. No portal, clique em **log de atividades** na folha do menu da sua porta frontal para acessar o log de auditoria. 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>Posso definir alertas com o serviço de porta frontal do Azure?

Sim, o serviço de porta frontal do Azure dá suporte a alertas. Os alertas são configurados em métricas. 

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
