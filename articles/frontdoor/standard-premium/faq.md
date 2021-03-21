---
title: 'Azure Front Door: Perguntas frequentes'
description: Esta página fornece respostas a perguntas frequentes sobre Azure Front Door Standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 6f6d71dec9726f009ab9a56e0a49ba21f5d218fd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181028"
---
# <a name="frequently-asked-questions-for-azure-front-door-standardpremium-preview"></a>Perguntas frequentes para Azure Front Door Standard/Premium (Preview)

Este artigo responde a perguntas comuns sobre funcionalidades e funcionalidades da Azure Front Door.

## <a name="general"></a>Geral

### <a name="what-is-azure-front-door"></a>O que é o Azure Front Door?

Azure Front Door é um CDN de nuvem moderna rápido, fiável e seguro com proteção inteligente de ameaças. Fornece aceleração estática e dinâmica de conteúdo, equilíbrio global de carga e segurança reforçada para as suas aplicações globais de hiperescala, APIs, websites e serviços na nuvem com proteção inteligente de ameaças.

### <a name="what-features-does-azure-front-door-support"></a>Quais as funcionalidades que o Azure Front Door suporta?

Suportes da Porta Frontal Azure:

* Conteúdo estático e aceleração dinâmica da aplicação
* Descarga TLS/SSL e fim para o fim do TLS
* Firewall de Aplicações Web
* Afinidade da sessão baseada em cookies
* Encaminhamento baseado em caminhos de url
* Certificados gratuitos e múltiplas gestões de domínio

Para obter uma lista completa de funcionalidades suportadas, consulte [a visão geral da Porta frontal Azure](overview.md).

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Qual é a diferença entre Azure Front Door e Azure Application Gateway?

Embora tanto a Porta Frontal como o Gateway de aplicação sejam equilibradores de carga da camada 7 (HTTP/HTTPS), a principal diferença é que a Porta Frontal é um serviço global. Application Gateway é um serviço regional. Enquanto a Porta Frontal pode carregar o equilíbrio entre as suas diferentes unidades de escala/clusters/unidades de selo em regiões, o Gateway de Aplicação permite-lhe carregar o equilíbrio entre os seus VM/contentores que se encontra dentro da unidade de escala.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Quando devemos colocar um Gateway de aplicação atrás da porta da frente?

Os cenários-chave para usar o Gateway de Aplicação atrás da Porta frontal são:

* A Porta Frontal pode fazer um equilíbrio de carga baseado no caminho apenas a nível global, mas se se quiser carregar ainda mais o tráfego de equilíbrio dentro da sua rede virtual (VNET), então deve usar o Gateway de Aplicação.
* Como a Porta frontal não funciona a um nível VM/contentor, por isso não pode fazer drenagem de ligação. No entanto, o Gateway de Aplicação permite-lhe fazer drenagem de ligação. 
* Com um Gateway de aplicação atrás da porta frontal, pode-se alcançar 100% de descarregamento TLS/SSL e apenas pedidos HTTP dentro da sua rede virtual (VNET).
* Porta da frente e gateway de aplicação ambos suportam afinidade da sessão. A Porta frontal pode direcionar o tráfego de uma sessão de utilizador para o mesmo cluster ou backend em uma determinada região. O Gateway de Aplicação pode direcionar a afinidade do tráfego para o mesmo servidor dentro do cluster.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Podemos colocar o Azure Load Balancer atrás da porta da frente?

AZure Front Door precisa de um VIP público ou um nome DNS disponível ao público para encaminhar o tráfego para. A colocação de um balançador de carga Azure atrás da porta frontal é um caso de uso comum.

### <a name="what-protocols-does-azure-front-door-support"></a>Que protocolos suporta o Azure Front Door?

Azure Front Door suporta HTTP, HTTPS e HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>Como suporta a Porta Frontal Azure HTTP/2?

O suporte ao protocolo HTTP/2 está disponível apenas para clientes que se ligam à Porta Frontal do Azure. A comunicação para backends na piscina de backend está sobre HTTP/1.1. O suporte HTTP/2 é ativado por predefinição.

### <a name="what-resources-are-supported-today-as-part-of-origin-group"></a>Que recursos são hoje apoiados como parte do grupo de origem?

O grupo de origem pode ser composto por Storage, Web App, instâncias Kubernetes ou qualquer outro nome de anfitrião personalizado que tenha conectividade pública. A porta frontal azure requer que as origens sejam definidas através de um IP público ou de um nome de anfitrião DNS publicamente resolúvel. Os membros do grupo de origem podem estar em zonas, regiões ou mesmo fora de Azure, desde que tenham conectividade pública.

### <a name="what-regions-is-the-service-available-in"></a>Em que regiões está disponível o serviço?

Azure Front Door é um serviço global e não está ligado a nenhuma região específica de Azure. A única localização que precisa de especificar ao criar uma Porta frontal é para o grupo de recursos. Essa localização está basicamente a especificar onde os metadados do grupo de recursos serão armazenados. O recurso Front Door em si é criado como um recurso global e a configuração é implantada globalmente para todos os POPs (Ponto de Presença). 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Quais são os locais pop para Azure Front Door?

A Azure Front Door tem a mesma lista de localizações POP (Ponto de Presença) que o Azure CDN da Microsoft. Para a lista completa dos nossos POPs, por favor, consulte as [localizações pop do Azure CDN da Microsoft](../../cdn/cdn-pop-locations.md).

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>A Azure Front Door é uma implementação dedicada para a minha aplicação ou é partilhada entre os clientes?

Azure Front Door é um serviço multi-inquilino distribuído globalmente. A infraestrutura para Porta da Frente é partilhada em todos os seus clientes. Ao criar um perfil da Porta frontal, está a definir a configuração específica necessária para a sua aplicação. As alterações efetuadas na porta da frente não afetam outras configurações da porta frontal.

### <a name="is-http-https-redirection-supported"></a>Http->reorientação HTTPS suportada?

Sim. Na verdade, a Porta Frontal Azure suporta o hospedeiro, o caminho, a reorientação das cordas de consulta e parte da reorientação do URL. Saiba mais sobre [a reorientação de URL.](concept-rule-set-url-redirect-and-rewrite.md) 

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Como posso bloquear o acesso ao meu backend para apenas Azure Front Door?

A melhor maneira de bloquear a sua aplicação para aceitar o tráfego apenas a partir da sua instância específica da Porta da Frente é publicar a sua aplicação através do Private Endpoint. O tráfego de rede entre a Porta Frontal e a aplicação atravessa o VNet e uma Ligação Privada na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública.

Saiba mais sobre [a origem de segurança para porta frontal com ligação privada.](concept-private-link.md)  

Forma alternativa de bloquear a sua aplicação para aceitar o tráfego apenas a partir da sua porta frontal específica, terá de configurar ACLs IP para o seu backend. Em seguida, restringir o tráfego do seu backend ao valor específico do cabeçalho 'X-Azure-FDID' enviado pela Porta da Frente. Estes passos são detalhados como abaixo:

* Configure o IP ACLing para que os seus backends aceitem o tráfego do espaço ip de backend da Azure Front Door e apenas os serviços de infraestrutura do Azure. Consulte os detalhes ip abaixo para ACLing o seu backend:
 
    * Consulte a secção *AzureFrontDoor.Backend* em [intervalos IP Azure e tags](https://www.microsoft.com/download/details.aspx?id=56519) de serviço para o intervalo de endereço IP de backend IPv4 da porta frontal. Também pode utilizar a etiqueta de serviço *AzureFrontDoor.Backend* nos seus [grupos de segurança de rede.](../../virtual-network/network-security-groups-overview.md#security-rules)
    * Os [serviços básicos de infraestrutura](../../virtual-network/network-security-groups-overview.md#azure-platform-considerations) do Azure através de endereços IP de hospedeiros virtualizados: `168.63.129.16` e `169.254.169.254` .

    > [!WARNING]
    > O espaço IP backend da Porta frontal pode mudar mais tarde, no entanto, vamos garantir que, antes disso, teríamos integrado com [as Gamas IP E Tags Azure](https://www.microsoft.com/download/details.aspx?id=56519)IP . Recomendamos que subscreva as [Gamas IP E Tags Azure](https://www.microsoft.com/download/details.aspx?id=56519) para quaisquer alterações ou atualizações.

* Faça uma operação GET na sua Porta frontal com a versão API `2020-01-01` ou superior. Na chamada da API, procure `frontdoorID` campo. Filtrar no cabeçalho de entrada '**X-Azure-FDID**' enviado pela Porta da Frente para o seu backend com o valor do campo `frontdoorID` . Também pode encontrar `Front Door ID` valor na secção Overview a partir da página do portal porta da porta da frente. 

* Aplique a filtragem de regras no seu servidor web backend para restringir o tráfego com base no valor do cabeçalho 'X-Azure-FDID'.

  Aqui está um exemplo para os Serviços de [Informação da Internet da Microsoft (IIS)](https://www.iis.net/):

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <rule name="Filter_X-Azure-FDID" patternSyntax="Wildcard" stopProcessing="true">
                        <match url="*" />
                        <conditions>
                            <add input="{HTTP_X_AZURE_FDID}" pattern="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" negate="true" />
                        </conditions>
                        <action type="AbortRequest" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration>
    ```

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>O IP pode mudar ao longo da vida da minha Porta da Frente?

O frontend qualquer IP para a porta da frente normalmente não deve mudar e pode permanecer estático durante o tempo de vida da porta da frente. No entanto, não há **garantias** para o mesmo. Por favor, não tenha dependências diretas do IP.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>A Azure Front Door suporta IPs estáticos ou dedicados?

Não, a Azure Front Door atualmente não suporta iPs estáticos ou dedicados. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>A Azure Front Door suporta cabeçalhos x-forward-for?

Sim, a Porta Frontal Azure suporta os cabeçalhos X-Forwarded-For, X-Forwarded-Host e X-Forwarded-Proto. Para x-Forward-For se o cabeçalho já estava presente, então a Porta frontal anexa o IP da tomada do cliente. Caso contrário, adiciona o cabeçalho com a tomada de cliente IP como valor. Para X-Forward-Host e X-Forwarded-Proto, o valor é ultrapassado.  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Quanto tempo demora a implantar uma Porta frontal Azure? A minha porta da frente ainda funciona quando é atualizada?

Uma nova criação da Porta Frontal ou quaisquer atualizações para uma Porta Frontal existente demora cerca de 3 a 5 minutos para a implantação global. Isto significa que em cerca de 3 a 5 minutos, a configuração da porta da frente será implantada em todos os nossos POPs a nível global.

Nota - As atualizações personalizadas do certificado TLS/SSL demoram cerca de 30 minutos a ser implementadas globalmente.

Quaisquer atualizações para rotas ou piscinas de backend são perfeitas e causarão zero tempo de inatividade (se a nova configuração estiver correta). As atualizações de certificados não causarão qualquer interrupção, a não ser que esteja a mudar de 'Azure Front Door Managed' para 'Use o seu próprio certificado' ou o contrário.


## <a name="configuration"></a>Configuração

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>O Azure Front Door pode equilibrar o equilíbrio ou o tráfego de rotas dentro de uma rede virtual?

AZure Front Door (AFD) requer um IP público ou um nome DNS publicamente resolúvel para encaminhar o tráfego. A Azure Front Door não pode encaminhar diretamente para recursos numa rede virtual. Pode utilizar um Gateway de Aplicações ou um Equilibrador de Carga Azure com um IP público para resolver este problema.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Quais são os vários intervalos e limites para a Porta frontal Azure?

Saiba mais sobre todos os [intervalos e limites documentados para a Porta frontal Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits).

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>Quanto tempo demora uma regra a entrar em vigor depois de ter sido adicionada ao motor das regras da porta da frente?

A configuração do Motor regras demora cerca de 10 a 15 minutos a concluir uma atualização. Pode esperar que a regra entre em vigor assim que a atualização estiver concluída. 

### <a name="can-i-configure-azure-cdn-behind-my-front-door-profile-or-front-door-behind-my-azure-cdn"></a>Posso configurar o Azure CDN atrás do meu perfil da porta da frente ou porta da frente atrás do meu CDN Azure?

A Azure Front Door e Azure CDN não podem ser configurados em conjunto porque ambos os serviços usam os mesmos sites de borda Azure ao responder a pedidos. 

## <a name="performance"></a>Desempenho

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Como é que o Azure Front Door suporta alta disponibilidade e escalabilidade?

Azure Front Door é uma plataforma multi-arrendatário distribuída globalmente com uma enorme capacidade de atender às necessidades de escalabilidade da sua aplicação. Entregue a partir da borda da rede global da Microsoft, a Front Door fornece uma capacidade global de equilíbrio de carga que permite que você falhe em toda a sua aplicação ou mesmo microserviços individuais em regiões ou diferentes nuvens.

## <a name="tls-configuration"></a>Configuração TLS

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Que versões TLS são suportadas pela Azure Front Door?

Todos os perfis front door criados após setembro de 2019 utilizam o TLS 1.2 como mínimo de incumprimento.

Porta frontal suporta as versões TLS 1.0, 1.1 e 1.2. TLS 1.3 ainda não está apoiado.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Que certificados são suportados na Porta frontal Azure?

Para ativar o protocolo HTTPS num domínio personalizado da Porta Frontal, pode escolher um certificado que seja gerido pela Porta Frontal Azure ou utilize o seu próprio certificado.
A porta da frente geriu disposições de um certificado padrão TLS/SSL via Digicert e armazenado no Cofre da Porta Frontal. Se optar por utilizar o seu próprio certificado, então pode embarcar num certificado de um CA suportado e pode ser um TLS padrão, certificado de validação alargado ou até mesmo um certificado wildcard. Os certificados auto-assinados não são suportados.

### <a name="does-front-door-support-autorotation-of-certificates"></a>A Porta frontal suporta a autorotação de certificados?

Para a opção de certificado gerido pela Porta da Frente, os certificados são autonotados pela Porta frontal. Se estiver a utilizar um certificado gerido pela Porta da Frente e se o certificado tiver uma data de validade inferior a 60 dias, preencha um bilhete de apoio.

Para o seu certificado TLS/SSL personalizado, a autorotação não é suportada. Tal como configura a primeira vez para um determinado domínio personalizado, terá de apontar a Porta frontal para a versão de certificado certo no seu Key Vault. Certifique-se de que o diretor de serviço da Porta frontal ainda tem acesso ao Cofre da Chave. Esta operação atualizada de lançamento de certificados pela Front Door não causa qualquer tempo de produção, desde que o nome do sujeito ou SAN para o certificado não se altere.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Quais são as atuais suítes de cifra suportadas pela Porta frontal Azure?

Para tls1.2 são suportadas as seguintes suítes cifradas: 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

Utilizando domínios personalizados com TLS1.0/1.1 ativado, suportam-se as seguintes suítes cifradas:

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

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Posso configurar a política TLS para controlar as versões do Protocolo TLS?

Pode configurar uma versão TLS mínima na Porta Frontal Azure nas definições HTTPS de domínio personalizado utilizando o portal Azure ou a [API AZure REST](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). Atualmente, pode escolher entre 1.0 e 1.2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Posso configurar a Porta da Frente apenas para suportar suítes de cifra específicas?

Não, configurar a Porta da Frente para suites de cifra específicas não é suportado. Pode obter o seu certificado TLS/SSL personalizado da sua Autoridade de Certificados (por exemplo, Verisign, Entrust ou Digicert). Em seguida, tenha suítes de cifra específicas marcadas no certificado quando o gerar. 

### <a name="does-front-door-support-ocsp-stapling"></a>A Porta frontal suporta agrafamento oCSP?

Sim, o agrafamento OCSP é suportado por padrão pela Porta frontal e não é necessária nenhuma configuração.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>O Azure Front Door também suporta a reencriminação do tráfego para o backend?

Sim, a Porta Frontal Azure suporta o descarregamento TLS/SSL e o fim para o fim do TLS, que reencripta o tráfego até ao backend. Uma vez que as ligações com o backend acontecem durante o IP público, recomenda-se que configuure a porta da frente para usar HTTPS como protocolo de encaminhamento.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>A Porta frontal suporta certificados auto-assinados no backend para ligação HTTPS?

Não, os certificados auto-assinados não são suportados na Porta da Frente e a restrição aplica-se a ambos:

* **Backends**: Não é possível utilizar certificados auto-assinados quando está a encaminhar o tráfego como sondas sanitárias HTTPS ou HTTPS ou a preencher a cache de origem para regras de encaminhamento com caching ativada.
* **Frontend**: Não é possível utilizar certificados auto-assinados quando utilizar o seu certificado TLS/SSL personalizado para permitir HTTPS no seu domínio personalizado.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Porque é que o tráfego HTTPS para o meu backend está a falhar?

Para ter ligações HTTPS bem sucedidas ao seu backend, seja para sondas de saúde ou para reencaminhamento de pedidos, pode haver duas razões pelas quais o tráfego HTTPS pode falhar:

* **Incompatibilidade do nome do sujeito do certificado**: Para ligações HTTPS, a Porta Frontal espera que o seu backend apresente um certificado de um CA válido com o(s) nome(s) sujeito correspondente ao nome de hospedeiro de backend. Como exemplo, se o seu nome de anfitrião de backend estiver definido `myapp-centralus.contosonews.net` e o certificado que o seu backend apresenta durante o aperto de mão TLS não tem ou no nome do `myapp-centralus.contosonews.net` `*myapp-centralus*.contosonews.net` sujeito. Em seguida, a Porta frontal recusará a ligação e resultará num erro. 
    * **Solução**: Não é recomendado do ponto de vista da conformidade, mas pode contornar este erro desativando o nome do sujeito do certificado para a sua porta frontal. Pode encontrar esta opção em Definições no portal Azure e em BackendPoolsSettings na API.
* **Certificado de hospedagem backend da AC inválida**: Só podem ser utilizados certificados de [certificados válidos As autoridades](troubleshoot-allowed-certificate-authority.md) podem ser utilizadas no backend com a Porta frontal. Não são permitidos certificados de CAs internos ou certificados auto-assinados.

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>Posso usar a autenticação cliente/mútua com a Porta frontal Azure?

N.º Embora a Azure Front Door suporte o TLS 1.2, que introduziu a autenticação cliente/mútua no [RFC 5246,](https://tools.ietf.org/html/rfc5246)atualmente, a Azure Front Door não suporta a autenticação cliente/mútua.

## <a name="diagnostics-and-logging"></a>Diagnóstico e registos

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Que tipos de métricas e registos estão disponíveis com a Porta frontal Azure?

Para obter informações sobre registos e outras capacidades de diagnóstico, consulte métricas de monitorização e registos para a Porta frontal.

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Qual é a política de retenção nos registos de diagnóstico?

Os registos de diagnóstico fluem para a conta de armazenamento dos clientes e os clientes podem definir a política de retenção com base na sua preferência. Os registos de diagnóstico também podem ser enviados para um Centro de Eventos ou registos do Monitor Azure. Para mais informações, consulte [o Registo da Porta frontal Azure.](how-to-logs.md)

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Como consigo registos de auditoria para a Porta frontal do Azure?

Os registos de auditoria estão disponíveis para a Porta frontal Azure. No portal, selecione **''' 'Registar'** atividades na página do menu da porta da frente para aceder ao registo de auditoria. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Posso definir alertas com a porta da frente Azure?

Sim, a Porta frontal Azure faz alertas de apoio. Os alertas estão configurados em métricas. 

## <a name="next-steps"></a>Passos seguintes

Saiba como [criar um Padrão/Premium da Porta da Frente.](create-front-door-portal.md)
