---
title: Aceder à API de serviços de multimédia do Azure com a autenticação do Azure Active Directory | Documentos da Microsoft
description: Saiba mais sobre conceitos e passos para utilizar o Azure Active Directory (Azure AD) para autenticar o acesso à API de serviços de multimédia do Azure.
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: b6071bef4e4bbd9aaca3f587e5dfa584831d082e
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316879"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Acesso à API de serviços de multimédia do Azure com a autenticação do Azure AD  
 
API de serviços de multimédia do Azure é uma API RESTful. Pode usá-lo para realizar operações nos recursos de suporte de dados com uma API REST ou através de SDKs de cliente disponíveis. Serviços de multimédia do Azure oferece um SDK do cliente de serviços de multimédia para o Microsoft .NET. Para ficar autorizada a aceder a recursos de serviços de multimédia e a API de serviços de suporte de dados, tem primeiro de ser autenticado. 

Serviços de multimédia suportam [do Azure Active Directory (Azure AD)-autenticação baseada em](../../active-directory/fundamentals/active-directory-whatis.md). O serviço de REST de suporte de dados do Azure requer que o utilizador ou aplicação que faz a API REST pedidos de ter o **contribuinte** ou **proprietário** função para acessar os recursos. Para obter mais informações, consulte [introdução ao controlo de acesso baseado em funções no portal do Azure](../../role-based-access-control/overview.md).  

> [!IMPORTANT]
> Atualmente, os Media Services suportam o modelo de autenticação do serviço de controlo de acesso do Azure. No entanto, a autorização de controlo de acesso vai ser preterida no dia 1 de Junho de 2018. Recomendamos que migre para o modelo de autenticação do Azure AD assim que for possível.

Este documento oferece uma visão geral de como acessar a API de serviços de multimédia com REST ou de APIs do .NET.

## <a name="access-control"></a>Controlo de acesso

Para a solicitação de REST de suporte de dados do Azure com êxito, o utilizador chamador tem de ter uma função de Contribuidor ou proprietário da conta de Media Services está a tentar aceder.  
Apenas um utilizador com a função de proprietário pode dar acesso a recursos (conta) suporte de dados para novos utilizadores ou aplicações. A função de Contribuidor pode aceder apenas o recurso de multimédia.
Pedidos não autorizados falharem, com o código de estado de 401. Se vir este código de erro, verifique se o usuário tem a função de proprietário ou contribuinte atribuída para a conta de utilizador dos serviços de multimédia. Pode verificar isto no portal do Azure. Procure a conta de multimédia e, em seguida, clique nas **controlo de acesso** separador. 

![Guia de controle de acesso](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Tipos de autenticação 
 
Quando utiliza a autenticação do Azure AD com os serviços de multimédia do Azure, tem duas opções de autenticação:

- **Autenticação de utilizador**. Autenticar-se uma pessoa que está a utilizar a aplicação para interagir com os recursos de serviços de multimédia. O aplicativo interativo deve primeiro solicitar ao utilizador as credenciais do utilizador. Um exemplo é uma aplicação de consola de gestão utilizada por utilizadores autorizados para monitorizar tarefas de codificação ou transmissão em direto. 
- **Autenticação do principal de serviço**. Autenticar-se um serviço. Aplicativos geralmente usam este método de autenticação são as aplicações que executem serviços de daemon, serviços de camada intermediária ou tarefas agendadas. Os exemplos são aplicações web, aplicações de funções, logic apps, API e microsserviços.

### <a name="user-authentication"></a>Autenticação de utilizador 

Os aplicativos que devem utilizar o método de autenticação de utilizador são de gerenciamento ou monitoramento de aplicativos nativos: aplicações móveis, aplicações do Windows e aplicativos de Console. Este tipo de solução é útil quando desejar interação humana com o serviço em um dos seguintes cenários:

- Dashboard de monitorização para os trabalhos de codificação.
- Dashboard de monitorização para transmissões em fluxo em direto.
- Aplicação de gestão para utilizadores de ambiente de trabalho ou móveis administrar recursos numa conta de Media Services.

> [!NOTE]
> Este método de autenticação não deve ser utilizado para aplicações voltadas para consumidores. 

Um aplicativo nativo deve primeiramente adquirir um token de acesso do Azure AD e, em seguida, utilizá-lo quando efetuar pedidos HTTP para a API de REST de serviços de multimédia. Adicione o token de acesso para o cabeçalho do pedido. 

O diagrama seguinte mostra um fluxo de autenticação do aplicativo interativo típico: 

![Diagrama de aplicativos nativos](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

No diagrama anterior, os números representam o fluxo de pedidos por ordem cronológica.

> [!NOTE]
> Quando utiliza o método de autenticação de utilizador, todas as aplicações partilham o mesmo ID de cliente de aplicação nativa (predefinição) e o URI de redirecionamento da aplicação nativa. 

1. Um prompt ao usuário as credenciais.
2. Pedir um token de acesso do Azure AD com os seguintes parâmetros:  

   * Endpoint de inquilino do Azure AD.

       As informações de inquilino podem ser obtidas a partir do portal do Azure. Coloque o cursor sobre o nome de utilizador com sessão iniciada no canto superior direito.
   * URI do recurso de serviços de multimédia. 

       Este URI é o mesmo para contas de serviços de multimédia que estão no mesmo ambiente do Azure (por exemplo, https://rest.media.azure.net).

   * ID de cliente de aplicação de serviços de multimédia (nativo).
   * URI de redirecionamento da aplicação de serviços de multimédia (nativo).
   * URI do recurso para os serviços de multimédia REST.
        
       O URI representa o ponto de final de REST API (por exemplo, https://test03.restv2.westus.media.azure.net/api/).

     Para obter os valores para estes parâmetros, veja [utilizar o portal do Azure para aceder às definições de autenticação do Azure AD](media-services-portal-get-started-with-aad.md) utilizando a opção de autenticação de utilizador.

3. O token de acesso do Azure AD é enviado ao cliente.
4. O cliente envia um pedido à API de REST de multimédia do Azure com o token de acesso do Azure AD.
5. O cliente recebe de volta os dados dos serviços de multimédia.

Para obter informações sobre como utilizar a autenticação do Azure AD para comunicar com pedidos REST utilizando o SDK de cliente .NET de serviços de multimédia, veja [autenticação de utilização do Azure AD para aceder à API de serviços de multimédia com .NET](media-services-dotnet-get-started-with-aad.md). 

Se não estiver a utilizar o SDK de cliente .NET de serviços de suporte de dados, tem de criar manualmente um pedido de token de acesso do Azure AD com os parâmetros descritos no passo 2. Para obter mais informações, consulte [como utilizar o Azure AD Authentication Library para obter o token do Azure AD](../../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Autenticação do principal de serviço

Os aplicativos geralmente usam este método de autenticação são aplicações que executam serviços de camada intermediária e tarefas agendadas: web apps, aplicações de funções, aplicações lógicas, APIs e microsserviços. Este método de autenticação também é adequado para aplicativos interativos em que pode querer utilizar uma conta de serviço para gerir os recursos.

Quando utiliza o método de autenticação do principal de serviço para criar cenários de consumidor, autenticação, normalmente, é processada na camada intermediária (por meio de alguns API) e não diretamente num aplicativo móvel ou ambiente de trabalho. 

Para utilizar este método, crie uma aplicação do Azure AD e o serviço principal no seu próprio inquilino. Depois de criar o aplicativo, dar a acesso à função de Contribuidor ou proprietário da aplicação para a conta de Media Services. Pode fazer isso no portal do Azure, com a CLI do Azure, ou com um script do PowerShell. Também pode utilizar uma aplicação do Azure AD existente. Pode registar e gerir a sua aplicação do Azure AD e principal de serviço [no portal do Azure](media-services-portal-get-started-with-aad.md). Também pode fazer isso usando [CLI do Azure](media-services-use-aad-auth-to-access-ams-api.md) ou [PowerShell](media-services-powershell-create-and-configure-aad-app.md). 

![Aplicações de camada intermediária](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Depois de criar a sua aplicação do Azure AD, obter valores para as seguintes definições. Vai precisar destes valores para a autenticação:

- ID de Cliente 
- Segredo do cliente 

Na figura anterior, os números representam o fluxo de pedidos por ordem cronológica:
    
1. Uma aplicação de camada intermediária (web API ou aplicação web) solicita um token de acesso do Azure AD que tenha os seguintes parâmetros:  

   * Endpoint de inquilino do Azure AD.

       As informações de inquilino podem ser obtidas a partir do portal do Azure. Coloque o cursor sobre o nome de utilizador com sessão iniciada no canto superior direito.
   * URI do recurso de serviços de multimédia. 

       Este URI é o mesmo para contas de serviços de multimédia que se encontram no mesmo ambiente do Azure (por exemplo, https://rest.media.azure.net).

   * URI do recurso para os serviços de multimédia REST.

       O URI representa o ponto de final de REST API (por exemplo, https://test03.restv2.westus.media.azure.net/api/).

   * Valores de aplicações do Azure AD: o ID de cliente e o segredo de cliente.
    
     Para obter os valores para estes parâmetros, veja [utilizar o portal do Azure para aceder às definições de autenticação do Azure AD](media-services-portal-get-started-with-aad.md) utilizando a opção de autenticação do principal de serviço.

2. O token de acesso do Azure AD é enviado para a camada intermediária.
4. A camada média envia o pedido à API de REST de multimédia do Azure com o token do Azure AD.
5. A camada média volta obtém os dados dos serviços de multimédia.

Para obter mais informações sobre como utilizar a autenticação do Azure AD para comunicar com pedidos REST utilizando o SDK de cliente .NET de serviços de multimédia, veja [autenticação de utilização do Azure AD para aceder à API de serviços de multimédia do Azure com .NET](media-services-dotnet-get-started-with-aad.md). 

Se não estiver a utilizar o SDK de cliente .NET de serviços de suporte de dados, tem de criar manualmente um pedido de token do Azure AD utilizando os parâmetros descritos na etapa 1. Para obter mais informações, consulte [como utilizar o Azure AD Authentication Library para obter o token do Azure AD](../../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>Resolução de problemas

Exceção: "O servidor remoto devolveu um erro: Não autorizada (401)."

Solução: Para a solicitação de REST de serviços de suporte de dados tenha êxito, o utilizador chamador tem de ser uma função de proprietário ou contribuinte na conta de Media Services está a tentar aceder. Para obter mais informações, consulte a [controlo de acesso](media-services-use-aad-auth-to-access-ams-api.md#access-control) secção.

## <a name="resources"></a>Recursos

Os seguintes artigos são proporciona uma visão geral dos conceitos de autenticação do Azure AD: 

- [Cenários de autenticação abordados pelo Azure AD](../../active-directory/develop/authentication-scenarios.md)
- [Adicionar, atualizar ou remover uma aplicação no Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)
- [Configurar e gerir o controlo de acesso baseado em funções com o PowerShell](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Passos Seguintes

* Utilizar o portal do Azure para [autenticação de acesso do Azure AD para consumir API de serviços de multimédia do Azure](media-services-portal-get-started-with-aad.md).
* Autenticação de utilização do Azure AD para [aceder à API de serviços de multimédia do Azure com .NET](media-services-dotnet-get-started-with-aad.md).

