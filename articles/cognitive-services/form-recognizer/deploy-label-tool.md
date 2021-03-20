---
title: Como implementar a ferramenta de rotulagem da amostra do Reconhecimento de Formulários
titleSuffix: Azure Cognitive Services
description: Aprenda as diferentes formas de implementar a ferramenta de rotulagem da amostra do Form Recogniser para ajudar na aprendizagem supervisionada.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 02/11/2021
ms.author: lajanuar
ms.openlocfilehash: 0f5f0714235ee23624b3a199eac744155d2bbdd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101093390"
---
# <a name="deploy-the-sample-labeling-tool"></a>Implementar a ferramenta de etiquetagem de exemplo

A ferramenta de rotulagem da amostra do Form Recogniser é uma aplicação que fornece uma interface de utilizador simples (UI), que pode utilizar para rotular manualmente formulários (documentos) para fins de aprendizagem supervisionada. Neste artigo, forneceremos links e instruções que o ensinam a:

* [Executar a ferramenta de rotulagem de amostra localmente](#run-the-sample-labeling-tool-locally)
* [Colocar a ferramenta de rotulagem da amostra numa instância de contentores Azure (ACI)](#deploy-with-azure-container-instances-aci)
* [Utilizar e contribuir para a ferramenta de rotulagem do formulário OCR de código aberto](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>Executar a ferramenta de rotulagem de amostra localmente

A forma mais rápida de começar a rotular dados é executar a ferramenta de rotulagem da amostra localmente. O seguinte quickstart utiliza a API do Reconhecimento de Formulários e a ferramenta de rotulagem da amostra para treinar um modelo personalizado com dados etiquetados manualmente. 

* [Quickstart: Label forms, train a model, and analyze a form using the sample labeling tool](./quickstarts/label-tool.md).

## <a name="deploy-with-azure-container-instances-aci"></a>Implantar com instâncias de contentores Azure (ACI)

Antes de começarmos, é importante notar que há duas maneiras de implantar a ferramenta de rotulagem da amostra para uma Instância de Contentores Azure (ACI). Ambas as opções são utilizadas para executar a ferramenta de rotulagem da amostra com ACI:

* [Utilizar o portal do Azure](#azure-portal)
* [Com a CLI do Azure](#azure-cli)

### <a name="azure-portal"></a>Portal do Azure

Siga estes passos para criar um novo recurso utilizando o portal Azure: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/signin/index/).
2. Selecione **Criar um recurso**.
3. Em seguida, selecione **Web App**.

   > [!div class="mx-imgBorder"]
   > ![Selecione aplicativo web](./media/quickstarts/create-web-app.png)

4. Em primeiro lugar, certifique-se de que o separador **Básicos** está selecionado. Agora, vai precisar de fornecer algumas informações:

   > [!div class="mx-imgBorder"]
   > ![Selecione Básicos](./media/quickstarts/select-basics.png)
   * Subscrição - Selecione uma subscrição Azure existente
   * Grupo de Recursos - Pode reutilizar um grupo de recursos existente ou criar um novo para este projeto. Recomenda-se a criação de um novo grupo de recursos.
   * Nome - Dê um nome à sua aplicação web. 
   * Publicar - Selecione **Estivador Container**
   * Sistema Operativo - Selecione **Linux**
   * Região - Escolha uma região que faça sentido para si.
   * Plano Linux - Selecione um nível de preços/plano para o seu serviço de aplicações. 

   > [!div class="mx-imgBorder"]
   > ![Configure a sua aplicação web](./media/quickstarts/select-docker.png)

5. Em seguida, selecione o **separador Docker.**

   > [!div class="mx-imgBorder"]
   > ![Selecione Docker](./media/quickstarts/select-docker.png)

6. Agora vamos configurar o seu contentor Docker. Todos os campos são necessários, salvo indicação em contrário:
<!-- markdownlint-disable MD025 -->
# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)

* Opções - Selecione **Um Único Recipiente**
* Fonte de imagem - Selecione **Registo Privado** 
* URL do servidor - Desemiste isto para `https://mcr.microsoft.com`
* Nome de utilizador (Opcional) - Crie um nome de utilizador. 
* Palavra-passe (Opcional) - Crie uma senha segura de que se lembrará.
* Imagem e etiqueta - Desemiste isto para `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview`
* Implementação Contínua - Desave-o **se** pretender receber atualizações automáticas quando a equipa de desenvolvimento esprodução de alterações na ferramenta de rotulagem da amostra.
* Comando de arranque - Desemalte isto para `./run.sh eula=accept`

# <a name="v20"></a>[v2.0](#tab/v2-0)  

* Opções - Selecione **Um Único Recipiente**
* Fonte de imagem - Selecione **Registo Privado** 
* URL do servidor - Desemiste isto para `https://mcr.microsoft.com`
* Nome de utilizador (Opcional) - Crie um nome de utilizador. 
* Palavra-passe (Opcional) - Crie uma senha segura de que se lembrará.
* Imagem e etiqueta - Desemiste isto para `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
* Implementação Contínua - Desave-o **se** pretender receber atualizações automáticas quando a equipa de desenvolvimento esprodução de alterações na ferramenta de rotulagem da amostra.
* Comando de arranque - Desemalte isto para `./run.sh eula=accept`

 ---

   > [!div class="mx-imgBorder"]
   > ![Configure Docker](./media/quickstarts/configure-docker.png)

7. Já está. Em seguida, selecione **Review + Create** e, em seguida, **Criar** para implementar a sua aplicação web. Quando estiver concluído, pode aceder à sua aplicação web no URL fornecido na **Visão Geral** do seu recurso.

> [!NOTE]
> Ao criar a sua aplicação web, também pode configurar a autorização/autenticação. Isto não é necessário para começar.

> [!IMPORTANT]
> Poderá ser necessário ativar o TLS para a sua aplicação web para a visualizar no seu `https` endereço. Siga as instruções em [Ativar um ponto final TLS](../../container-instances/container-instances-container-group-ssl.md) para configurar um recipiente de sidecar do que ativar o TLS/SSL para a sua aplicação web.
<!-- markdownlint-disable MD001 -->
### <a name="azure-cli"></a>CLI do Azure

Como alternativa à utilização do portal Azure, pode criar um recurso utilizando o Azure CLI. Antes de continuar, terá de instalar o [Azure CLI](/cli/azure/install-azure-cli). Pode saltar este passo se já estiver a trabalhar com o Azure CLI. 

Há algumas coisas que precisa saber sobre este comando:

* `DNS_NAME_LABEL=aci-demo-$RANDOM` gera um nome DNS aleatório. 
* Esta amostra pressupõe que tem um grupo de recursos que pode usar para criar um recurso. Substitua `<resource_group_name>` por um grupo de recursos válido associado à sua subscrição. 
* Terá de especificar onde pretende criar o recurso. `<region name>`Substitua-a pela região desejada para a aplicação web.
* Este comando aceita automaticamente a EULA.

A partir do Azure CLI, executar este comando para criar um recurso de aplicação web para a ferramenta de rotulagem da amostra:

<!-- markdownlint-disable MD024 -->
# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"

```

# <a name="v20"></a>[v2.0](#tab/v2-0)


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


---

### <a name="connect-to-azure-ad-for-authorization"></a>Ligue-se à Azure AD para autorização

Recomenda-se que conecte a sua aplicação web ao Azure Ative Directory. Isto garante que apenas os utilizadores com credenciais válidas podem iniciar sôm e utilizar a sua aplicação web. Siga as instruções na [Configuração da sua aplicação De Serviço de Aplicações](../../app-service/configure-authentication-provider-aad.md) para ligar ao Azure Ative Directory.

## <a name="open-source-on-github"></a>Fonte aberta no GitHub

A Ferramenta de Rotulagem do Formulário OCR também está disponível como um projeto de código aberto no GitHub. A ferramenta é uma aplicação web construída usando React + Redux, e está escrita no TypeScript. Para saber mais ou contribuir, consulte [a Ferramenta de Rotulagem do Formulário OCR](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

## <a name="next-steps"></a>Passos seguintes

Utilize o [Comboio com etiquetas](./quickstarts/label-tool.md) de arranque rápido para aprender a usar a ferramenta para rotular manualmente dados de treino e realizar aprendizagem supervisionada.