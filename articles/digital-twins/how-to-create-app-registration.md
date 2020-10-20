---
title: Criar um registo de aplicativos
titleSuffix: Azure Digital Twins
description: Veja como criar um registo de aplicações AD Azure, como uma opção de autenticação para aplicações de clientes.
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f252471cd3cd7e3a950bf2cfe324e580da129041
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210171"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>Criar um registo de aplicativos para usar com a Azure Digital Twins

Ao trabalhar com uma instância Azure Digital Twins, é comum interagir com esse caso através de aplicações de clientes, como uma aplicação personalizada para clientes ou uma amostra como [o ADT Explorer.](quickstart-adt-explorer.md) Essas aplicações precisam de autenticar com a Azure Digital Twins para interagir com ela, e alguns dos mecanismos de autenticação que as [aplicações](how-to-authenticate-client.md) podem usar envolvem um registo de **aplicações** [Azure Ative (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md)

Isto não é necessário para todos os cenários de autenticação. No entanto, se estiver a utilizar uma estratégia de autenticação ou uma amostra de código que exija um registo de aplicações, incluindo um **ID de cliente** e **iD do inquilino,** este artigo mostra-lhe como configurar uma.

## <a name="using-azure-ad-app-registrations"></a>Usando registos de aplicações AD Azure

[Azure Ative Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) é o serviço de gestão de identidade e acesso baseado na nuvem da Microsoft. A criação de um **registo de aplicações** no Azure AD é uma forma de conceder acesso a uma aplicação de clientes a Azure Digital Twins.

Este registo de aplicações é onde configura permissões de acesso às [APIs das Gémeas Digitais Azure.](how-to-use-apis-sdks.md) Posteriormente, as aplicações do cliente podem autenticar-se contra o registo da aplicação utilizando os valores de **identificação**do cliente e do inquilino do registo , e como resultado ser-lhes concedidas as permissões de acesso configuradas às APIs.

>[!TIP]
> Pode preferir configurar um novo registo de aplicações sempre que precisar de uma, *ou* fazê-lo apenas uma vez, estabelecendo um registo de uma única aplicação que será partilhado entre todos os cenários que o exijam.

## <a name="create-the-registration"></a>Criar o registo

Comece por navegar para o [Azure Ative Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) no portal Azure (pode utilizar este link ou encontrá-lo com a barra de pesquisa do portal). Selecione *as inscrições* da App no menu de serviço e, em seguida, *+ Novo registo.*

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Vista da página de serviço AZure AD no portal Azure, destacando a opção de menu 'Registos de Aplicações' e o botão '+ Novo registo'":::

No *Registo de uma* página de candidatura que se segue, preencha os valores solicitados:
* **Nome**: Nome de exposição de pedidos Azure AD para associar ao registo
* **Tipos de conta suportados**: Selecione *contas apenas neste diretório organizacional (Diretório padrão - Inquilino único)*
* **Redirecionamento URI**: *URL de resposta a pedido azure AD* para a aplicação AZure AD. Adicione um *cliente público/nativo (mobile & desktop)* URI para `http://localhost` .

Quando terminar, acerte no botão *Registar.*

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="Vista da página de serviço AZure AD no portal Azure, destacando a opção de menu 'Registos de Aplicações' e o botão '+ Novo registo'":::

Quando a inscrição terminar a configuração, o portal irá redirecioná-lo para a página de detalhes.

## <a name="collect-client-id-and-tenant-id"></a>Recolher iD do cliente e iD do inquilino

Em seguida, colete alguns valores importantes sobre o registo da aplicação na sua página de detalhes:

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="Vista da página de serviço AZure AD no portal Azure, destacando a opção de menu 'Registos de Aplicações' e o botão '+ Novo registo'":::

Tome nota do ID de _**Aplicação (cliente)**_ e _**Diretório (inquilino)**_ mostrado na **sua** página. Estes são os valores que uma aplicação do cliente terá de utilizar este registo para autenticar com a Azure Digital Twins.

## <a name="provide-azure-digital-twins-api-permission"></a>Forneça permissão AZure Digital Twins API

Em seguida, configurar o registo de aplicações que criou com permissões de base para as APIs das Gémeas Digitais Azure.

A partir da página do portal para o registo da sua aplicação, selecione *permissões API* no menu. Na página de permissões seguintes, carregar no botão + Adicionar um botão *de permissão.*

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Vista da página de serviço AZure AD no portal Azure, destacando a opção de menu 'Registos de Aplicações' e o botão '+ Novo registo'":::

Na página de permissões da *API request* que se segue, mude para as *APIs a minha organização usa* o separador e procura *gémeos digitais azuis.* Selecione _**Azure Digital Twins**_ a partir dos resultados da pesquisa para proceder à atribuição de permissões para as APIs de Gémeos Digitais Azure.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Vista da página de serviço AZure AD no portal Azure, destacando a opção de menu 'Registos de Aplicações' e o botão '+ Novo registo'":::

>[!NOTE]
> Se a sua subscrição ainda tiver uma instância Azure Digital Twins existente da pré-visualização pública do serviço (antes de julho de 2020), terá de procurar e selecionar o _**Azure Smart Spaces Service.**_ Este é um nome mais antigo para o mesmo conjunto de APIs (note que o *ID de Aplicação (cliente)* é o mesmo que na imagem acima), e a sua experiência não será alterada para além deste passo.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Vista da página de serviço AZure AD no portal Azure, destacando a opção de menu 'Registos de Aplicações' e o botão '+ Novo registo'":::

Em seguida, você irá selecionar quais permissões conceder para estas APIs. Expanda a permissão **de Ler (1)** e verifique a caixa que diz *Ler.Escreva* para conceder a esta aplicação de registo de permissões e permissões de escritor.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Vista da página de serviço AZure AD no portal Azure, destacando a opção de menu 'Registos de Aplicações' e o botão '+ Novo registo'":::

*Acerte As permissões* de adicionar quando terminar.

### <a name="verify-success"></a>Verificar o sucesso

Na página de permissões da *API,* verifique se existe agora uma entrada para as Gémeas Digitais Azure que refletem permissões de Leitura/Escrita:

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Vista da página de serviço AZure AD no portal Azure, destacando a opção de menu 'Registos de Aplicações' e o botão '+ Novo registo'":::

Também é possível verificar a ligação com a Azure Digital Twins dentro do *manifest.js*do registo da aplicação , que foi automaticamente atualizado com as informações da Azure Digital Twins quando adicionou as permissões da API.

Para isso, *selecione Manifesto* no menu para ver o código manifesto do registo da aplicação. Percorra a parte inferior da janela de código e procure estes campos sob `requiredResourceAccess` . Os valores devem corresponder aos da imagem abaixo:

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Vista da página de serviço AZure AD no portal Azure, destacando a opção de menu 'Registos de Aplicações' e o botão '+ Novo registo'":::

Se estes valores estiverem em falta, respiro os passos na [secção para adicionar a permissão da API](#provide-azure-digital-twins-api-permission).

## <a name="other-possible-steps-for-your-organization"></a>Outros passos possíveis para a sua organização

É possível que a sua organização exija ações adicionais de proprietários/administradores de subscrição para configurar com sucesso um registo de aplicações. Os passos necessários podem variar dependendo das definições específicas da sua organização.

Aqui estão algumas atividades potenciais comuns que um Proprietário/administrador na subscrição pode precisar realizar. Estas e outras operações podem ser realizadas a partir da página de registos da [*App Azure AD*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) no portal Azure.
* Conceder consentimento administrativo para o registo da aplicação. A sua organização pode ter *o Consentimento Admin Exigido* globalmente ligado em Azure AD para todos os registos de aplicações dentro da sua subscrição. Em caso afirmativo, o Proprietário/administrador terá de selecionar este botão para a sua empresa na página de *permissões API* do registo da aplicação para que o registo da aplicação seja válido:

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="Vista da página de serviço AZure AD no portal Azure, destacando a opção de menu 'Registos de Aplicações' e o botão '+ Novo registo'":::
  - Se o consentimento tiver sido concedido com sucesso, a entrada para a Azure Digital Twins deve então mostrar um valor de *Estado* de _Concedido para **(a sua empresa)** _
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="Vista da página de serviço AZure AD no portal Azure, destacando a opção de menu 'Registos de Aplicações' e o botão '+ Novo registo'":::
* Ativar o acesso do cliente público
* Definir URLs de resposta específico para acesso web e desktop
* Permitir fluxos implícitos de autenticação OAuth2

Para obter mais informações sobre o registo de aplicações e as suas diferentes opções de configuração, consulte [*Registar uma aplicação com a plataforma de identidade microsoft.*](/graph/auth-register-app-v2)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um registo de aplicações AD AZure que pode ser usado para autenticar aplicações de clientes com as APIs de Gémeos Digitais Azure.

Em seguida, leia sobre mecanismos de autenticação, incluindo um que utiliza registos de aplicações e outros que não:
* [*Como fazer: Escrever código de autenticação de aplicativos*](how-to-authenticate-client.md)