---
title: Ativar SSL de ponta a ponta no Gateway de aplicação do Azure
description: Este artigo é uma visão geral do Gateway de aplicação que suporte SSL ponto a ponto.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 92799019d13de71d911767d8e400598513587667
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258410"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Descrição geral de terminação de SSL e o SSL ponto a ponto com o Gateway de aplicação

Secure Sockets Layer (SSL) é a tecnologia de segurança padrão para estabelecer uma ligação encriptada entre um servidor web e um browser. Esta ligação garante que todos os dados passados entre o servidor web e os browsers permanecem encriptados e privadas. Gateway de aplicação suporta ambos os terminação de SSL no gateway, bem como a encriptação SSL ponto a ponto.

## <a name="ssl-termination"></a>Terminação de SSL

Gateway de aplicação suporta a terminação de SSL no gateway, após o qual o tráfego normalmente flui desencriptado para os servidores de back-end. Há uma série de vantagens de fazer a terminação de SSL no gateway de aplicação:

- **Desempenho melhorado** – o maior impacto durante a desencriptação de SSL de desempenho é o handshake inicial. Para melhorar o desempenho, o servidor fazendo a descriptografia coloca em cache IDs de sessão SSL e gere pedidos de sessão TLS. Se isso for feito no gateway de aplicação, todos os pedidos do mesmo cliente podem utilizar os valores do cache. Se isso é feito nos servidores de back-end, em seguida, sempre que pedidos do cliente aceda a um servidor diferente, o cliente deve re‑authenticate. A utilização de pedidos de suporte TLS pode ajudar a atenuar este problema, mas eles não são suportados por todos os clientes e podem ser difícil de configurar e gerir.
- **Melhor utilização dos servidores de back-end** – processamento de SSL/TLS é exigente em termos de CPU muito e está se tornando mais intensivo, como aumentam a tamanhos de chave. Remover este trabalho de servidores de back-end permite que eles se concentrem nas quais eles são mais eficientes na distribuição de conteúdos.
- **Encaminhamento inteligente** – ao desencriptar o tráfego, o gateway de aplicação tem acesso para o conteúdo do pedido, como cabeçalhos, o URI e assim por diante e pode utilizar estes dados para encaminhar os pedidos.
- **Gestão de certificados** – certificados só tem de ser adquiridas e instalados no gateway de aplicação e nem todos os servidores de back-end. Isso poupa tempo e dinheiro.

Para configurar a terminação de SSL, um certificado SSL deve ser adicionado ao serviço de escuta para ativar o gateway de aplicação derivar uma chave simétrica de acordo com a especificação de protocolo SSL. A chave simétrica, em seguida, é utilizada para encriptar e desencriptar o tráfego enviado para o gateway. O certificado SSL tem de estar no formato Personal Information Exchange (PFX). Esse formato de arquivo permite-lhe exportar a chave privada que é necessário pelo gateway de aplicação para efetuar a encriptação e desencriptação de tráfego.

> [!NOTE] 
>
> Gateway de aplicação não fornece qualquer capacidade de criar um novo certificado ou enviar um pedido de certificado para uma autoridade de certificação.

Para a ligação de SSL funcionar, terá de garantir que o certificado SSL cumpre as condições seguintes:

- É que a data e hora atuais no "Válido de" e "Válidos para" intervalo de datas no certificado.
- Corresponde a que o certificado "Common Name" (CN) o cabeçalho de anfitrião no pedido. Por exemplo, se o cliente estiver a um pedido para `https://www.contoso.com/`, em seguida, tem de ser o CN `www.contoso.com`.

### <a name="certificates-supported-for-ssl-termination"></a>Certificados suportados para a terminação de SSL

Gateway de aplicação suporta os seguintes tipos de certificados:

- Certificado de CA (autoridade de certificação): Um certificado de AC é um certificado digital emitido por uma autoridade de certificação (AC)
- Certificado EV (validação estendida): Um certificado EV é um diretrizes de certificado padrão do setor. Isto irá ativar a barra de localizador de navegador verde e publicar também o nome da empresa.
- Certificado de caráter universal: Este certificado suporta qualquer número de subdomínios com base em *. site.com, onde o subdomínio substituiria o *. No entanto, ele não, suporta site.com, portanto, no caso dos utilizadores estão a aceder seu Web site sem escrever o líder "www", o certificado de caráter universal não irão abrangê que.
- Certificados Autoassinados: Browsers cliente não confiar nestes certificados e irão avisar o utilizador que o certificado do serviço virtual não faz parte de uma cadeia confiável. Certificados autoassinados são bons para testes ou ambientes nos quais os administradores a controlar os clientes e podem ignorar com segurança os alertas de segurança do navegador. Cargas de trabalho de produção nunca devem utilizar certificados autoassinados.

Para obter mais informações, consulte [configurar a terminação de SSL com o gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

## <a name="end-to-end-ssl-encryption"></a>A encriptação SSL de ponta a ponta

Alguns clientes talvez não pretendidos ao nível da comunicação sem encriptação para os servidores de back-end. Tal pode dever-se aos requisitos de segurança, aos requisitos de conformidade ou a aplicação pode aceitar apenas uma ligação segura. Para essas aplicações, o gateway de aplicação suporta a encriptação SSL de ponta a ponta.

SSL de ponto a ponto permite-lhe transmitir dados confidenciais para o back-end encriptado e tirar partido dos benefícios das funcionalidades de balanceamento de carga de camada 7 que o gateway de aplicação fornece de forma segura. Algumas destas funcionalidades são a afinidade do cookie baseado na sessão, encaminhamento baseado em URL, suporte para encaminhamento baseado em sites ou capacidade para injetar cabeçalhos Encaminhados para X-*.

Quando configurado com o modo de comunicação SSL de ponta a ponta, o gateway de aplicação termina as sessões de SSL no gateway e desencripta o tráfego de utilizador. Em seguida, aplica as regras configuradas para selecionar uma instância de conjunto de back-end adequada para encaminhar o tráfego. O gateway de aplicação, em seguida, inicia uma nova ligação SSL ao servidor de back-end e encripta novamente os dados com o certificado de chave pública do servidor de back-end antes de transmitir o pedido para o back-end. Qualquer resposta do servidor Web atravessa o mesmo processo para o utilizador final. SSL de ponto a ponto é ativada ao configurar a definição do protocolo no [definição de HTTP de back-end](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) como HTTPS que, em seguida, é aplicado a um conjunto de back-end.

Se aplica a política SSL para tráfego de front-end e back-end. No front-end, o Gateway de aplicação atua como o servidor e impõe a política. Back-end, o Gateway de aplicação atua como o cliente e envia as informações de protocolo/cifras como a preferência durante o handshake SSL.

Gateway de aplicação comunica apenas com essas instâncias de back-end que colocaram o respetivo certificado com o gateway de aplicação ou cujos certificados são assinados pela bem conhecidas autoridades de AC em que o CN do certificado corresponde ao nome de anfitrião em HTTP definições de back-end. Estes incluem os serviços do Azure fidedignos, tais como aplicações de web do serviço de aplicações do Azure e a API Management do Azure.

Se os certificados dos membros no agrupamento de back-end não estão assinados pelo bem conhecidas autoridades de AC, cada instância do conjunto de back-end com SSL de ponta a ponta ativado deve ser configurada com um certificado para permitir a comunicação segura. A adicionar o certificado garante que o gateway de aplicação comunica apenas com instâncias de back-end conhecidas. Isso protege ainda mais a comunicação de ponto-a-ponto.

> [!NOTE] 
>
> Configuração de certificado de autenticação não é necessária para serviços do Azure confiáveis, como aplicações de web do serviço de aplicações do Azure e a API Management do Azure.

> [!NOTE] 
>
> O certificado adicionado ao **definição de HTTP de back-end** para autenticar o back-end servidores podem ser o mesmo que o certificado adicionado à **serviço de escuta** para terminação de SSL no gateway de aplicação ou diferentes para Segurança aprimorada.

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
