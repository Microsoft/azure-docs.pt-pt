---
title: 'Quickstart: Criar uma aplicação web Node.js linux'
description: Implemente o seu primeiro Node.js Hello World para o Serviço de Aplicações Azure em minutos.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 59a2c96987762e6b56cc7b453877cebe3124e443
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109214"
---
<!-- default for linux -->

## <a name="3-deploy-to-azure-app-service-from-visual-studio-code"></a>3. Implementar para o Serviço de Aplicações Azure a partir do Código do Estúdio Visual

1. Abra a sua pasta de aplicação no Código do Estúdio Visual.

    ```bash
    code .
    ```

1. No explorador **AZURE APP SERVICE,** selecione **Iniciar súpido para Azure...** e siga as instruções. Uma vez assinado, o explorador deve mostrar o nome da sua subscrição Azure.

    ![Inscreva-se em Azure](../media/quickstart-nodejs/sign-in.png)
    <br>
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

1. Escolha **Criar uma nova Aplicação Web,** que implementa o Serviço de Aplicações no Linux por padrão.

1. Digite um globalmente único <abbr title="Os caracteres válidos para um nome de aplicação são 'a-z', '0-9' e '--'.">name</abbr> para a sua Web App e prima **Enter**. 

1. Escolha a sua **versãoNode.js,** recomenda-se o LTS.

    O canal de notificação mostra os recursos Azure que estão a ser criados para a sua aplicação.

1. Selecione **Sim** quando solicitado para atualizar a sua configuração para executar `npm install` no servidor alvo. A sua aplicação é então implementada.

    :::image type="content" source="../media/quickstart-nodejs/server-build.png" alt-text="Screenshot da solicitação para atualizar a sua configuração no servidor alvo com o botão sim selecionado.":::

1. Escolha **Sim** quando for solicitado para atualizar o seu espaço de trabalho para que as implementações posteriores direcionem automaticamente a mesma App Service Web App. 

    :::image type="content" source="../media/quickstart-nodejs/save-configuration.png" alt-text="Screenshot do pedido para atualizar o seu espaço de trabalho com o botão sim selecionado.":::




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


