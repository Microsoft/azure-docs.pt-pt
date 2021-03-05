---
title: Configurar uma instância e autenticação (com script)
titleSuffix: Azure Digital Twins
description: Veja como configurar um exemplo do serviço Azure Digital Twins, executando um script de implementação automatizado
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q3
ms.openlocfilehash: 13f5db52917cd9659438c551ac2f01c477cc1c7b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201299"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Configurar uma instância e autenticação de Gémeos Digitais Azure (scripted)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Este artigo abrange as etapas para **a criação de um novo exemplo de Azure Digital Twins**, incluindo a criação do caso e a criação de autenticação. Depois de completar este artigo, terá uma instância Azure Digital Twins pronta para começar a programar contra.

Esta versão deste artigo completa estes passos executando uma amostra [ **de script de implementação automatizada**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) que simplifica o processo. 
* Para ver os passos manuais do CLI que o script percorre nos bastidores, consulte a versão CLI deste artigo: [*Como-a-fazer: Configurar uma instância e autenticação (CLI)*](how-to-set-up-instance-cli.md).
* Para ver os passos manuais de acordo com o portal Azure, consulte a versão porta deste artigo: [*Como-a-fazer: Configurar uma instância e autenticação (portal)*](how-to-set-up-instance-portal.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prerequisites-download-the-script"></a>Pré-requisitos: Descarregue o script

O guião da amostra está escrito no PowerShell. Faz parte das amostras de ponta a ponta dos [**Gémeos Digitais Azure,**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)que pode descarregar para a sua máquina navegando para essa ligação de amostra e selecionando o botão *de código Browse* por baixo do título. Isto irá levá-lo ao gitHub repo para as amostras, que você pode baixar como um *. ZIP* selecionando o botão *Código* e *Descarregando ZIP*.

:::image type="content" source="media/includes/download-repo-zip.png" alt-text="Vista para as amostras digitais de gémeos no GitHub. O botão Código é selecionado, produzindo uma pequena caixa de diálogo onde é realçado o botão Download ZIP." lightbox="media/includes/download-repo-zip.png":::

Isto irá descarregar um *. Pasta ZIP* para a sua máquina **comodigital-twins-samples-master.zip**. Navegue para a pasta da sua máquina e desaperte-a para extrair os ficheiros.

Na pasta desapertado, o script de implementação está localizado em _scripts digitais-gémeos-mestre > > **deploy.ps1**_.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>Executar o script de implementação

Este artigo utiliza uma amostra de código Azure Digital Twins para implementar uma instância Azure Digital Twins e a autenticação necessária semi-automáticamente. Também pode ser usado como ponto de partida para escrever as suas próprias interações escritas.

Aqui estão os passos para executar o script de implementação em Cloud Shell.
1. Vá a uma janela [Azure Cloud Shell](https://shell.azure.com/) no seu navegador. Inscreva-se usando este comando:
    ```azurecli-interactive
    az login
    ```
    Se o CLI conseguir abrir o seu navegador predefinido, fá-lo-á e carregará uma página de inscrição do Azure. Caso contrário, abra uma página do navegador *https://aka.ms/devicelogin* e introduza o código de autorização exibido no seu terminal.
 
2. Na barra de ícones Cloud Shell, certifique-se de que a cloud shell está definida para executar a versão PowerShell.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Janela Cloud Shell mostrando a seleção da versão PowerShell":::

1. Selecione o ícone "Carregar/Descarregar ficheiros" e escolha "Upload".

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Janela cloud Shell mostrando a seleção do ícone upload":::

    Navegue para o ficheiro _**deploy.ps1**_ na sua máquina (em _scripts digitais-gémeos-mestre > > **deploy.ps1)**_ e atinja "Open". Isto irá enviar o ficheiro para cloud Shell para que possa executá-lo na janela Cloud Shell.

4. Executar o script enviando o `./deploy.ps1` comando na janela Cloud Shell. Pode copiar o comando abaixo (lembre-se que para colar em Cloud Shell, pode utilizar **Ctrl+Shift+V** no Windows e Linux, ou **Cmd+Shift+V** no macOS. Também pode utilizar o menu de clique à direita).

    ```azurecli-interactive
    ./deploy.ps1
    ```

    O script criará uma instância Azure Digital Twins e atribuirá ao seu utilizador Azure o papel *de Proprietário de Dados Azure Digital Twins* no caso.

    À medida que o script passa pelos passos de configuração automatizados, ser-lhe-á pedido que passe nos seguintes valores:
    * Por exemplo: o ID de *subscrição* da sua subscrição Azure para usar
    * Por exemplo: um *local* onde gostaria de desdobrar o caso. Para ver que regiões suportam a Azure Digital Twins, visite [*os produtos Azure disponíveis por região.*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)
    * Por exemplo: um nome *de grupo de recursos.* Pode utilizar um grupo de recursos existente ou introduzir um novo nome para criar.
    * Por exemplo: um *nome* para o seu exemplo de Azure Digital Twins. Se a sua subscrição tiver outro exemplo de Azure Digital Twins na região que já está a usar o nome especificado, será solicitado que escolha um nome diferente.

Aqui está um excerto do registo de saída do script:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="Janela Cloud Shell mostrando log de entrada e saída através da execução do script de implementação" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

Se o guião estiver concluído com sucesso, a impressão final dirá `Deployment completed successfully` . Caso contrário, endereça a mensagem de erro e reencando o script. Irá contornar os passos que já completou e começar a solicitar a entrada novamente no ponto onde deixou de lado.

> [!NOTE]
> O script atribui atualmente o papel de gestão necessário dentro da Azure Digital Twins *(Azure Digital Twins Data Owner*) ao mesmo utilizador que executa o script da Cloud Shell. Se precisar de atribuir esta função a outra pessoa que esteja a gerir o caso, pode fazê-lo agora através do portal Azure[(instruções)](how-to-set-up-instance-portal.md#set-up-user-access-permissions)ou CLI[(instruções).](how-to-set-up-instance-cli.md#set-up-user-access-permissions)

>[!NOTE]
>Existe atualmente um **problema conhecido** com a configuração escrita, no qual alguns utilizadores (especialmente utilizadores em contas pessoais da [Microsoft (MSAs)](https://account.microsoft.com/account)podem encontrar a atribuição de **funções para _Azure Digital Twins Data Owner_ não foi criada**.
>
>Pode verificar a atribuição de funções com a secção [*de atribuição de funções de utilizador*](#verify-user-role-assignment) verificar mais tarde neste artigo e, se necessário, configurar a atribuição de funções manualmente utilizando o portal [Azure](how-to-set-up-instance-portal.md#set-up-user-access-permissions) ou [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions).
>
>Para mais detalhes sobre este assunto, consulte [*Troubleshooting: Questões conhecidas em Azure Digital Twins*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup).

## <a name="verify-success-and-collect-important-values"></a>Verifique o sucesso e colete valores importantes

Para verificar a criação dos seus recursos e permissões configurada pelo script, pode vê-los no [portal Azure](https://portal.azure.com) com as instruções abaixo. Se não conseguir verificar o sucesso de qualquer passo, reprei-o. Pode executar os passos individualmente utilizando as instruções do [portal Azure](how-to-set-up-instance-portal.md) ou [CLI.](how-to-set-up-instance-cli.md)

Esta secção também mostra como encontrar valores importantes a partir dos recursos configurados pelo script que você pode precisar à medida que continua a trabalhar com a sua instância Azure Digital Twins. Deve guardar estes valores num local seguro ou voltar a esta secção para os encontrar mais tarde quando precisar. Se outros utilizadores estiverem a programar contra a instância, também deverá partilhar estes valores com eles.

### <a name="verify-instance"></a>Verificar a ocorrência

Para verificar se o seu caso foi criado, aceda à [página Azure Digital Twins](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) no portal Azure. Você mesmo pode chegar a esta página procurando por *Azure Digital Twins* na barra de pesquisa do portal.

Esta página lista todas as suas instâncias Azure Digital Twins. Procure o nome do seu recém-criado caso na lista.

Se a verificação não tiver sido bem sucedida, pode voltar a tentar criar uma instância utilizando o [portal](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance) ou [o CLI](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance).

### <a name="collect-instance-values"></a>Recolher os valores das instâncias

Selecione o nome do seu caso na [página Azure Digital Twins](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) para abrir a página *geral* do caso. Note o seu *Nome,* *Grupo de Recursos* e Nome *hospedeiro.* Pode precisar mais tarde para identificar e ligar-se ao seu caso.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Realçando os valores importantes da página geral da instância":::

### <a name="verify-user-role-assignment"></a>Verificar a atribuição da função do utilizador

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> Lembre-se que o script atribui atualmente esta função necessária ao mesmo utilizador que executa o script a partir de Cloud Shell. Se precisar de atribuir esta função a outra pessoa que esteja a gerir o caso, pode fazê-lo agora através do portal Azure[(instruções)](how-to-set-up-instance-portal.md#set-up-user-access-permissions)ou CLI[(instruções).](how-to-set-up-instance-cli.md#set-up-user-access-permissions)

Se a verificação não tiver sido bem sucedida, também pode refazer a sua própria função utilizando o [portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) ou [o CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions).

## <a name="next-steps"></a>Passos seguintes

Teste as chamadas individuais da API REST no seu caso utilizando os comandos CLI dos Gémeos Digitais Azure: 
* [az dt referência](/cli/azure/ext/azure-iot/dt)
* [*Como fazer: Use o CLI das Gémeas Digitais Azure*](how-to-use-cli.md)

Ou, veja como ligar uma aplicação do cliente ao seu caso com código de autenticação:
* [*Como fazer: Escrever código de autenticação de aplicativos*](how-to-authenticate-client.md)