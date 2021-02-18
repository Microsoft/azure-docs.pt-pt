---
title: Construa um ponto final SCIM para o fornecimento de utilizadores a apps do Azure Ative Directory
description: Aprenda a desenvolver um ponto final SCIM, integre a sua API SCIM com AZure AD e provisa automaticamente utilizadores e grupos nas suas aplicações em nuvem com o Azure Ative Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 9fac8db6747d1e081fa884ed794e55b98f44577f
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652042"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Tutorial: Desenvolver uma amostra scim ponto final

Ninguém quer construir um novo ponto final do zero, por isso criámos um código de [referência](https://aka.ms/scimreferencecode) para começares com [o System for Cross-domain Identity Management (SCIM)](https://aka.ms/scimoverview). Podes pôr o teu ponto de partida do SCIM a funcionar sem código em apenas cinco minutos.

Este tutorial descreve como implementar o código de referência SCIM em Azure e testá-lo utilizando o Carteiro ou integrando-se com o cliente SCIM do Azure Ative Directory (Azure AD). Este tutorial destina-se a programadores que queiram começar com o SCIM, ou qualquer pessoa interessada em testar um ponto final do SCIM.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Coloque o seu ponto final SCIM em Azure.
> * Teste o seu ponto final SCIM.

## <a name="deploy-your-scim-endpoint-in-azure"></a>Implemente o seu ponto final SCIM em Azure

Os passos aqui implantam o ponto final do SCIM para um serviço utilizando [o Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) e [o Azure App Service](https://docs.microsoft.com/azure/app-service/). O código de referência SCIM também pode ser executado localmente, hospedado por um servidor no local, ou implantado para outro serviço externo.

1. Aceda ao código de [referência](https://github.com/AzureAD/SCIMReferenceCode) do GitHub e selecione **Clone ou descarregue.**

1. Selecione **Abrir no Ambiente de Trabalho**, ou copiar o link, abrir o Visual Studio e selecionar Clone ou **verificar** código para introduzir o link copiado e fazer uma cópia local.

1. No Visual Studio, certifique-se de iniciar sedução na conta que tem acesso aos seus recursos de hospedagem.

1. No Solution Explorer, abra *microsoft.SCIM.sln* e clique com o botão direito no ficheiro *Microsoft.SCIM.WebHostSample.* Selecione **Publicar**.

    ![Screenshot que mostra o ficheiro da amostra.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > Para executar esta solução localmente, clique duas vezes no projeto e selecione o **IIS Express** para lançar o projeto como uma página web com um URL anfitrião local.

1. Selecione **Criar perfil** e certifique-se de que **o Serviço de Aplicações** e **criar novos** são selecionados.

    ![Screenshot que mostra a janela Publicar.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Passe pelas opções de diálogo e mude o nome da app para um nome à sua escolha. Este nome é usado tanto na aplicação como no URL do ponto final do SCIM.

    ![Screenshot que mostra a criação de um novo serviço de aplicações.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Selecione o grupo de recursos para utilizar e **selecione Publicar.**

1. Aceda à aplicação na Configuração **do Serviço de Aplicações Azure**  >   e selecione nova definição de **aplicação** para adicionar a definição *Token__TokenIssuer* com o valor `https://sts.windows.net/<tenant_id>/` . `<tenant_id>`Substitua-se pela sua identificação de inquilino Azure AD. Se pretender testar o ponto final do SCIM utilizando o [Carteiro,](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)adicione uma definição *de ASPNETCORE_ENVIRONMENT* com o valor `Development` .

   ![Screenshot que mostra a janela de definições de aplicação.](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Quando testar o seu ponto final com uma aplicação empresarial no [portal Azure,](use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client)tem duas opções. Pode manter o ambiente `Development` dentro e fornecer o token de teste a partir do ponto `/scim/token` final, ou pode mudar o ambiente `Production` para e deixar o campo simbólico vazio.

Já está! O seu ponto final SCIM já foi publicado e pode utilizar o URL do Serviço de Aplicações Azure para testar o ponto final do SCIM.

## <a name="test-your-scim-endpoint"></a>Teste o seu ponto final SCIM

Os pedidos a um ponto final do SCIM requerem autorização. A norma SCIM tem múltiplas opções de autenticação e autorização, incluindo cookies, autenticação básica, autenticação do cliente TLS ou qualquer um dos métodos listados no [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2).

Certifique-se de evitar métodos que não sejam seguros, como o nome de utilizador e a palavra-passe, em favor de um método mais seguro, como o OAuth. A Azure AD apoia fichas de portador de longa data (para aplicações de galerias e não galerias) e a concessão de autorização da OAuth (para pedidos de galeria).

> [!NOTE]
> Os métodos de autorização fornecidos no repo são apenas para testes. Quando se integra com a Azure AD, pode rever a orientação de autorização. Ver [Plano que prevê um ponto final SCIM](use-scim-to-provision-users-and-groups.md).

O ambiente de desenvolvimento permite funcionalidades que não são seguras para a produção, como o código de referência para controlar o comportamento da validação do símbolo de segurança. O código de validação de fichas usa um token de segurança auto-assinado e a chave de assinatura é armazenada no ficheiro de configuração. Consulte o parâmetro **Token:EmiterSigningKey** no *appsettings.Development.jsficheiro.*

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> Quando envia um pedido **GET** para o `/scim/token` ponto final, é emitido um sinal usando a chave configurada. Esse símbolo pode ser usado como símbolo portador para posterior autorização.

O código de validação por defeito é configurado para usar um token AD Azure e requer que o inquilino emissor seja configurado utilizando o parâmetro **Token:TokenIssuer** no *appsettings.jsno* ficheiro.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Use o Carteiro para testar pontos finais

Depois de implantar o ponto final do SCIM, pode testar para garantir que está em conformidade com o SCIM RFC. Este exemplo fornece um conjunto de testes no Carteiro que validam operações CRUD (criar, ler, atualizar e excluir) operações em utilizadores e grupos, filtragem, atualizações para membros do grupo e utilizadores incapacitados.

Os pontos finais estão no `{host}/scim/` diretório, e pode utilizar pedidos HTTP padrão para interagir com eles. Para modificar a `/scim/` rota, consulte *ControllerConstant.cs* nos Controladores ScimReferenceApi da **AzureADProvisioningSCIMreference**  >    >  .

> [!NOTE]
> Só pode utilizar pontos finais HTTP para testes locais. O serviço de fornecimento de Azure AD requer que o seu suporte ao ponto final HTTPS.

1. Descarregue [o Carteiro](https://www.getpostman.com/downloads/) e inicie a aplicação.
1. Copiar e colar esta ligação ao Carteiro para importar a coleção de ensaios: `https://aka.ms/ProvisioningPostman` .

    ![Screenshot que mostra importar a coleção de testes no Carteiro.](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Criar um ambiente de teste que tenha estas variáveis:

   |Ambiente|Variável|Valor|
   |-|-|-|
   |Executar o projeto localmente usando o IIS Express|||
   ||**Servidor**|`localhost`|
   ||**Porta**|`:44359`*(não se esqueça **`:`** do)*|
   ||**Api**|`scim`|
   |Executar o projeto localmente usando Kestrel|||
   ||**Servidor**|`localhost`|
   ||**Porta**|`:5001`*(não se esqueça **`:`** do)*|
   ||**Api**|`scim`|
   |Hospedar o ponto final em Azure|||
   ||**Servidor**|*(inserir o seu URL SCIM)*|
   ||**Porta**|*(deixar em branco)*|
   ||**Api**|`scim`|

1. Utilize **a Chave Get** da coleção Do Carteiro para enviar um pedido **GET** para o ponto final simbólico e recuperar um sinal de segurança para ser armazenado na variável **simbólica** para pedidos posteriores.

   ![Screenshot que mostra a pasta Carteiro Get Key.](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Para tornar um ponto final SCIM seguro, precisa de um sinal de segurança antes de se ligar. O tutorial usa o `{host}/scim/token` ponto final para gerar um símbolo auto-assinado.

Já está! Agora pode executar a coleção **Do Carteiro** para testar a funcionalidade do ponto final do SCIM.

## <a name="next-steps"></a>Passos seguintes

Para desenvolver um utilizador compatível com SCIM e ponto final de grupo com interoperabilidade para um cliente, consulte a [implementação do cliente SCIM](http://www.simplecloud.info/#Implementations2).

> [!div class="nextstepaction"]
> [Tutorial: Desenvolver e planear o provisionamento para um ponto](use-scim-to-provision-users-and-groups.md) 
>  final do SCIM [Tutorial: Configurar provisão para uma aplicação de galeria](configure-automatic-user-provisioning-portal.md)
