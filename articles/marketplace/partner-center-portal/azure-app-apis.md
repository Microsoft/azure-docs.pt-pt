---
title: Partner Center submissão API a apps a bordo da Azure no mercado comercial da Microsoft
description: Aprenda os pré-requisitos para utilizar a API de submissão do Partner Center para aplicações Azure no mercado comercial no Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: fec52caa3cb7565a86a6c50d300fe448b6518457
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320073"
---
# <a name="partner-center-submission-api-to-onboard-azure-apps-in-partner-center"></a>Parceiro Center submissão API para apps Azure a bordo no Partner Center

Utilize a *API de submissão do Centro parceiro* para consulta programática, crie submissões e publique ofertas Azure.  Esta API é útil se a sua conta gere muitas ofertas e pretende automatizar e otimizar o processo de submissão destas ofertas.

## <a name="api-prerequisites"></a>Pré-requisitos da API

Existem alguns ativos programáticos que você precisa para usar o Centro Parceiro API para Produtos Azure: 

- um pedido de Diretório Ativo Azure.
- um token de acesso Azure Ative Directory (Azure AD).

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Passo 1: Pré-requisitos completos para a utilização da API de submissão do Centro Parceiro

Antes de começar a escrever código para ligar para a API de submissão do Centro Parceiro, certifique-se de que completou os seguintes pré-requisitos.

- Você (ou sua organização) deve ter um diretório AD Azure e você deve ter permissão [de administrador global](../../active-directory/users-groups-roles/directory-assign-admin-roles.md) para o diretório. Se já utiliza o Microsoft 365 ou outros serviços empresariais da Microsoft, já tem o diretório AD AZure. Caso contrário, pode [criar um novo AD Azure no Partner Center](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) sem custos adicionais.

- Deve [associar uma aplicação AD da Azure à sua conta partner Center](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) e obter a identificação do seu inquilino, identificação do cliente e chave. Precisa destes valores para obter um token de acesso AD Azure, que utilizará em chamadas para a API de submissão da Microsoft Store.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Como associar uma aplicação AD AZure com a sua conta Partner Center

Para utilizar a API de submissão da Microsoft Store, deve associar uma aplicação AD Azure à sua conta Partner Center, recuperar o ID do inquilino e o ID do cliente para a aplicação e gerar uma chave. A aplicação AZure AD representa a app ou serviço a partir do qual pretende chamar a API de submissão do Centro Parceiro. Você precisa da identificação do inquilino, identificação do cliente e chave para obter um sinal de acesso AD Azure que você passa para a API.

>[!Note]
>Só precisas de executar esta tarefa uma vez. Depois de ter a identificação do inquilino, identificação do cliente e chave, pode reutilizá-los sempre que precisar para criar um novo token de acesso AD Azure.

1. No Partner Center, [associe a conta partner center da sua organização com o diretório AD da sua organização.](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center)
1. Em seguida, a partir da página **de Utilizadores** na secção de **definições** de Conta do Partner Center, [adicione a aplicação AD Azure](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) que representa a app ou serviço que utilizará para aceder às submissões para a sua conta Partner Center. Certifique-se de atribuir esta aplicação à função **Manager.** Se a aplicação ainda não existir no seu diretório AD Azure, pode [criar uma nova aplicação AD AZure no Partner Center](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. Volte à página **do Utilizadores,** clique no nome da sua aplicação AD Azure para ir às definições da aplicação e copie os valores de ID e **Identificação** do Cliente do **Inquilino.**
1. Clique **Em Adicionar nova tecla**. No ecrã seguinte, copie o valor **chave.** Não poderá aceder a esta informação novamente depois de sair desta página. Para obter mais informações, consulte [gerir as teclas para uma aplicação AD Azure](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Passo 2: Obter um token de acesso AD Azure

Antes de ligar para qualquer um dos métodos da API de submissão do Centro Parceiro, deve primeiro obter um sinal de acesso AD Azure que passe para o cabeçalho de **autorização** de cada método na API. Depois de obter um token de acesso, tem 60 minutos para usá-lo antes de expirar. Após o fim do token, pode refrescar o token para que possa continuar a usá-lo em futuras chamadas para a API.

Para obter o token de acesso, siga as instruções em [Serviço para Chamadas de Serviço Usando credenciais](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/) de cliente para enviar um para o ponto `HTTP POST` `https://login.microsoftonline.com/<tenant_id>/oauth2/token` final. Aqui está um pedido de amostra:

JSONCopy
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

Para o *valor tenant_id* nos `POST URI` parâmetros *client_id* e *client_secret,* especifique o ID do inquilino, o ID do cliente e a chave para a sua aplicação que recuperou do Partner Center na secção anterior. Para o parâmetro *de recurso,* deve especificar `https://api.partner.microsoft.com` .

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Passo 3: Utilize a API de submissão da Microsoft Store

Depois de ter um token de acesso AD Azure, pode ligar para métodos na API de submissão do Centro Parceiro. Para criar ou atualizar submissões, normalmente, você liga vários métodos na API de submissão do Centro Parceiro numa ordem específica. Para obter informações sobre cada cenário e a sintaxe de cada método, consulte a API ingestion.

https://apidocs.microsoft.com/services/partneringestion/

## <a name="next-steps"></a>Passos seguintes

* Saiba como criar [um ativo técnico Azure VM](create-azure-container-technical-assets.md)
* Saiba como criar uma oferta de [recipiente azure](create-azure-container-offer.md)
