---
title: Alternativas para o portal de desenvolvimento de auto-hospedagem
titleSuffix: Azure API Management
description: Saiba mais sobre abordagens alternativas que pode utilizar quando se auto-hospedar um portal de desenvolvedores na Azure API Management.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: cb14ecd05c1590667ac9b5618b3f0de9da30ce96
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741870"
---
# <a name="alternative-approaches-to-self-host-developer-portal"></a>Abordagens alternativas ao portal de desenvolvedores auto-hospedeiros

Existem várias abordagens alternativas que pode explorar quando [se auto-hospeda um portal de desenvolvedores:](developer-portal-self-host.md)

* Use as construções de produção do designer e da editora.

* Utilize uma App de função Azure para publicar o seu portal.

* Forneça os ficheiros do seu portal com uma Rede de Entrega de Conteúdos (CDN) para reduzir os tempos de carregamento de página.

Este artigo fornece informações sobre cada uma destas abordagens. 

Se ainda não o fez, crie um [ambiente local](developer-portal-self-host.md#step-1-set-up-local-environment) para o mais recente lançamento do portal de desenvolvimento.

## <a name="build-for-production"></a>Construir para produção

Se quiser acolher o ambiente de desenvolvimento do portal online para fins de colaboração, utilize as construções de produção do designer e da editora. A produção constrói os ficheiros, exclui mapas de origem, etc.

Crie um pacote no `./dist/designer` diretório executando o comando:

```sh
npm run build-designer
```

O resultado é uma aplicação de uma única página, pelo que ainda pode implantá-la num anfitrião estático, como o Website Estático de Armazenamento de Blob Azure.

Da mesma forma, coloque uma editora compilada e otimizada na `./dist/publisher` pasta:

```sh
npm run build-publisher
```

## <a name="use-function-app-to-publish-the-portal"></a>Use a App de função para publicar o portal

Execute o passo de publicação na nuvem como uma alternativa para executá-lo localmente.

Para implementar a publicação com uma App de Função Azure, precisa dos seguintes pré-requisitos:

- [Criar uma Função Azure](../azure-functions/functions-create-first-azure-function.md). A Função tem de ser uma função de linguagem JavaScript.
- Instalar ferramentas principais de funções Azure:
    ```console
    npm install –g azure-function-core-tools
    ```

### <a name="step-1-configure-output-storage"></a>Passo 1: Configurar o armazenamento da saída

O upload do conteúdo diretamente para o website de hospedagem ("$web" recipiente de armazenamento de saída), em vez de uma pasta local. Configure esta alteração no `./src/config.publish.json` ficheiro:

```json
{
   ...
   "outputBlobStorageContainer": "$web",
   "outputBlobStorageConnectionString": "DefaultEndpointsProtocol=...",
   ...
}
```

### <a name="step-2-build-and-deploy-the-function-app"></a>Passo 2: Construir e implementar a App de Função

Existe uma função de gatilho HTTP na `./examples` pasta. Para construí-la e colocá-la `./dist/function` em, executar o seguinte comando:

```sh
npm run build-function
```

Em seguida, inscreva-se no CLI Azure e implemente-o:

```sh
az login
cd ./dist/function
func azure functionapp publish <function app name>
```

Uma vez implantado, pode invocá-lo com uma chamada HTTP:

```sh
curl -X POST https://<function app name>.azurewebsites.net/api/publish
```

## <a name="hosting-and-cdn"></a>Hospedagem e CDN

Em [auto-anfitrião, sugerimos](developer-portal-self-host.md) usar uma conta de armazenamento Azure para hospedar o seu website. No entanto, pode publicar os ficheiros através de qualquer solução, incluindo serviços de prestadores de hospedagem.

Também pode fazer frente aos ficheiros com uma Rede de Entrega de Conteúdos (CDN) para reduzir os tempos de carregamento de páginas. Recomendamos a utilização de [Azure CDN](https://azure.microsoft.com/services/cdn/).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o portal de desenvolvedores:

- [Descrição geral do portal do programador da Gestão de API do Azure](api-management-howto-developer-portal.md)
