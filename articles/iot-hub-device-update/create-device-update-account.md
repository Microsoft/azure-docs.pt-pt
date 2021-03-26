---
title: Crie uma conta de atualização de dispositivos na Atualização do Dispositivo para o Azure IoT Hub | Microsoft Docs
description: Crie uma conta de atualização do dispositivo na Atualização do Dispositivo para O Hub IoT Azure.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 5956b7b74d27a4f9a2b79ee3950c8ac765610c70
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558487"
---
# <a name="device-update-for-iot-hub-resource-management"></a>Atualização de Dispositivos da Gestão de Recursos do Hub IoT

Para começar com a Atualização do Dispositivo, terá de criar uma conta de Atualização de Dispositivos, instância e definir funções de controlo de acesso. 

## <a name="prerequisites"></a>Pré-requisitos

* Acesso a um hub IoT. Recomenda-se que utilize um nível S1 (Standard) ou superior. 
* Navegadores suportados:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>Criar uma conta de atualização de dispositivo

1. Ir para o [portal Azure](https://portal.azure.com)

2. Clique em Criar um recurso e procure por "Atualização de Dispositivos para IoT Hub"

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="Screenshot da Atualização do Dispositivo para recurso IoT Hub." lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. Clique em Criar Atualização de Dispositivos de > para IoT Hub

4. Especifique a Subscrição Azure para ser associada à sua Conta de Atualização de Dispositivos e Grupo de Recursos

5. Especifique um nome e localização para a sua conta de atualização de dispositivo

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="Screenshot dos detalhes da conta." lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > Você pode ir à [página Azure Products-by-região](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) para descobrir as regiões onde a Atualização de Dispositivos para IoT Hub está disponível. Se a Atualização do Dispositivo para ioT Hub não estiver disponível na sua região, pode optar por criar uma conta numa região disponível mais próxima. 

6. Clique em "Seguinte: Revisão + criar>"

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="Screenshot da revisão de detalhes da conta." lightbox="media/create-device-update-account/account-review.png":::

7. Reveja os detalhes e, em seguida, selecione "Criar". Verá que a sua implantação está em andamento. 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="Screenshot da implementação de conta em andamento." lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. Verá a alteração do estado de implantação para "completo" em poucos minutos. Clique em "Ir para o recurso"

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="Screenshot da implementação da conta completa." lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>Criar uma instância de atualização de dispositivo 

Uma instância de Atualização do Dispositivo está associada a um único hub IoT. Selecione o hub IoT que será utilizado com a Atualização do Dispositivo. Criaremos uma nova política de Acesso Partilhado durante este passo para garantir que a Atualização do Dispositivo utiliza apenas as permissões necessárias para trabalhar com o IoT Hub (registo de escrita e ligação de serviço). Esta política garante que o acesso se limita apenas à Atualização do Dispositivo.

Para criar uma instância de Atualização de Dispositivos depois de uma conta ter sido criada.

1. Assim que estiver no seu recurso de conta recém-criado, vá à lâmina "Instâncias" de Gestão de Instâncias

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="Screenshot da gestão de casos dentro da conta." lightbox="media/create-device-update-account/instance-blade.png":::

2. Clique em "Criar e especificar um nome de instância e selecionar o seu Hub IoT

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="Screenshot de detalhes de exemplo." lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > O Hub IoT que liga ao seu recurso de Atualização de Dispositivos não precisa de estar na mesma região que a sua Conta de Atualização de Dispositivos. No entanto, para um melhor desempenho, recomenda-se que o seu IoT Hub esteja numa região igual ou próxima da região da sua conta de Atualização de Dispositivos. 

3. Clique em "Criar". Verá o caso num estado de "Criar". 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="Screenshot de exemplo a criar." lightbox="media/create-device-update-account/instance-creating.png":::

4. Permitir que 5-10 minutos para a colocação por exemplo esteja concluída. Refresque o estatuto até ver o "Estado de Provisionamento" virar-se para "Bem sucedido".

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="Screenshot de exemplo criação conseguiu." lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>Configure IoT Hub 

Para que a Atualização do Dispositivo receba notificações de alteração do IoT Hub, a Atualização do Dispositivo integra-se com o "Built-In" Event Hub. Clicar no botão "Configure IoT Hub" configura as rotas de mensagens necessárias e a política de acesso necessária para comunicar com dispositivos IoT. 

Para configurar o IoT Hub

1. Uma vez que a instância "Estado de provisionamento" se transforme em "Bem sucedido", selecione a instância na lâmina de gestão de exemplos. Clique em "Configure IoT Hub"

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="Screenshot de configurar o IoT Hub para um exemplo." lightbox="media/create-device-update-account/instance-configure.png":::

2. Selecione "Concordo em fazer estas alterações"

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="Screenshot de concordar em configurar o IoT Hub, por exemplo." lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. Clique em "Update"

[Conheça as rotas de mensagem configuradas.](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>Configure funções de controlo de acesso

Para que outros utilizadores tenham acesso à Atualização do Dispositivo, os utilizadores devem ter acesso a este recurso. 

1. Ir para o controlo de acesso (IAM) dentro da conta de Atualização de Dispositivos

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="Screenshot do controlo de acesso dentro da conta De atualização do dispositivo." lightbox="media/create-device-update-account/account-access-control.png":::

2. Clique em "Adicionar atribuições de funções"

3. Em "Selecione uma Função", selecione uma função de Atualização de Dispositivos a partir das opções dadas
     - Administrador de atualização de dispositivos
     - Leitor de atualização de dispositivos
     - Administrador de conteúdo de atualização de dispositivos
     - Leitor de conteúdo de atualização de dispositivos
     - Administrador de implementação de atualização de dispositivos
     - Leitor de implementações de atualização de dispositivos
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="Screenshot das atribuições de funções de controlo de acesso dentro da conta De Atualização do Dispositivo." lightbox="media/create-device-update-account/role-assignment.png":::
    
    [Saiba mais sobre o controlo de acesso baseado em funções na Atualização do Dispositivo para IoT Hub](device-update-control-access.md) 
    
4. Atribuir acesso a um utilizador ou grupo AD Azure
5. Clicar em Guardar
6. Está agora pronto para utilizar a experiência de Atualização de Dispositivos dentro do seu Hub IoT

## <a name="next-steps"></a>Passos seguintes

Tente atualizar um dispositivo utilizando um dos seguintes tutoriais rápidos:

 - [Atualização do dispositivo num simulador](device-update-simulator.md)
 - [Atualização do dispositivo em Raspberry Pi](device-update-raspberry-pi.md)
 - [Atualização do dispositivo no Ubuntu Server 18.04 x64 Package agent](device-update-ubuntu-agent.md)

[Saiba mais sobre a conta e instância da atualização do dispositivo.](device-update-resources.md) 

[Saiba mais sobre as funções de controlo de acesso à atualização do dispositivo. ](device-update-control-access.md) 

