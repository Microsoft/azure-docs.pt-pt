---
title: Web App Tutorial - Configurar AZure API para FHIR
description: Este tutorial percorre um exemplo de implementação de uma simples aplicação web. Este primeiro tutorial descreve os pré-requisitos e a implantação da API Azure para fHIR
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.custom: devx-track-js
ms.openlocfilehash: 9b1dc7d7eee263b781d39c4beccf9388efbaa3d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334142"
---
# <a name="deploy-javascript-app-to-read-data-from-fhir-service"></a>Implementar aplicativo JavaScript para ler dados do serviço FHIR
Neste tutorial, irá implementar uma pequena aplicação JavaScript, que lê dados de um serviço FHIR. Os passos neste tutorial são:
1. Implementar um servidor FHIR
1. Registar uma aplicação cliente pública
1. Teste de acesso à aplicação
1. Crie uma aplicação web que leia estes dados FHIR

## <a name="prerequisites"></a>Pré-requisitos
Antes de iniciar este conjunto de tutoriais, você precisará dos seguintes itens:
1. Uma subscrição do Azure
1. Um inquilino do Azure Active Directory
1. [Carteiro](https://www.getpostman.com/) instalado

> [!NOTE]
> Para este tutorial, o serviço FHIR, a aplicação AD Azure e os utilizadores AD Azure estão todos no mesmo inquilino AD AZure. Se não for esse o caso, ainda pode acompanhar este tutorial, mas poderá ter de mergulhar em alguns dos documentos referenciados para fazer etapas adicionais.

## <a name="deploy-azure-api-for-fhir"></a>Implementar o Azure API for FHIR
O primeiro passo no tutorial é obter corretamente a sua API Azure para a configuração FHIR.

1. Implementar a [API Azure para fHIR](fhir-paas-portal-quickstart.md)
1. Uma vez implantado o seu API Azure para FHIR, configufique as definições [do CORS](configure-cross-origin-resource-sharing.md) indo ao seu API Azure para FHIR e selecionando CORS. 
    1. Definir **origens** para *
    1. Definir **cabeçalhos** para *
    1. Em **Métodos,** escolha **Selecione todos**
    1. Definir a **idade máxima** para **600**

## <a name="next-steps"></a>Passos Seguintes
Agora que tem a sua API Azure para fHIR implantada, está pronta para registar uma aplicação de cliente público.

>[!div class="nextstepaction"]
>[Registar aplicação cliente pública](tutorial-web-app-public-app-reg.md)
