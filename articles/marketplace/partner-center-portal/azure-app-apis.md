---
title: Asubmissão do Partner Center a apps Azure a bordo no mercado comercial da Microsoft
description: Aprenda os pré-requisitos para utilizar a Submissão do Partner Center API para aplicações Azure no mercado comercial no Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: e50534639f5f46b1675ba5c074fb8fdd843ac87e
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770062"
---
# <a name="partner-center-submission-api-to-onboard-azure-apps-in-partner-center"></a>Submissão do Partner Center API para aplicações azure a bordo no Partner Center

Utilize a Submissão do *Partner Center API* para consulta programática, crie submissões para e publique ofertas azure.  Esta API é útil se a sua conta gere muitas ofertas e pretende automatizar e otimizar o processo de submissão destas ofertas.

## <a name="api-prerequisites"></a>Pré-requisitos da API

Existem alguns ativos programáticos que você precisa para usar o Partner Center API para produtos Azure: 

- uma aplicação azure Ative Diretório.
- um sinal de acesso azure Ative Directory (Azure AD).

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Passo 1: Preencha os pré-requisitos para a utilização da API de submissão do Partner Center

Antes de começar a escrever código para ligar para a API de submissão do Partner Center, certifique-se de que preencheu os seguintes pré-requisitos.

- Você (ou a sua organização) deve ter um diretório Azure AD e você deve ter permissão de [administrador global](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para o diretório. Se já utiliza o Office 365 ou outros serviços empresariais da Microsoft, já tem um diretório Azure AD. Caso contrário, pode [criar um novo Azure AD no Partner Center](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) sem custos adicionais.

- Deve [associar um pedido de AD Azure à sua conta partner center](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) e obter o seu ID de inquilino, identificação do cliente e chave. Precisa destes valores para obter um token de acesso AD Azure, que utilizará em chamadas para a API de submissão da Microsoft Store.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Como associar uma aplicação Azure AD à sua conta Partner Center

Para utilizar a API de submissão da Microsoft Store, deve associar uma aplicação Azure AD à sua conta Partner Center, recuperar o ID do inquilino e o ID do cliente para a aplicação e gerar uma chave. A aplicação Azure AD representa a app ou serviço a partir do qual pretende ligar para a API de submissão do Partner Center. Você precisa da identificação do inquilino, identificação do cliente e chave para obter um sinal de acesso Azure AD que você passa para a API.

>[!Note]
>Só precisas de fazer esta tarefa uma vez. Depois de ter a identificação do inquilino, identificação do cliente e chave, pode reutilizá-los sempre que precisar para criar um novo sinal de acesso à AD Azure.

1. No Partner Center, associe a [conta do Partner Center da sua organização com o diretório Azure AD da sua organização.](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center)
1. Em seguida, a partir da página **utilizadores** na secção de **definições** de Conta do Partner Center, [adicione a aplicação Azure AD](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) que representa a app ou serviço que utilizará para aceder a submissões para a sua conta Partner Center. Certifique-se de atribuir a esta aplicação a função **de Gestor.** Se a aplicação ainda não existir no seu diretório Azure AD, pode [criar uma nova aplicação Azure AD no Partner Center](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. Volte à página **de Utilizadores,** clique no nome da sua aplicação Azure AD para ir às definições da aplicação e copie os valores de ID do **Arrendatário** e **do Cliente.**
1. Clique **Em adicionar nova tecla**. No ecrã seguinte, copie o valor **chave.** Não poderá aceder a esta informação depois de deixar esta página. Para mais informações, consulte [Gerir as chaves para uma aplicação Azure AD](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Passo 2: Obter um sinal de acesso a AD Azure

Antes de ligar para qualquer um dos métodos da API de submissão do Partner Center, primeiro deve obter um sinal de acesso Azure AD que passe para o cabeçalho de **Autorização** de cada método na API. Depois de obter um sinal de acesso, tem 60 minutos para usá-lo antes de expirar. Depois de expirar o token, pode refrescar o token para que possa continuar a usá-lo em futuras chamadas para a API.

Para obter o sinal de acesso, siga as instruções em [Serviço para Chamadas de Serviço Utilizando Credenciais de Cliente](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/) para enviar um para o ponto `HTTP POST` `https://login.microsoftonline.com/<tenant_id>/oauth2/token` final. Aqui está um pedido de amostra:

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

Para o *valor tenant_id* nos `POST URI` parâmetros *client_id* e *client_secret,* especifique o ID do inquilino, o ID do cliente e a chave para a sua aplicação que recuperou do Partner Center na secção anterior. Para o parâmetro de *recurso,* deve especificar `https://api.partner.microsoft.com` .

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Passo 3: Utilize a API de submissão da Microsoft Store

Depois de ter um token de acesso AD Azure, pode ligar para métodos na Submissão API do Partner Center. Para criar ou atualizar submissões, normalmente chama vários métodos na API de submissão do Partner Center numa ordem específica. Para obter informações sobre cada cenário e a sintaxe de cada método, consulte a ingestão da API.

https://apidocs.microsoft.com/services/partneringestion/

## <a name="next-steps"></a>Passos seguintes

* Saiba como criar um [ativo técnico Azure VM](create-azure-container-technical-assets.md)
* Saiba como criar uma oferta de [contentores Azure](create-azure-container-offer.md)
