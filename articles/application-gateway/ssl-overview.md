---
title: Habilitar fim ao TLS no Portal de Aplicação Azure
description: Este artigo é uma visão geral do gateway de aplicação final para acabar com o suporte TLS.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 5/13/2020
ms.author: victorh
ms.openlocfilehash: adaf3dea5855a4af75977cb820ae12675c7f2ced
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648130"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Visão geral da rescisão de TLS e fim para terminar TLS com Gateway de Aplicação

A Segurança da Camada de Transporte (TLS), anteriormente conhecida como Secure Sockets Layer (SSL), é a tecnologia de segurança padrão para estabelecer uma ligação encriptada entre um servidor web e um navegador. Este link garante que todos os dados transmitidos entre o servidor web e os navegadores permanecem privados e encriptados. O gateway da aplicação suporta a rescisão de TLS no gateway, bem como a encriptação TLS final.

## <a name="tls-termination"></a>TLS rescisão

O Gateway de aplicação suporta a rescisão de TLS no gateway, após o qual o tráfego normalmente flui desencriptado para os servidores de backend. Existem várias vantagens em fazer a rescisão de TLS no gateway da aplicação:

- **Melhor desempenho** – O maior sucesso de desempenho ao fazer a desencriptação do TLS é o aperto de mão inicial. Para melhorar o desempenho, o servidor que faz os iDs da sessão tLS caches de desencriptação e gere os bilhetes de sessão TLS. Se isso for feito no gateway da aplicação, todos os pedidos do mesmo cliente podem utilizar os valores em cache. Se for feito nos servidores de backend, então cada vez que os pedidos do cliente vão para um servidor diferente o cliente deve reautenticar. A utilização de bilhetes TLS pode ajudar a mitigar este problema, mas não são suportados por todos os clientes e podem ser difíceis de configurar e gerir.
- **Melhor utilização dos servidores de backend** – O processamento de SSL/TLS é muito intensivo em CPU e está a tornar-se mais intensivo à medida que os tamanhos das teclas aumentam. Remover este trabalho dos servidores de backend permite-lhes concentrar-se no que são mais eficientes, fornecendo conteúdo.
- **Encaminhamento inteligente** – Ao desencriptar o tráfego, o portal da aplicação tem acesso aos conteúdos de pedido, tais como cabeçalhos, URI, e assim por diante, e pode usar esses dados para encaminhar pedidos.
- **Gestão de certificados** – Os certificados só precisam de ser adquiridos e instalados no gateway da aplicação e nem todos os servidores de backend. Isto poupa tempo e dinheiro.

Para configurar a rescisão de TLS, é necessário adicionar ao ouvinte um certificado TLS/SSL para permitir que o Gateway de Aplicação obtenha uma chave simétrica de acordo com a especificação do protocolo TLS/SSL. A chave simétrica é então usada para encriptar e desencriptar o tráfego enviado para o portal. O certificado TLS/SSL tem de estar no formato Personal Information Exchange (PFX). Este formato de ficheiro permite-lhe exportar a chave privada que é exigida pela porta de entrada de aplicação para realizar a encriptação e desencriptação do tráfego.

> [!IMPORTANT] 
> Por favor, note que o certificado no ouvinte requer que toda a cadeia de certificados seja carregada. 


> [!NOTE] 
>
> O gateway de aplicação não fornece qualquer capacidade para criar um novo certificado ou enviar um pedido de certificado para uma autoridade de certificação.

Para que a ligação TLS funcione, é necessário garantir que o certificado TLS/SSL satisfaz as seguintes condições:

- Que a data e a hora em vigor estão dentro da gama de data "Válido de" e "Válido para" no certificado.
- O “Nome Comum” (NC) do certificado corresponde ao cabeçalho do anfitrião no pedido. Por exemplo, se o cliente estiver a realizar um pedido para `https://www.contoso.com/`, o NC tem de ser `www.contoso.com`.

### <a name="certificates-supported-for-tls-termination"></a>Certificados suportados para a rescisão de TLS

O gateway de aplicação suporta os seguintes tipos de certificados:

- Certificado CA (Autoridade de Certificados): Um certificado CA é um certificado digital emitido por uma autoridade de certificados (CA)
- Certificado EV (Validação Alargada): Um certificado EV é um certificado que está em conformidade com as diretrizes de certificados padrão da indústria. Isto tornará o bar de localização do navegador verde e publicará o nome da empresa também.
- Certificado Wildcard: Este certificado suporta qualquer número de subdomínios baseados em *.site.com, onde o seu subdomínio substituiria o *. No entanto, não suporta site.com, pelo que, caso os utilizadores acedam ao seu website sem digitar o "www", o certificado wildcard não cobre isso.
- Certificados auto-assinados: Os navegadores de clientes não confiam nestes certificados e vão avisar o utilizador de que o certificado do serviço virtual não faz parte de uma cadeia fiduciário. Os certificados auto-assinados são bons para testes ou ambientes onde os administradores controlam os clientes e podem contornar com segurança os alertas de segurança do navegador. As cargas de trabalho de produção nunca devem utilizar certificados auto-assinados.

Para mais informações, consulte [a configuração da terminação do TLS com o gateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)da aplicação .

### <a name="size-of-the-certificate"></a>Tamanho do certificado
Verifique a secção limites de gateway de [aplicação](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) para saber o tamanho máximo do certificado TLS/SSL suportado.

## <a name="end-to-end-tls-encryption"></a>Encriptação TLS de ponta a ponta

Pode não querer uma comunicação não encriptada para os servidores de backend. Pode ter requisitos de segurança, requisitos de conformidade ou o pedido só pode aceitar uma ligação segura. O Portal de Aplicações Azure tem encriptação TLS de ponta a ponta para suportar estes requisitos.

O TLS de ponta a ponta permite-lhe encriptar e transmitir de forma segura dados sensíveis para o backend enquanto utiliza as funcionalidades de equilíbrio de carga layer-7 do Application Gateway. Estas funcionalidades incluem afinidade de sessão baseada em cookies, encaminhamento baseado em URL, suporte para encaminhamento com base em sites, a capacidade de reescrever ou injetar cabeçalhos X-Forwarded-* e assim por diante.

Quando configurado com o modo de comunicação TLS de ponta a ponta, o Gateway da aplicação termina as sessões TLS no gateway e desencripta o tráfego do utilizador. Em seguida, aplica as regras configuradas para selecionar uma instância de conjunto de back-end adequada para encaminhar o tráfego. O Gateway de aplicação inicia então uma nova ligação TLS ao servidor backend e reencripta dados utilizando o certificado de chave pública do servidor de backend antes de transmitir o pedido para o backend. Qualquer resposta do servidor Web atravessa o mesmo processo para o utilizador final. O TLS de ponta a ponta é ativado através da definição de protocolo na definição de [BACKEnd HTTP](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) para HTTPS, que é depois aplicada a um pool de backend.

Para o Gateway de Aplicação e WAF v1 SKU, a política TLS aplica-se tanto ao tráfego frontal como ao tráfego de backend. Na parte frontal, o Application Gateway atua como servidor e aplica a política. No backend, application Gateway atua como cliente e envia a informação protocolar/cifra como a preferência durante o aperto de mão TLS.

Para o Gateway de Aplicação e WAF v2 SKU, a política TLS aplica-se apenas ao tráfego frontal e todas as cifras são oferecidas ao servidor backend, que tem controlo para selecionar cifras específicas e versão TLS durante o aperto de mão.

O Application Gateway comunica apenas com os servidores backend que tenham listado o seu certificado com o Gateway de Aplicação ou cujos certificados sejam assinados por autoridades de CA bem conhecidas e o NC do certificado corresponde ao nome do anfitrião nas definições de backend HTTP. Estes incluem os serviços azure confiáveis, tais como O Serviço de Aplicações Azure/Web Apps e A Gestão Da API Azure.

Se os certificados dos membros do pool de backend não forem assinados por autoridades de A bem conhecidas, cada instância na piscina de backend com tLS de extremidade ativada deve ser configurada com um certificado que permita uma comunicação segura. A adição do certificado garante que o gateway da aplicação comunica apenas com instâncias conhecidas. Isto assegura ainda a comunicação de ponta a ponta.

> [!NOTE] 
>
> O certificado adicionado ao **Backend HTTP Definição** para autenticar os servidores de backend pode ser o mesmo que o certificado adicionado ao **ouvinte** para a rescisão de TLS no gateway da aplicação ou diferente para uma segurança reforçada.

![final para fim do cenário TLS][1]

Neste exemplo, os pedidos que utilizam o TLS1.2 são encaminhados para servidores de backend no Pool1 utilizando o TLS final.

## <a name="end-to-end-tls-and-whitelisting-of-certificates"></a>Fim do TLS final e lista branca de certificados

Application Gateway apenas comunica com instâncias conhecidas de backend que tenham listado o seu certificado com o gateway de aplicação. Existem algumas diferenças no processo de configuração tLS de ponta a ponta no que diz respeito à versão do Gateway de aplicação utilizada. A secção seguinte explica-os individualmente.

## <a name="end-to-end-tls-with-the-v1-sku"></a>TLS de ponta a ponta com o V1 SKU

Para permitir o TLS de ponta a ponta com os servidores backend e para o Gateway de Aplicação para encaminhar os seus pedidos, as sondas de saúde devem ter sucesso e devolver uma resposta saudável.

Para as sondas de saúde HTTPS, o Gateway de aplicação v1 SKU utiliza uma correspondência exata do certificado de autenticação (chave pública do certificado de servidor de backend e não do certificado raiz) para ser enviado para as definições HTTP.

Apenas são permitidas ligações a back-ends conhecidos e na lista de permissões. Os restantes backends são considerados insalubres pelas sondas de saúde. Os certificados autoassinados são apenas para fins de teste e não são recomendados para cargas de trabalho de produção. Esses certificados devem ser listados com o gateway de aplicação, tal como descrito nos passos anteriores antes de poderem ser utilizados.

> [!NOTE]
> A autenticação e a configuração de certificados de raiz fidedignos não são necessárias para serviços Azure fidedignos, como o Azure App Service. São considerados de confiança por defeito.

## <a name="end-to-end-tls-with-the-v2-sku"></a>TLS de ponta a ponta com o V2 SKU

Os Certificados de Autenticação foram depreciados e substituídos por Certificados de Raiz Fidedignos no Gateway de Aplicação v2 SKU. Funcionam da mesma forma com os Certificados de Autenticação com algumas diferenças fundamentais:

- Os certificados assinados por autoridades de CA bem conhecidas cujo NC corresponde ao nome do anfitrião nas definições de backend HTTP não requerem qualquer passo adicional para o fim do TLS para o trabalho. 

   Por exemplo, se os certificados de backend forem emitidos por um CA bem conhecido e tiver um CN de contoso.com, e o campo de acolhimento do backend http configurar também está definido para contoso.com, então não são necessários passos adicionais. Pode definir o protocolo de definição de http de backend para HTTPS e tanto a sonda de saúde como o caminho dos dados estariam ativados por TLS. Se estiver a utilizar o Azure App Service ou outros serviços web Azure como seu backend, então estes são implicitamente confiáveis também e não são necessários mais passos para o fim do TLS final.
   
> [!NOTE] 
>
> Para que seja fidedigno um certificado TLS/SSL, esse certificado do servidor backend deve ter sido emitido por um CA que seja bem conhecido. Se o certificado não foi emitido por um CA de confiança, o gateway de aplicação verificará então para verificar se o certificado da AC emissora foi emitido por um CA de confiança, e assim por diante até que seja encontrada uma AC fidedigna (altura em que será estabelecida uma ligação segura e fidedigna) ou se não pode ser encontrada uma AC fidedigna (altura em que o gateway de aplicação marcará o backend insalubre). Portanto, recomenda-se que o certificado de servidor de backend contenha tanto os CAs radiculares como os caqueis intermédios.

- Se o certificado for auto-assinado ou assinado por intermediários desconhecidos, então para permitir tLS de ponta a ponta no V2 SKU deve ser definido um certificado raiz de confiança. Application Gateway comunica apenas com backends cujo certificado de raiz do certificado de servidor corresponde a uma das listas de certificados de raiz fidedignos na definição de http de backend associada à piscina.

- Além da correspondência do certificado raiz, o Gateway v2 de aplicação também valida se a definição do Anfitrião especificada no backend http definição corresponde à do nome comum (CN) apresentado pelo certificado TLS/SSL do servidor backend. Ao tentar estabelecer uma ligação TLS ao backend, o Gateway v2 de aplicação define a extensão de indicação de nome do servidor (SNI) ao Anfitrião especificada na definição de http backend.

- Se escolher o **nome de anfitrião do endereço backend** em vez do campo Anfitrião na definição de http end, então o cabeçalho SNI é sempre definido para o pool de backend FQDN e o NC no certificado tLS/SSL do servidor de backend deve corresponder ao seu FQDN. Os membros do backend pool com IPs não são apoiados neste cenário.

- O certificado de raiz é um certificado de raiz codificado base64 dos certificados do servidor backend.

## <a name="sni-differences-in-the-v1-and-v2-sku"></a>Diferenças SNI no V1 e v2 SKU

Como mencionado anteriormente, o Application Gateway encerra o tráfego tLS do cliente no Application Gateway Listener (vamos chamá-lo de ligação frontal), desencripta o tráfego, aplica as regras necessárias para determinar o servidor backend a que o pedido tem de ser encaminhado, e estabelece uma nova sessão de TLS com o servidor backend (vamos chamá-lo de ligação de backend).

As tabelas seguintes descrevem as diferenças no SNI entre o V1 e o V2 SKU em termos de ligações frontendas e backend.

### <a name="frontend-tls-connection-client-to-application-gateway"></a>Ligação Frontend TLS (cliente à porta de aplicação)

---
Cenário | v1 | v2 |
| --- | --- | --- |
| Se o cliente especificar o cabeçalho SNI e todos os ouvintes multi-sites estiverem ativados com a bandeira "RequireS SNI" | Devolva o certificado apropriado e se o site não existir (de acordo com a server_name), então a ligação é redefinida. | Devoluções de certificado apropriado se disponível, caso contrário, devolve o certificado do primeiro ouvinte HTTPS configurado (por ordem)|
| Se o cliente não especificar um cabeçalho SNI e se todos os cabeçalhos multi-locais estiverem ativados com "Require SNI" | Repõe a ligação | Devolve o certificado do primeiro ouvinte HTTPS configurado (por ordem)
| Se o cliente não especificar o cabeçalho SNI e se houver um ouvinte básico configurado com um certificado | Devolve o certificado configurado no ouvinte básico ao cliente (certificado de incumprimento ou recuo) | Devolve o certificado do primeiro ouvinte HTTPS configurado (por ordem) |

### <a name="backend-tls-connection-application-gateway-to-the-backend-server"></a>Ligação TLS backend (porta de entrada de aplicação para o servidor backend)

#### <a name="for-probe-traffic"></a>Para o tráfego de sonda

---
Cenário | v1 | v2 |
| --- | --- | --- |
| Cabeçalho SNI (server_name) durante o aperto de mão TLS como FQDN | Definir como FQDN da piscina de backend. De acordo com os endereços [RFC 6066](https://tools.ietf.org/html/rfc6066), os endereços iPv4 e IPv6 literais não são permitidos em nome de anfitrião SNI. <br> **Nota:** FQDN na piscina de backend caso o DNS decida apoiar o endereço IP do servidor (público ou privado) | O cabeçalho SNI (server_name) é definido como o nome de anfitrião da sonda personalizada anexada às definições HTTP (se configurado), caso contrário a partir do nome de anfitrião mencionado nas definições HTTP, caso contrário a partir do FQDN mencionado na piscina de backend. A ordem de precedência é sonda personalizada > definições de HTTP > piscina de backend. <br> **Nota:** Se os nomes de anfitriões configurados nas definições http e sonda personalizada forem diferentes, então de acordo com a precedência, o SNI será definido como o nome de anfitrião da sonda personalizada.
| Se o endereço de piscina de backend for um endereço IP (v1) ou se o nome de anfitrião da sonda personalizada estiver configurado como endereço IP (v2) | O SNI (server_name) não será definido. <br> **Nota:** Neste caso, o servidor backend deve poder devolver um certificado de retorno/retorno predefinido e este deve ser listado em definições http sob certificado de autenticação. Se não houver nenhum certificado predefinido/recuo configurado no servidor backend e sni é esperado, o servidor pode redefinir a ligação e irá levar a falhas de sonda | Na ordem de precedência mencionada anteriormente, se tiverem endereço IP como nome de anfitrião, então o SNI não será definido de acordo com [o RFC 6066](https://tools.ietf.org/html/rfc6066). <br> **Nota:** O SNI também não será definido em sondas v2 se nenhuma sonda personalizada estiver configurada e nenhum nome de anfitrião for definido nas definições de HTTP ou piscina de backend |

> [!NOTE] 
> Se uma sonda personalizada não estiver configurada, então o Application Gateway envia uma sonda padrão neste formato - \< protocolo \> ://127.0.0.1: \< porta \> /. Por exemplo, para uma sonda HTTPS predefinida, será enviada como https://127.0.0.1:443/ . Note que, o 127.0.0.1 aqui mencionado é usado apenas como cabeçalho de hospedeiro HTTP e de acordo com o RFC 6066, não será utilizado como cabeçalho SNI. Para obter mais informações sobre os erros da sonda de [saúde,](application-gateway-backend-health-troubleshooting.md)consulte o guia de resolução de problemas de saúde .

#### <a name="for-live-traffic"></a>Para tráfego ao vivo

---
Cenário | v1 | v2 |
| --- | --- | --- |
| Cabeçalho SNI (server_name) durante o aperto de mão TLS como FQDN | Definir como FQDN da piscina de backend. De acordo com os endereços [RFC 6066](https://tools.ietf.org/html/rfc6066), os endereços iPv4 e IPv6 literais não são permitidos em nome de anfitrião SNI. <br> **Nota:** FQDN na piscina de backend caso o DNS decida apoiar o endereço IP do servidor (público ou privado) | O cabeçalho SNI (server_name) é definido como o nome de anfitrião das definições HTTP, caso contrário, se a opção *PickHostNameFromBackendAddress* for escolhida ou se não for mencionado nenhum nome de anfitrião, então será definido como fQDN na configuração do pool backend
| Se o endereço de piscina de backend for um endereço IP ou nome de anfitrião não está definido nas definições http | O SNI não será definido de acordo com o [RFC 6066](https://tools.ietf.org/html/rfc6066) se a entrada na piscina de backend não for um FQDN | O SNI será definido como o nome de anfitrião da entrada FQDN do cliente e o NC do certificado de backend tem de corresponder a este nome de anfitrião.

## <a name="next-steps"></a>Passos seguintes

Depois de aprender sobre o fim do TLS, vá para configurar fim [para terminar TLS usando o Gateway de aplicação com powerShell](application-gateway-end-to-end-ssl-powershell.md) para criar um gateway de aplicação usando o fim do TLS.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
