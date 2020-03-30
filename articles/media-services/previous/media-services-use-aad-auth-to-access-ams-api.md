---
title: Access Azure Media Services API com autenticação de Diretório Ativo Azure [ Microsoft Docs
description: Conheça conceitos e passos a tomar para utilizar o Azure Ative Directory (Azure AD) para autenticar o acesso à API azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 8b38b38789edfd5a0a30fdd589849bfa345eaac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77157861"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Aceder à API de Serviços de Multimédia do Azure com a autenticação do Azure AD  

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

A API azure Media Services é uma API RESTful. Pode usá-lo para realizar operações em recursos de mídia utilizando uma API REST ou utilizando SDKs de clientes disponíveis. A Azure Media Services oferece um sDK de clientes de Media Services para a Microsoft .NET. Para ser autorizado a aceder aos recursos dos Serviços de Media e à API dos Serviços de Media, tem primeiro de ser autenticado. 

A Media Services apoia a [autenticação baseada em Azure Ative Directory (Azure AD).](../../active-directory/fundamentals/active-directory-whatis.md) O serviço Azure Media REST exige que o utilizador ou aplicação que efetue os pedidos rest API tenha a função **de Contribuinte** ou **Proprietário** para aceder aos recursos. Para mais informações, consulte [Iniciar o Controlo de Acesso baseado em Funções no portal Azure](../../role-based-access-control/overview.md).  

Este documento dá uma visão geral de como aceder à API dos Serviços de Media utilizando APIs REST ou .NET.

> [!NOTE]
> A autorização de Controlo de Acesso foi deprecada a 1 de junho de 2018.

## <a name="access-control"></a>Controlo de acesso

Para que o pedido do Azure Media REST tenha sucesso, o utilizador de chamadas deve ter uma função de Contribuinte ou Proprietário para a conta media Services a que está a tentar aceder.  
Apenas um utilizador com a função Proprietário pode dar acesso a novos utilizadores ou aplicações de recursos de mídia (conta). O papel do Contribuinte só pode aceder ao recurso dos meios de comunicação social.
Pedidos não autorizados falham, com código de estado de 401. Se vir este código de erro, verifique se o seu utilizador tem a função De Contribuinte ou Proprietário atribuída para a conta de Media Services do utilizador. Pode verificar isto no portal Azure. Procure a sua conta de mídia e, em seguida, clique no separador **controlo de acesso.** 

![Separador de controlo de acesso](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Tipos de autenticação 
 
Quando utiliza a autenticação Azure AD com o Azure Media Services, tem duas opções de autenticação:

- **Autenticação de utilizador**. Autenticar uma pessoa que está a usar a app para interagir com os recursos dos Media Services. A aplicação interativa deve primeiro solicitar ao utilizador as credenciais do utilizador. Um exemplo é uma aplicação de consola de gestão usada por utilizadores autorizados para monitorizar trabalhos de codificação ou streaming ao vivo. 
- **Autenticação do principal de serviço.** Autenticar um serviço. Aplicações que geralmente utilizam este método de autenticação são aplicações que executam serviços de daemon, serviços de nível médio ou empregos regulares. Exemplos são aplicações web, aplicações de função, aplicações lógicas, API e microserviços.

### <a name="user-authentication"></a>Autenticação de utilizador 

As aplicações que devem utilizar o método de autenticação do utilizador são a gestão ou monitorização de aplicações nativas: aplicações móveis, aplicações Windows e aplicações de consola. Este tipo de solução é útil quando se quer interação humana com o serviço num dos seguintes cenários:

- Monitorização do painel para os seus trabalhos de codificação.
- Painel de monitorização para os seus fluxos ao vivo.
- Aplicação de gestão para utilizadores de desktop ou mobile para administrar recursos numa conta de Media Services.

> [!NOTE]
> Este método de autenticação não deve ser utilizado para aplicações viradas para o consumidor. 

Uma aplicação nativa deve primeiro adquirir um token de acesso da Azure AD e depois usá-la quando fizer pedidos http para a Media Services REST API. Adicione o sinal de acesso ao cabeçalho de pedido. 

O diagrama seguinte mostra um fluxo típico de autenticação de aplicação interativa: 

![Diagrama de aplicativos nativos](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

No diagrama anterior, os números representam o fluxo dos pedidos por ordem cronológica.

> [!NOTE]
> Quando utiliza o método de autenticação do utilizador, todas as aplicações partilham o mesmo ID (padrão) do cliente de aplicação nativa e redirecionam o URI da aplicação nativa. 

1. Insto um utilizador para obter credenciais.
2. Solicite um sinal de acesso a AD Azure com os seguintes parâmetros:  

   * Ponto final do inquilino da AD Azure.

       A informação do inquilino pode ser recuperada do portal Azure. Coloque o cursor sobre o nome do utilizador inscrito no canto superior direito.
   * Recurso URI dos Serviços de Media. 

       Este URI é o mesmo para contas de Media Services que https://rest.media.azure.net)estão no mesmo ambiente Azure (por exemplo, .

   * Id do cliente de aplicação de Media Services (nativo).
   * A aplicação de Media Services (nativa) redireciona o URI.
   * Recursos URI para REST Media Services.
        
       O URI representa o ponto final da https://test03.restv2.westus.media.azure.net/api/)API REST (por exemplo, .

     Para obter valores para estes parâmetros, consulte [Utilize o portal Azure para aceder às definições de autenticação do Azure AD](media-services-portal-get-started-with-aad.md) utilizando a opção de autenticação do utilizador.

3. O sinal de acesso da AD Azure é enviado ao cliente.
4. O cliente envia um pedido à API Azure Media REST com o token de acesso da AD Azure.
5. O cliente recebe os dados dos Serviços de Media.

Para obter informações sobre como utilizar a autenticação Azure AD para comunicar com pedidos REST através do Cliente SDK dos Media Services .NET, consulte [a autenticação AD Use Azure para aceder à API](media-services-dotnet-get-started-with-aad.md)dos Media Services com .NET . 

Se não estiver a utilizar o Cliente SDK do Media Services .NET, deve criar manualmente um pedido de acesso AD Azure utilizando os parâmetros descritos no passo 2. Para mais informações, consulte Como utilizar a Biblioteca de [Autenticação AD Azure para obter o token Azure AD](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Autenticação do principal de serviço

Aplicações que usam comumente este método de autenticação são aplicações que executam serviços de nível médio e empregos programados: aplicações web, aplicações de funções, aplicações lógicas, APIs e microserviços. Este método de autenticação também é adequado para aplicações interativas nas quais você pode querer usar uma conta de serviço para gerir recursos.

Quando utiliza o método de autenticação principal do serviço para construir cenários de consumo, a autenticação é normalmente manuseada no nível médio (através de alguma API) e não diretamente numa aplicação móvel ou de ambiente de trabalho. 

Para utilizar este método, crie um diretor de aplicação e serviço Azure AD no seu próprio inquilino. Depois de criar a aplicação, dê à app Colaborador ou Proprietário acesso à conta de Media Services. Pode fazê-lo no portal Azure, utilizando o Azure CLI, ou com um script PowerShell. Também pode utilizar uma aplicação Azure AD existente. Pode registar-se e gerir a sua app e diretor de serviço Azure AD [no portal Azure.](media-services-portal-get-started-with-aad.md) Também pode fazê-lo utilizando [o Azure CLI](media-services-use-aad-auth-to-access-ams-api.md) ou [o PowerShell.](media-services-powershell-create-and-configure-aad-app.md) 

![Aplicativos de nível médio](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Depois de criar a sua aplicação Azure AD, obtém valores para as seguintes definições. Precisa destes valores para autenticação:

- ID de Cliente 
- Segredo do cliente 

Na figura anterior, os números representam o fluxo dos pedidos por ordem cronológica:
    
1. Uma aplicação de nível médio (Web API ou aplicação web) solicita um sinal de acesso Azure AD que tem os seguintes parâmetros:  

   * Ponto final do inquilino da AD Azure.

       A informação do inquilino pode ser recuperada do portal Azure. Coloque o cursor sobre o nome do utilizador inscrito no canto superior direito.
   * Recurso URI dos Serviços de Media. 

       Este URI é o mesmo para contas de Media Services que estão localizadas no mesmo ambiente Azure (por exemplo, https://rest.media.azure.net).

   * Recursos URI para REST Media Services.

       O URI representa o ponto final da https://test03.restv2.westus.media.azure.net/api/)API REST (por exemplo, .

   * Valores de aplicação Azure AD: o ID do cliente e o segredo do cliente.
    
     Para obter valores para estes parâmetros, consulte [Utilize o portal Azure para aceder às definições de autenticação do Azure AD](media-services-portal-get-started-with-aad.md) utilizando a opção de autenticação principal do serviço.

2. O sinal de acesso azure ad é enviado para o nível médio.
4. O nível médio envia um pedido à API Azure Media REST com o token Azure AD.
5. O nível médio recupera os dados dos Serviços de Media.

Para obter mais informações sobre como utilizar a autenticação Azure AD para comunicar com pedidos REST através do Cliente SDK media Services .NET, consulte [a autenticação AD De utilização azure para aceder à API azure Media Services com .NET](media-services-dotnet-get-started-with-aad.md). 

Se não estiver a utilizar o Cliente SDK do Media Services .NET, deve criar manualmente um pedido de ficha ada, utilizando parâmetros descritos no passo 1. Para mais informações, consulte Como utilizar a Biblioteca de [Autenticação AD Azure para obter o token Azure AD](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>Resolução de problemas

Exceção: "O servidor remoto devolveu um erro: (401) Não autorizado."

Solução: Para que o pedido de apoio ao media tenha sucesso, o utilizador de chamadas deve ser um papel de Contribuinte ou Proprietário na conta de Serviços de Media que está a tentar aceder. Para mais informações, consulte a secção de controlo de [acesso.](media-services-use-aad-auth-to-access-ams-api.md#access-control)

## <a name="resources"></a>Recursos

Os seguintes artigos são vistas gerais dos conceitos de autenticação da AD Azure: 

- [Cenários de autenticação abordados pela Azure AD](../../active-directory/develop/authentication-scenarios.md)
- [Adicionar, atualizar ou remover uma aplicação em Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)
- [Configure e gerencie o controlo de acesso baseado em funções utilizando a PowerShell](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Passos seguintes

* Utilize o portal Azure para aceder à [autenticação Azure AD para consumir a API azure Media Services.](media-services-portal-get-started-with-aad.md)
* Utilize a autenticação Azure AD para [aceder à API azure Media Services com .NET](media-services-dotnet-get-started-with-aad.md).

