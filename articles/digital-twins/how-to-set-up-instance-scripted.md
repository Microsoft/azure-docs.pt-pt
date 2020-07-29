---
title: Configurar uma instância e autenticação (com script)
titleSuffix: Azure Digital Twins
description: Veja como configurar uma instância do serviço Azure Digital Twins, incluindo a autenticação adequada. Versão escrita.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 522096b921faf34130f0c37f727d89c7bf95c530
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337913"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Configurar uma instância e autenticação de Gémeos Digitais Azure (scripted)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Este artigo abrange as etapas para **a criação de um novo exemplo de Azure Digital Twins**, incluindo a criação do caso e a criação de autenticação. Depois de completar este artigo, terá uma instância Azure Digital Twins pronta para começar a programar contra.

Esta versão deste artigo completa estes passos executando uma amostra [ **de script de implementação automatizada** ](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/) que simplifica o processo. Para ver os passos manuais que o script percorre nos bastidores, consulte a versão manual deste artigo: [*Como-a-fazer: Configurar uma instância e autenticação (manual)*](how-to-set-up-instance-manual.md).

[!INCLUDE [digital-twins-setup-starter.md](../../includes/digital-twins-setup-starter.md)]

## <a name="run-the-deployment-script"></a>Executar o script de implementação

Este artigo utiliza uma amostra de código Azure Digital Twins para implementar uma instância Azure Digital Twins e a autenticação necessária semi-automáticamente. Também pode ser usado como ponto de partida para escrever as suas próprias interações escritas.

O guião da amostra está escrito no PowerShell. Faz parte das [amostras Azure Digital Twins,](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/)que pode descarregar para a sua máquina navegando para essa ligação de amostra e selecionando o botão *Download ZIP* por baixo do título.

Na pasta de amostra descarregada, o script de implementação está localizado em _scriptsAzure_Digital_Twins_samples.zip > > **deploy.ps1** _.

Aqui estão os passos para executar o script de implementação em Cloud Shell.
1. Vá a uma janela [Azure Cloud Shell](https://shell.azure.com/) no seu navegador. Inscreva-se usando este comando:
    ```azurecli-interactive
    az login
    ```
    Se o CLI conseguir abrir o seu navegador predefinido, fá-lo-á e carregará uma página de inscrição do Azure. Caso contrário, abra uma página do navegador *https://aka.ms/devicelogin* e introduza o código de autorização exibido no seu terminal.
 
2. Depois de iniciar sessão, olhe para a barra de ícones da janela Cloud Shell. Selecione o ícone "Carregar/Descarregar ficheiros" e escolha "Upload".

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell-upload.png" alt-text="Janela Cloud Shell mostrando a seleção da opção Upload":::

    Navegue para o _**ficheirodeploy.ps1**_ na sua máquina e acerte em "Open". Isto irá enviar o ficheiro para cloud Shell para que possa executá-lo na janela Cloud Shell.

3. Executar o script enviando o `./deploy.ps1` comando na janela Cloud Shell. À medida que o script passa pelos passos de configuração automatizados, ser-lhe-á pedido que passe nos seguintes valores:
    * Por exemplo: o ID de *subscrição* da sua subscrição Azure para usar
    * Por exemplo: um *local* onde gostaria de desdobrar o caso. Para ver que regiões suportam a Azure Digital Twins, visite [*os produtos Azure disponíveis por região.*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)
    * Por exemplo: um nome *de grupo de recursos.* Pode utilizar um grupo de recursos existente ou introduzir um novo nome para criar.
    * Por exemplo: um *nome* para o seu exemplo de Azure Digital Twins. O nome do novo caso deve ser único na região para a sua subscrição (o que significa que se a sua subscrição tiver outra instância Azure Digital Twins na região que já está a usar o nome que escolher, será solicitado que escolha um nome diferente).
    * Para o registo da aplicação: um *nome de exposição de aplicações AZure AD* para associar ao registo. Este registo de aplicações é onde configura permissões de acesso às [APIs das Gémeas Digitais Azure.](how-to-use-apis-sdks.md) Posteriormente, a aplicação do cliente irá autenticar-se contra o registo da aplicação e, consequentemente, será-lhe concedidas as permissões de acesso configuradas às APIs.
    * Para o registo da aplicação: um *URL de resposta a aplicação Azure AD* para a aplicação AZure AD. Pode `http://localhost` usar.

O script criará uma instância Azure Digital Twins, atribuirá ao seu utilizador Azure o papel *de Azure Digital Twins Owner (Preview)* no caso, e configurará um registo de aplicações AD AZure para a aplicação do seu cliente para usar.

Aqui está um excerto do registo de saída do script:

:::image type="content" source="media/how-to-set-up-instance/deployment-script-output.png" alt-text="Janela Cloud Shell mostrando log de entrada e saída através da execução do script de implementação" lightbox="media/how-to-set-up-instance/deployment-script-output.png":::

Se o guião estiver concluído com sucesso, a impressão final dirá `Deployment completed successfully` . Caso contrário, endereça a mensagem de erro e reencando o script. Irá contornar os passos que já completou e começar a solicitar a entrada novamente no ponto onde deixou de lado.

Após a conclusão do script, tem agora uma instância Azure Digital Twins pronta a ser executada e permissões configuradas para a gerir.

## <a name="collect-important-values"></a>Recolher valores importantes

Existem dois valores importantes do registo da aplicação que serão necessários mais tarde para [autenticar uma aplicação do cliente contra as APIs das Gémeas Digitais Azure.](how-to-authenticate-client.md) 

Para encontrá-los, siga [este link](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) para navegar para a página geral de registo da aplicação AZure no portal Azure. Esta página mostra todos os registos de aplicações que foram criados na sua subscrição.

Você deve ver o registo de aplicações que acabou de criar nesta lista. Selecione-o para abrir os seus detalhes:

:::image type="content" source="media/how-to-set-up-instance/app-important-values.png" alt-text="Visão do portal dos valores importantes para o registo da aplicação":::

Tome nota do ID de *Aplicação (cliente)* e *Diretório (inquilino)* mostrado na **sua** página. Se você não é a pessoa que vai escrever código para aplicações de clientes, você precisará compartilhar estes valores com a pessoa que será.

## <a name="verify-success"></a>Verificar o sucesso

Se quiser verificar a criação dos seus recursos e permissões configurada pelo script, pode vê-los no [portal Azure](https://portal.azure.com).

### <a name="verify-instance"></a>Verificar a ocorrência

Para verificar se o seu caso foi criado, aceda à [página Azure Digital Twins](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) no portal Azure. Esta página lista todas as suas instâncias Azure Digital Twins. Procure o nome do seu recém-criado caso na lista.

### <a name="verify-user-role-assignment"></a>Verificar a atribuição da função do utilizador

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

### <a name="verify-app-registration"></a>Verificar registo de aplicativos

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Em primeiro lugar, verifique se as permissões das permissões Azure Digital Twins foram corretamente definidas no registo. Para isso, selecione *Manifesto* da barra de menu para ver o código manifesto do registo da aplicação. Percorra a parte inferior da janela de código e procure estes campos sob `requiredResourceAccess` . Os valores devem corresponder aos da imagem abaixo:

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

## <a name="other-possible-steps-for-your-organization"></a>Outros passos possíveis para a sua organização

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Passos seguintes

Veja como ligar a aplicação do seu cliente ao seu caso escrevendo o código de autenticação da aplicação do cliente:
* [*Como fazer: Escrever código de autenticação de aplicativos*](how-to-authenticate-client.md)
