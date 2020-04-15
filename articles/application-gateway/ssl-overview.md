---
title: Habilitar fim ao TLS no Portal de Aplicação Azure
description: Este artigo é uma visão geral do gateway de aplicação final para acabar com o suporte TLS.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: ae80b49c3bfb40743665768622d3f4a8a6990c12
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311854"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Visão geral da rescisão de TLS e fim para terminar TLS com Gateway de Aplicação

A Segurança da Camada de Transporte (TLS), anteriormente conhecida como Secure Sockets Layer (SSL), é a tecnologia de segurança padrão para estabelecer uma ligação encriptada entre um servidor web e um navegador. Este link garante que todos os dados transmitidos entre o servidor web e os navegadores permanecem privados e encriptados. O gateway da aplicação suporta a rescisão de TLS no gateway, bem como a encriptação TLS final.

## <a name="tls-termination"></a>TLS rescisão

O Gateway de aplicação suporta a rescisão de TLS no gateway, após o qual o tráfego normalmente flui desencriptado para os servidores de backend. Existem várias vantagens em fazer a rescisão de TLS no gateway da aplicação:

- **Melhor desempenho** – O maior sucesso de desempenho ao fazer a desencriptação do TLS é o aperto de mão inicial. Para melhorar o desempenho, o servidor que faz os iDs da sessão tLS caches de desencriptação e gere os bilhetes de sessão TLS. Se isso for feito no gateway da aplicação, todos os pedidos do mesmo cliente podem utilizar os valores em cache. Se for feito nos servidores de backend, então cada vez que os pedidos do cliente vão para um servidor diferente o cliente deve reautenticar. A utilização de bilhetes TLS pode ajudar a mitigar este problema, mas não são suportados por todos os clientes e podem ser difíceis de configurar e gerir.
- **Melhor utilização dos servidores de backend** – O processamento de SSL/TLS é muito intensivo em CPU e está a tornar-se mais intensivo à medida que os tamanhos das teclas aumentam. Remover este trabalho dos servidores de backend permite-lhes concentrar-se no que são mais eficientes, fornecendo conteúdo.
- **Encaminhamento inteligente** – Ao desencriptar o tráfego, o portal da aplicação tem acesso aos conteúdos de pedido, tais como cabeçalhos, URI, e assim por diante, e pode usar esses dados para encaminhar pedidos.
- **Gestão de certificados** – Os certificados só precisam de ser adquiridos e instalados no gateway da aplicação e nem todos os servidores de backend. Isto poupa tempo e dinheiro.

Para configurar a rescisão de TLS, é necessário adicionar ao ouvinte um certificado TLS/SSL para permitir que o portal de aplicação obtenha uma chave simétrica de acordo com a especificação do protocolo TLS/SSL. A chave simétrica é então usada para encriptar e desencriptar o tráfego enviado para o portal. O certificado TLS/SSL tem de estar no formato Personal Information Exchange (PFX). Este formato de ficheiro permite-lhe exportar a chave privada que é exigida pela porta de entrada de aplicação para realizar a encriptação e desencriptação do tráfego.

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

## <a name="end-to-end-tls-encryption"></a>Encriptação TLS final

Alguns clientes podem não desejar uma comunicação não encriptada aos servidores de backend. Tal pode dever-se aos requisitos de segurança, aos requisitos de conformidade ou a aplicação pode aceitar apenas uma ligação segura. Para tais aplicações, o gateway da aplicação suporta o fim da encriptação TLS.

O TLS de ponta a ponta permite-lhe transmitir de forma segura dados sensíveis para o backend encriptado, aproveitando ainda os benefícios das funcionalidades de equilíbrio de carga da Camada 7 que o gateway da aplicação fornece. Algumas destas funcionalidades são a afinidade do cookie baseado na sessão, encaminhamento baseado em URL, suporte para encaminhamento baseado em sites ou capacidade para injetar cabeçalhos Encaminhados para X-*.

Quando configurado com o modo de comunicação TLS final, o gateway da aplicação termina as sessões TLS no gateway e desencripta o tráfego do utilizador. Em seguida, aplica as regras configuradas para selecionar uma instância de conjunto de back-end adequada para encaminhar o tráfego. O gateway da aplicação inicia então uma nova ligação TLS ao servidor backend e reencripta dados utilizando o certificado de chave pública do servidor de backend antes de transmitir o pedido para o backend. Qualquer resposta do servidor Web atravessa o mesmo processo para o utilizador final. O TLS de ponta a ponta é ativado através da definição de protocolo na [Definição DE BACKEnd HTTP](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) para HTTPS, que é depois aplicada a uma piscina de backend.

A política de TLS aplica-se tanto ao tráfego frontal como ao tráfego de backend. Na parte frontal, o Application Gateway atua como servidor e aplica a política. No backend, application Gateway atua como cliente e envia a informação protocolar/cifra como a preferência durante o aperto de mão TLS.

O gateway de aplicação comunica apenas com as instâncias de backend que tenham listado o seu certificado com o gateway de pedido ou cujos certificados sejam assinados por autoridades de CA bem conhecidas onde o certificado CN corresponde ao nome do anfitrião nas definições de backend HTTP. Estes incluem os serviços de confiança azure, tais como aplicações web de serviço Azure App e Azure API Management.

Se os certificados dos membros do pool de backend não forem assinados por autoridades de A bem conhecidas, cada instância na piscina de backend com tLS de extremidade ativada deve ser configurada com um certificado que permita uma comunicação segura. A adição do certificado garante que o gateway da aplicação comunica apenas com instâncias conhecidas. Isto assegura ainda a comunicação de ponta a ponta.

> [!NOTE] 
>
> A configuração do certificado de autenticação não é necessária para serviços azure fidedignos, tais como aplicações web de serviço Azure App e Azure API Management.

> [!NOTE] 
>
> O certificado adicionado ao **Backend HTTP Definição** para autenticar os servidores de backend pode ser o mesmo que o certificado adicionado ao **ouvinte** para a rescisão de TLS no gateway da aplicação ou diferente para uma segurança reforçada.

![cenário de fim a ponta tls][1]

Neste exemplo, os pedidos que utilizam o TLS1.2 são encaminhados para servidores de backend no Pool1 utilizando o TLS final.

## <a name="end-to-end-tls-and-whitelisting-of-certificates"></a>Fim do TLS final e lista branca de certificados

O gateway de aplicação comunica apenas com instâncias de back-end conhecidas que colocaram o respetivo certificado na lista de permissões com o gateway de aplicação. Para ativar a lista de certificados permitidos, tem de carregar a chave pública dos certificados de servidor de back-end para o gateway de aplicação (e não o certificado de raiz). Apenas são permitidas ligações a back-ends conhecidos e na lista de permissões. Os restantes back-ends resultam num erro de gateway. Os certificados autoassinados são apenas para fins de teste e não são recomendados para cargas de trabalho de produção. Esses certificados devem ser listados com o gateway de aplicação, tal como descrito nos passos anteriores antes de poderem ser utilizados.

> [!NOTE]
> A configuração do certificado de autenticação não é necessária para serviços Azure fidedignos, como o Azure App Service.

## <a name="end-to-end-tls-with-the-v2-sku"></a>Fim para terminar TLS com o v2 SKU

Os Certificados de Autenticação foram depreciados e substituídos por Certificados de Raiz Fidedignos no Gateway de Aplicação v2 SKU. Funcionam da mesma forma com os Certificados de Autenticação com algumas diferenças fundamentais:

- Os certificados assinados por autoridades de CA bem conhecidas cujo NC corresponde ao nome do anfitrião nas definições de backend HTTP não requerem qualquer passo adicional para o fim do TLS para o trabalho. 

   Por exemplo, se os certificados de backend forem emitidos por um CA bem conhecido e tiver um CN de contoso.com, e o campo de acolhimento do backend http configurar também está definido para contoso.com, então não são necessários passos adicionais. Pode definir o protocolo de definição de http de backend para HTTPS e tanto a sonda de saúde como o caminho dos dados estariam ativados por TLS. Se estiver a utilizar o Azure App Service ou outros serviços web Azure como seu backend, então estes são implicitamente confiáveis também e não são necessários mais passos para o fim do TLS final.
   
> [!NOTE] 
>
> Para que um certificado TLS/SSL seja confiável, esse certificado do servidor backend deve ter sido emitido por um CA que esteja incluído na loja fidedigna do Gateway de Aplicação.Se o certificado não tiver sido emitido por um CA fidedigno, o Gateway de aplicação verificará então se o certificado da AC emissora foi emitido por um CA de confiança, e assim por diante até que seja encontrada uma AC fidedigna (altura em que será estabelecida uma ligação segura e fidedigna) ou se não pode ser encontrada uma AC fidedigna (altura em que o Gateway de aplicação marcará o backend insalubre). Portanto, recomenda-se que o certificado de servidor de backend contenha tanto os CAs radiculares como os caqueis intermédios.

- Se o certificado for auto-assinado ou assinado por intermediários desconhecidos, então para permitir o fim do TLS em v2 SKU deve ser definido um certificado raiz fidedigno. O Gateway de aplicação só comunicará com backends cujo certificado de raiz do Certificado de Servidor corresponde a uma das listas de certificados de raiz fidedignos na definição de http de backend associada à piscina.

> [!NOTE] 
>
> O certificado auto-assinado deve fazer parte de uma cadeia de certificados. Um único certificado auto-assinado sem corrente não é suportado no V2 SKU.

- Além da correspondência do certificado raiz, o Application Gateway também valida se a definição do Anfitrião especificada no backend http definição corresponde à do nome comum (CN) apresentado pelo certificado TLS/SSL do servidor backend. Ao tentar estabelecer uma ligação TLS ao backend, o Gateway da aplicação define a extensão de indicação de nome do servidor (SNI) ao Anfitrião especificada na definição de http backend.
- Se escolher o **nome de anfitrião do endereço backend** em vez do campo Anfitrião na definição de http end, então o cabeçalho SNI é sempre definido para o pool de backend FQDN e o NC no certificado tLS/SSL do servidor de backend deve corresponder ao seu FQDN. Os membros do backend pool com IPs não são apoiados neste cenário.
- O certificado de raiz é um certificado de raiz codificado base64 dos certificados de backend Server.

## <a name="next-steps"></a>Passos seguintes

Depois de aprender sobre o fim do TLS, vá para configurar fim [para terminar TLS usando o Gateway de aplicação com powerShell](application-gateway-end-to-end-ssl-powershell.md) para criar um gateway de aplicação usando o fim do TLS.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
