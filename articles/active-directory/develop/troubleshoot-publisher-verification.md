---
title: '| de verificação de editores de resolução de problemas Rio Azure'
titleSuffix: Microsoft identity platform
description: Descreve como resolver problemas na verificação de editores para a plataforma de identidade da Microsoft, chamando APIs do Microsoft Graph.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 01/28/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 9ae5d2279baab69d762a71b2f6400009747c16fe
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584218"
---
# <a name="troubleshoot-publisher-verification"></a>Resolver problemas da verificação do publicador
Se não conseguir completar o processo ou estiver a experimentar comportamentos inesperados com [a verificação do editor,](publisher-verification-overview.md)deve começar por fazer o seguinte se estiver a receber erros ou a ver comportamentos inesperados: 

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
    1. Navegue para a página de perfil do [parceiro](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) onde o ID MPN e o contacto de conta primária serão listados

- **Não sei quem é o meu administrador global da Azure AD (também conhecido como administrador da empresa ou administrador de inquilinos) como é que os encontro? E o Administrador de Aplicações ou Administrador de Aplicações em Nuvem?**
    1. Inscreva-se no [Portal AD Azure](https://aad.portal.azure.com) usando uma conta de utilizador no principal inquilino da sua organização
    1. Navegar para a [Gestão de Papéis](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)
    1. Clique no papel de administração desejado
    1. A lista de utilizadores designados para que o papel será exibida

- **Eu não sei quem são os administradores para a minha conta MPN** Vá à [página mpn User Management](https://partner.microsoft.com/pcv/users) e filtre a lista de utilizadores para ver quais os utilizadores em várias funções de administração.

- **Estou a receber um erro a dizer que o meu MPN ID é inválido ou que não tenho acesso a ele.**
    1. Vá ao perfil do seu [parceiro](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) e verifique se: 
        - O ID da MPN está correto. 
        - Não há erros ou "ações pendentes" mostrados, e o estado de verificação no perfil de negócio legal e informações de Parceiro ambos dizem "autorizado" ou "sucesso".
    1. Vá à [página de gestão](https://partner.microsoft.com/dashboard/account/v3/tenantmanagement) de inquilinos da MPN e confirme que o inquilino em que a app está registada e que está a assinar com uma conta de utilizador de que está na lista de inquilinos associados. Para adicionar um inquilino adicional, siga as instruções [aqui.](/partner-center/multi-tenant-account) Por favor, esteja ciente de que todos os Administradores Globais de qualquer inquilino que adicionar receberão privilégios de Administração Global na sua conta partner Center.
    1. Vá à [página mpn User Management](https://partner.microsoft.com/pcv/users) e confirme o utilizador em que está a iniciar sessão, tal como é um Administrador Global, MPN Admin ou Administração de Contas. Para adicionar um utilizador a uma função no Partner Center, siga as instruções [aqui](/partner-center/create-user-accounts-and-set-permissions).

- **Quando assino no portal AD Azure, não vejo nenhuma aplicação registada. Porquê?** 
    Os registos da sua aplicação podem ter sido criados utilizando uma conta de utilizador diferente neste inquilino, uma conta pessoal/consumidor, ou num inquilino diferente. Certifique-se de que está inscrito com a conta correta no arrendatário onde foram criados os registos da sua aplicação.

- **Estou a receber um erro relacionado com a autenticação de vários fatores. O que devo fazer?** 
    Certifique-se de que [a autenticação multi-factor](../fundamentals/concept-fundamentals-mfa-get-started.md) está ativada e **necessária** para o utilizador com quem está a iniciar sessão e para este cenário. Por exemplo, o MFA pode ser:
    - Sempre necessário para o utilizador com quem está a iniciar sessão
    - [Requerido para a gestão do Azure.](../conditional-access/howto-conditional-access-policy-azure-management.md)
    - [Requerido para o tipo de administrador](../conditional-access/howto-conditional-access-policy-admin-mfa.md) com o que está a iniciar sessão.

## <a name="making-microsoft-graph-api-calls"></a>Fazer chamadas API do Microsoft Graph 

Se estiver a ter um problema mas não conseguir perceber porquê, com base no que está a ver na UI, poderá ser útil realizar mais resoluções de problemas utilizando chamadas do Microsoft Graph para executar as mesmas operações que pode realizar no portal De Registo de Aplicações.

A forma mais fácil de fazer estes pedidos é usar [o Graph Explorer.](https://developer.microsoft.com/graph/graph-explorer) Também pode considerar outras opções como usar [o Carteiro,](https://www.postman.com/)ou usar o PowerShell para [invocar um pedido web.](/powershell/module/microsoft.powershell.utility/invoke-webrequest)  

Pode utilizar o Microsoft Graph tanto para definir como para desmarcar o editor verificado da sua aplicação e verificar o resultado depois de efetuar uma destas operações. O resultado pode ser visto tanto no objeto da [aplicação](/graph/api/resources/application) correspondente ao registo da sua app como em quaisquer princípios de serviço que tenham sido [instantâneos](/graph/api/resources/serviceprincipal) a partir dessa aplicação. Para obter mais informações sobre a relação entre esses objetos, consulte: [Aplicação e serviço principais objetos no Azure Ative Directory](app-objects-and-service-principals.md).  

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
GET https://graph.microsoft.com/v1.0/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

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
GET https://graph.microsoft.com/v1.0/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

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

O ID MPN que forneceu `MPNID` não existe, ou não tem acesso a ele. Forneça um ID MPN válido e tente novamente.
    
Mais frequentemente causado pelo utilizador inscrito não ser membro do papel adequado para a conta MPN no Partner Center- ver [requisitos](publisher-verification-overview.md#requirements) para uma lista de funções elegíveis e ver [questões comuns](#common-issues) para mais informações. Também pode ser causado pelo inquilino a aplicação está registada em não ser adicionada à conta MPN, ou um ID MPN inválido.

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound

O ID MPN que forneceu `MPNID` não é válido. Forneça um ID MPN válido e tente novamente.
    
Causada mais frequentemente quando um ID MPN é fornecido que corresponde a uma Conta de Localização de Parceiro (PLA). Apenas as Contas Globais de Parceiros são suportadas. Consulte [a estrutura da conta partner Center](/partner-center/account-structure) para obter mais detalhes.

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid

O ID MPN que forneceu `MPNID` não é válido. Forneça um ID MPN válido e tente novamente.
    
Mais frequentemente causada pela identificação de MPN errada.

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted

O MPN ID ( `MPNID` ) que forneceu não completou o processo de verificação. Complete este processo no Partner Center e tente novamente. 
    
Mais frequentemente causada por quando a conta MPN não tiver concluído o processo [de verificação.](/partner-center/verification-responses)

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount

O ID MPN que forneceu `MPNID` não é válido. Forneça um ID MPN válido e tente novamente. 
   
Mais frequentemente causada pela identificação de MPN errada.

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount

O ID MPN que forneceu `MPNID` não é válido. Forneça um ID MPN válido e tente novamente.
    
Mais frequentemente causada pela identificação de MPN errada.

### <a name="applicationnotfound"></a>AplicaçãoNotFound

O pedido-alvo `AppId` () não pode ser encontrado. Forneça um ID de aplicação válido e tente novamente.
    
A maior parte das vezes causada quando a verificação é realizada através da API do gráfico, e o id da aplicação fornecida é incorreto. Nota: o id do pedido deve ser fornecido, não o AppId/ClientId.

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed

Esta capacidade não é suportada num inquilino Azure AD B2C.

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed

Esta capacidade não é suportada num inquilino verificado por e-mail.

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication

A aplicação-alvo `AppId` () deve ter um conjunto de Domínio editor. Desaça um domínio de editor e tente novamente.

Ocorre quando um [Domínio de Editor](howto-configure-publisher-domain.md) não está configurado na aplicação.

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch

O domínio de editor da aplicação-alvo `publisherDomain` () não corresponde ao domínio utilizado para efetuar a verificação de e-mail no Partner Center ( `pcDomain` ). Certifique-se de que estes domínios coincidem e tente novamente. 
    
Ocorre quando nem o Domínio de [Editor da](howto-configure-publisher-domain.md) aplicação nem um dos [domínios personalizados adicionados](../fundamentals/add-custom-domain.md) ao inquilino AD AZure correspondem ao domínio utilizado para realizar a verificação de e-mail no Partner Center.

### <a name="notauthorizedtoverifypublisher"></a>Não éautorizadoToVerifyPublisher

Não está autorizado a definir o imóvel de editor verificado a pedido `AppId` (<) 
  
Mais frequentemente causado pelo utilizador inscrito não ser membro do papel adequado para a conta MPN em Azure AD- ver [requisitos](publisher-verification-overview.md#requirements) para uma lista de funções elegíveis e ver [questões comuns](#common-issues) para mais informações.

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided

O ID MPN não foi fornecido no órgão de pedido ou o tipo de conteúdo do pedido não era "aplicação/json".

### <a name="msanotsupported"></a>MSANotSupportado 

Esta funcionalidade não é suportada para contas de consumidores da Microsoft. Apenas as aplicações registadas no Azure AD por um utilizador AZure AD são suportadas.

### <a name="interactionrequired"></a>InteracçãoRequired

Ocorre quando a autenticação de vários fatores não foi realizada antes de tentar adicionar um editor verificado à aplicação. Consulte [as questões comuns](#common-issues) para mais informações. Nota: O MFA deve ser realizado na mesma sessão quando se tenta adicionar um editor verificado. Se o MFA estiver ativado mas não for necessário para ser realizado na sessão, o pedido falhará. 

A mensagem de erro exibida será: "Devido a uma alteração de configuração feita pelo seu administrador, ou porque se mudou para um novo local, deve utilizar a autenticação de vários fatores para prosseguir."

### <a name="unabletoaddpublisher"></a>ImperávelToAddPublisher

A mensagem de erro apresentada é: "Um editor verificado não pode ser adicionado a esta aplicação. Por favor contacte o seu administrador para obter assistência."

Primeiro, verifique se cumpriu os requisitos de verificação da [editora.](publisher-verification-overview.md#requirements)

Quando um pedido de adição de um editor verificado é feito, um número de sinais são usados para fazer uma avaliação de risco de segurança. Se o pedido for determinado como arriscado, será devolvido um erro. Por razões de segurança, a Microsoft não divulga os critérios específicos utilizados para determinar se um pedido é arriscado ou não.

## <a name="next-steps"></a>Passos seguintes

Se tiver revisto todas as informações anteriores e ainda estiver a receber um erro do Microsoft Graph, recolha o máximo de informações possíveis relacionadas com o pedido de falha e contacte o suporte da [Microsoft.](developer-support-help-options.md#open-a-support-request)

- CarimboDeDataEHora 
- CorrelationId 
- ObjectID ou UserPrincipalName assinado no utilizador 
- ObjectId da aplicação-alvo
- Aplicação de destino
- TenantId onde a app está registada
- ID do MPN
- Pedido de DESCANSO a ser feito 
- Código de erro e mensagem sendo devolvidos