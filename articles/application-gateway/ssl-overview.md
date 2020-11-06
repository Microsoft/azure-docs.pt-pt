---
title: Permitir o fim do TLS no Gateway de Aplicações Azure
description: Este artigo é uma visão geral do fim do Gateway de Aplicação para acabar com o suporte TLS.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: victorh
ms.openlocfilehash: c39401289ffc6f27c292168adaa15c5163a3967b
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93396928"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Visão geral da rescisão de TLS e fim do fim do TLS com Gateway de aplicação

Transport Layer Security (TLS), anteriormente conhecida como Secure Sockets Layer (SSL), é a tecnologia de segurança padrão para estabelecer uma ligação encriptada entre um servidor web e um navegador. Este link garante que todos os dados passados entre o servidor web e os navegadores permanecem privados e encriptados. O gateway de aplicações suporta a rescisão de TLS no gateway, bem como a encriptação de fim ao fim do TLS.

## <a name="tls-termination"></a>Rescisão de TLS

O Gateway de Aplicação suporta a terminação do TLS no gateway, após o qual o tráfego normalmente flui desencriptado para os servidores de backend. Existem várias vantagens de fazer a rescisão de TLS no gateway de aplicação:

- **Melhor desempenho** – O maior sucesso de desempenho ao fazer desencriptação TLS é o aperto de mão inicial. Para melhorar o desempenho, o servidor que faz os caches de desencriptação IDs de sessão TLS e gere os bilhetes de sessão TLS. Se isto for feito no gateway de aplicações, todos os pedidos do mesmo cliente podem usar os valores em cache. Se for feito nos servidores de backend, cada vez que os pedidos do cliente vão para um servidor diferente, o cliente deve reautenticar. A utilização de bilhetes TLS pode ajudar a mitigar este problema, mas não são suportados por todos os clientes e podem ser difíceis de configurar e gerir.
- **Uma melhor utilização dos servidores backend** – o processamento de SSL/TLS é muito intensivo e está a tornar-se mais intensivo à medida que os tamanhos das chaves aumentam. Remover este trabalho dos servidores backend permite-lhes concentrar-se naquilo em que são mais eficientes, fornecendo conteúdo.
- **Encaminhamento inteligente** – Ao desencriptar o tráfego, o gateway de aplicações tem acesso ao conteúdo do pedido, como cabeçalhos, URI, e assim por diante, e pode usar estes dados para encaminhar pedidos.
- **Gestão de certificados** – Os certificados só precisam de ser comprados e instalados no gateway de aplicações e nem todos os servidores backend. Isto poupa tempo e dinheiro.

Para configurar a rescisão de TLS, é necessário adicionar ao ouvinte um certificado TLS/SSL para permitir que o Gateway de Aplicação produza uma chave simétrica de acordo com a especificação do protocolo TLS/SSL. A chave simétrica é então usada para encriptar e desencriptar o tráfego enviado para o portal. O certificado TLS/SSL tem de estar no formato Personal Information Exchange (PFX). Este formato de ficheiro permite-lhe exportar a chave privada que é exigida pelo gateway da aplicação para realizar a encriptação e desencriptação do tráfego.

> [!IMPORTANT] 
> O certificado no ouvinte exige que toda a cadeia de certificados seja carregada (o certificado raiz da AC, os intermediários e o certificado de folha) para estabelecer a cadeia de confiança. 


> [!NOTE] 
>
> O gateway de aplicações não fornece qualquer capacidade para criar um novo certificado ou enviar um pedido de certificado a uma autoridade de certificação.

Para a ligação TLS ao trabalho, é necessário garantir que o certificado TLS/SSL satisfaz as seguintes condições:

- Que a data e hora atuais se encontra dentro do intervalo de data "Válido a partir" e "Válido para" no certificado.
- O “Nome Comum” (NC) do certificado corresponde ao cabeçalho do anfitrião no pedido. Por exemplo, se o cliente estiver a realizar um pedido para `https://www.contoso.com/`, o NC tem de ser `www.contoso.com`.

### <a name="certificates-supported-for-tls-termination"></a>Certificados suportados para a rescisão de TLS

O gateway de aplicações suporta os seguintes tipos de certificados:

- Certificado CA (Autoridade de Certificados): Um certificado de CA é um certificado digital emitido por uma autoridade de certificados (CA)
- Certificado EV (Validação Alargada): Um certificado EV é um certificado de conformidade com as diretrizes de certificados padrão da indústria. Isto irá tornar o localizador de navegador verde e publicar o nome da empresa também.
- Certificado Wildcard: Este certificado suporta qualquer número de subdomínios com base em *.site.com, onde o seu subdomínio substituiria o *. No entanto, não suporta site.com, pelo que, no caso de os utilizadores acederem ao seu website sem dactilografia do "www" principal, o certificado wildcard não cobre isso.
- Self-Signed certificados: Os navegadores de clientes não confiam nestes certificados e avisam o utilizador de que o certificado do serviço virtual não faz parte de uma cadeia de fidedignidade. Os certificados auto-assinados são bons para testes ou ambientes onde os administradores controlam os clientes e podem contornar com segurança os alertas de segurança do navegador. As cargas de trabalho de produção nunca devem utilizar certificados auto-assinados.

Para obter mais informações, consulte [a rescisão de TLS com o gateway de aplicações.](./create-ssl-portal.md)

### <a name="size-of-the-certificate"></a>Tamanho do certificado
Consulte a secção [de limites do Gateway de Aplicação](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits) para saber o tamanho máximo do certificado TLS/SSL suportado.

## <a name="end-to-end-tls-encryption"></a>Encriptação TLS de ponta a ponta

Pode não querer uma comunicação não encriptada para os servidores backend. Pode ter requisitos de segurança, requisitos de conformidade, ou a aplicação só pode aceitar uma ligação segura. O Azure Application Gateway tem encriptação TLS de ponta a ponta para suportar estes requisitos.

O TLS de ponta a ponta permite-lhe encriptar e transmitir de forma segura dados sensíveis para o backend enquanto utiliza as funcionalidades de equilíbrio de carga Layer-7 do Application Gateway. Estas funcionalidades incluem afinidade da sessão baseada em cookies, encaminhamento baseado em URL, suporte para encaminhamento baseado em sites, a capacidade de reescrever ou injetar cabeçalhos X-Forwarded,, e assim por diante.

Quando configurado com o modo de comunicação TLS de ponta a ponta, o Application Gateway encerra as sessões TLS no gateway e desencripta o tráfego do utilizador. Em seguida, aplica as regras configuradas para selecionar uma instância de conjunto de back-end adequada para encaminhar o tráfego. O Application Gateway inicia então uma nova ligação TLS ao servidor backend e reencripta os dados utilizando o certificado de chave pública do servidor de backend antes de transmitir o pedido para o backend. Qualquer resposta do servidor Web atravessa o mesmo processo para o utilizador final. O TLS de ponta a ponta é ativado através da definição de protocolo na [definição http de backend](./configuration-overview.md#http-settings) para HTTPS, que é depois aplicada a um pool de backend.

Para o Gateway de Aplicações e WAF v1 SKU, a política TLS aplica-se tanto ao tráfego frontal como ao tráfego de backend. Na parte frontal, o Application Gateway funciona como o servidor e aplica a política. No backend, o Application Gateway atua como cliente e envia a informação protocolo/cifra como a preferência durante o aperto de mão TLS.

Para o Gateway de Aplicações e WAF v2 SKU, a política TLS aplica-se apenas ao tráfego frontal e todas as cifras são oferecidas ao servidor backend, que tem controlo para selecionar cifras específicas e versão TLS durante o aperto de mão.

O Application Gateway comunica apenas com os servidores backend que permitiram a lista do seu certificado com o Gateway de Aplicação ou cujos certificados são assinados por autoridades conhecidas da AC e o CN do certificado corresponde ao nome de anfitrião nas definições de backend HTTP. Estes incluem os serviços fidedignos da Azure, tais como Azure App Service/Web Apps e Azure API Management.

Se os certificados dos membros do pool de backend não forem assinados por autoridades conhecidas da AC, cada instância no pool de backend com TLS de ponta a ponta ativada deve ser configurada com um certificado para permitir uma comunicação segura. A adição do certificado garante que o gateway de aplicação apenas comunica com instâncias de back-end conhecidas. Isto assegura ainda mais a comunicação de ponta a ponta.

> [!NOTE] 
>
> O certificado adicionado ao **Backend HTTP Definição** para autenticar os servidores backend pode ser o mesmo que o certificado adicionado ao **ouvinte** para a rescisão de TLS no gateway de aplicações ou diferente para uma segurança reforçada.

![fim para terminar o cenário TLS][1]

Neste exemplo, os pedidos que utilizam o TLS1.2 são encaminhados para os servidores de backend na Pool1 utilizando o fim do TLS.

## <a name="end-to-end-tls-and-allow-listing-of-certificates"></a>Fim ao fim dos TLS e permitir a listagem de certificados

O Application Gateway comunica apenas com instâncias de backend conhecidas que permitiram a lista do seu certificado com o gateway de aplicação. Existem algumas diferenças no processo de configuração do TLS de ponta a ponta no que diz respeito à versão do Gateway de Aplicação utilizado. A secção seguinte explica-os individualmente.

## <a name="end-to-end-tls-with-the-v1-sku"></a>TLS de ponta a ponta com o V1 SKU

Para permitir o TLS de ponta a ponta com os servidores backend e para o Application Gateway para os pedidos de rota, as sondas de saúde devem ter sucesso e devolver uma resposta saudável.

Para as sondas sanitárias HTTPS, o Application Gateway v1 SKU utiliza uma correspondência exata do certificado de autenticação (chave pública do certificado do servidor de backend e não do certificado raiz) a ser carregado para as definições HTTP.

Só são permitidas ligações a backends conhecidos e permitidos. Os restantes backends são considerados insalubres pelas sondas de saúde. Os certificados autoassinados são apenas para fins de teste e não são recomendados para cargas de trabalho de produção. Esses certificados devem ser indicados com o gateway de aplicação, tal como descrito nas etapas anteriores antes de poderem ser utilizados.

> [!NOTE]
> A autenticação e a configuração de certificados de raiz fidedignos não são necessárias para serviços fidedignos da Azure, como o Azure App Service. São considerados de confiança por defeito.

## <a name="end-to-end-tls-with-the-v2-sku"></a>TLS de ponta a ponta com o V2 SKU

Os certificados de autenticação foram depreciados e substituídos por Certificados de Raiz Fidedigna no Gateway de Aplicação v2 SKU. Funcionam da mesma forma com certificados de autenticação com algumas diferenças fundamentais:

- Os certificados assinados por autoridades conhecidas da AC, cuja CN corresponde ao nome de anfitrião nas definições de backend HTTP, não requerem qualquer passo adicional para que o fim do TLS termine o trabalho. 

   Por exemplo, se os certificados de backend forem emitidos por um ca bem conhecido e dense um CN de contoso.com, e o campo de anfitrião de backend http também estiver definido para contoso.com, então não são necessários passos adicionais. Pode definir o protocolo de definição http backend para HTTPS e tanto a sonda de saúde como o caminho dos dados estariam ativados por TLS. Se estiver a utilizar o Azure App Service ou outros serviços web Azure como backend, então estes também são implicitamente confiáveis e não são necessários mais passos para o fim do TLS.
   
> [!NOTE] 
>
> Para que seja confiável um certificado TLS/SSL, esse certificado do servidor backend deve ter sido emitido por uma AC que é bem conhecida. Se o certificado não foi emitido por uma AC fidedigna, o gateway de aplicação verificará então se o certificado da AC emissora foi emitido por uma AC fidedigna, e assim por diante até que um ca fidedigno seja encontrado (altura em que será estabelecida uma ligação segura e confiável) ou não pode ser encontrada nenhuma AC fidedigna (altura em que o gateway de aplicação marcará o backend insalubre). Portanto, recomenda-se que o certificado do servidor de backend contenha tanto os CAs raiz como os CAs intermédios.

- Se o certificado for auto-assinado ou assinado por intermediários desconhecidos, deve então permitir que o TLS de ponta a ponta no V2 SKU seja definido um certificado de raiz fidedigno. O Application Gateway apenas comunica com backends cujo certificado de raiz do certificado de servidor corresponde a uma das listas de certificados de raiz fidedignos no backend http definição associada ao pool.

- Além da correspondência do certificado de raiz, o Application Gateway v2 também valida se a definição do Anfitrião especificada no backend http definição corresponder à do nome comum (CN) apresentado pelo certificado TLS/SSL do servidor de backend. Ao tentar estabelecer uma ligação TLS ao backend, o Gateway de Aplicação v2 define a extensão de Indicação de Nome do Servidor (SNI) para o Anfitrião especificado na definição http backend.

- Se **escolher o nome anfitrião do alvo de backend** em vez do campo Host na definição http backend, então o cabeçalho SNI é sempre definido para o pool backend FQDN e o CN no servidor de backend TLS/SSL deve corresponder ao seu FQDN. Os membros do backend pool com IPs não são apoiados neste cenário.

- O certificado raiz é um certificado de raiz codificado base64 a partir dos certificados do servidor backend.

## <a name="sni-differences-in-the-v1-and-v2-sku"></a>Diferenças de SNI no V1 e v2 SKU

Como mencionado anteriormente, o Application Gateway termina o tráfego TLS do cliente no Gateway De aplicação Listener (vamos chamá-lo de ligação frontal), desencripta o tráfego, aplica as regras necessárias para determinar o servidor backend para o qual o pedido tem de ser reencaminhado, e estabelece uma nova sessão de TLS com o servidor backend (vamos chamá-lo de ligação backend).

As tabelas a seguir descrevem as diferenças no SNI entre o V1 e o V2 SKU em termos de ligações fronte e backend.

### <a name="frontend-tls-connection-client-to-application-gateway"></a>Ligação Frontend TLS (cliente para porta de aplicação)

---
Cenário | v1 | v2 |
| --- | --- | --- |
| Se o cliente especificar o cabeçalho SNI e todos os ouvintes multi-locais estiverem ativados com a bandeira "Require SNI" | Devolva o certificado apropriado e se o site não existir (de acordo com o server_name), então a ligação é reiniciada. | Devolve o certificado adequado se disponível, caso contrário, devolve o certificado do primeiro ouvinte HTTPS configurado (na ordem)|
| Se o cliente não especificar um cabeçalho SNI e se todos os cabeçalhos multi-locais estiverem ativados com "Require SNI" | Reinicia a ligação | Devolve o certificado do primeiro ouvinte HTTPS configurado (na ordem)
| Se o cliente não especificar o cabeçalho SNI e se houver um ouvinte básico configurado com um certificado | Devolve o certificado configurado no ouvinte básico ao cliente (certificado de incumprimento ou recuo) | Devolve o certificado do primeiro ouvinte HTTPS configurado (na ordem) |

### <a name="backend-tls-connection-application-gateway-to-the-backend-server"></a>Ligação Backend TLS (porta de entrada de aplicação para o servidor backend)

#### <a name="for-probe-traffic"></a>Para o tráfego de sonda

---
Cenário | v1 | v2 |
| --- | --- | --- |
| Cabeçalho SNI (server_name) durante o aperto de mão TLS como FQDN | Definido como FQDN a partir da piscina de backend. De acordo com [rfc 6066,](https://tools.ietf.org/html/rfc6066)os endereços IPv4 e IPv6 literais não são permitidos no nome de anfitrião SNI. <br> **Nota:** FQDN no pool de backend caso o DNS resolva reesar o endereço IP do servidor (público ou privado) | O cabeçalho SNI (server_name) é definido como o nome de anfitrião da sonda personalizada anexada às definições HTTP (se configurado), caso contrário, a partir do nome de anfitrião mencionado nas definições HTTP, caso contrário a partir do FQDN mencionado no pool de backend. A ordem de precedência é sonda personalizada > configurações HTTP > pool de backend. <br> **Nota:** Se os nomes de anfitrião configurados nas definições HTTP e na sonda personalizada forem diferentes, então de acordo com a precedência, o SNI será definido como o nome de anfitrião da sonda personalizada.
| Se o endereço do pool backend for um endereço IP (v1) ou se o nome de anfitrião da sonda personalizada for configurado como endereço IP (v2) | SNI (server_name) não será definido. <br> **Nota:** Neste caso, o servidor backend deverá poder devolver um certificado de incumprimento/recuo, o que deverá permitir a sua cotação nas definições HTTP sob certificado de autenticação. Se não houver nenhum certificado predefinido/recuo configurado no servidor backend e se espera SNI, o servidor pode reiniciar a ligação e levará a falhas na sonda | Na ordem de precedência anteriormente mencionada, se tiverem endereço IP como nome de hospedeiro, então o SNI não será definido de acordo com [o RFC 6066](https://tools.ietf.org/html/rfc6066). <br> **Nota:** O SNI também não será definido em sondas V2 se nenhuma sonda personalizada estiver configurada e nenhum nome de anfitrião estiver definido nas definições HTTP ou pool de backend |

> [!NOTE] 
> Se uma sonda personalizada não estiver configurada, então o Application Gateway envia uma sonda padrão neste formato - \<protocol\> ://127.0.0.1: \<port\> /. Por exemplo, para uma sonda HTTPS predefinida, será enviada como https://127.0.0.1:443/ . Note que o 127.0.0.1 aqui mencionado é utilizado apenas como cabeçalho de anfitrião HTTP e, de acordo com RFC 6066, não será utilizado como cabeçalho SNI. Para obter mais informações sobre erros da sonda de saúde, consulte o [guia de resolução de problemas de saúde](application-gateway-backend-health-troubleshooting.md).

#### <a name="for-live-traffic"></a>Para tráfego ao vivo

---
Cenário | v1 | v2 |
| --- | --- | --- |
| Cabeçalho SNI (server_name) durante o aperto de mão TLS como FQDN | Definido como FQDN a partir da piscina de backend. De acordo com [rfc 6066,](https://tools.ietf.org/html/rfc6066)os endereços IPv4 e IPv6 literais não são permitidos no nome de anfitrião SNI. <br> **Nota:** FQDN no pool de backend caso o DNS resolva reesar o endereço IP do servidor (público ou privado) | O cabeçalho SNI (server_name) é definido como o nome de anfitrião a partir das definições HTTP, caso contrário, se a opção *PickHostnameFromBackendAddress* for escolhida ou se não for mencionado nenhum nome de anfitrião, então será definido como O FQDN na configuração do pool backend
| Se o endereço de pool backend for um endereço IP ou o nome de anfitrião não estiver definido nas definições HTTP | SNI não será definido de acordo com [RFC 6066](https://tools.ietf.org/html/rfc6066) se a entrada na piscina de backend não for um FQDN | O SNI será definido como o nome de anfitrião a partir da entrada FQDN do cliente e o certificado de backend CN tem que corresponder com este nome de anfitrião.

## <a name="next-steps"></a>Passos seguintes

Depois de aprender sobre o fim do TLS, vá ao [Configure end to end TLS usando o Application Gateway com PowerShell](application-gateway-end-to-end-ssl-powershell.md) para criar um gateway de aplicação utilizando o fim do TLS.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png