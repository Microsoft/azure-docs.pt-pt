---
title: 'QuickStart: Criar uma aplicação web estática HTML'
description: Implemente o seu primeiro HTML Hello World para o Serviço de Aplicações Azure em minutos. Você implementa usando Git, que é uma das muitas maneiras de implementar para o Serviço de Aplicações.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 603d8e642cd2e88beec6ae34094a2c6c43d179ee
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768936"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Criar uma aplicação Web HTML estática no Azure

Este quickstart mostra como implementar um site básico HTML+CSS para <abbr title="Um serviço baseado em HTTP para hospedar aplicações web, APIs REST e aplicações móveis de back-end.">Serviço de Aplicações do Azure</abbr>. Você completará este quickstart em [Cloud Shell,](../cloud-shell/overview.md)mas também pode executar estes comandos localmente com [Azure CLI](/cli/azure/install-azure-cli).

## <a name="1-prepare-your-environment"></a>1. Prepare o seu ambiente

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Em [Cloud Shell,](../cloud-shell/overview.md)crie um diretório de arranque rápido e, em seguida, mude para ele.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Em seguida, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu diretório de início rápido.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```
<hr/>

## <a name="2-create-a-web-app"></a>2. Criar uma aplicação web

Mude para o diretório que contém o código de exemplo e execute o comando `az webapp up`. **Substituir** `<app-name>` com um nome globalmente único.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

<details>
<summary>Resolução de problemas</summary>
<ul>
<li>Se o <code>az</code> comando não for reconhecido, certifique-se de que tem o CLI Azure instalado como descrito no <a href="#1-prepare-your-environment">Preparar o seu ambiente</a>.</li>
<li>Substitua <code>&lt;app-name&gt;</code> por um nome único em todo o Azure <em> (caracteres válidos <code>a-z</code> <code>0-9</code> são, e <code>-</code> </em> . Um bom padrão é usar uma combinação do nome da sua empresa e um identificador de aplicativos.</li>
<li>O <code>--sku F1</code> argumento cria a aplicação web no nível de preços gratuitos. Omita este argumento para usar um nível premium mais rápido, que incorre num custo de hora a hora.</li>
<li>O <code>--html</code> argumento diz para tratar todos os conteúdos de pasta como conteúdo estático e desativar a automatização de construção.</li>
<li>Pode opcionalmente incluir o argumento <code>--location &lt;location-name&gt;</code> onde <code>&lt;location-name&gt;</code> está uma região de Azure disponível. Pode recuperar uma lista de regiões admissíveis para a sua conta Azure, executando o <a href="/cli/azure/appservice#az_appservice_list_locations"> <code>az account list-locations</code> </a> comando.</li>
</ul>
</details>

O comando pode demorar alguns minutos a ser concluído. 

<details>
<summary>O que está a <code>az webapp up</code> fazer?</summary>
<p>O comando <code>az webapp up</code> executa as seguintes ações:</p>
<ul>
<li>Cria um grupo de recursos predefinido.</li>
<li>Crie um plano de Serviço de Aplicações predefinido.</li>
<li><a href="/cli/azure/webapp#az_webapp_create">Crie uma aplicação de Serviço de Aplicações</a> com o nome especificado.</li>
<li><a href="/azure/app-service/deploy-zip">Zip implementa</a> ficheiros do diretório de trabalho atual para a aplicação.</li>
<li>Durante a execução, fornece mensagens sobre criação de recursos, registo e implementação zip.</li>
</ul>

Quando termina, apresenta informações semelhantes ao seguinte exemplo:

```output
{
  "app_url": "https://&lt;app_name&gt;.azurewebsites.net",
  "location": "westeurope",
  "name": "&lt;app_name&gt;",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/&lt;username&gt;/quickstart/html-docs-hello-world ",
  &lt; JSON data removed for brevity. &gt;
}
```

</details>

Vai precisar do `resourceGroup` valor para limpar os [recursos](#6-clean-up-resources) mais tarde.

<hr/>

## <a name="3-browse-to-the-app"></a>3. Navegue para a aplicação

Num browser, aceda ao URL da aplicação: `http://<app_name>.azurewebsites.net` .

A página está a ser executada como uma aplicação Web do Serviço de Aplicações do Azure.

![Home page da aplicação de exemplo](media/quickstart-html/hello-world-in-browser-az.png)

<hr/>

## <a name="4-update-and-redeploy-the-app"></a>4. Atualizar e recolocar a app

Na Cloud Shell, **escreva** `nano index.html` para abrir o editor de texto nano. 

Na etiqueta de `<h1>` posição, altere "Azure App Service - Sample Static HTML Site" para "Azure App Service".

![Nano index.html](media/quickstart-html/nano-index-html.png)

**Guarde** as suas alterações utilizando o comando `^O` .

**Saída** nano utilizando o comando `^X` .

Recolocar a aplicação com `az webapp up` comando.

```bash
az webapp up --html
```

Volte para a janela do navegador que abriu na Navegação para o passo **da aplicação.**

**Refresque** a página.

![Página inicial atualizada da aplicação de exemplo](media/quickstart-html/hello-azure-in-browser-az.png)

<hr/>

## <a name="5-manage-your-new-azure-app"></a>5. Gerencie a sua nova app Azure

**Navegue** até ao [portal Azure](https://portal.azure.com)., 

**Procure** e **selecione** **Serviços de Aplicações.**

![Selecione Serviços de Aplicações no portal Azure](./media/quickstart-html/portal0.png)

**Selecione** o nome da sua aplicação Azure.

![Navegação do portal para a aplicação do Azure](./media/quickstart-html/portal1.png)

É apresentada a página de descrição geral da sua aplicação Web. Aqui, pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar.

![Painel Serviço de Aplicações no portal do Azure](./media/quickstart-html/portal2.png)

O menu à esquerda fornece diferentes páginas para configurar a sua aplicação.

<hr/>

## <a name="6-clean-up-resources"></a>6. Limpar recursos

Nos passos anteriores, criou os recursos do Azure num grupo de recursos. Se achar que não vai precisar destes recursos no futuro, execute o seguinte comando no Cloud Shell para eliminar o grupo de recursos. Lembre-se de que o nome do grupo de recursos foi gerado automaticamente para si no passo [criar uma aplicação Web](#2-create-a-web-app).

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

Este comando pode demorar alguns minutos a ser executado.

<hr/>

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Mapear domínio personalizado](app-service-web-tutorial-custom-domain-uiex.md)
