---
title: Tutorial para configurar Azure Ative Directy B2C com Onfido
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação Azure AD B2C com o Onfido para verificação de ID de documento e biometria facial
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 46c6eac80ddbff73d99e05c070e66aa1700da174
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928635"
---
# <a name="tutorial-for-configuring-onfido-with-azure-active-directory-b2c"></a>Tutorial para configurar Onfido com Azure Ative Directory B2C

Neste tutorial de amostra, fornecemos orientações sobre como integrar o Azure AD B2C com [o Onfido.](https://onfido.com/) Onfido é uma aplicação de verificação de identificação de documentos e biometria facial. Permite que as empresas cumpram os requisitos *de Cliente* e identidade em tempo real. Onfido usa sofisticada verificação de identidade baseada em IA, que primeiro verifica uma identificação fotográfica, em seguida, corresponde-a com a sua biometria facial. Esta solução liga uma identidade digital à sua pessoa no mundo real e proporciona uma experiência de embarque segura, reduzindo a fraude.

Nesta amostra, conectamos o serviço da Onfido no fluxo de inscrição ou login para fazer a verificação de identidade. As decisões informadas sobre o produto e serviço a que o utilizador pode aceder são tomadas com base nos resultados do Onfido.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

- Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

- [Um inquilino Azure AD B2C](./tutorial-create-tenant.md) que está ligado à sua assinatura Azure.

- Uma conta [de julgamento](https://onfido.com/signup/)onfido.

## <a name="scenario-description"></a>Descrição do cenário

A integração onfido inclui os seguintes componentes:

- Inquilino Azure AD B2C – O servidor de autorização, responsável pela verificação das credenciais do utilizador com base em políticas personalizadas definidas no arrendatário. Também é conhecido como o fornecedor de identidade. Acolhe a aplicação do cliente Onfido, que recolhe os documentos do utilizador e o transmite para o serviço Onfido API.

- Cliente Onfido – Um utilitário de recolha de documentos de clientes JavaScript configurado implantado noutras páginas web. Recolhe os documentos e faz verificações preliminares como o tamanho e qualidade do documento.

- A API de Repouso Intermédio – Fornece pontos finais para o inquilino Azure AD B2C comunicar com o serviço Onfido API, manuseando o tratamento do tratamento de dados e aderindo aos requisitos de segurança de ambos.

- Serviço Onfido API – O serviço de backend prestado pela Onfido, que guarda e verifica os documentos fornecidos pelo utilizador.

O seguinte diagrama de arquitetura mostra a implementação.

![screenshot para onfido-arquitetura-diagrama](media/partner-onfido/onfido-architecture-diagram.png)

|Passo | Descrição |
|:-----| :-----------|
| 1. | O utilizador chega a uma página de login. O utilizador inscreve-se para criar uma nova conta e introduz informações na página. Azure AD B2C recolhe os atributos do utilizador. A aplicação do cliente Onfido hospedada no Azure AD B2C faz verificações preliminares para a informação do utilizador.
| 2. | Azure AD B2C chama a API de camada média e transmite os atributos do utilizador.
| 3. | A API de camada média recolhe atributos do utilizador e transforma-os num formato que a API do Onfido poderia consumir. Depois, envia-o para Onfido.
| 4. | O Onfido consome a informação e processa-a para validar a identificação do utilizador. Em seguida, devolve o resultado à API de camada média.
| 5. | A API de camada média processa a informação e envia de volta informações relevantes no formato JSON correto para Azure AD B2C.
| 6. | Azure AD B2C recebe informações da API de camada média. Se mostrar uma resposta de Falha, é apresentada uma mensagem de erro ao utilizador. Se mostrar uma resposta de Sucesso, o utilizador é autenticado e escrito no diretório.

## <a name="onboard-with-onfido"></a>A bordo com Onfido

1. Para criar uma conta Onfido, contacte [o Onfido.](https://onfido.com/signup/)

2. Uma vez criada uma conta, crie uma [chave API](https://documentation.onfido.com/). As teclas ao vivo são faturas, no entanto, pode utilizar as [chaves da caixa de areia para testar](https://documentation.onfido.com/?javascript#sandbox-and-live-differences) a solução. As teclas sandbox produzem a mesma estrutura de resultados que as teclas vivas, no entanto, os resultados são sempre pré-determinados. Os documentos não são processados ou guardados.

>[!NOTE]
> Vai precisar da chave mais tarde.

Para mais informações sobre o Onfido, consulte [a documentação da API onfido](https://documentation.onfido.com) e o [Onfido Developer Hub.](https://developers.onfido.com)  

## <a name="configure-azure-ad-b2c-with-onfido"></a>Configure Azure AD B2C com Onfido

### <a name="part-1---deploy-the-api"></a>Parte 1 - Implantar a API

- Implementar o [código API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/API/Onfido.Api) fornecido para um serviço Azure. O código pode ser publicado a partir do Visual Studio, seguindo estas [instruções](/visualstudio/deployment/quickstart-deploy-to-azure).
- Configurar CORS, adicionar **Origem Permitida** como https://{your_tenant_name}.b2clogin.com

>[!NOTE]
>Você precisará do URL do serviço implantado para configurar Azure AD com as configurações necessárias.

#### <a name="adding-sensitive-configuration-settings"></a>Adicionar configurações sensíveis

As definições de aplicação podem ser configuradas no [serviço app em Azure.](../app-service/configure-common.md#configure-app-settings) O serviço app permite que as definições sejam configuradas de forma segura sem as colocar num repositório. A API de Repouso necessita das seguintes definições:

| Nome de definição de aplicação | Origem | Notas |
|:-------------------------|:-------|:-------|
|OnfidoSettings:AuthToken| Conta Onfido |

### <a name="part-2---deploy-the-ui"></a>Parte 2 - Implementar a UI

#### <a name="configure-your-storage-location"></a>Configure a sua localização de armazenamento

1. Crie um [recipiente de armazenamento de bolhas na sua conta de armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)

2. Guarde os ficheiros de UI da [pasta UI](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI) para o seu recipiente blob.

3. Permitir o acesso do CORS ao recipiente de armazenamento que criou seguindo estas instruções:

   a. Ir para **Definições**  > **De Origem Permitida,** inserir `https://{your_tenant_name}.b2clogin.com` . Substitua o seu nome de inquilino pelo nome do seu inquilino Azure AD B2C. Por exemplo, https://fabrikam.b2clogin.com . Use todas as letras minúsculas ao inserir o nome do seu inquilino.

   b. Para **Métodos Permitidos,** selecione `GET` e `PUT` .

   c. Selecione **Guardar**.

#### <a name="update-ui-files"></a>Atualizar ficheiros de UI

1. Nos ficheiros de UI, vá à pasta [ **ocean_blue**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI/ocean_blue)

2. Abra cada ficheiro html.

3. Encontre e substitua {your-ui-blob-container-url} por url de onde está o seu UI **ocean_blue,** **dist**, e pastas **de ativos**

4. Encontre e substitua {your-intermediate-api-url} por URL do serviço de aplicações API intermédio.

#### <a name="upload-your-files"></a>Faça upload dos seus ficheiros

1. Guarde os ficheiros de UI da pasta UI para o seu recipiente blob.

2. Utilize [o Azure Storage Explorer](../virtual-machines/disks-use-storage-explorer-managed-disks.md) para gerir os seus ficheiros e obter permissões de acesso.

### <a name="part-3---configure-azure-ad-b2c"></a>Parte 3 - Configurar Azure Ad B2C

#### <a name="replace-the-configuration-values"></a>Substitua os valores de configuração

Nas [políticas personalizadas](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/Policies)fornecidas, encontre os seguintes espaços reservados e substitua-os pelos valores correspondentes da sua instância.

| Marcador de posição | Substituir por valor | Exemplo  |
|:---------------|:----------------|:-------------------|
| {your_tenant_name}  | Seu inquilino nome curto |  "yourtenant" de yourtenant.onmicrosoft.com |
| {your_tenantID} | InquilinoID do seu inquilino Azure AD B2C | 01234567-89ab-cdef-0123-456789abcdef           |
| {your_tenant_IdentityExperienceFramework_appid}        | ID da aplicação IdentityExperienceFramework configurado no seu inquilino Azure AD B2C      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | ID da aplicação ProxyIdentityExperienceFramework configurado no seu inquilino Azure AD B2C | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | ID da aplicação da aplicação de armazenamento do seu inquilino                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | ID de objeto da aplicação de armazenamento do seu inquilino                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_app_insights_instrumentation_key} | Chave de instrumentação da sua instância de insights de aplicação*| 01234567-89ab-cdef-0123-456789abcdef|
|{your_ui_file_base_url}| URL do local onde estão localizadas as suas **pastas UI ocean_blue,** **dist** e **ativos** | https://yourstorage.blob.core.windows.net/UI/|
| {your_app_service_URL}                                 | URL do serviço de aplicações que criou                                             | `https://yourapp.azurewebsites.net`          |

*Insights de aplicativos podem estar em um inquilino diferente. Este passo é opcional. Remova os respetivos Filtros Técnicos e OrquestraçõesSteps, se não necessário.

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>Parte 4 - Configurar a política Azure AD B2C

Consulte este [documento](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) para obter instruções sobre como configurar o seu inquilino Azure AD B2C e configurar políticas.

>[!NOTE]
> Como uma boa prática, recomendamos que os clientes adicionem a notificação de consentimento na página de recolha de atributos. Notifique os utilizadores de que as informações serão enviadas para serviços de terceiros para verificação de identidade.

## <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Abra o inquilino Azure AD B2C e em Políticas selecione **Quadro de Experiência de Identidade.**

2. Selecione o **seu Signo De Assinatura de Inscrição** anteriormente criado.

3. Selecione **Executar o fluxo do utilizador** e selecione as definições:

   a. **Aplicação**: selecione a aplicação registada (a amostra é JWT)

   b. **URL de resposta**: selecione o **URL de redirecionamento**

   c. Selecione **Executar o fluxo do utilizador**.

4. Passe pelo fluxo de inscrição e crie uma conta

5. O serviço Onfido será chamado durante o fluxo, após a criação do atributo do utilizador. Se o fluxo estiver incompleto, verifique se o utilizador não está guardado no diretório.

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais, reveja os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Começar com políticas personalizadas em Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
