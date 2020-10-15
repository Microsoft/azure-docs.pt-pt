---
title: Ver e gerir sensores a bordo no Azure Defender para IoT
description: Este artigo descreve como visualizar e eliminar sensores a bordo, bem como descarregar novos ficheiros de ativação para sensores a bordo no Azure Defender para IoT.
author: rkarlin
ms.author: rkarlin
ms.date: 10/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 6aec19d413e1730bc1599f6cbac1c62e9b304ecd
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094528"
---
# <a name="view-and-manage-onboarded-sensors"></a>Ver e gerir sensores a bordo

Este artigo descreve como visualizar e apagar sensores a bordo, bem como descarregar novos ficheiros de ativação para sensores a bordo.

## <a name="update-sensor-management-mode"></a>Atualizar o modo de gestão de sensores

É melhor atualizar o modo de gestão do seu sensor. Quando o fizer, tem de remover o sensor atual da página de gestão do sensor e carregar um novo ficheiro de Ativação.

- **Trabalhar no modo Cloud Managed em vez do modo Gerido localmente**: Atualize o ficheiro de ativação para o seu sensor gerido localmente com um ficheiro de ativação para um sensor Cloud Managed. Após a reativação, as deteções de sensores são apresentadas tanto no sensor como no Azure Defender para o portal IoT. Após o ficheiro de reativação ser carregado com sucesso, as informações de alerta recentemente detetadas são enviadas para a Azure.

- **Trabalhar no modo Gerido localmente em vez do modo Cloud Managed**: Atualize o ficheiro de ativação para um sensor Cloud Managed com um ficheiro de ativação para um sensor gerido localmente. Após a reativação, as informações de deteção do sensor só são apresentadas no sensor.

- **Associe o sensor a um novo Hub IoT**: Pode querer associar o seu sensor a um novo Hub IoT. Para isso, reregumente o sensor e o upload de um novo ficheiro de ativação.

**Para reativar o sensor:**

1. Navegue para o Azure Defender para ioT, página **de Gestão de Sensores.**

2. Selecione o sensor para o qual pretende carregar um novo ficheiro de ativação.

3. Apague-o.

4. A bordo do sensor novamente a partir da página **de bordo** no novo modo ou com um novo Hub IoT.

5. Descarregue o ficheiro de ativação a partir da página de **ficheiros de ativação de descarregamento.**

6. Faça login no Azure Defender para consola de sensores IoT.

7. Na consola do sensor, selecione **Definições do Sistema** e, em seguida, selecione **Reativação**.

   ![Screenshot da vista de reativação](media/updates/image14.png)

8. Selecione **Upload** e selecione o ficheiro que guardou.

9. **Selecione Ativar**.
 
## <a name="delete-sensors"></a>Eliminar sensores

Se eliminar um sensor gerido pela nuvem, as informações não serão enviadas para o Hub IoT.

Elimine os sensores geridos localmente quando já não estiver a trabalhar com eles.

**Para eliminar sensores:**

1. Navegue para a página Azure Defender para **gestão de sensores** IoT.

2. Selecione os sensores que pretende eliminar.

3. **Selecione Eliminar o sensor**.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as opções de configuração, continue a guiar como fazer para a configuração do módulo.
> [!div class="nextstepaction"]
> [Configuração do módulo como guiar](./how-to-agent-configuration.md)
