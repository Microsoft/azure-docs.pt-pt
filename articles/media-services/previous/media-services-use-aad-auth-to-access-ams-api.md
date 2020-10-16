---
title: Acesso Azure Media Services API com autenticação do Azure Ative Directory Microsoft Docs
description: Conheça os conceitos e passos a tomar para utilizar o Azure Ative Directory (Azure AD) para autenticar o acesso à API dos Serviços de Media Azure.
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
ms.openlocfilehash: c0d3ece75d15ae07091f613348389f845ec74ef7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89262520"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Aceder à API de Serviços de Multimédia do Azure com a autenticação do Azure AD 

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

A Azure Media Services API é uma API RESTful. Pode usá-lo para realizar operações em recursos de mídia utilizando uma API REST ou utilizando SDKs de clientes disponíveis. A Azure Media Services oferece um cliente de Media Services SDK para a Microsoft .NET. Para obter autorização para aceder aos recursos dos Serviços de Multimédia e à API dos Serviços de Multimédia, primeiro tem de se autenticar. 

Os Serviços de Comunicação Social suportam [a autenticação baseada em Azure Ative Directory (Azure AD).](../../active-directory/fundamentals/active-directory-whatis.md) O serviço Azure Media REST exige que o utilizador ou aplicação que faz os pedidos de API REST tenha a função **de Contribuinte** ou **Proprietário** para aceder aos recursos. Para mais informações, consulte [Começar com Role-Based Controlo de Acesso no portal Azure.](../../role-based-access-control/overview.md)  

Este documento apresenta uma visão geral de como aceder à API dos Serviços de Mídia utilizando ASE REST ou .NET.

> [!NOTE]
> A autorização de Controlo de Acesso foi depreada a 1 de junho de 2018.

## <a name="access-control"></a>Controlo de acesso

Para que o pedido de REPOUSO Azure Media seja bem sucedido, o utilizador chamador deve ter uma função de Contribuinte ou Proprietário para a conta de Serviços de Comunicação social a que está a tentar aceder.  
Apenas um utilizador com a função Proprietário pode dar acesso a novos utilizadores ou aplicações de recursos de mídia (conta). A função Colaboradora só pode aceder ao recurso de mídia.
Os pedidos não autorizados falham, com o código de estado do 401. Se vir este código de erro, verifique se o seu utilizador tem a função Contribuinte ou Proprietário atribuída para a conta de Serviços de Mídia do utilizador. Pode verificar isto no portal Azure. Procure a sua conta de media e, em seguida, clique no separador **'Saber' e,** em seguida, clique no separador 'Saber'. 

![Separador de controlo de acesso](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Tipos de autenticação 
 
Quando utiliza a autenticação AZure AD com a Azure Media Services, tem duas opções de autenticação:

- **Autenticação de utilizador**. Autenticar uma pessoa que está a usar a app para interagir com os recursos dos Media Services. A aplicação interativa deve primeiro solicitar ao utilizador as credenciais do utilizador. Um exemplo é uma aplicação da consola de gestão utilizada pelos utilizadores autorizados para monitorizar as tarefas de codificação ou a transmissão em direto. 
- **Autenticação principal do serviço.** Autenticar um serviço. As aplicações que normalmente utilizam este método de autenticação são aplicações que executam serviços daemon, serviços de camada média ou tarefas agendadas. Exemplos são aplicações web, apps de funções, aplicações lógicas, API e microserviços.

### <a name="user-authentication"></a>Autenticação de utilizador 

Aplicações que devem utilizar o método de autenticação do utilizador são a gestão ou monitorização de aplicações nativas: aplicações móveis, aplicações Windows e aplicações para consolas. Este tipo de solução é útil quando se pretende a interação humana com o serviço num dos seguintes cenários:

- Painel de monitorização para os seus trabalhos de codificação.
- Painel de monitorização para as suas transmissões ao vivo.
- Aplicação de gestão para utilizadores de desktop ou móveis para administrar recursos numa conta de Serviços de Mídia.

> [!NOTE]
> Este método de autenticação não deve ser utilizado para aplicações viradas para o consumidor. 

Uma aplicação nativa deve primeiro adquirir um token de acesso a Azure AD e, em seguida, usá-lo quando escrupura pedidos HTTP para a API dos Serviços de Comunicação Social. Adicione o sinal de acesso ao cabeçalho do pedido. 

O seguinte diagrama mostra um fluxo típico de autenticação de aplicações interativas: 

![Diagrama de aplicativos nativos](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

No diagrama anterior, os números representam o fluxo dos pedidos por ordem cronológica.

> [!NOTE]
> Quando utiliza o método de autenticação do utilizador, todas as aplicações partilham o mesmo ID (predefinido) do cliente de aplicação nativa e o redirecionamento de aplicações nativas URI. 

1. Indique um utilizador para obter credenciais.
2. Solicite um token de acesso AD AZure com os seguintes parâmetros:  

   * Azure AD inquilino ponto final.

       A informação do inquilino pode ser recuperada a partir do portal Azure. Coloque o cursor sobre o nome do utilizador inscrito no canto superior direito.
   * Recursos de mídia URI. 

       Este URI é o mesmo para contas de Serviços de Media que estão no mesmo ambiente Azure (por exemplo, https: \/ /rest.media.azure.net).

   * Identificação do cliente de aplicação dos Media Services (nativo).
   * Aplicação media Services (nativa) redireciona URI.
   * URI de recurso para rest media Services.
        
       O URI representa o ponto final da API REST (por exemplo, https://test03.restv2.westus.media.azure.net/api/) .

     Para obter valores para estes parâmetros, consulte [utilizar o portal Azure para aceder às definições de autenticação Azure AD](media-services-portal-get-started-with-aad.md) utilizando a opção de autenticação do utilizador.

3. O sinal de acesso Azure AD é enviado ao cliente.
4. O cliente envia um pedido à Azure Media REST API com o token de acesso AZure AD.
5. O cliente recebe os dados dos Serviços de Comunicação Social.

Para obter informações sobre como utilizar a autenticação Azure AD para comunicar com os pedidos de REST utilizando o cliente SDK dos Serviços de Comunicação Social.NET, consulte [a autenticação Ad AZure para aceder à API dos Serviços de Mídia com .NET](media-services-dotnet-get-started-with-aad.md). 

Se não estiver a utilizar o Cliente SDK dos Media Services .NET, deve criar manualmente um pedido de sinal de acesso AD Azure utilizando os parâmetros descritos no passo 2. Para mais informações, consulte [Como utilizar a Biblioteca de Autenticação AD Azure para obter o token AD AZure](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Autenticação do principal de serviço

As aplicações que normalmente utilizam este método de autenticação são aplicações que executam serviços de nível médio e empregos programados: aplicações web, aplicações de funções, apps lógicas, APIs e microserviços. Este método de autenticação também é adequado para aplicações interativas nas quais poderá querer utilizar uma conta de serviço para gerir recursos.

Quando utiliza o método principal de autenticação do serviço para construir cenários de consumo, a autenticação normalmente é manuseada no nível médio (através de alguma API) e não diretamente numa aplicação móvel ou de ambiente de trabalho. 

Para utilizar este método, crie um azure AD application and service principal no seu próprio inquilino. Depois de criar a aplicação, dê à app o acesso da função Contribuinte ou Proprietário à conta dos Serviços de Comunicação Social. Pode fazê-lo no portal Azure, utilizando o CLI Azure, ou com um script PowerShell. Também pode utilizar uma aplicação AD Azure existente. Pode registar-se e gerir a sua app AD Azure e o seu diretor de serviço [no portal Azure.](media-services-portal-get-started-with-aad.md) Também pode fazê-lo utilizando [Azure CLI](media-services-use-aad-auth-to-access-ams-api.md) ou [PowerShell](media-services-powershell-create-and-configure-aad-app.md). 

![Aplicativos de nível médio](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Depois de criar a sua aplicação AD Azure, obtém valores para as seguintes definições. Precisa destes valores para a autenticação:

- ID de Cliente 
- Segredo do cliente 

No valor anterior, os números representam o fluxo dos pedidos por ordem cronológica:
    
1. Uma aplicação de nível médio (Web API ou aplicação web) solicita um token de acesso AD Azure que tem os seguintes parâmetros:  

   * Azure AD inquilino ponto final.

       A informação do inquilino pode ser recuperada a partir do portal Azure. Coloque o cursor sobre o nome do utilizador inscrito no canto superior direito.
   * Recursos de mídia URI. 

       Este URI é o mesmo para contas de Serviços de Mídia que estão localizadas no mesmo ambiente Azure (por exemplo, https: \/ /rest.media.azure.net).

   * URI de recurso para rest media Services.

       O URI representa o ponto final da API REST (por exemplo, https://test03.restv2.westus.media.azure.net/api/) .

   * Valores de aplicação AD Azure: identificação do cliente e segredo do cliente.
    
     Para obter valores para estes parâmetros, consulte [utilizar o portal Azure para aceder às definições de autenticação Azure AD](media-services-portal-get-started-with-aad.md) utilizando a opção de autenticação principal do serviço.

2. O sinal de acesso Azure AD é enviado para o nível médio.
4. O nível médio envia pedido à Azure Media REST API com o token AD Azure.
5. O nível médio recebe de volta os dados dos Serviços de Comunicação Social.

Para obter mais informações sobre como utilizar a autenticação Azure AD para comunicar com os pedidos de REST utilizando o cliente de Mídia .NET SDK, consulte [a autenticação AD do Azure para aceder à Azure Media Services API com .NET](media-services-dotnet-get-started-with-aad.md). 

Se não estiver a utilizar o Cliente SDK dos Media Services .NET, deve criar manualmente um pedido de ficha AD Azure utilizando parâmetros descritos no passo 1. Para mais informações, consulte [Como utilizar a Biblioteca de Autenticação AD Azure para obter o token AD AZure](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>Resolução de problemas

Exceção: "O servidor remoto devolveu um erro: (401) Não autorizado."

Solução: Para que o pedido de DESCANSO dos Serviços de Comunicação Social tenha sucesso, o utilizador chamador deve ser um colaborador ou titular na conta dos Serviços de Comunicação social a que está a tentar aceder. Para mais informações, consulte a secção [de controlo de acesso.](media-services-use-aad-auth-to-access-ams-api.md#access-control)

## <a name="resources"></a>Recursos

Os seguintes artigos são visão geral dos conceitos de autenticação AZure AD: 

- [Cenários de autenticação abordados pela Azure AD](../../active-directory/develop/authentication-vs-authorization.md)
- [Adicionar, atualizar ou remover uma aplicação no Azure AD](../../active-directory/develop/quickstart-register-app.md)
- [Configure e gere Role-Based Controlo de Acesso utilizando o PowerShell](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Passos seguintes

* Utilize o portal Azure para aceder à [autenticação Azure AD para consumir Azure Media Services API](media-services-portal-get-started-with-aad.md).
* Utilize a autenticação Azure AD para aceder à [Azure Media Services API com .NET](media-services-dotnet-get-started-with-aad.md).
