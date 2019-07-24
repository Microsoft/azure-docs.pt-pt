---
title: Habilitando o SSL de ponta a ponta no gateway de Aplicativo Azure
description: Este artigo é uma visão geral do suporte a SSL de ponta a ponta do gateway de aplicativo.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 199fcdf2ebf10852906b842f09fe7beafd2acdb5
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326612"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Visão geral da terminação SSL e SSL de ponta a ponta com o gateway de aplicativo

O protocolo SSL (SSL) é a tecnologia de segurança padrão para estabelecer um vínculo criptografado entre um servidor Web e um navegador. Esse link garante que todos os dados passados entre o servidor Web e os navegadores permaneçam privados e criptografados. O gateway de aplicativo dá suporte ao encerramento de SSL no gateway, bem como à criptografia SSL de ponta a ponta.

## <a name="ssl-termination"></a>Terminação de SSL

O Gateway de Aplicação suporta a terminação de SSL no gateway, após a qual o tráfego flui normalmente desencriptado para os servidores de back-end. Há uma série de vantagens de fazer terminação SSL no gateway de aplicativo:

- **Melhor desempenho** – o maior impacto de desempenho ao fazer a descriptografia SSL é o handshake inicial. Para melhorar o desempenho, o servidor que faz a descriptografia armazena em cache IDs de sessão SSL e gerencia tíquetes de sessão TLS. Se isso for feito no gateway de aplicativo, todas as solicitações do mesmo cliente poderão usar os valores armazenados em cache. Se for feito nos servidores de back-end, cada vez que as solicitações do cliente vão para um servidor diferente, o cliente precisará autenticar novamente. O uso de tíquetes TLS pode ajudar a atenuar esse problema, mas eles não têm suporte de todos os clientes e podem ser difíceis de configurar e gerenciar.
- **Melhor utilização dos servidores back-end – o** processamento de SSL/TLS é muito intensivo de CPU e está ficando mais intensivo à medida que os tamanhos de chave aumentam. A remoção desse trabalho dos servidores de back-end permite que eles se concentrem no que são mais eficientes no fornecimento de conteúdo.
- **Roteamento inteligente** – descriptografando o tráfego, o gateway de aplicativo tem acesso ao conteúdo da solicitação, como cabeçalhos, URI e assim por diante, e pode usar esses dados para rotear solicitações.
- **Gerenciamento de certificados** – os certificados só precisam ser comprados e instalados no gateway de aplicativo e não em todos os servidores de back-end. Isso economiza tempo e dinheiro.

Para configurar a terminação SSL, um certificado SSL deve ser adicionado ao ouvinte para permitir que o gateway de aplicativo derive uma chave simétrica de acordo com a especificação do protocolo SSL. A chave simétrica é usada para criptografar e descriptografar o tráfego enviado para o gateway. O certificado SSL precisa estar no formato PFX (troca de informações pessoais). Esse formato de arquivo permite exportar a chave privada exigida pelo gateway de aplicativo para executar a criptografia e a descriptografia do tráfego.

> [!NOTE] 
>
> O gateway de aplicativo não fornece nenhum recurso para criar um novo certificado ou enviar uma solicitação de certificado a uma autoridade de certificação.

Para que a conexão SSL funcione, você precisa garantir que o certificado SSL atenda às seguintes condições:

- Que a data e a hora atuais estão dentro do intervalo de datas "válido de" e "válido para" no certificado.
- O “Nome Comum” (NC) do certificado corresponde ao cabeçalho do anfitrião no pedido. Por exemplo, se o cliente estiver a realizar um pedido para `https://www.contoso.com/`, o NC deverá ser `www.contoso.com`.

### <a name="certificates-supported-for-ssl-termination"></a>Certificados com suporte para terminação SSL

O gateway de aplicativo dá suporte aos seguintes tipos de certificados:

- Certificado de AC (autoridade de certificação): Um certificado de autoridade de certificação é um certificado digital emitido por uma autoridade de certificação (CA)
- Certificado EV (validação estendida): Um certificado EV é uma diretriz de certificado padrão do setor. Isso ativará a barra de localizador de navegador como verde e publicará o nome da empresa também.
- Certificado curinga: Esse certificado dá suporte a qualquer número de subdomínios com base em *. site.com, em que o subdomínio substituiria o *. No entanto, ele não dá suporte a site.com, portanto, caso os usuários estejam acessando seu site sem digitar o "www" líder, o certificado curinga não abordará isso.
- Certificados autoassinados: Os navegadores cliente não confiam nesses certificados e avisam o usuário de que o certificado do serviço virtual não faz parte de uma cadeia de confiança. Os certificados autoassinados são bons para testes ou ambientes em que os administradores controlam os clientes e podem ignorar com segurança os alertas de segurança do navegador. As cargas de trabalho de produção nunca devem usar certificados autoassinados.

Para obter mais informações, consulte [Configurar terminação SSL com o gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Tamanho do certificado
Verifique a seção [limites do gateway de aplicativo](https://docs.microsoft.com/azure/azure-subscription-service-limits#application-gateway-limits) para saber o tamanho máximo de certificado SSL com suporte.

## <a name="end-to-end-ssl-encryption"></a>Criptografia SSL de ponta a ponta

Alguns clientes podem não desejar comunicação não criptografada com os servidores de back-end. Tal pode dever-se aos requisitos de segurança, aos requisitos de conformidade ou a aplicação pode aceitar apenas uma ligação segura. Para essas aplicações, o gateway de aplicação suporta a encriptação SSL de ponta a ponta.

O SSL ponto a ponto permite-lhe transmitir dados confidenciais de forma segura para o back-end encriptado e tirar partido dos benefícios das funcionalidades de balanceamento de carga da Camada 7 que o gateway de aplicação proporciona. Algumas destas funcionalidades são a afinidade do cookie baseado na sessão, encaminhamento baseado em URL, suporte para encaminhamento baseado em sites ou capacidade para injetar cabeçalhos Encaminhados para X-*.

Quando configurado com o modo de comunicação SSL de ponta a ponta, o gateway de aplicação termina as sessões de SSL no gateway e desencripta o tráfego de utilizador. Em seguida, aplica as regras configuradas para selecionar uma instância de conjunto de back-end adequada para encaminhar o tráfego. O gateway de aplicação, em seguida, inicia uma nova ligação SSL ao servidor de back-end e encripta novamente os dados com o certificado de chave pública do servidor de back-end antes de transmitir o pedido para o back-end. Qualquer resposta do servidor Web atravessa o mesmo processo para o utilizador final. O SSL de ponta a ponta é habilitado pela definição da configuração de protocolo na [configuração de http de back-end](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) como https, que é então aplicada a um pool de back-end.

A política SSL se aplica ao tráfego de front-end e backend. No front-end, o gateway de aplicativo atua como o servidor e impõe a política. No back-end, o gateway de aplicativo atua como o cliente e envia as informações de protocolo/codificação como a preferência durante o handshake de SSL.

O gateway de aplicativo se comunica apenas com as instâncias de back-end que têm seu certificado na lista de permissões com o gateway de aplicativo ou cujos certificados são assinados por autoridades de certificação conhecidas em que o certificado CN corresponde ao nome do host no HTTP configurações de back-end. Isso inclui os serviços confiáveis do Azure, como Azure App aplicativos Web do serviço e o gerenciamento de API do Azure.

Se os certificados dos membros no pool de back-end não forem assinados por autoridades de certificação conhecidas, cada instância no pool de back-end com o SSL de ponta a ponta habilitado deverá ser configurada com um certificado para permitir a comunicação segura. A adição do certificado garante que o gateway de aplicativo se comunique somente com instâncias de back-end conhecidas. Isso protege ainda mais a comunicação de ponta a ponta.

> [!NOTE] 
>
> A configuração do certificado de autenticação não é necessária para serviços confiáveis do Azure, como Azure App aplicativos Web do serviço e o gerenciamento de API do Azure.

> [!NOTE] 
>
> O certificado adicionado à **configuração de http de back-end** para autenticar os servidores de back-end pode ser  o mesmo que o certificado adicionado ao ouvinte para terminação SSL no gateway de aplicativo ou diferente para aumentar a segurança.

![cenário de SSL ponto a ponto][1]

Neste exemplo, os pedidos que utilizam o TLS1.2 são encaminhados para servidores de back-end no Pool1 através do SSL ponto a ponto.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>SSL ponto a ponto e lista de certificados permitidos

O gateway de aplicação comunica apenas com instâncias de back-end conhecidas que colocaram o respetivo certificado na lista de permissões com o gateway de aplicação. Para ativar a lista de certificados permitidos, tem de carregar a chave pública dos certificados de servidor de back-end para o gateway de aplicação (e não o certificado de raiz). Apenas são permitidas ligações a back-ends conhecidos e na lista de permissões. Os restantes back-ends resultam num erro de gateway. Os certificados autoassinados são apenas para fins de teste e não são recomendados para cargas de trabalho de produção. Esses certificados devem ser colocados na lista de permissões com o gateway de aplicativo, conforme descrito nas etapas anteriores, antes que possam ser usados.

> [!NOTE]
> A configuração do certificado de autenticação não é necessária para serviços confiáveis do Azure, como o serviço de Azure App.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>SSL de ponta a ponta com a SKU v2

Os certificados de autenticação foram preteridos e substituídos por certificados raiz confiáveis no SKU do gateway de aplicativo v2. Eles funcionam de forma semelhante aos certificados de autenticação com algumas diferenças principais:

- Certificados assinados por autoridades de certificação bem conhecidas cujo CN corresponde ao nome do host nas configurações de back-end HTTP não exigem nenhuma etapa adicional para que o SSL de ponta a ponta funcione. 

   Por exemplo, se os certificados de back-end forem emitidos por uma autoridade de certificação conhecida e tiver um CN de contoso.com e o campo Host da configuração http de back-end também for definido como contoso.com, nenhuma etapa adicional será necessária. Você pode definir o protocolo de configuração http de back-end como HTTPS e a investigação de integridade e o caminho de dados seriam habilitados para SSL. Se você estiver usando Azure App serviço ou outros serviços Web do Azure como seu back-end, eles serão implicitamente confiáveis e nenhuma outra etapa será necessária para o SSL de ponta a ponta.
   
> [!NOTE] 
>
> Para que um certificado SSL seja confiável, esse certificado do servidor de back-end deve ter sido emitido por uma autoridade de certificação incluída no repositório confiável do gateway permissão. se o certificado não tiver sido emitido por uma AC confiável, o gateway de aplicativo irá, então, verificar Veja se o certificado da AC emissora foi emitido por uma AC confiável e assim por diante até que uma AC confiável seja encontrada (ponto em que uma conexão confiável, segura será estabelecida) ou nenhuma AC confiável possa ser encontrada (ponto em que o gateway permissão marcará o unhe de back-end althy). Portanto, é recomendável que o certificado do servidor back-end contenha as CAs raiz e de intermidi.

- Se o certificado for auto-assinado ou assinado por intermediários desconhecidos, para habilitar o SSL de ponta a ponta no SKU v2, um certificado raiz confiável deverá ser definido. O gateway de aplicativo só se comunicará com back-ends cujo certificado raiz do certificado de servidor corresponda a uma da lista de certificados raiz confiáveis na configuração de http de back-end associada ao pool.

> [!NOTE] 
>
> O certificado autoassinado deve fazer parte de uma cadeia de certificados. Não há suporte para um único certificado autoassinado sem nenhuma cadeia no SKU v2.

- Além da correspondência de certificado raiz, o gateway de aplicativo também valida se a configuração de host especificada na configuração de http de back-end corresponde à do nome comum (CN) apresentado pelo certificado SSL do servidor de back-end. Ao tentar estabelecer uma conexão SSL com o back-end, o gateway de aplicativo define a extensão Indicação de Nome de Servidor (SNI) para o host especificado na configuração http de back-end.
- Se **escolher nome do host do endereço de back-end** for escolhido em vez do campo host na configuração http de back-end, o cabeçalho SNI sempre será definido como o FQDN do pool de back-end e o CN no certificado SSL do servidor de back-end deverá corresponder ao FQDN. Não há suporte para membros do pool de back-end com IPs neste cenário.
- O certificado raiz é um certificado raiz codificado em base64 dos certificados do servidor back-end.

## <a name="next-steps"></a>Passos seguintes

Depois de aprender sobre o SSL de ponta a ponta, acesse [Configurar o SSL de ponta a ponta usando o gateway de aplicativo com o PowerShell](application-gateway-end-to-end-ssl-powershell.md) para criar um gateway de aplicativo usando o SSL de ponta a ponta.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
