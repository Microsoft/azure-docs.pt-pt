---
title: Verificação de editores de troubleshoot - plataforma de identidade da Microsoft Azure
description: Descreve como resolver a verificação da editora (pré-visualização) para a plataforma de identidade da Microsoft, chamando o Microsoft Graph APIs.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: cf886b7b43280e542f1941e7c0edb570868525d9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598179"
---
# <a name="troubleshoot-publisher-verification-preview"></a>Verificação da editora de troubleshoot (pré-visualização)
Se não conseguir completar o processo ou estiver a ter um comportamento inesperado com a verificação do [editor (pré-visualização),](publisher-verification-overview.md)deve começar por fazer o seguinte se estiver a receber erros ou a ver comportamentos inesperados: 

1. Reveja os [requisitos](publisher-verification-overview.md#requirements) e certifique-se de que todos foram cumpridos.

1. Reveja as instruções para [marcar uma aplicação como a editora verificou](mark-app-as-publisher-verified.md) e certifique-se de que todos os passos foram realizados com sucesso.

1. Reveja a lista de [questões comuns.](#common-issues)

1. Reproduza o pedido utilizando o [Graph Explorer](#making-microsoft-graph-api-calls) para recolher informações adicionais e excluir quaisquer problemas na UI.

## <a name="common-issues"></a>Problemas Comuns
Abaixo estão algumas questões comuns que podem ocorrer durante o processo. 

- **Não conheço o meu ID de Rede de Parceiros microsoft (MPN ID) ou não quem é o principal contacto para a conta** 
    1. Navegue para a [página de inscrição](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new) do MPN
    1. Inscreva-se com uma conta de utilizador no principal inquilino azure da org 
    1. Se já existir uma conta MPN, esta será reconhecida e será adicionada à conta 
    1. Navegue na página de perfil do [parceiro](https://partner.microsoft.com/en-us/pcv/accountsettings/connectedpartnerprofile) onde o ID mpn e o conta principal serão listados

- **Não sei quem é o meu Administrador Global Azure AD (também conhecido como Administrador da Empresa ou Administrador de Inquilinos), como os encontro? E o Administrador da App, ou um papel de administrador diferente?**
    1. Inscreva-se no [Portal AD Azure](https://aad.portal.azure.com) utilizando uma conta de utilizador no principal inquilino da sua organização
    1. Navegar para gestão de [funções](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)
    1. Clique em "Administrador Global", ou na função de administrador desejada
    1. A lista de utilizadores atribuídos a essa função será exibida

- **Não sei quem são os administradores para a minha conta MPN** Vá à página de Gestão de [Utilizadores da MPN](https://partner.microsoft.com/en-us/pcv/users) e filtre a lista de utilizadores para ver quais os utilizadores em várias funções de administração.

- **Estou a ter um erro ao dizer que a minha identificação da MPN é inválida ou que não tenho acesso a ela.**
    1. Vá ao perfil do seu [parceiro](https://partner.microsoft.com/en-us/pcv/accountsettings/connectedpartnerprofile) e verifique se: 
        - A identificação da MPN está correta. 
        - Não existem erros ou "ações pendentes" demonstrados, e o estado de verificação ao abrigo do perfil de negócio legal e da informação do Parceiro dizem "autorizado" ou "sucesso".
    1. Vá à página de gestão de inquilinos da [MPN](https://partner.microsoft.com/en-us/dashboard/account/v3/tenantmanagement) e confirme que o inquilino em que a app está registada e que está a assinar com uma conta de utilizador está na lista de inquilinos associados.
    1. Vá à página de Gestão de [Utilizadores mpN](https://partner.microsoft.com/en-us/pcv/users) e confirme o utilizador em que está a assinar, tal como um Administrador Global, mpN Admin ou Accounts Admin.

- **Quando inscrevo no portal Azure AD, não vejo nenhuma aplicação registada. Porquê?** 
    Os registos da sua aplicação podem ter sido criados através de uma conta de utilizador diferente, ou num inquilino diferente. Certifique-se de que está inscrito na conta correta no inquilino onde foram criados os registos da sua aplicação.

- **Como sei quem é o proprietário de uma inscrição de aplicações no Azure AD?** 
    Quando assinou com um inquilino onde a aplicação está registada, navegue para a lâmina de registos de aplicações, clique numa aplicação e clique em Proprietários.

## <a name="making-microsoft-graph-api-calls"></a>Fazer chamadas aPi do Microsoft Graph 

Se está a ter um problema mas incapaz de compreender porque é que, com base no que está a ver na UI, pode ser útil realizar mais resoluções de problemas utilizando chamadas do Microsoft Graph para realizar as mesmas operações que pode realizar no portal de Registo de Aplicações. Durante a fase de pré-visualização, estas APIs só estarão disponíveis no ponto final /beta do Microsoft Graph.  

A maneira mais fácil de fazer estes pedidos é usar [o Graph Explorer.](https://developer.microsoft.com/graph/graph-explorer) Também pode considerar outras opções como usar [o Carteiro,](https://www.postman.com/)ou usar o PowerShell para [invocar um pedido web](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7).  

Pode utilizar o Microsoft Graph para definir e desdefinir o editor verificado da sua aplicação e verificar o resultado depois de realizar uma destas operações. O resultado pode ser visto tanto no objeto de [aplicação](/graph/api/resources/application?view=graph-rest-beta) correspondente ao registo da sua aplicação como em quaisquer [diretores](/graph/api/resources/serviceprincipal?view=graph-rest-beta) de serviço que tenham sido instantaneamente instantâneos a partir dessa aplicação. Para obter mais informações sobre a relação entre esses objetos, consulte: [Aplicação e serviço principais objetos no Diretório Ativo azure](app-objects-and-service-principals.md).  

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
> *verificadoEditorID* é o seu ID MPN. 

### <a name="unset-verified-publisher"></a>Editora Verificada Desset 

Pedido:  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
Resposta 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>Obtenha informações da Editora Verificada da Aplicação 
 
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

### <a name="get-verified-publisher-info-from-service-principal"></a>Obtenha informações da Editora Verificada do Diretor de Serviço 
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

Segue-se uma lista dos potenciais códigos de erro que poderá receber, quer quando se resolve mariscos com o Microsoft Graph, quer através do processo no portal de registo da aplicação.

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundornoAccess     

A identificação mpn que forneceu <MPNID> não existe, ou não tem acesso a ela. Forneça uma identificação mpn válida e tente novamente. 

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound     

A identificação mpn que forneceu <MPNID> não é válida. Forneça uma identificação mpn válida e tente novamente. 

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid    

A identificação mpn que forneceu <MPNID> não é válida. Forneça uma identificação mpn válida e tente novamente. 

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted  

O ID MPN ( ) que forneceu não concluiu o processo de <MPNID> verificação. Complete este processo no Partner Center e tente novamente. 

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount  

A identificação mpn que forneceu <MPNID> não é válida. Forneça uma identificação mpn válida e tente novamente. 

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount    

A identificação mpn que forneceu <MPNID> não é válida. Forneça uma identificação mpn válida e tente novamente. 

### <a name="applicationnotfound"></a>AplicaçõesNotFound  

A aplicação-alvo <AppId> não pode ser encontrada. Forneça um ID de aplicação válido e tente novamente. 

### <a name="b2ctenantnotallowed"></a>B2CTenantNotallowed  

Esta capacidade não é suportada num inquilino Azure AD B2C. 

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed    

Esta capacidade não é suportada num e-mail verificado inquilino. 

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication   

A aplicação-alvo () deve ter um conjunto de <AppId> domínio da editora. Despeça um Domínio da Editora e tente novamente. 

### <a name="publisherdomainisnotdnsverified"></a>PublisherDomainIsNotDNSVerified  

O Domínio editor da aplicação-alvo <publisherDomain> () não é um domínio verificado neste inquilino. Verifique um domínio de inquilino usando a verificação de DNS e tente novamente. 

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch  

O Domínio editor da aplicação alvo <publisherDomain> () não corresponde ao domínio utilizado para efetuar verificação de email no Partner Center <pcDomain> ( ). Certifique-se de que estes domínios correspondem e tente novamente. 

### <a name="notauthorizedtoverifypublisher"></a>Não AutorizadoA VerificarEditor   

Não está autorizado a definir a propriedade da editora verificada no pedido ( <AppId> ) 

### <a name="mpnidwasnotprovided"></a>MPNIDWasNotProvided  

O MPN ID não foi fornecido no órgão de pedido ou o tipo de conteúdo solicitado não era "pedido/json". 

### <a name="msanotsupported"></a>MSANotSupported  

Esta funcionalidade não é suportada para as contas de consumo da Microsoft. Apenas são suportadas aplicações registadas em Azure AD por um utilizador da AD Azure. 

## <a name="next-steps"></a>Passos seguintes

Se tiver revisto todas as informações anteriores e ainda estiver a receber um erro do Microsoft Graph, recolha o máximo de informações que possíveis relacionadas com o pedido de falha e [contacte](developer-support-help-options.md#open-a-support-request)o suporte da Microsoft .

- Carimbo de data/hora 
- CorrelationId 
- ObjectID ou UserPrincipalName de assinado no utilizador 
- ObjectId da aplicação alvo
- AppId da aplicação alvo
- TenantId onde a app está registada
- ID do MPN
- Pedido de REPOUSO sendo feito 
- Código de erro e mensagem a ser devolvida 
