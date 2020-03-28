---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: f63510771e4bd71a3ab6cf048bc5fb5296042a4d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75564740"
---
## <a name="create-a-container-registry"></a>Criar um registo de contentores

Neste tutorial, utiliza a extensão Azure IoT Tools para construir um módulo e criar uma imagem de **recipiente** a partir dos ficheiros. Em seguida, vai enviar essa imagem para um **registo** que armazena e gere as suas imagens. Por fim, vai implementar a imagem a partir do registo para ser executada no seu dispositivo IoT Edge.

Pode utilizar qualquer registo compatível com o Docker para guardar as imagens do seu contentor. Dois serviços populares de registo docker são O Registo de [Contentores Azure](https://docs.microsoft.com/azure/container-registry/) e [Docker Hub.](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) Este tutorial utiliza o Azure Container Registry.

Se ainda não tem um registo de contentores, siga estes passos para criar um novo em Azure:

1. No [Portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Contentores** > **Registo de Contentor**.

2. Indique os valores seguintes para criar o seu registo de contentor:

   | Campo | Valor |
   | ----- | ----- |
   | Nome de registo | Indique um nome exclusivo. |
   | Subscrição | Selecione uma subscrição na lista pendente. |
   | Grupo de recursos | Recomendamos que utilize o mesmo grupo de recursos para todos os recursos de teste que criou durante os inícios rápidos e tutoriais do IoT Edge. Por exemplo, **IoTEdgeResources**. |
   | Localização | Escolha uma localização perto de si. |
   | Utilizador admin | Defina para **Ativar**. |
   | SKU | Selecione **Básico**. |

3. Selecione **Criar**.

4. Depois de criado o registo de contentores, navegue até ele e, a partir do painel esquerdo, selecione **as teclas** de acesso do menu localizado em **Definições**.

5. Copie os valores para **o servidor de login,** **nome de utilizador**e **palavra-passe** e guarde-os em algum lugar conveniente. Utiliza estes valores ao longo deste tutorial para fornecer acesso ao registo de contentores.

   ![Copiar servidor de login, nome de utilizador e senha para registo de contentores](./media/iot-edge-create-container-registry/registry-access-key.png)