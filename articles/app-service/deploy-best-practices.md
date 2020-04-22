---
title: Melhores práticas de implementação
description: Conheça os principais mecanismos de implantação para o Azure App Service. Encontre recomendações específicas da linguagem e outras ressalvas.
keywords: serviço de aplicações azure, web app, implementação, implantação, oleodutos, construir
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 4dd959d75fd582d787e68db4a415a4a694b9cda8
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770652"
---
# <a name="deployment-best-practices"></a>Implementação de Boas Práticas

Todas as equipas de desenvolvimento têm requisitos únicos que podem dificultar a implementação de um pipeline de implantação eficiente em qualquer serviço na nuvem. Este artigo introduz os três principais componentes da implantação para o Serviço de Aplicações: fontes de implantação, oleodutos de construção e mecanismos de implantação. Este artigo também abrange algumas boas práticas e dicas para pilhas de idiomas específicas.

## <a name="deployment-components"></a>Componentes de implantação

### <a name="deployment-source"></a>Fonte de Implantação

Uma fonte de implantação é a localização do seu código de aplicação. Para aplicações de produção, a fonte de implantação é geralmente um repositório hospedado por software de controlo de versão como [GitHub, BitBucket ou Azure Repos](deploy-continuous-deployment.md). Para cenários de desenvolvimento e teste, a fonte de implantação pode ser [um projeto na sua máquina local.](deploy-local-git.md) O Serviço de Aplicações também suporta [as pastas OneDrive e Dropbox](deploy-content-sync.md) como fontes de implementação. Embora as pastas de nuvem possam facilitar o início com o App Service, não é tipicamente recomendado utilizar esta fonte para aplicações de produção a nível empresarial. 

### <a name="build-pipeline"></a>Pipeline de Compilação

Assim que decidires sobre uma fonte de implantação, o teu próximo passo é escolher um oleoduto de construção. Um pipeline de construção lê o seu código fonte a partir da fonte de implementação e executa uma série de passos (como a compilação de código, minificação HTML e JavaScript, testes de execução e componentes de embalagem) para obter a aplicação em estado de execução. Os comandos específicos executados pelo oleoduto dependem da sua pilha de linguagem. Estas operações podem ser executadas num servidor de construção, como o Azure Pipelines, ou executadas localmente.

### <a name="deployment-mechanism"></a>Mecanismo de Implantação

O mecanismo de implementação é a ação usada para colocar a sua aplicação incorporada no diretório */home/site/wwwroot* da sua aplicação web. O diretório */wwwroot* é um local de armazenamento montado partilhado por todos os casos da sua aplicação web. Quando o mecanismo de implementação coloca a sua aplicação neste diretório, os seus casos recebem uma notificação para sincronizar os novos ficheiros. O Serviço de Aplicações suporta os seguintes mecanismos de implementação:

- Pontos finais kudu: [Kudu](https://github.com/projectkudu/kudu/wiki) é a ferramenta de produtividade de desenvolvedores de código aberto que funciona como um processo separado no Serviço de Aplicações Windows, e como um segundo recipiente no Serviço de Aplicações Linux. Kudu lida com implementações contínuas e fornece pontos finais HTTP para implantação, como zipdeploy.
- FTP e WebDeploy: Utilizando as credenciais do seu [site ou utilizador,](deploy-configure-credentials.md)pode carregar ficheiros via [FTP](deploy-ftp.md) ou WebDeploy. Estes mecanismos não passam por Kudu.  

Ferramentas de implantação como pipelines Azure, Jenkins e plugins editor usam um destes mecanismos de implementação.

## <a name="use-deployment-slots"></a>Utilizar ranhuras de implantação

Sempre que possível, utilize ranhuras de [implantação](deploy-staging-slots.md) ao implementar uma nova construção de produção. Ao utilizar um nível padrão de plano de aplicação ou melhor, pode implementar a sua aplicação para um ambiente de encenação, validar as suas alterações e fazer testes de fumo. Quando estiver pronto, pode trocar as suas faixas horárias de preparação e produção. A operação de troca aquece as instâncias de trabalho necessárias para corresponder à sua escala de produção, eliminando assim o tempo de inatividade.

### <a name="continuously-deploy-code"></a>Código de implantação contínua

Se o seu projeto designou ramos para testes, QA e encenação, então cada um desses ramos deve ser continuamente implantado para uma ranhura de encenação. (Isto é conhecido como o [design Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow).) Isto permite que as suas partes interessadas avaliem e testem facilmente o ramo implantado. 

A implantação contínua nunca deve ser ativada para a sua ranhura de produção. Em vez disso, o seu ramo de produção (muitas vezes mestre) deve ser implantado numa ranhura de não produção. Quando estiver pronto para libertar o ramo base, troque-o na ranhura de produção. A troca em produção — em vez de se implantar na produção — evita o tempo de inatividade e permite-lhe reverter as alterações trocando novamente. 

![Visual de utilização da ranhura](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>Implantar continuamente contentores

Para recipientes personalizados do Docker ou outros registos de contentores, desloque a imagem para uma ranhura de preparação e troque em produção para evitar o tempo de inatividade. A automatização é mais complexa do que a implementação de código, pois deve empurrar a imagem para um registo de contentores e atualizar a etiqueta de imagem na webapp.

Para cada ramo que pretende implantar para uma ranhura, instale a automatização para fazer o seguinte em cada compromisso com o ramo.

1. **Construa e marque a imagem.** Como parte do pipeline de construção, marque a imagem com o git commit ID, timestamp ou outra informação identificável. É melhor não usar a etiqueta "mais recente" padrão. Caso contrário, é difícil rastrear o código atualmente implementado, o que torna a depuração muito mais difícil.
1. **Empurre a imagem marcada.** Uma vez que a imagem é construída e marcada, o gasoduto empurra a imagem para o nosso registo de contentores. No próximo passo, a ranhura de implantação retirará a imagem marcada do registo do contentor.
1. **Atualize a ranhura de implementação com a nova etiqueta de imagem**. Quando esta propriedade for atualizada, o site reiniciará automaticamente e puxará a nova imagem do recipiente.

![Visual de utilização da ranhura](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

Há exemplos abaixo para quadros comuns de automação.

### <a name="use-azure-devops"></a>Use Azure DevOps

O Serviço de Aplicações tem [entrega contínua incorporada](deploy-continuous-deployment.md) para contentores através do Centro de Implantação. Navegue para a sua aplicação no [portal Azure](https://portal.azure.com/) e selecione **Centro de Implantação** sob **Implantação**. Siga as instruções para selecionar o seu repositório e o seu ramo. Isto configurará um oleoduto de construção e libertação de DevOps para construir, etiquetar e implantar automaticamente o seu recipiente quando novos compromissos forem empurrados para o seu ramo selecionado.

### <a name="use-github-actions"></a>Use ações gitHub

Também pode automatizar a implantação do seu contentor [com ações GitHub](containers/deploy-container-github-action.md).  O ficheiro de fluxo de trabalho abaixo irá construir e etiquetar o recipiente com o ID de compromisso, empurrá-lo para um registo de contentores e atualizar a ranhura especificada do site com a nova etiqueta de imagem.

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
    - uses: actions/checkout@master

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

As etapas anteriormente enumeradas aplicam-se a outros utilitários de automação, como o CircleCI ou o Travis CI. No entanto, é necessário utilizar o Azure CLI para atualizar as ranhuras de implementação com novas etiquetas de imagem no passo final. Para utilizar o Azure CLI no seu script de automação, gere um Diretor de Serviço utilizando o seguinte comando.

```shell
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

No seu script, `az login --service-principal`faça login na utilização, fornecendo as informações do diretor. Em seguida, `az webapp config container set` pode utilizar para definir o nome do recipiente, etiqueta, URL de registo e senha de registo. Abaixo estão alguns links úteis para você construir o seu processo CI recipiente.

- [Como iniciar sessão no Azure CLI no Circle CI](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>Considerações específicas da linguagem

### <a name="java"></a>Java

Utilize o [zipdeploy/API](deploy-zip.md) Kudu para a implementação de aplicações JAR e [wardeploy/para](deploy-zip.md#deploy-war-file) aplicações WAR. Se estiver a usar o Jenkins, pode utilizar essas APIs diretamente na sua fase de implantação. Para obter mais informações, consulte [este artigo](../jenkins/execute-cli-jenkins-pipeline.md).

### <a name="node"></a>Nó

Por padrão, kudu executa os passos`npm install`de construção para a sua aplicação Nó (). Se está a usar um serviço de construção como o Azure DevOps, então a construção de Kudu é desnecessária. Para desativar a construção kudu, crie uma definição de app, `SCM_DO_BUILD_DURING_DEPLOYMENT`com um valor de `false`.

### <a name="net"></a>.NET 

Por padrão, kudu executa os passos`dotnet build`de construção para a sua aplicação .NET ( ). Se está a usar um serviço de construção como o Azure DevOps, então a construção de Kudu é desnecessária. Para desativar a construção kudu, crie uma definição de app, `SCM_DO_BUILD_DURING_DEPLOYMENT`com um valor de `false`.

## <a name="other-deployment-considerations"></a>Outras considerações de implantação

### <a name="local-cache"></a>Local Cache

O conteúdo do Serviço de Aplicações Azure é armazenado no Armazenamento Azure e é surgido de forma duradoura como partilha de conteúdo. No entanto, algumas aplicações apenas precisam de uma loja de conteúdos de alto desempenho, apenas de leitura, que podem executar com elevada disponibilidade. Estas aplicações podem beneficiar da utilização de [cache local.](overview-local-cache.md) A cache local não é recomendada para sites de gestão de conteúdos como o WordPress.

Utilize sempre a cache local em conjunto com [as ranhuras](deploy-staging-slots.md) de implantação para evitar o tempo de inatividade. Consulte esta secção para obter informações sobre a [utilização](overview-local-cache.md#best-practices-for-using-app-service-local-cache) destas funcionalidades em conjunto.

### <a name="high-cpu-or-memory"></a>Alta CPU ou Memória

Se o seu Plano de Serviço de Aplicações estiver a utilizar mais de 90% do CPU ou memória disponível, a máquina virtual subjacente pode ter problemas em processar a sua implementação. Quando isto acontecer, aumenta temporariamente a contagem de casos para realizar a implantação. Uma vez terminada a implantação, pode devolver a contagem de ocorrências ao seu valor anterior.

Para obter mais informações sobre as melhores práticas, visite [o App Service Diagnostics](https://docs.microsoft.com/azure/app-service/overview-diagnostics) para descobrir as melhores práticas atol específicas do seu recurso.

- Navegue para a sua Web App no [portal Azure](https://portal.azure.com).
- Clique em **Diagnosticar e resolver problemas** na navegação à esquerda, o que abre diagnósticos de serviço de aplicações.
- Escolha o azulejo da página inicial **das Melhores Práticas.**
- Clique em **Boas Práticas para Disponibilidade & Desempenho** ou Boas **Práticas para Uma Configuração Ideal** para ver o estado atual da sua aplicação no que diz respeito a estas boas práticas.

Também pode utilizar este link para abrir diretamente os `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`Diagnósticos do Serviço de Aplicações para o seu recurso: .
