---
title: Melhores práticas de implementação
description: Conheça os mecanismos-chave de implantação no Azure App Service. Encontre recomendações específicas da linguagem e outras ressalvas.
keywords: serviço de aplicativos azure, web app, implementar, implementar, oleodutos, construir
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: ac4e8d93e2e729db387b0f9cf81017b6c58b6750
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951339"
---
# <a name="deployment-best-practices"></a>Implementação de Melhores Práticas

Todas as equipas de desenvolvimento têm requisitos únicos que podem dificultar a implementação de um gasoduto de implementação eficiente em qualquer serviço de nuvem. Este artigo introduz os três principais componentes da implantação no Serviço de Aplicações: fontes de implantação, construção de oleodutos e mecanismos de implantação. Este artigo também abrange algumas boas práticas e dicas para pilhas de linguagem específicas.

## <a name="deployment-components"></a>Componentes de implantação

### <a name="deployment-source"></a>Fonte de implantação

Uma fonte de implantação é a localização do seu código de aplicação. Para aplicações de produção, a fonte de implementação é geralmente um repositório hospedado por software de controlo de versão como [GitHub, BitBucket ou Azure Repos](deploy-continuous-deployment.md). Para cenários de desenvolvimento e teste, a fonte de implantação pode ser [um projeto na sua máquina local.](deploy-local-git.md) O Serviço de Aplicações também suporta [as pastas OneDrive e Dropbox](deploy-content-sync.md) como fontes de implementação. Embora as pastas em nuvem possam facilitar o início com o App Service, não é normalmente recomendado utilizar esta fonte para aplicações de produção a nível empresarial. 

### <a name="build-pipeline"></a>Pipeline de Compilação

Uma vez que você decide sobre uma fonte de implantação, o seu próximo passo é escolher um oleoduto de construção. Um gasoduto de construção lê o seu código fonte a partir da fonte de implantação e executa uma série de etapas (tais como código de compilação, minimização html e JavaScript, testes de execução e componentes de embalagem) para obter a aplicação num estado runable. Os comandos específicos executados pelo gasoduto de construção dependem da sua pilha de idiomas. Estas operações podem ser executadas num servidor de construção, como a Azure Pipelines, ou executadas localmente.

### <a name="deployment-mechanism"></a>Mecanismo de Implantação

O mecanismo de implementação é a ação usada para colocar a sua aplicação incorporada no *diretório /home/site/wwwroot* da sua aplicação web. O *diretório /wwwroot* é um local de armazenamento montado partilhado por todos os casos da sua aplicação web. Quando o mecanismo de implementação coloca a sua aplicação neste diretório, os seus casos recebem uma notificação para sincronizar os novos ficheiros. O Serviço de Aplicações suporta os seguintes mecanismos de implantação:

- Kudu pontos finais: [Kudu](https://github.com/projectkudu/kudu/wiki) é a ferramenta de produtividade do desenvolvedor de código aberto que funciona como um processo separado no Windows App Service, e como um segundo recipiente no Linux App Service. Kudu lida com implementações contínuas e fornece pontos finais HTTP para implementação, como zipdeploy.
- FTP e WebDeploy: Utilizando [as credenciais do seu site ou do utilizador,](deploy-configure-credentials.md)pode fazer o upload de ficheiros [através de FTP](deploy-ftp.md) ou WebDeploy. Estes mecanismos não passam por Kudu.  

Ferramentas de implementação como Azure Pipelines, Jenkins e plugins editor usam um destes mecanismos de implementação.

## <a name="use-deployment-slots"></a>Utilize slots de implantação

Sempre que possível, utilize [slots de implantação](deploy-staging-slots.md) ao implementar uma nova construção de produção. Ao utilizar um nível de Plano de Serviço de Aplicações Padrão ou melhor, pode implementar a sua aplicação num ambiente de preparação, validar as suas alterações e fazer testes de fumo. Quando estiver pronto, pode trocar as suas ranhuras de encenação e produção. A operação de troca aquece as instâncias necessárias para corresponder à sua escala de produção, eliminando assim o tempo de inatividade.

### <a name="continuously-deploy-code"></a>Código de implantação contínua

Se o seu projeto designou ramos para testes, QA e encenação, então cada um desses ramos deve ser continuamente implantado para uma ranhura de encenação. (Isto é conhecido como o [design gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow).) Isto permite que as suas partes interessadas avaliem e testem facilmente o ramo implantado. 

A implantação contínua nunca deve ser ativada para a sua ranhura de produção. Em vez disso, o seu ramo de produção (muitas vezes principal) deve ser implantado numa ranhura não-produção. Quando estiver pronto para libertar o ramo base, troque-o pela ranhura de produção. A troca em produção - em vez de implantar para a produção - impede o tempo de inatividade e permite-lhe reverter as alterações trocando novamente. 

![Diagrama que mostra o fluxo entre os ramos Dev, Staging e Main e as ranhuras para as quais estão implantados.](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>Transportes contínuos

Para recipientes personalizados do Docker ou de outros registos de contentores, inserta a imagem numa ranhura de encenação e troque para a produção para evitar tempo de inatividade. A automatização é mais complexa do que a implementação de códigos porque deve empurrar a imagem para um registo de contentores e atualizar a etiqueta de imagem no webapp.

Para cada ramo que pretende implantar numa ranhura, crie automatização para fazer o seguinte em cada um comprometendo-se com o ramo.

1. **Construa e marque a imagem.** Como parte do oleoduto de construção, marque a imagem com o git commit ID, timetamp, ou outras informações identificáveis. É melhor não usar a etiqueta "mais recente" padrão. Caso contrário, é difícil rastrear o código que está atualmente implantado, o que torna a depuragem muito mais difícil.
1. **Empurre a imagem marcada**. Uma vez que a imagem é construída e marcada, o gasoduto empurra a imagem para o nosso registo de contentores. No passo seguinte, a ranhura de implantação retirará a imagem marcada do registo do contentor.
1. **Atualize a ranhura de implementação com a nova etiqueta de imagem**. Quando esta propriedade for atualizada, o site reiniciará automaticamente e puxará a nova imagem do recipiente.

![Visual de utilização de slot](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

Há exemplos abaixo para quadros comuns de automação.

### <a name="use-azure-devops"></a>Use Azure DevOps

O Serviço de Aplicações tem [entrega contínua incorporada](deploy-continuous-deployment.md) para contentores através do Centro de Implantação. Navegue para a sua aplicação no [portal Azure](https://portal.azure.com/) e selecione **o Centro de Implementação** em **Implementação.** Siga as instruções para selecionar o seu repositório e ramo. Isto configurará um gasoduto de construção e libertação de DevOps para construir, etiquetar e implantar automaticamente o seu contentor quando novos compromissos forem empurrados para o seu ramo selecionado.

### <a name="use-github-actions"></a>Use ações do GitHub

Também pode automatizar a colocação do seu contentor [com as ações do GitHub](./deploy-ci-cd-custom-container.md).  O ficheiro de fluxo de trabalho abaixo irá construir e marcar o contentor com o ID do compromisso, empurrá-lo para um registo de contentores e atualizar a ranhura do site especificado com a nova etiqueta de imagem.

```yaml
name: Build and deploy a container image to Azure Web Apps

on:
  push:
    branches:
    - <your-branch-name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@main

    -name: Authenticate using a Service Principal
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_SP }}

    - uses: azure/container-actions/docker-login@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push the image tagged with the git commit hash
      run: |
        docker build . -t contoso/demo:${{ github.sha }}
        docker push contoso/demo:${{ github.sha }}

    - name: Update image tag on the Azure Web App
      uses: azure/webapps-container-deploy@v1
      with:
        app-name: '<your-webapp-name>'
        slot-name: '<your-slot-name>'
        images: 'contoso/demo:${{ github.sha }}'
```

### <a name="use-other-automation-providers"></a>Utilizar outros fornecedores de automação

Os passos listados anteriormente aplicam-se a outros utilitários de automação, tais como CircleCI ou Travis CI. No entanto, é necessário utilizar o CLI Azure para atualizar as ranhuras de implementação com novas etiquetas de imagem no passo final. Para utilizar o Azure CLI no seu script de automação, gere um Diretor de Serviço utilizando o seguinte comando.

```azurecli
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

No seu script, faça login `az login --service-principal` usando, fornecendo a informação do diretor. Em seguida, pode utilizar `az webapp config container set` para definir o nome do recipiente, etiqueta, URL de registo e senha de registo. Abaixo estão alguns links úteis para você construir o seu processo de CI de contentor.

- [Como iniciar sessão no Azure CLI no Circle CI](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>considerações Language-Specific

### <a name="java"></a>Java

Utilize o [zipdeploy/API](deploy-zip.md) de Kudu para a implementação de aplicações JAR e [wardeploy/](deploy-zip.md#deploy-war-file) para aplicações WAR. Se estiver a usar o Jenkins, pode utilizar essas APIs diretamente na sua fase de implantação. Para obter mais informações, consulte [este artigo](/azure/developer/jenkins/deploy-to-azure-app-service-using-azure-cli).

### <a name="node"></a>Nó

Por predefinição, a Kudu executa os passos de construção para a sua aplicação Nó `npm install` (). Se estiver a utilizar um serviço de construção como o Azure DevOps, então a construção kudu é desnecessária. Para desativar a construção kudu, crie uma configuração de `SCM_DO_BUILD_DURING_DEPLOYMENT` aplicação, com um valor de `false` .

### <a name="net"></a>.NET 

Por predefinição, a Kudu executa os passos de construção para a sua aplicação .NET `dotnet build` (). Se estiver a utilizar um serviço de construção como o Azure DevOps, então a construção kudu é desnecessária. Para desativar a construção kudu, crie uma configuração de `SCM_DO_BUILD_DURING_DEPLOYMENT` aplicação, com um valor de `false` .

## <a name="other-deployment-considerations"></a>Outras Considerações de Implantação

### <a name="local-cache"></a>Local Cache

O conteúdo do Azure App Service é armazenado no Azure Storage e surge de forma duradoura como uma partilha de conteúdo. No entanto, algumas aplicações apenas precisam de uma loja de conteúdo de alto desempenho e apenas de leitura que possam ser executadas com alta disponibilidade. Estas aplicações podem beneficiar da utilização de [cache local.](overview-local-cache.md) A cache local não é recomendada para sites de gestão de conteúdos, como o WordPress.

Utilize sempre cache local em conjunto com [ranhuras de implantação](deploy-staging-slots.md) para evitar tempo de inatividade. Consulte [esta secção](overview-local-cache.md#best-practices-for-using-app-service-local-cache) para obter informações sobre a utilização destas funcionalidades em conjunto.

### <a name="high-cpu-or-memory"></a>CpU alto ou memória

Se o seu Plano de Serviço de Aplicações estiver a utilizar mais de 90% do CPU ou memória disponível, a máquina virtual subjacente poderá ter problemas em processar a sua implementação. Quando isto acontecer, aumenta temporariamente a contagem de ocorrências para executar a implantação. Uma vez terminada a implantação, pode devolver a contagem de ocorrências ao valor anterior.

Para obter mais informações sobre as melhores práticas, visite [o App Service Diagnostics](./overview-diagnostics.md) para descobrir as melhores práticas ações específicas do seu recurso.

- Navegue para a sua Web App no [portal Azure.](https://portal.azure.com)
- Clique no **Diagnóstico e resolva problemas** na navegação à esquerda, que abre o App Service Diagnostics.
- Escolha o azulejo inicial **das Melhores Práticas.**
- Clique em **Melhores Práticas para Disponibilidade & Desempenho** ou **Melhores Práticas para Configuração Ideal** para ver o estado atual da sua aplicação no que diz respeito a estas melhores práticas.

Também pode utilizar este link para abrir diretamente os Diagnósticos de Serviço de Aplicações para o seu `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot` recurso: