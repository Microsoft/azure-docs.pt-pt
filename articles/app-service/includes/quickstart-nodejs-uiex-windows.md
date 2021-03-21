---
title: 'Quickstart: Criar uma aplicação web Node.js - janelas'
description: Implemente o seu primeiro serviço de aplicações hello world Node.js para a Azure em minutos para a Plataforma Windows.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 324919c1d085302e675e0bc9a370ce35f52a0f1d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102109389"
---
<!-- advanced for windows -->

## <a name="3-deploy-to-azure-app-service-from-visual-studio-code"></a>3. Implementar para o Serviço de Aplicações Azure a partir do Código do Estúdio Visual

1. Abra a sua pasta de aplicação no Código do Estúdio Visual.

    ```bash
    code .
    ```

1. No explorador **AZURE APP SERVICE,** selecione **Iniciar súpido para Azure...** e siga as instruções. Uma vez assinado, o explorador deve mostrar o nome da sua subscrição Azure.

    ![Iniciar sessão no Azure](../media/quickstart-nodejs/sign-in.png)

    <details>
    <summary>Resolução de problemas Azure sign-in</summary>
    
    Se vir o erro **"Não é possível encontrar subscrição com nome [ID de subscrição]"** ao iniciar sessão no Azure, pode ser porque está por detrás de um representante e não consegue chegar à API Azure. Configure `HTTP_PROXY` `HTTPS_PROXY` variáveis e ambientais com a sua informação de procuração no seu terminal utilizando `export` .
    
    ```bash
    export HTTPS_PROXY=https://username:password@proxy:8080
    export HTTP_PROXY=http://username:password@proxy:8080
    ```

    [Reportar um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)


1. No explorador **AZURE APP SERVICE,** selecione o ícone de seta azul para cima para implementar a sua aplicação para Azure. 

    :::image type="content" source="../media/quickstart-nodejs/deploy.png" alt-text="Screenshot do serviço Azure App em VS Code mostrando o ícone de seta azul selecionado.":::

1. Escolha o diretório que tem atualmente aberto, `nodejs-docs-hello-world` .

1. Escolha **Criar nova Aplicação Web... Avançado**, para implementar no Serviço de Aplicações no Windows.

1. Digite um globalmente único <abbr title="Os caracteres válidos para um nome de aplicação são 'a-z', '0-9' e '--'.">name</abbr> para a sua aplicação web e prima **Enter**. 
1. Selecione **Criar um novo grupo de recursos** e, em seguida, insira um nome para o grupo de recursos, como `AppServiceQS-rg` .
1. Escolha a sua **versãoNode.js,** recomenda-se o LTS.

    O canal de notificação mostra os recursos Azure que estão a ser criados para a sua aplicação.
1. Selecione **Windows** para o sistema operativo.
1. **Selecione Crie um novo plano de Serviço de Aplicações** e, em seguida, insira um nome para o plano (como), `AppServiceQS-plan` em seguida, selecione **F1 Grátis** para o nível de preços.
1. Escolha **Skip por agora** quando solicitado sobre Insights de Aplicação.
1. Escolha uma região perto de si ou perto de recursos a que deseje aceder.

1. Escolha **Sim** quando for solicitado para atualizar o seu espaço de trabalho para que as implementações posteriores direcionem automaticamente a mesma App Service Web App. 

    :::image type="content" source="../media/quickstart-nodejs/save-configuration.png" alt-text="Screenshot do pedido para atualizar o seu espaço de trabalho com o botão sim selecionado.":::

1. Clique no nó para o serviço de aplicações mais uma vez e selecione **Implementar para a Web App.**

1. Clique no nó para o serviço de aplicações mais uma vez e selecione **Browse Website**.

    [Reportar um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

1. Assim que a implementação estiver concluída, **selecione Browse Website** no pedido para ver a sua aplicação web recentemente implementada.

<br/>
<details>
<summary><strong>Resolução de problemas</strong></summary>

Verifique se não conseguiu completar estes passos:

* Certifique-se de que a sua aplicação está a ouvir na porta fornecida pela variável ambiente PORT: `process.env.PORT` .

* Se vir o erro **"Não tem permissão para ver este diretório ou página."** Reveja a saída de registo para encontrar e corrigir o erro. 

</details>

<br>

[Reportar um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br/>
<hr/>
