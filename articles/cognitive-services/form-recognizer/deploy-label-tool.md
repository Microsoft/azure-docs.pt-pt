---
title: Como implementar a ferramenta de rotulagem da amostra de reconhecimento de formulário
titleSuffix: Azure Cognitive Services
description: Aprenda as diferentes formas de implementar a ferramenta de rotulagem da amostra 'Reconhecimento de Formulários' para ajudar na aprendizagem supervisionada.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 27afbafcadb4c482e97e1d003706e7d2712e63c9
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117272"
---
# <a name="deploy-the-sample-labeling-tool"></a>Implementar a ferramenta de etiquetagem de exemplo

A ferramenta de rotulagem da amostra 'Reconhecimento de Formulários' é uma aplicação que fornece uma interface simples de utilizador (UI), que pode utilizar para rotular manualmente formulários (documentos) para fins de aprendizagem supervisionada. Neste artigo, forneceremos links e instruções que o ensinam a:

* [Executar a ferramenta de rotulagem de amostras localmente](#run-the-sample-labeling-tool-locally)
* [Implante a ferramenta de rotulagem da amostra para uma instância de contentores Azure (ACI)](#deploy-with-azure-container-instances-aci)
* [Utilizar e contribuir para a ferramenta de rotulagem de formulário seleções OCR de código aberto](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>Executar a ferramenta de rotulagem de amostras localmente

A forma mais rápida de começar a rotular os dados é executar a ferramenta de rotulagem de amostras localmente. O quickstart seguinte utiliza a API REST API do Reconhecimento de Formulários e a ferramenta de rotulagem da amostra para treinar um modelo personalizado com dados manualmente rotulados. 

* [Arranque rápido: Rotular formas, treinar um modelo e analisar um formulário utilizando a ferramenta](./quickstarts/label-tool.md)de rotulagem da amostra .

## <a name="deploy-with-azure-container-instances-aci"></a>Implantação com instâncias de contentores Azure (ACI)

Antes de começarmos, é importante notar que existem duas maneiras de implementar a ferramenta de rotulagem de amostras para um Caso de Contentores Azure (ACI). Ambas as opções são utilizadas para executar a ferramenta de rotulagem de amostras com ACI: 

* [Utilizar o portal do Azure](#azure-portal)
* [Com a CLI do Azure](#azure-cli)

### <a name="azure-portal"></a>Portal do Azure

Siga estes passos para criar um novo recurso utilizando o portal Azure: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/signin/index/).
2. Selecione **Criar um recurso**. 
3. Em seguida, selecione **Web App**. 

   > [!div class="mx-imgBorder"]
   > ![Selecione aplicativo web](./media/quickstarts/formre-create-web-app.png)
   
4. Em primeiro lugar, certifique-se de que o separador **Basics** é selecionado. Agora, vai precisar de fornecer alguma sinuosa: 

   > [!div class="mx-imgBorder"]
   > ![Selecione Básicos](./media/quickstarts/formre-select-basics.png)
   * Subscrição - Selecione uma subscrição Azure existente
   * Grupo de Recursos - Pode reutilizar um grupo de recursos existente ou criar um novo para este projeto. Recomenda-se a criação de um novo grupo de recursos.
   * Nome - Dê um nome à sua aplicação web. 
   * Publicar - Selecione **Docker Container**
   * Sistema Operativo - Selecione **Linux**
   * Região - Escolha uma região que faça sentido para si.
   * Plano Linux - Selecione um nível/plano de preços para o seu serviço de aplicações. 

   > [!div class="mx-imgBorder"]
   > ![Configure a sua aplicação web](./media/quickstarts/formre-select-docker-linux.png)

5. Em seguida, selecione o separador **Docker.** 

   > [!div class="mx-imgBorder"]
   > ![Selecione Docker](./media/quickstarts/formre-select-docker.png)

6. Agora vamos configurar o seu contentor Docker. Todos os campos são necessários, salvo indicação em contrário:

   * Opções - Selecione **Recipiente Único**
   * Fonte de Imagem - Selecione **Registo Privado** 
   * URL do servidor - Delineie isto para`https://mcr.microsoft.com`
   * Nome de utilizador (Opcional) - Crie um nome de utilizador. 
   * Palavra-passe (Opcional) - Crie uma palavra-passe segura de que se lembrará.
   * Imagem e etiqueta - Definir isto para`mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
   * Implementação Contínua - Desloque-o para **Ligar** se pretender receber atualizações automáticas quando a equipa de desenvolvimento fizer alterações na ferramenta de rotulagem da amostra.
   * Comando de arranque - Definir isto para`./run.sh eula=accept`

   > [!div class="mx-imgBorder"]
   > ![Configure Docker](./media/quickstarts/formre-configure-docker.png)

7. Já está. Em seguida, selecione **Review + Create**e, em seguida, **Criar** para implementar a sua aplicação web. Quando estiver concluído, pode aceder à sua aplicação web no URL fornecido na **visão geral** do seu recurso.

> [!NOTE]
> Ao criar a sua aplicação web, também pode configurar a autorização/autenticação. Isto não é necessário para começar. 

### <a name="azure-cli"></a>CLI do Azure

Como alternativa à utilização do portal Azure, pode criar um recurso utilizando o Azure CLI. Antes de continuar, terá de instalar o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Pode saltar este passo se já estiver a trabalhar com o Azure CLI. 

Há algumas coisas que precisa de saber sobre este comando:

* `DNS_NAME_LABEL=aci-demo-$RANDOM`gera um nome DNS aleatório. 
* Esta amostra pressupõe que você tem um grupo de recursos que você pode usar para criar um recurso. Substitua-o `<resource_group_name>` por um grupo de recursos válido associado à sua subscrição. 
* Precisa especificar onde quer criar o recurso. Substitua-a `<region name>` pela região desejada para a aplicação web. 
* Este comando aceita automaticamente o EULA.

A partir do Azure CLI, execute este comando para criar um recurso de aplicação web para a ferramenta de rotulagem de amostras: 

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"
```

### <a name="connect-to-azure-ad-for-authorization"></a>Ligue-se à AD Azure para autorização

Recomenda-se que ligue a sua aplicação web ao Azure Ative Directory. Isto garante que apenas os utilizadores com credenciais válidas podem iniciar sessão e utilizar a sua aplicação web. Siga as instruções na [Configuração da sua app App Service](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) para ligar ao Diretório Ativo Azure.

## <a name="open-source-on-github"></a>Fonte aberta no GitHub

A Ferramenta de Rotulagem de Formulários OCR também está disponível como um projeto de código aberto no GitHub. A ferramenta é uma aplicação web construída com react + Redux, e está escrita no TypeScript. Para saber mais ou contribuir, consulte a Ferramenta de Rotulagem de [Formulários OCR](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

## <a name="next-steps"></a>Passos seguintes

Utilize o [Comboio com etiquetas](./quickstarts/label-tool.md) quickstart para aprender a usar a ferramenta para rotular manualmente os dados de treino e realizar uma aprendizagem supervisionada.
