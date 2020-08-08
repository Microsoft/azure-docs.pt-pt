---
title: Configurar uma instância e a autenticação (portal)
titleSuffix: Azure Digital Twins
description: Veja como configurar uma instância do serviço Azure Digital Twins usando o portal Azure
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 33772d46f363b161c1faa5c953f48a702ae2b8bf
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009674"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Configurar uma instância e autenticação Azure Digital Twins (portal)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Este artigo abrange as etapas para **a criação de um novo exemplo de Azure Digital Twins**, incluindo a criação do caso e a criação de autenticação. Depois de completar este artigo, terá uma instância Azure Digital Twins pronta para começar a programar contra.

Esta versão deste artigo passa por estes passos manualmente, um a um, utilizando o portal Azure. O portal do Azure é uma consola unificada baseada na Web que constitui uma alternativa às ferramentas de linha de comandos.
* Para passar por estes passos manualmente utilizando o CLI, consulte a versão CLI deste artigo: [*Como-a-fazer: Configurar um caso e autenticação (CLI)*](how-to-set-up-instance-cli.md).
* Para passar por uma configuração automatizada utilizando uma amostra de script de implementação, consulte a versão escrita deste artigo: [*Como-a-fazer: Configurar uma instância e autenticação (scripted)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Criar a instância Azure Digital Twins

Nesta secção, irá **criar uma nova instância de Azure Digital Twins** utilizando o portal [Azure](https://ms.portal.azure.com/). Navegue até ao portal e faça login com as suas credenciais.

Uma vez no portal, comece por selecionar _Criar um recurso_ no menu de página inicial dos serviços Azure.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Selecionando 'Criar um recurso' a partir da página inicial do portal Azure":::

Procure *por Gémeos Digitais Azure* na caixa de pesquisa e escolha o serviço **Azure Digital Twins (Preview)** a partir dos resultados. Selecione o botão _Criar_ para criar uma nova instância do serviço.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="Selecionando 'Criar' a partir da página de serviço Azure Digital Twins":::

Na página *de Criar Recursos,* preencha os valores abaixo indicados:
* **Subscrição**: A subscrição Azure que está a usar
  - **Grupo de recursos**: Um grupo de recursos para implantar o caso. Se ainda não tiver um grupo de recursos existente em mente, pode criar um aqui selecionando o novo link *Create* e introduzindo um nome para um novo grupo de recursos
* **Localização**: Uma região ativada por Gémeos Digitais Azure para a implantação. Para mais detalhes sobre o apoio regional, visite [*os produtos Azure disponíveis por região (Azure Digital Twins)*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* **Nome do recurso**: Um nome para a sua instância Azure Digital Twins. O nome do novo caso deve ser único na região para a sua subscrição (o que significa que se a sua subscrição tiver outra instância Azure Digital Twins na região que já está a usar o nome que escolher, será solicitado que escolha um nome diferente).

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="Preenchendo os valores descritos para criar um recurso Azure Digital Twins":::

Quando terminar, selecione _Review + create_. Isto irá levá-lo a uma página de resumo, onde pode rever os detalhes de casos introduzidos e bater _Create_. 

### <a name="verify-success-and-collect-important-values"></a>Verifique o sucesso e colete valores importantes

Depois de empurrar *Criar,* pode visualizar o estado da implementação do seu caso nas notificações do Azure ao longo da barra de ícones do portal. A notificação indicará quando a implementação foi bem sucedida, e poderá selecionar o botão _De recurso para_ visualizar a sua instância criada.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="Vista das notificações do Azure mostrando uma implementação bem sucedida e destacando o botão 'Ir para o recurso'":::

Em alternativa, se a implementação falhar, a notificação indicará o porquê. Observe os conselhos da mensagem de erro e recandiduça criando o caso.

>[!TIP]
>Uma vez que o seu caso seja criado, pode voltar à sua página a qualquer momento, procurando o nome do seu caso na barra de pesquisa do portal Azure.

A partir *da* página geral do caso, note o seu *nome,* *grupo de recursos*e nome *anfitrião.* Estes são todos os valores importantes que poderá necessitar enquanto continua a trabalhar com a sua instância Azure Digital Twins. Se outros utilizadores estiverem a programar contra a ocorrência, deverá partilhar estes valores com eles.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Realçando os valores importantes da página geral da instância":::

Agora tens uma instância do Azure Digital Twins pronta para ir. Em seguida, você dará as permissões apropriadas do utilizador Azure para geri-lo.

## <a name="set-up-user-access-permissions"></a>Configurar permissões de acesso ao utilizador

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Primeiro, abra a página para a sua instância Azure Digital Twins no portal Azure. A partir do menu da instância, selecione *Access control (IAM)*. Selecione o botão *Adicionar* sob *Adicionar uma atribuição de função*.

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="Selecionando para adicionar uma atribuição de função a partir da página 'Controlo de acesso (IAM)'":::

Na página seguinte *de atribuição de funções Adicionar,* preencha os valores (deve ser preenchido por um utilizador com [permissões suficientes](#prerequisites-permission-requirements) na subscrição do Azure):
* **Função**: Selecione *Azure Digital Twins Owner (Preview)* do menu suspenso
* **Atribuir acesso a**: Selecione *Azure AD utilizador, grupo ou principal de serviço* a partir do menu suspenso
* **Selecione:** Procure o nome ou endereço de e-mail do utilizador para atribuir. Quando selecionar o resultado, o utilizador aparecerá numa secção *de membros selecionados.*

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="Preenchendo os campos listados no diálogo "Adicionar função"":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Quando terminar de introduzir os detalhes, acerte no botão *Guardar.*

### <a name="verify-success"></a>Verificar o sucesso

Pode ver a atribuição de funções que estabeleceu no controlo de *acesso (IAM) > atribuições de Função*. O utilizador deve aparecer na lista com um papel de *Azure Digital Twins Owner (Preview)*. 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Vista das atribuições de funções para uma instância Azure Digital Twins no portal Azure":::

Tem agora uma instância Azure Digital Twins pronta a ser executada, e atribuiu permissões para geri-lo. Em seguida, irá configurar permissões para uma aplicação de clientes para aceder a ela.

## <a name="set-up-access-permissions-for-client-applications"></a>Configurar permissões de acesso para aplicações de clientes

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

Comece por navegar para o [Azure Ative Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) no portal Azure (pode utilizar este link ou encontrá-lo com a barra de pesquisa do portal). Selecione *as inscrições* da App no menu de serviço e, em seguida, *+ Novo registo.*

:::image type="content" source="media/how-to-set-up-instance/portal/new-registration.png" alt-text="Vista da página de serviço AZure AD no portal Azure, destacando a opção de menu 'Registos de Aplicações' e o botão '+ Novo registo'":::

No *Registo de uma* página de candidatura que se segue, preencha os valores solicitados:
* **Nome**: Nome de exposição de pedidos Azure AD para associar ao registo
* **Tipos de conta suportados**: Selecione *contas apenas neste diretório organizacional (Diretório padrão - Inquilino único)*
* **Redirecionamento URI**: *URL de resposta a pedido azure AD* para a aplicação AZure AD. Pode `http://localhost` usar.

Quando terminar, acerte no botão *Registar.*

:::image type="content" source="media/how-to-set-up-instance/portal/register-an-application.png" alt-text="Vista da página 'Registar uma candidatura' com os valores descritos preenchidos":::

Quando a inscrição terminar a configuração, o portal irá redirecioná-lo para a página de detalhes.

### <a name="provide-azure-digital-twins-api-permission"></a>Forneça permissão AZure Digital Twins API

Em seguida, configurar o registo de aplicações que criou com permissões de base para as APIs das Gémeas Digitais Azure.

A partir da página do portal para o registo da sua aplicação, selecione *permissões API* no menu. Na página de permissões seguintes, carregar no botão + Adicionar um botão *de permissão.*

:::image type="content" source="media/how-to-set-up-instance/portal/add-permission.png" alt-text="Vista para o registo da aplicação no portal Azure, destacando a opção de menu 'permissões API' e botão '+ Adicionar uma permissão'":::

Na página de permissões da *API request* que se segue, mude para as *APIs a minha organização usa* o separador e procura *gémeos digitais azuis.* Selecione *Azure Digital Twins* a partir dos resultados da pesquisa para proceder à atribuição de permissões para as APIs de Gémeos Digitais Azure.

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-1.png" alt-text="Ver o resultado da pesquisa da página 'Request API Permissões' mostrando Azure Digital Twins":::

Em seguida, você irá selecionar quais permissões conceder para estas APIs. Expanda a permissão **de Ler (1)** e verifique a caixa que diz *Ler.Escreva* para conceder a esta aplicação de registo de permissões e permissões de escritor.

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-2.png" alt-text="Vista da página 'Request API Permissões' selecionando permissões 'Read.Write' para as APIs de Gémeos Digitais Azure":::

*Acerte As permissões* de adicionar quando terminar.

### <a name="verify-success"></a>Verificar o sucesso

De volta à página de permissões da *API,* verifique se existe agora uma entrada para as Gémeas Digitais Azure que refletem permissões de Leitura/Escrita:

:::image type="content" source="media/how-to-set-up-instance/portal/verify-api-permissions.png" alt-text="Vista do portal das permissões da API para o registo da aplicação AZure AD, mostrando 'Read/Write Access' para Azure Digital Twins":::

Também é possível verificar a ligação com a Azure Digital Twins dentro do *manifest.js*do registo da aplicação , que foi automaticamente atualizado com as informações da Azure Digital Twins quando adicionou as permissões da API.

Para isso, *selecione Manifesto* no menu para ver o código manifesto do registo da aplicação. Percorra a parte inferior da janela de código e procure estes campos sob `requiredResourceAccess` . Os valores devem corresponder aos da imagem abaixo:

:::image type="content" source="media/how-to-set-up-instance/portal/verify-manifest.png" alt-text="Vista do portal do manifesto para o registo da aplicação Azure AD. Aninhado em "RequeredResourceAccess", há um valor 'resourceAppId' de 0b07f429-9f4b-4714-9392-cc5e8e80c8b0, e um valor 'resourceAccess > id' de 4589bd03-58cb-4e6c-b17f-b580e39652f8":::

### <a name="collect-important-values"></a>Recolher valores importantes

Em seguida, *selecione Overview* da barra de menus para ver os detalhes do registo da aplicação:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Visão do portal dos valores importantes para o registo da aplicação":::

Tome nota do ID de *Aplicação (cliente)* e *Diretório (inquilino)* mostrado na **sua** página. Estes valores serão necessários mais tarde para [autenticar uma aplicação do cliente contra as APIs das Gémeas Digitais Azure.](how-to-authenticate-client.md) Se você não é a pessoa que vai escrever código para tais aplicações, você deve compartilhar estes valores com a pessoa que será.

### <a name="other-possible-steps-for-your-organization"></a>Outros passos possíveis para a sua organização

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Passos seguintes

Veja como ligar a aplicação do seu cliente ao seu caso escrevendo o código de autenticação da aplicação do cliente:
* [*Como fazer: Escrever código de autenticação de aplicativos*](how-to-authenticate-client.md)