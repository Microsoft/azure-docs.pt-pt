---
title: Verificação de editores de resolução de problemas - Plataforma de identidade da Microsoft Rio Azure
description: Descreve como resolver problemas na verificação do editor (pré-visualização) para a plataforma de identidade da Microsoft, chamando APIs do Microsoft Graph.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: dcdce6ee6683c5770f97f5f3dc20e1c9b409ead0
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85477044"
---
# <a name="troubleshoot-publisher-verification-preview"></a>Resolver problemas da verificação do publicador (pré-visualização)
Se não conseguir completar o processo ou estiver a experimentar comportamentos inesperados com [a verificação do editor (pré-visualização)](publisher-verification-overview.md), deve começar por fazer o seguinte se estiver a receber erros ou a ver comportamentos inesperados: 

1. Reveja os [requisitos](publisher-verification-overview.md#requirements) e certifique-se de que todos foram cumpridos.

1. Reveja as instruções para [marcar uma aplicação como editor verificado](mark-app-as-publisher-verified.md) e certifique-se de que todas as etapas foram executadas com sucesso.

1. Reveja a lista de [questões comuns.](#common-issues)

1. Reproduza o pedido utilizando o [Graph Explorer](#making-microsoft-graph-api-calls) para recolher informações adicionais e excluir quaisquer problemas na UI.

## <a name="common-issues"></a>Problemas Comuns
Abaixo estão algumas questões comuns que podem ocorrer durante o processo. 

- **Não conheço o meu ID da Rede de Parceiros da Microsoft (MPN ID) ou não sei quem é o principal contacto para a conta** 
    1. Navegue para a [página de inscrição mpn](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new)
    1. Inscreva-se com uma conta de utilizador no inquilino principal da Azure AD da org 
    1. Se uma conta MPN já existir, esta será reconhecida e você será adicionado à conta 
    1. Navegue para a página de perfil do [parceiro](https://partner.microsoft.com/en-us/pcv/accountsettings/connectedpartnerprofile) onde o ID MPN e o contacto de conta primária serão listados

- **Não sei quem é o meu administrador global da Azure AD (também conhecido como Administrador da Empresa ou Administrador Inquilino) como é que os encontro? E o Administrador da App, ou um papel de administração diferente?**
    1. Inscreva-se no [Portal AD Azure](https://aad.portal.azure.com) usando uma conta de utilizador no principal inquilino da sua organização
    1. Navegar para a [Gestão de Papéis](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)
    1. Clique em "Administrador Global", ou no papel de administrador desejado
    1. A lista de utilizadores designados para que o papel será exibida

- **Eu não sei quem são os administradores para a minha conta MPN** Vá à [página mpn User Management](https://partner.microsoft.com/en-us/pcv/users) e filtre a lista de utilizadores para ver quais os utilizadores em várias funções de administração.

- **Estou a receber um erro a dizer que o meu MPN ID é inválido ou que não tenho acesso a ele.**
    1. Vá ao perfil do seu [parceiro](https://partner.microsoft.com/en-us/pcv/accountsettings/connectedpartnerprofile) e verifique se: 
        - O ID da MPN está correto. 
        - Não há erros ou "ações pendentes" mostrados, e o estado de verificação no perfil de negócio legal e informações de Parceiro ambos dizem "autorizado" ou "sucesso".
    1. Vá à [página de gestão](https://partner.microsoft.com/en-us/dashboard/account/v3/tenantmanagement) de inquilinos da MPN e confirme que o inquilino em que a app está registada e que está a assinar com uma conta de utilizador de que está na lista de inquilinos associados.
    1. Vá à [página mpn User Management](https://partner.microsoft.com/en-us/pcv/users) e confirme o utilizador em que está a iniciar sessão, tal como é um Administrador Global, MPN Admin ou Administração de Contas.

- **Quando assino no portal AD Azure, não vejo nenhuma aplicação registada. Porquê?** 
    Os registos da sua aplicação podem ter sido criados usando uma conta de utilizador diferente, ou num inquilino diferente. Certifique-se de que está inscrito com a conta correta no arrendatário onde foram criados os registos da sua aplicação.

- **Como é que eu sei quem é o dono de uma aplicação no Azure AD?** 
    Quando se inscrever num inquilino onde a app está registada, navegue para a lâmina de Registos de Aplicações, clique numa aplicação e, em seguida, clique em Proprietários.

## <a name="making-microsoft-graph-api-calls"></a>Fazer chamadas API do Microsoft Graph 

Se estiver a ter um problema mas não conseguir perceber porquê, com base no que está a ver na UI, poderá ser útil realizar mais resoluções de problemas utilizando chamadas do Microsoft Graph para executar as mesmas operações que pode realizar no portal De Registo de Aplicações. Durante a fase de pré-visualização, estas APIs só estarão disponíveis no ponto final /beta do Microsoft Graph.  

A forma mais fácil de fazer estes pedidos é usar [o Graph Explorer.](https://developer.microsoft.com/graph/graph-explorer) Também pode considerar outras opções como usar [o Carteiro,](https://www.postman.com/)ou usar o PowerShell para [invocar um pedido web.](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7)  

Pode utilizar o Microsoft Graph tanto para definir como para desmarcar o editor verificado da sua aplicação e verificar o resultado depois de efetuar uma destas operações. O resultado pode ser visto tanto no objeto da [aplicação](/graph/api/resources/application?view=graph-rest-beta) correspondente ao registo da sua app como em quaisquer princípios de serviço que tenham sido [instantâneos](/graph/api/resources/serviceprincipal?view=graph-rest-beta) a partir dessa aplicação. Para obter mais informações sobre a relação entre esses objetos, consulte: [Aplicação e serviço principais objetos no Azure Ative Directory](app-objects-and-service-principals.md).  

Aqui estão exemplos de alguns pedidos úteis:  

### <a name="set-verified-publisher"></a>Definir Editor Verificado 

Pedir

```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/setVerifiedPublisher 

{ 

    "verifiedPublisherId": "12345678" 

} 
```
 
Resposta 
```
204 No Content 
```
> [!NOTE]
> *verificadoPublisherID* é o seu ID MPN. 

### <a name="unset-verified-publisher"></a>Editor verificado da Unet 

Pedido:  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
Resposta 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>Obtenha informações de Editores Verificados da Aplicação 
 
```
GET https://graph.microsoft.com/beta/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

HTTP/1.1 200 OK 

{ 
    "id": "0cd04273-0d11-4e62-9eb3-5c3971a7cbec", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

### <a name="get-verified-publisher-info-from-service-principal"></a>Obtenha informações de editores verificados do diretor de serviço 
```
GET https://graph.microsoft.com/beta/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

HTTP/1.1 200 OK 

{ 
    "id": "010422a7-4d77-4f40-9335-b81ef5c22dd4", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

## <a name="error-reference"></a>Referência de erro 

Segue-se uma lista dos potenciais códigos de erro que poderá receber, quer quando se desresem os problemas com o Microsoft Graph, quer através do processo no portal de registo de aplicações.

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundOrNoAccess     

O ID MPN que forneceu <MPNID> não existe, ou não tem acesso a ele. Forneça um ID MPN válido e tente novamente. 

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound     

O ID MPN que forneceu <MPNID> não é válido. Forneça um ID MPN válido e tente novamente. 

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid    

O ID MPN que forneceu <MPNID> não é válido. Forneça um ID MPN válido e tente novamente. 

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted  

O MPN ID ( <MPNID> ) que forneceu não completou o processo de verificação. Complete este processo no Partner Center e tente novamente. 

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount  

O ID MPN que forneceu <MPNID> não é válido. Forneça um ID MPN válido e tente novamente. 

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount    

O ID MPN que forneceu <MPNID> não é válido. Forneça um ID MPN válido e tente novamente. 

### <a name="applicationnotfound"></a>AplicaçãoNotFound  

O pedido-alvo <AppId> () não pode ser encontrado. Forneça um ID de aplicação válido e tente novamente. 

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed  

Esta capacidade não é suportada num inquilino Azure AD B2C. 

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed    

Esta capacidade não é suportada num inquilino verificado por e-mail. 

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication   

A aplicação-alvo <AppId> () tem de ter um conjunto de Domínio editor. Desaça um domínio de editor e tente novamente. 

### <a name="publisherdomainisnotdnsverified"></a>PublisherDomainIsNotdNSVerified  

O domínio editor da aplicação-alvo <publisherDomain> não é um domínio verificado neste inquilino. Verifique um domínio do inquilino utilizando a verificação de DNS e tente novamente. 

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch  

O domínio de editor da aplicação-alvo <publisherDomain> () não corresponde ao domínio utilizado para efetuar a verificação de e-mail no Partner Center ( <pcDomain> ). Certifique-se de que estes domínios coincidem e tente novamente. 

### <a name="notauthorizedtoverifypublisher"></a>Não éautorizadoToVerifyPublisher   

Não está autorizado a definir o imóvel de editor verificado a pedido <AppId> () 

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided  

O ID MPN não foi fornecido no órgão de pedido ou o tipo de conteúdo do pedido não era "aplicação/json". 

### <a name="msanotsupported"></a>MSANotSupportado  

Esta funcionalidade não é suportada para contas de consumidores da Microsoft. Apenas as aplicações registadas no Azure AD por um utilizador AZure AD são suportadas. 

## <a name="next-steps"></a>Passos seguintes

Se tiver revisto todas as informações anteriores e ainda estiver a receber um erro do Microsoft Graph, recolha o máximo de informações possíveis relacionadas com o pedido de falha e contacte o suporte da [Microsoft.](developer-support-help-options.md#open-a-support-request)

- Carimbo de data/hora 
- CorrelationId 
- ObjectID ou UserPrincipalName assinado no utilizador 
- ObjectId da aplicação-alvo
- Aplicação de destino
- TenantId onde a app está registada
- ID do MPN
- Pedido de DESCANSO a ser feito 
- Código de erro e mensagem sendo devolvidos 
