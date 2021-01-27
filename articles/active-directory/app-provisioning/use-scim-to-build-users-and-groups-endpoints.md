---
title: Construa um ponto final SCIM para o fornecimento de utilizadores a apps a partir do Azure AD
description: O sistema de gestão de identidade de domínio cruzado (SCIM) normaliza o fornecimento automático do utilizador. Aprenda a desenvolver um ponto final SCIM, integre a sua API SCIM com o Azure Ative Directory e comece a automatizar utilizadores e grupos de provisionamento nas suas aplicações em nuvem.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ae36af981b113d44ac1b8fd45a1d084760b0294
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900245"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Tutorial: Desenvolver uma amostra scim ponto final

Ninguém quer construir um novo ponto final do zero, por isso criámos um código de [referência](https://aka.ms/scimreferencecode) para começares com o [SCIM.](https://aka.ms/scimoverview) Este tutorial descreve como implementar o código de referência SCIM em Azure e testá-lo usando carteiro ou integrando-se com o cliente Azure AD SCIM. Pode colocar o seu ponto de final do SCIM a funcionar sem código em apenas 5 minutos. Este tutorial destina-se a programadores que pretendam começar com o SCIM ou outros interessados em testar um ponto final sicm. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Faça o download do código de referência
> * Implemente o seu ponto final SCIM em Azure
> * Teste o seu ponto final SCIM

As capacidades do ponto final, incluídas são:

|Ponto final|Descrição|
|---|---|
|`/User`|Executar operações CRUD num recurso do utilizador: **Criar,** **Atualizar,** **Eliminar,** **Obter,** **Lista,** **Filtro**|
|`/Group`|Executar operações CRUD num recurso de grupo: **Criar,** **Atualizar,** **Eliminar,** **Obter,** **Lista,** **Filtro**|
|`/Schemas`|Recupere um ou mais esquemas suportados.<br/><br/>O conjunto de atributos de um recurso suportado por cada prestador de serviços pode variar, por exemplo, o Prestador de Serviços A suporta "nome", "título" e "e-mails" enquanto o Fornecedor de Serviços B suporta "nome", "título" e "telefoneNumbers" para os utilizadores.|
|`/ResourceTypes`|Recupere os tipos de recursos suportados.<br/><br/>O número e tipos de recursos suportados por cada prestador de serviços pode variar, por exemplo, o Prestador de Serviços A suporta os utilizadores enquanto o Fornecedor de Serviços B suporta utilizadores e grupos.|
|`/ServiceProviderConfig`|Configuração SCIM do prestador de serviços de recuperação<br/><br/>As funcionalidades SCIM suportadas por cada prestador de serviços podem variar, por exemplo, o Fornecedor de ServiçoS A suporta as operações de Patch, enquanto o Fornecedor de Serviços B suporta as Operações de Patch e a Schema Discovery.|

## <a name="download-the-reference-code"></a>Faça o download do código de referência

O [código de referência](https://github.com/AzureAD/SCIMReferenceCode) a ser descarregado inclui os seguintes projetos:

- **Microsoft.SystemForCrossDomainIdentityManagement**, a API web .NET Core MVC para construir e providenciar uma API SCIM
- **Microsoft.SCIM.WebHostSample**, um exemplo de trabalho de um ponto final SCIM

Os projetos contêm as seguintes pastas e ficheiros:

|Arquivo/pasta|Descrição|
|-|-|
|Pasta **De Esquemas**| Os modelos para os recursos **do Utilizador** e **do Grupo** juntamente com algumas classes abstratas como o Schematized para funcionalidades partilhadas.<br/><br/> Uma pasta **Atributos** que contém as definições de classe para atributos complexos de **Utilizadores** e **Grupos,** tais como endereços.|
|Pasta **de serviço** | Contém lógica para ações relacionadas com a forma como os recursos são consultados e atualizados.<br/><br/> O código de referência tem serviços para devolver utilizadores e grupos.<br/><br/>A pasta **dos controladores** contém os vários pontos finais do SCIM. Os controladores de recursos incluem verbos HTTP para executar operações CRUD no recurso **(GET**, **POST,** **PUT,** **PATCH**, **DELETE**). Os controladores confiam nos serviços para executar as ações.|
|**Pasta de protocolo**|Contém lógica para ações relacionadas com a forma como os recursos são devolvidos de acordo com o SCIM RFC, tais como:<br/><ul><li>Devolvendo vários recursos como lista.</li><li>Devolvendo apenas recursos específicos com base num filtro.</li><li>Transformar uma consulta numa lista de listas ligadas de filtros individuais.</li><li>Transformar um pedido PATCH numa operação com atributos relativos ao percurso de valor.</li><li>Definição do tipo de operação que pode ser usado para aplicar alterações em objetos de recursos.</li></ul>|
|`Microsoft.SystemForCrossDomainIdentityManagement`| Código fonte de amostra.|
|`Microsoft.SCIM.WebHostSample`| Implementação da amostra da biblioteca SCIM.|
|*.gitignore*|Defina o que ignorar no momento de compromisso.|
|*CHANGELOG.md*|Lista de alterações à amostra.|
|*CONTRIBUTING.md*|Orientações para contribuir para a amostra.|
|*README.md*|Este ficheiro **README.**|
|*LICENÇA*|A licença para a amostra.|

> [!NOTE]
> Este código destina-se a ajudar a iniciar a construção de um ponto final SCIM e é fornecido **COMO IS**. As referências incluídas não têm garantia de manutenção ativa ou suporte.
>
> Este projeto adotou o [Microsoft Open Source Code of Conduct (Código de Conduta do Microsoft Open Source)](https://opensource.microsoft.com/codeofconduct/). Como [tal,](https://github.com/AzureAD/SCIMReferenceCode/wiki/Contributing-Overview) as contribuições da comunidade são bem-vindas para ajudar a construir e manter o repo, e como outras contribuições de código aberto, você concordará com um Contrato de Licença de Contribuinte (CLA). Este acordo declara que tem e concede os direitos de utilização da sua contribuição, para mais detalhes, consulte [o Microsoft Open Source](https://cla.opensource.microsoft.com).
>
> Para obter mais informações, veja a [Code of Conduct FAQ (FAQ do Código de Conduta)](https://opensource.microsoft.com/codeofconduct/faq/) ou envie um e-mail para [opencode@microsoft.com](mailto:opencode@microsoft.com) com quaisquer perguntas ou comentários adicionais.

###  <a name="use-multiple-environments"></a>Use vários ambientes

O código SCIM incluído utiliza um ambiente core ASP.NET para controlar a sua autorização de utilização no desenvolvimento e após a implantação, ver [Utilizar vários ambientes em ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments?view=aspnetcore-3.1).

```csharp
private readonly IWebHostEnvironment _env;
...

public void ConfigureServices(IServiceCollection services)
{
    if (_env.IsDevelopment())
    {
        ...
    }
    else
    {
        ...
    }
```

## <a name="deploy-your-scim-endpoint-in-azure"></a>Implemente o seu ponto final SCIM em Azure

Os passos aqui fornecidos implantam o ponto final do SCIM para um serviço que utiliza o [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) e [o Azure App Services](https://docs.microsoft.com/azure/app-service/). O código de referência SCIM também pode ser executado localmente, hospedado por um servidor no local, ou implantado para outro serviço externo. 

### <a name="open-solution-and-deploy-to-azure-app-service"></a>Abrir solução e implementar para o Azure App Service

1. Aceda ao código de [referência](https://github.com/AzureAD/SCIMReferenceCode) do GitHub e selecione **Clone ou descarregue.**

1. Opte por **abrir no Ambiente de Trabalho**, ou, copie o link, abra o Visual **Studio,** selecione **Clone ou verifique** código para introduzir o link copiado e fazer uma cópia local.

1. No **Visual Studio,** certifique-se de assinar na conta que tem acesso aos seus recursos de hospedagem.

1. No **Solution Explorer**, abra *microsoft.SCIM.sln* e clique com o botão direito no ficheiro *Microsoft.SCIM.WebHostSample.* Selecione **Publicar**.

    ![publicação em nuvem](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > Para executar esta solução localmente, clique duas vezes no projeto e selecione o **IIS Express** para lançar o projeto como uma página web com um URL anfitrião local.

1. Selecione **Criar perfil** e certifique-se de que **o Serviço de Aplicações** e **criar novos** são selecionados.

    ![nuvem publicar 2](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Passe pelas opções de diálogo e mude o nome da app para um nome à sua escolha. Este nome é usado tanto na aplicação como no URL do ponto final do SCIM.

    ![nuvem publicar 3](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Selecione o grupo de recursos para utilizar e escolher **Publicar.**

1. Navegue para a aplicação na Configuração **de Serviços de Aplicações Azure**  >   e selecione nova definição de **aplicação** para adicionar a definição *Token__TokenIssuer* com o valor `https://sts.windows.net/<tenant_id>/` . `<tenant_id>`Substitua-o pelo seu AD AD Azure tenant_id e se estiver à procura de testar o ponto final do SCIM utilizando o [Carteiro](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint), adicione também uma definição *de ASPNETCORE_ENVIRONMENT* com o valor `Development` . 

   ![definições de serviço de aplicativo](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Ao testar o seu ponto final com uma Aplicação Enterprise no portal Azure, opte por manter o ambiente como `Development` e fornecer o símbolo gerado a partir do ponto final para testar ou alterar o ambiente para `/scim/token` e deixar o campo simbólico vazio na `Production` aplicação da empresa no [portal Azure.](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client) 

Já está! O seu ponto final SCIM já foi publicado e permite-lhe utilizar o URL do Serviço de Aplicações Azure para testar o ponto final do SCIM.

## <a name="test-your-scim-endpoint"></a>Teste o seu ponto final SCIM

Os pedidos a um ponto final do SCIM requerem autorização e a norma SCIM deixa múltiplas opções de autenticação e autorização, tais como cookies, autenticação básica, autenticação do cliente TLS ou qualquer um dos métodos listados no [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2).

Certifique-se de evitar métodos inseguros, como o nome de utilizador/palavra-passe, em favor de um método mais seguro, como o OAuth. A Azure AD suporta fichas de portador de longa data (para aplicações de galeria e não galeria) e a concessão de autorização da OAuth (para aplicações publicadas na galeria de aplicações).

> [!NOTE]
> Os métodos de autorização fornecidos no repo são apenas para testes. Ao integrar-se com a Azure AD, pode rever a orientação de autorização, consulte [o Plano de Provisionamento para um ponto final SCIM](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#authorization-for-provisioning-connectors-in-the-application-gallery). 

O ambiente de desenvolvimento permite características inseguras para a produção, como código de referência para controlar o comportamento da validação do símbolo de segurança. O código de validação de fichas está configurado para utilizar um token de segurança auto-assinado e a chave de assinatura é armazenada no ficheiro de configuração, consulte o parâmetro **Token:EmiterSigningKey** no *appsettings.Development.jsno* ficheiro.

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> Ao enviar um pedido **GET** para o `/scim/token` ponto final, um token é emitido usando a chave configurada e pode ser usado como símbolo portador para posterior autorização.

O código de validação por defeito é configurado para utilizar um símbolo emitido pelo Azure Ative Directory e exige que o inquilino emissor seja configurado usando o parâmetro **Token:TokenIssuer** no *appsettings.jsno* ficheiro.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Use o Carteiro para testar pontos finais

Depois de o ponto final do SCIM ser implantado, pode testar para garantir que está em conformidade com o SCIM RFC. Este exemplo fornece um conjunto de testes no **Carteiro** para validar operações CRUD em utilizadores e grupos, filtragem, atualizações para membros do grupo e utilizadores incapacitantes.

Os pontos finais estão localizados no `{host}/scim/` diretório e podem ser interagidos com a utilização de pedidos HTTP padrão. Para modificar a `/scim/` rota, consulte *ControllerConstant.cs* nos Controladores ScimReferenceApi da **AzureADProvisioningSCIMreference**  >    >  .

> [!NOTE]
> Só pode utilizar pontos finais HTTP para testes locais, uma vez que o serviço de fornecimento de Azure AD requer o seu suporte de ponto final HTTPS.

#### <a name="open-postman-and-run-tests"></a>Abrir carteiro e fazer testes

1. Descarregue [o Carteiro](https://www.getpostman.com/downloads/) e inicie a aplicação.
1. Copie a ligação [https://aka.ms/ProvisioningPostman](https://aka.ms/ProvisioningPostman) e cole no Carteiro para importar a coleção de testes.

    ![coleção de carteiro](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Criar um ambiente de teste com as variáveis abaixo:

   |Ambiente|Variável|Valor|
   |-|-|-|
   |Executar projeto local usando o IIS Express|||
   ||**Servidor**|`localhost`|
   ||**Porta**|`:44359`*(não se esqueça do **:**)*|
   ||**Api**|`scim`|
   |Executar projeto local usando Kestrel|||
   ||**Servidor**|`localhost`|
   ||**Porta**|`:5001`*(não se esqueça do **:**)*|
   ||**Api**|`scim`|
   |Hospedar o ponto final em Azure|||
   ||**Servidor**|*(inserir o seu URL SCIM)*|
   ||**Porta**|*(deixar em branco)*|
   ||**Api**|`scim`|

1. Utilize **a Chave Get** da Coleção Carteiro para enviar um pedido **GET** para o ponto final simbólico e recuperar um sinal de segurança para ser armazenado na variável **simbólica** para pedidos posteriores. 

   ![carteiro obter chave](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Para tornar um ponto final SCIM seguro, você precisa de um token de segurança antes de ligar, e o tutorial usa o `{host}/scim/token` ponto final para gerar um token auto-assinado.

Já está! Agora pode executar a coleção **Do Carteiro** para testar a funcionalidade do ponto final do SCIM.

## <a name="next-steps"></a>Passos Seguintes

Para desenvolver um utilizador compatível com o SCIM e ponto final de grupo com interoperabilidade para um cliente, consulte a implementação do [cliente SCIM](http://www.simplecloud.info/#Implementations2).

> [!div class="nextstepaction"]
> [Tutorial: Desenvolver e planear o provisionamento para um ponto](use-scim-to-provision-users-and-groups.md) 
>  final do SCIM [Tutorial: Configurar provisão para uma aplicação de galeria](configure-automatic-user-provisioning-portal.md)