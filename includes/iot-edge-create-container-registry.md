---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: f8cb734e8a57e3b0ee114ae20f454d0034e9cdc9
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387625"
---
## <a name="create-a-container-registry"></a>Criar um registo de contentor

Neste tutorial, utiliza-se a extensão Azure IoT Tools para construir um módulo e criar uma imagem de **recipiente** a partir dos ficheiros. Em seguida, vai enviar essa imagem para um **registo** que armazena e gere as suas imagens. Por fim, vai implementar a imagem a partir do registo para ser executada no seu dispositivo IoT Edge.

Pode utilizar qualquer registo compatível com o Docker para guardar as imagens do seu contentor. Dois serviços populares de registo do Docker são [o Registo de Contentores Azure](https://docs.microsoft.com/azure/container-registry/) e [o Docker Hub.](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) Este tutorial utiliza o Azure Container Registry.

Se ainda não tiver um registo de contentores, siga estes passos para criar um novo em Azure:

1. No [Portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Contentores** > **Registo de Contentor**.

2. Indique os valores seguintes para criar o seu registo de contentor:

   | Campo | Valor |
   | ----- | ----- |
   | Subscrição | Selecione uma subscrição na lista pendente. |
   | Grupo de recursos | Recomendamos que utilize o mesmo grupo de recursos para todos os recursos de teste que criou durante os inícios rápidos e tutoriais do IoT Edge. Por exemplo, **IoTEdgeResources**. |
   | Nome de registo | Indique um nome exclusivo. |
   | Localização | Escolha uma localização perto de si. |
   | SKU | Selecione **Básico**. |

3. Selecione **Criar**.

4. Após a criação do registo do contentor, procure-o e, a partir do painel esquerdo, selecione **as teclas** de acesso do menu localizado em **Definições**.

5. Clique para permitir ao utilizador do Administrador visualizar o **nome de utilizador** e a **palavra-passe** para o seu registo de contentores.

6. Copie os valores para **o servidor de Login,** **nome de utilizador**e **password** e guarde-os num local conveniente. Utiliza estes valores ao longo deste tutorial para fornecer acesso ao registo do contentor.

   ![Copiar servidor de login, nome de utilizador e senha para registo de contentores](./media/iot-edge-create-container-registry/registry-access-key.png)