---
title: Ativar SSL de ponta a ponta no Gateway de aplicação do Azure
description: Este artigo é uma visão geral do Gateway de aplicação que suporte SSL ponto a ponto.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/12/2019
ms.author: victorh
ms.openlocfilehash: 16ba6b73dd0c64298f319d4b18750d753f166987
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849385"
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>Descrição geral do SSL ponto a ponto com o Gateway de Aplicação

Gateway de aplicação suporta a terminação de SSL no gateway, após o qual o tráfego normalmente flui desencriptado para os servidores de back-end. Esta funcionalidade permite que os servidores Web estejam livres de sobrecarga de encriptação e desencriptação dispendiosa. No entanto, para alguns clientes, a comunicação sem encriptação para os servidores de back-end não é uma opção aceitável. Esta comunicação desencriptada pode dever-se aos requisitos de segurança, requisitos de conformidade ou a aplicação pode aceitar apenas uma ligação segura. Para essas aplicações, o gateway de aplicação suporta a encriptação SSL de ponta a ponta.

SSL de ponto a ponto permite-lhe transmitir dados confidenciais para o back-end encriptado e tirar partido dos benefícios das funcionalidades de balanceamento de carga de camada 7 que o gateway de aplicação fornece de forma segura. Algumas destas funcionalidades são a afinidade do cookie baseado na sessão, encaminhamento baseado em URL, suporte para encaminhamento baseado em sites ou capacidade para injetar cabeçalhos Encaminhados para X-*.

Quando configurado com o modo de comunicação SSL de ponta a ponta, o gateway de aplicação termina as sessões de SSL no gateway e desencripta o tráfego de utilizador. Em seguida, aplica as regras configuradas para selecionar uma instância de conjunto de back-end adequada para encaminhar o tráfego. O gateway de aplicação, em seguida, inicia uma nova ligação SSL ao servidor de back-end e encripta novamente os dados com o certificado de chave pública do servidor de back-end antes de transmitir o pedido para o back-end. SSL de ponto a ponto é ativada ao configurar a definição do protocolo no **BackendHTTPSetting** como HTTPS que, em seguida, é aplicado a um conjunto de back-end. Cada servidor de back-end no conjunto de back-end com SSL de ponta a ponta ativado deve ser configurado com um certificado, para permitir a comunicação segura.

Se aplica a política SSL para tráfego de front-end e back-end. No front-end, o Gateway de aplicação atua como o servidor e impõe a política. Back-end, o Gateway de aplicação atua como o cliente e envia as informações de protocolo/cifras como a preferência durante o handshake SSL.

![cenário de SSL ponto a ponto][1]

Neste exemplo, os pedidos que utilizam o TLS1.2 são encaminhados para servidores de back-end no Pool1 através do SSL ponto a ponto.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>SSL ponto a ponto e lista de certificados permitidos

O gateway de aplicação comunica apenas com instâncias de back-end conhecidas que colocaram o respetivo certificado na lista de permissões com o gateway de aplicação. Para ativar a lista de certificados permitidos, tem de carregar a chave pública dos certificados de servidor de back-end para o gateway de aplicação (e não o certificado de raiz). Apenas são permitidas ligações a back-ends conhecidos e na lista de permissões. Os restantes back-ends resultam num erro de gateway. Os certificados autoassinados são apenas para fins de teste e não são recomendados para cargas de trabalho de produção. Esses certificados têm de estar na lista de permissões com o gateway de aplicação, conforme descrito nos passos anteriores, antes de poderem ser utilizados.

> [!NOTE]
> Configuração de certificado de autenticação não é necessária para serviços do Azure confiáveis, como o serviço de aplicações do Azure.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>Ponto a ponto SSL com o SKU de v2

Certificados de autenticação foram preteridos e substituídos por certificados de raiz fidedigna no SKU do Gateway de aplicação v2. Eles funcionam da mesma forma para certificados de autenticação com algumas diferenças importantes:

- Certificados assinados por bem conhecidas autoridades de AC cuja CN coincide com o nome de anfitrião nas definições de back-end de HTTP não requerem qualquer passo adicional para o SSL de ponta a ponta trabalhar. 

   Por exemplo, se os certificados de back-end são emitidos por uma AC conhecida e tem um CN de contoso.com, e o campo de anfitrião a definição de http de back-end também está definido como contoso.com, em seguida, não são necessários passos adicionais. Pode definir o http de back-end na definição de protocolo HTTPS, e tanto o estado de funcionamento sonda e dos dados caminho seria SSL ativado. Se estiver a utilizar o serviço de aplicações do Azure ou outros serviços da web do Azure como o back-end, em seguida, estes são implicitamente confiáveis também e nenhuma outra etapa é necessária para o SSL ponto a ponto.
- Se o certificado é autoassinado ou assinado pelo intermediários desconhecidos, em seguida ativar o SSL de ponta a ponta no SKU de v2 um certificado de raiz fidedigna tem de ser definido. Gateway de aplicação irá apenas comunicar com o certificado de raiz de cujo certificado de servidor corresponde a uma lista de certificados de raiz fidedigna na definição de http de back-end associados ao agrupamento de back-ends.
- Além de correspondência do certificado de raiz, o Gateway de aplicação também valida se corresponder a configuração especificada na definição de http de back-end de Host que o nome comum (CN) apresentado pelo certificado SSL do servidor back-end. Ao tentar estabelecer uma conexão SSL para o back-end, o Gateway de aplicação define a extensão de indicação de nome de servidor (SNI) para o anfitrião especificado na definição de http de back-end.
- Se **escolha o nome de anfitrião do endereço de back-end** for escolhida em vez do campo de anfitrião na definição de http de back-end, em seguida, o cabeçalho SNI está sempre definido como o conjunto de back-end FQDN e o CN no servidor de back-end SSL certificado tem de corresponder ao respetivo FQDN. Membros do agrupamento de back-end com IPs não são suportados neste cenário.
- O certificado de raiz é um certificado de raiz com codificação de base64 dos certificados de servidor de back-end.

## <a name="next-steps"></a>Passos Seguintes

Após a aprendizagem sobre SSL ponto a ponto, aceda a [configurar SSL ponto a ponto com o Gateway de aplicação com o PowerShell](application-gateway-end-to-end-ssl-powershell.md) para criar um gateway de aplicação com SSL de ponto a ponto.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
