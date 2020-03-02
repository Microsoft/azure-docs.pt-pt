---
title: Como implementar a ferramenta de rotulagem da amostra de reconhecimento de formulário
titleSuffix: Azure Cognitive Services
description: Aprenda as diferentes formas de implementar a ferramenta de rotulagem da amostra 'Reconhecimento de Formulários' para ajudar na aprendizagem supervisionada.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: pafarley
ms.openlocfilehash: fa419d7dd9668ac2ce8f2b0eb904117c7e22692d
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207846"
---
# <a name="deploy-the-sample-labeling-tool"></a>Implementar a ferramenta de rotulagem da amostra

A ferramenta de rotulagem da amostra 'Reconhecimento de Formulários' é uma aplicação que funciona num recipiente do Docker. Fornece um UI útil que pode usar para rotular manualmente documentos de formulário para fins de aprendizagem supervisionada. O [Comboio com etiquetas](./quickstarts/label-tool.md) quickstart mostra-lhe como executar a ferramenta no seu computador local, que é o cenário mais comum. 

Este guia explicará formas alternativas de implantar e executar a ferramenta de rotulagem da amostra. 

## <a name="deploy-with-azure-container-instances"></a>Implantação com instâncias de contentores Azure

Pode executar a ferramenta de etiqueta num recipiente de aplicação web Docker. Em primeiro lugar, [crie um novo recurso Web App](https://ms.portal.azure.com/#create/Microsoft.WebSite) no portal Azure. Preencha o formulário com os detalhes do seu grupo de subscrição e recursos. Introduza as seguintes informações nos campos requeridos:
* **Publicação**: Docker Container
* **Funcionamento** Sistema: Linux

Na página seguinte, preencha os seguintes campos para a configuração do contentor Docker:

* **Opções**: Recipiente Único
* **Fonte de imagem**: Registo de Contentores Azure
* **Tipo de acesso:** público
* **Imagem e etiqueta**: mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest

Os passos que se seguem são opcionais. Uma vez que a sua aplicação termine de ser implementada, pode executá-la e aceder online à ferramenta de etiquetagem.

### <a name="connect-to-azure-ad-for-authorization"></a>Ligue-se à AD Azure para autorização

Recomendamos ligar a sua aplicação web ao Azure Ative Diretor (AAD) para que apenas alguém com as suas credenciais possa iniciar sessão e utilizar a aplicação. Siga as instruções na [Configuração da sua app App Service](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) para ligar ao AAD.

## <a name="next-steps"></a>Passos seguintes

Volte ao [Comboio com etiquetas](./quickstarts/label-tool.md) rapidamente para aprender a usar a ferramenta para rotular manualmente os dados de formação e fazer aprendizagem supervisionada.
