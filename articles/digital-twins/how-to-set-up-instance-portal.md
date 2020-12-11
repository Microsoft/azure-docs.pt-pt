---
title: Configurar uma instância e a autenticação (portal)
titleSuffix: Azure Digital Twins
description: Veja como configurar uma instância do serviço Azure Digital Twins usando o portal Azure
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 90a425fe64424c946a02f3c113889b62b58fbeb4
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032325"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Configurar uma instância e autenticação Azure Digital Twins (portal)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Este artigo abrange as etapas para **a criação de um novo exemplo de Azure Digital Twins**, incluindo a criação do caso e a criação de autenticação. Depois de completar este artigo, terá uma instância Azure Digital Twins pronta para começar a programar contra.

Esta versão deste artigo passa por estes passos manualmente, um a um, utilizando o portal Azure. O portal do Azure é uma consola unificada baseada na Web que constitui uma alternativa às ferramentas de linha de comandos.
* Para passar por estes passos manualmente utilizando o CLI, consulte a versão CLI deste artigo: [*Como-a-fazer: Configurar um caso e autenticação (CLI)*](how-to-set-up-instance-cli.md).
* Para passar por uma configuração automatizada utilizando uma amostra de script de implementação, consulte a versão escrita deste artigo: [*Como-a-fazer: Configurar uma instância e autenticação (scripted)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Criar a instância Azure Digital Twins

Nesta secção, irá **criar uma nova instância de Azure Digital Twins** utilizando o portal [Azure](https://ms.portal.azure.com/). Navegue até ao portal e faça login com as suas credenciais.

Uma vez no portal, comece por selecionar _Criar um recurso_ no menu de página inicial dos serviços Azure.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Selecionando 'Criar um recurso' a partir da página inicial do portal Azure":::

Procure *por Azure Digital Twins* na caixa de pesquisa e escolha o serviço **Azure Digital Twins** a partir dos resultados. Selecione o botão _Criar_ para criar uma nova instância do serviço.

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

A partir *da* página geral do caso, note o seu *nome,* *grupo de recursos* e nome *anfitrião.* Estes são todos os valores importantes que poderá necessitar enquanto continua a trabalhar com a sua instância Azure Digital Twins. Se outros utilizadores estiverem a programar contra a ocorrência, deverá partilhar estes valores com eles.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Realçando os valores importantes da página geral da instância":::

Agora tens uma instância do Azure Digital Twins pronta para ir. Em seguida, você dará as permissões apropriadas do utilizador Azure para geri-lo.

## <a name="set-up-user-access-permissions"></a>Configurar permissões de acesso ao utilizador

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Primeiro, abra a página para a sua instância Azure Digital Twins no portal Azure. A partir do menu da instância, selecione *Access control (IAM)*. Selecione o botão  *Adicionar* sob *Adicionar uma atribuição de função*.

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="Selecionando para adicionar uma atribuição de função a partir da página 'Controlo de acesso (IAM)'":::

Na página seguinte *de atribuição de funções Adicionar,* preencha os valores (deve ser preenchido por um utilizador com [permissões suficientes](#prerequisites-permission-requirements) na subscrição do Azure):
* **Função**: Selecione *Azure Digital Twins Data Owner* do menu dropdown
* **Atribuir acesso a**: Selecione *Azure AD utilizador, grupo ou principal de serviço* a partir do menu suspenso
* **Selecione:** Procure o nome ou endereço de e-mail do utilizador para atribuir. Quando selecionar o resultado, o utilizador aparecerá numa secção *de membros selecionados.*

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="Preenchendo os campos listados no diálogo &quot;Adicionar função&quot;":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Quando terminar de introduzir os detalhes, acerte no botão *Guardar.*

### <a name="verify-success"></a>Verificar o sucesso

Pode ver a atribuição de funções que estabeleceu no controlo de *acesso (IAM) > atribuições de Função*. O utilizador deve aparecer na lista com um papel de *Azure Digital Twins Data Owner*. 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Vista das atribuições de funções para uma instância Azure Digital Twins no portal Azure":::

Tem agora uma instância Azure Digital Twins pronta a ser executada, e atribuiu permissões para geri-lo.

## <a name="next-steps"></a>Passos seguintes

Teste as chamadas individuais da API REST no seu caso utilizando os comandos CLI dos Gémeos Digitais Azure: 
* [az dt referência](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*Como fazer: Use o CLI das Gémeas Digitais Azure*](how-to-use-cli.md)

Ou, veja como ligar uma aplicação do cliente ao seu caso com código de autenticação:
* [*Como fazer: Escrever código de autenticação de aplicativos*](how-to-authenticate-client.md)